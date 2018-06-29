# einsteintoolkit
FROM centos:7

# Installing extra releases
RUN yum -y install epel-release
RUN yum -y install https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm
RUN yum clean all


# Install necessary CentOS packages
RUN     yum install -y                                  \
    curl git perl python subversion wget                \
    build-essential g++ gcc gfortran make pkg-config    \
    gsl-bin libgsl0-dev                                 \
    h5utils hdf5-helpers hdf5-tools libhdf5-openmpi-dev \
    hwloc-nox libhwloc-dev libnuma-dev                  \
    libfftw3-bin libfftw3-dev libfftw3-mpi-dev          \
    libjpeg-dev                                         \
    libltdl-dev                                         \
    libopenblas-dev                                     \
    libopenmpi-dev openmpi-bin                          \
    libpapi-dev papi-tools                              \
    libssl-dev                                          \
    petsc-dev                                           \
    zlib1g zlib1g-dev


# Download installer (need to do this as root)
ADD https://raw.github.com/gridaphobe/CRL/ET_2016_05/GetComponents /tmp/
ADD https://bitbucket.org/einsteintoolkit/manifest/raw/ET_2016_05/einsteintoolkit.th /tmp/
RUN chmod a+rx /tmp/GetComponents &&            \
    chmod a+r /tmp/einsteintoolkit.th

## Overwriting again
#RUN yum -y install perl-Devel-Trace

#RUN yum -y install rpm-build
#RUN wget ftp://ftp.icm.edu.pl/vol/rzm6/linux-oracle-repo/OracleLinux/OL6/latest/SRPMS/perl-Devel-Trace-0.11-1.el6.src.rpm -O /tmp/perl-Devel-Trace-0.11-1.el6.src.rpm
#RUN rpmbuild --rebuild /tmp/perl-Devel-Trace-0.11-1.el6.src.rpm
RUN yum -y install perl-*
ADD perl-Devel-Trace-0.11-1.el7.noarch.rpm /tmp/
RUN yum -y install /tmp/perl-Devel-Trace-0.11-1.el7.noarch.rpm
ADD GetComponents /tmp/GetComponents
RUN chmod a+rx /tmp/GetComponents

# Configure the machine
RUN useradd -m scientist
USER scientist
WORKDIR /home/scientist
RUN git config --global user.email "scientist@localhost" &&             \
    git config --global user.name "Scientist, Einstein Toolkit" &&      \
    echo 'einsteintoolkit' >"$HOME/.hostname"

# Download the Einstein Toolkit
RUN mkdir Cactus simulations &&                                 \
    perl -d:Trace /tmp/GetComponents --verbose --debug --parallel /tmp/einsteintoolkit.th
WORKDIR Cactus
# TODO: Move these into the Einstein Toolkit
COPY /einsteintoolkit.ini simfactory/mdb/machines/
COPY /einsteintoolkit.cfg simfactory/mdb/optionlists/
COPY /einsteintoolkit.sub simfactory/mdb/submitscripts/
COPY /einsteintoolkit.run simfactory/mdb/runscripts/
# Configure Simfactory
COPY /defs.local.ini simfactory/etc/

# Build
RUN ./simfactory/bin/sim build --debug

# Export source and simulation directories
VOLUME /home/scientist/Cactus /home/scientist/simulations

# Run Simfactory by default
# Hint: Use "execute" to execute arbitrary shell commands
ENTRYPOINT ["./simfactory/bin/sim"]
CMD []
