For interactive session, Ivy Bridge nodes:
qsub -I -q devel -l select=300:ncpus=20:model=ivy,walltime=02:00:00
qsub -I -q long -l select=300:ncpus=20:model=ivy,walltime=120:00:00
qsub -I -q long -l select=300:ncpus=20:model=ivy,min_walltime=30:00,max_walltime=120:00:00
qsub -I -q normal -l select=300:ncpus=20:model=ivy,walltime=8:00:00

For batch submission:
qsub -q devel -l select=300:ncpus=20:model=ivy,walltime=02:00:00 runscript
qsub qsub_llc2160.csh 

==============

cvs co MITgcm_code
cd MITgcm
module purge
module load comp-intel/2011.7.256 mpi-sgi/mpt.2.06r6 netcdf/4.0
mkdir build run
lfs setstripe -c -1 /nobackupp5/dmenemen/llc_4320/MITgcm/run
cd build
../tools/genmake2 -of ~/tarballs/llc_4320/code-async/linux_amd64_ifort+mpi_ice_nas -mpi -mods \
 '/nobackup/dmenemen/tarballs/llc_4320/code /nobackup/dmenemen/tarballs/llc_4320/code-async'
make depend
make -j 16
cd ../run
ln -sf ../build/mitgcmuv .
ln -sf /nobackup/dmenemen/tarballs/llc_4320/run_template/* .
ln -sf /nobackup/dmenemen/forcing/era_interim/EIG_*_2* .
ln -sf /nobackup/dmenemen/forcing/era_interim_corrected/EIG_dlw_sub5p_2* .
cp /nobackup/dmenemen/tarballs/llc_4320/input/* .
mpiexec -n 6000 ./mitgcmuv

==============

look at output

for ts=[0 120 600:10:980 1080:120:2280]
    fld=quikread_llc(['Eta.' myint2str(ts,10) '.data'],4320);
    clf,quikplot_llc(fld),caxis([-2.5 2]),thincolorbar
    title(ts)
    pause(.1)
end

==============

to determine empty tiles:
grep Empty STDOUT.*

=============

memory requirements:
nPx  sNx sNy nSx cpu node0        total
3744 180 180   2 san 22,106,128kb 5,195,641,224kb - node ran out of memory and crashed
5616 120 120   3 san - node ran out of memory and crashed
7488 180 180   1 san 

=============

2               =    2
3               =    3
2*2             =    4
5               =    5
2*3             =    6
2*2*2           =    8
3*3             =    9
2*5             =   10
2*2*3           =   12
3*5             =   15
2*2*2*2         =   16
2*3*3           =   18
2*2*5           =   20
2*2*2*3         =   24
3*3*3           =   27
2*3*5           =   30
2*2*2*2*2       =   32
2*2*3*3         =   36
2*2*2*5         =   40
3*3*5           =   45
2*2*2*2*3       =   48
2*3*3*3         =   54
2*2*3*5         =   60
2*2*2*3*3       =   72 * 60
2*2*2*2*5       =   80 * 54
2*3*3*5         =   90 * 48
2*2*2*2*2*3     =   96 * 45
2*2*3*3*3       =  108 * 40
2*2*2*3*5       =  120 * 36
3*3*3*5         =  135 * 32
2*2*2*2*3*3     =  144 * 30
2*2*2*2*2*5     =  160 * 27
2*2*3*3*5       =  180 * 24
2*2*2*3*3*3     =  216 * 20
2*2*2*2*3*5     =  240 * 18
2*3*3*3*5       =  270 * 16
2*2*2*2*2*3*3   =  288 * 15
2*2*2*3*3*5     =  360 * 12
2*2*2*2*3*3*3   =  432 * 10
2*2*2*2*2*3*5   =  480 *  9
2*2*3*3*3*5     =  540 *  8
2*2*2*2*3*3*5   =  720 *  6
2*2*2*2*2*3*3*3 =  864 *  5
2*2*2*3*3*3*5   = 1080 *  4
2*2*2*2*2*3*3*5 = 1440 *  3
2*2*2*2*3*3*3*5 = 2160 *  2
