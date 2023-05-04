Singularity Containers
======================

Singularity + Docker receipts can be found on github https://github.com/louisbondonneau/Docker_receipts

Depending on the installation singularity executable can be named "singularity" or "apptainer".

Run a contaner
--------------

| INSTALL            |   pschive_py2  |   pschive_py3  |
| :----------------- |:---------------|:---------------|
| psrchive           | OK (py2)       | OK (py3)       |
| tempo2             | OK             | OK             |
| tempo1             | OK             | OK             |
| presto             | OK (v2.2 py2)  | OK (v4 py3)    |
| dspsr              | OK (py2)       | OK (py3)       |
| psrsalsa           | OK             | OK             |
| SIGPROC            | OK             | OK             |
| RFICLEAN           | OK             | OK             |
| GPTOOL             | OK             | OK             |
| pylib - nenupy     | OK (py3)       | OK (py3)       |
| pylib - AntPat     | OK (py3)       | OK (py3)       |
| pylib - dreamBeam  | OK (py3)       | OK (py3)       |
| pylib - psrqpy     | OK (py3)       | OK (py3)       |

### RUN pschive_py2 container
``` bash
singularity run -B $HOME:$HOME  -B /databf:/databf -B /data:/data -B /cep:/cep -B ~/.Xauthority:/home/root/.Xauthority /cep/lofar/pulsar/Singularity/pschive_py2.sif
```

### RUN pschive_py3 container
``` bash
singularity run -B $HOME:$HOME  -B /databf:/databf -B /data:/data -B /cep:/cep -B ~/.Xauthority:/home/root/.Xauthority /cep/lofar/pulsar/Singularity/pschive_py3.sif
```

known issues
------------
  1. psrsmooth do not exist!
  2. psrdata, hdf5... and other things in Vlad installed used by LOFAR are not installed at this time
  3. python installation on your home or environment variables in your bashrc can affect the operation inside the container. To avoid this, add the following lines to the beginning of your ~/.bashrc ~/.bash_profile
``` bash
# Check if we are inside a Singularity container
if [ -n "$SINGULARITY_CONTAINER" ]; then
    # If we are inside a Singularity container, exit the script here
    return
fi
```

Build a container from nothing
------------------------------

### install Go and Singularity
``` bash
apt-get update
apt-get install -y build-essential libssl-dev uuid-dev libgpgme11-dev squashfs-tools libseccomp-dev wget pkg-config git cryptsetup libglib2.0-dev
GO_VERSION=1.20.2 OS=linux ARCH=amd64
wget -O /tmp/go${GO_VERSION}.${OS}-${ARCH}.tar.gz https://dl.google.com/go/go${GO_VERSION}.${OS}-${ARCH}.tar.gz
tar -C /usr/local -xzf /tmp/go${GO_VERSION}.${OS}-${ARCH}.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
. ~/.bashrc
git clone --recurse-submodules https://github.com/sylabs/singularity.git singularity
cd singularity
./mconfig
cd builddir
make
make install
```

### build python2 psrchive container
``` bash
git clone https://github.com/louisbondonneau/Docker_receipts
cd Docker_receipts/psrchive_py2
singularity build /cep/lofar/pulsar/Singularity/pschive_py2.sif Singularity
```

### build python3 psrchive container
``` bash
git clone https://github.com/louisbondonneau/Docker_receipts
cd Docker_receipts/psrchive_py3
singularity build /cep/lofar/pulsar/Singularity/pschive_py3.sif Singularity
```

### try an installation in the container

> singularity run --writable-tmpfs


TODO
----
ajouter:
  spyder

CONTAINER TEST
--------------

tempo1
``` bash
bash /cep/lofar/pulsar/ephem_scripts/par_conv_to_tempo1.sh /databf/nenufar-pulsar/ES03/ephem/B1919+21.par
```

tempo2
``` bash
cd /usr/local/pulsar/tempo2/example_data
tempo2 -gr plk -f example1.par example1.tim  -nofit
psrchive_info  # Tempo2::Predictor support enabled~
```

psrchive
``` bash
python -c 'import psrchive'
python /cep/lofar/pulsar/NenPlot...
```

psrcat
``` bash
psrcat -E B1919+21
```

psredit
> psredit -c dm ....

presto
``` bash
python -c 'import presto'
python /usr/local/pulsar/presto/tests/test_presto_python.py
```

psrsalsa
> 

dreamBeam
> calibration of a NenuFAR archive

dspsr
> python -c 'import dspsr'
> by runnning it on a single-pulses file