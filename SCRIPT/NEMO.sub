#!/bin/ksh
#SBATCH -J N36T421b
#SBATCH -N 473
#SBATCH -A cmems
#SBATCH --qos=coper
#SBATCH -p normal256
#SBATCH  --exclusive
#SBATCH  --no-requeue
#SBATCH  --time=04:00:00
#SBATCH  --mem=247000
#SBATCH -o N36T421b.out
#SBATCH -e N36T421b.err

set -xv

export QSUB_WORKDIR=`/bin/pwd`
cd $QSUB_WORKDIR
export FORT_BUFFERED=true

#########################################################################
#
#########################################################################
CTLDIR=$QSUB_WORKDIR
AWUSER=/scratch/work/bricaudc
CONFIG=ORCA36-T421b

DIRRUN=$AWUSER/${CONFIG}-RUN
DIRVPPI=$AWUSER/INITIALISATION/INPUTS_iORCA_R36/NEMO_4.2_BENCH
BIN=/home/ext/mr/smer/bricaudc/SAVE/nemoref_trunk_r14365/cfgs/GLO-BENCH/BLD/bin/nemo.exe

#------------------------------------------------------------------------
cp ${CTLDIR}/NAMLST/* ${DIRRUN}/
cp ${CTLDIR}/XML/*    ${DIRRUN}/

cp ${BIN} ${DIRRUN}/

for f in ${DIRVPPI}/*.nc ; do 
   ln -sf $f ${DIRRUN}/
done


#########################################################################
#
#########################################################################


NEMO_PROCS=29799
XIOS_PROCS=480

## MPI task distribution (procs/node) 
NEMO_DIST=128
XIOS_DIST=2

#########################################################################
#
#########################################################################
MODULE_LIST="\
   gcc/9.2.0\
   intel/2018.5.274\
   mvapich2-2.3.5\
   hdf5-1.8.18-mvapich2\
   netcdf-4.7.1-mvapich2\
   xios_trunk_rev2130"


module_ksh ()
{
  eval `/usr/bin/modulecmd ksh $*`
}

if [ -n "${MODULE_LIST}" ] ; then
   module_ksh purge
   for module in ${MODULE_LIST} ; do
      module_ksh load ${module}
   done
fi
module_ksh list

#########################################################################
#
#########################################################################
cd ${DIRRUN}

MULTIPROG_CONF=config_file
BIN_NEMO=nemo.exe
BIN_XIOS=xios_server.exe
PROCS=$(( ${PROC} + ${XIO} ))


source /home/ext/mr/smer/soniv/SAVE/env-vars/mvapich2-vars-t3 

# Machine-file 
MACHINEFILE=machinefile 

# Remove existing machine-file
rm -f $MACHINEFILE 

# Get allocated nodelist 
mylist=`scontrol show hostnames` 
arr=($mylist) 

# NEMO-XIOS do not share nodes 
NEMO_NODES=$(((NEMO_PROCS+NEMO_DIST-1)/NEMO_DIST)) 
XIOS_NODES=$(((XIOS_PROCS+XIOS_DIST-1)/XIOS_DIST)) 
TOT_NODES=$((NEMO_NODES+XIOS_NODES)) 
if [ $TOT_NODES -ne $SLURM_JOB_NUM_NODES ]; then 
   echo "Mis-match of allocated and required number of nodes." 
   scancel $SLURM_JOB_ID 
fi 

# Assigning NEMO procs to nodes 
n=-1 
for ((nemo=0; nemo<$NEMO_PROCS; nemo+=$NEMO_DIST)); do 
   n=$((n+1)) 
   if [[ $((nemo+NEMO_DIST)) -gt $NEMO_PROCS ]]; then 
      NEMO_DIST=$((NEMO_PROCS-nemo)) 
   fi 
   for ((d=0; d<$NEMO_DIST; d++)); do 
      echo "${arr[$n]}" >> $MACHINEFILE 
   done 
done 

# Assigning XIOS procs to nodes 
m=$n 
for ((xios=0; xios<$XIOS_PROCS; xios+=$XIOS_DIST)); do 
   m=$((m+1)) 
   if [[ $((xios+XIOS_DIST)) -gt $XIOS_PROCS ]]; then 
      XIOS_DIST=$((XIOS_PROCS-xios)) 
   fi 
   for ((d=0; d<$XIOS_DIST; d++)); do 
      echo "${arr[$m]}" >> $MACHINEFILE 
   done 
done 

export SLURM_HOSTFILE=$MACHINEFILE 

# Slurm MPMD config_file
rm -f config_file

cat > config_file <<EOF
0-$(( $NEMO_PROCS -1)) ./nemo.exe
$NEMO_PROCS-$(( $NEMO_PROCS + $XIOS_PROCS -1)) ./xios_server.exe
EOF



#########################################################################
# Launch computation 
#########################################################################
export FORT_BUFFERED=yes
time srun --mpi=pmix -K1 --distribution=arbitrary --ntasks=$(($NEMO_PROCS+$XIOS_PROCS)) --multi-prog config_file 

unset SLURM_HOSTFILE

sacct --format=jobid,jobname,partition,ntasks,elapsed,state -j $SLURM_JOBID
#########################################################################
##                                END                                  ##
#########################################################################
