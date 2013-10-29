For interactive session, Ivy Bridge nodes:
qsub -I -q devel -l select=103:ncpus=20:model=ivy,walltime=02:00:00
qsub -I -q long -l select=103:ncpus=20:model=ivy,walltime=120:00:00
qsub -I -q long -l select=103:ncpus=20:model=ivy,min_walltime=30:00,max_walltime=120:00:00
qsub -I -q normal -l select=103:ncpus=20:model=ivy,walltime=8:00:00
qsub -q ecco -I -W group_list=g26209 -l select=400:ncpus=20:aoe=sles11,walltime=100:00:00 -m abe -M menemenlis@jpl.nasa.gov

For batch submission:
qsub -q devel -l select=103:ncpus=20:model=ivy,walltime=02:00:00 runscript
qsub qsub_llc2160.csh 

==============

cd ~/llc_2160
cvs co MITgcm_code
cvs co MITgcm_contrib/llc_hires/llc_2160
cd MITgcm
module purge
module load comp-intel/2012.0.032 mpi-sgi/mpt.2.06rp16  netcdf/4.0
mkdir build run
lfs setstripe -c -1 run
cd build
cp ../../MITgcm_contrib/llc_hires/llc_2160/code/SIZE.h_90x90x7488 SIZE.h
../tools/genmake2 -of \
 ../../MITgcm_contrib/llc_hires/llc_2160/code-async/linux_amd64_ifort+mpi_ice_nas -mpi -mods \
 '../../MITgcm_contrib/llc_hires/llc_2160/code ../../MITgcm_contrib/llc_hires/llc_2160/code-async'
make depend
make -j 16
cd ../run
ln -sf ../build/mitgcmuv .
ln -sf /nobackup/dmenemen/tarballs/llc_2160/run_template/* .
ln -sf /nobackup/dmenemen/forcing/ECMWF_operational/* .
cp ../../MITgcm_contrib/llc_hires/llc_2160/input/* .
mv data.exch2_90x90x7488 data.exch2
mpiexec -n 8000 ./mitgcmuv

==============

look at output

for ts=[0 120 600:10:980 1080:120:2280]
    fld=quikread_llc(['Eta.' myint2str(ts,10) '.data'],2160);
    clf,quikplot_llc(fld),caxis([-2.5 2]),thincolorbar
    title(ts)
    pause(.1)
end

==============

to determine empty tiles:
grep Empty STDOUT.*

==============

memory requirements:
nPx  sNx sNy nSx cpu node0        total           rank0 rankm
936  180 180   2 san node ran out of memory and crashed with singlecpuio
1053 240 240   1 san node ran out of memory and crashed with singlecpuio
1300 216 216   1 san node ran out of memory and crashed with singlecpuio
1872 180 180   1 wes node ran out of memory and crashed with singlecpuio
1872 180 180   1 wes 21,377,644kb 3,294,676,080kb node ran out of memory with singlecpuio and bigmem=true:mem=90GB for node 0
1872 180 180   1 san node ran out of memory and crashed with singlecpuio
1872 180 180   1 san 11,558,588kb 1,356,676,140kb singlecpuio=.FALSE.
2925 144 144   1 san  8,374,668kb 1,538,454,112kb 886MB 892MB singlecpuio=.FALSE.
2925 144 144   1 san 27,284,996kb 4,942,949,704kb node ran out of memory and crashed with singlecpuio
3328 135 135   1 san rank 0 run out of memory
3328 135 135   1 san some random node run out of memory (full node for rank 0)
4212 120 120   1 san node ran out of memory
5200 108 108   1 san node ran out of memory

=============

2             =    2
3             =    3
2*2           =    4
5             =    5
2*3           =    6
2*2*2         =    8
3*3           =    9
2*5           =   10
2*2*3         =   12
3*5           =   15
2*2*2*2       =   16
2*3*3         =   18
2*2*5         =   20
2*2*2*3       =   24
3*3*3         =   27
2*3*5         =   30
2*2*3*3       =   36
2*2*2*5       =   40
3*3*5         =   45
2*2*2*2*3     =   48 * 45
2*3*3*3       =   54 * 40
2*2*3*5       =   60 * 36
2*2*2*3*3     =   72 * 30
2*2*2*2*5     =   80 * 27
2*3*3*5       =   90 * 24
2*2*3*3*3     =  108 * 20
2*2*2*3*5     =  120 * 18
3*3*3*5       =  135 * 16
2*2*2*2*3*3   =  144 * 15
2*2*3*3*5     =  180 * 12
2*2*2*3*3*3   =  216 * 10
2*2*2*2*3*5   =  240 *  9
2*3*3*3*5     =  270 *  8
2*2*2*3*3*5   =  360 *  6
2*2*2*2*3*3*3 =  432 *  5
2*2*3*3*3*5   =  540 *  4
2*2*2*2*3*3*5 =  720 *  3
2*2*2*3*3*3*5 = 1080 *  2
