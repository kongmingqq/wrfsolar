FROM centos:7
MAINTAINER Ming Kong <kongmingqq@gmail.com>
# 
# This Dockerfile setup environment for compiling and executing WRF
#
RUN yum -y update \
  && yum -y install file gcc gcc-gfortran gcc-c++ glibc.i686 libgcc.i686 libpng-devel jasper jasper-devel hostname m4 make perl \ 
  tar tcsh time wget which zlib zlib-devel openssh-clients openssh-server net-tools epel-release git \
  && yum clean all
#
# now get 3rd party EPEL builds of netcdf and openmpi dependencies
RUN yum -y install netcdf-openmpi-devel.x86_64 netcdf-fortran-openmpi-devel.x86_64 netcdf-fortran-openmpi.x86_64 hdf5-openmpi.x86_64 openmpi.x86_64 openmpi-devel.x86_64 \
  && yum clean all
#
WORKDIR /wrf
#
# Download original sources
#
## RUN git clone 
#
# Set environment for interactive container shells
#
RUN echo export LDFLAGS="-lm" >> /etc/bashrc \
 && echo export NETCDF=/wrf/netcdf_links >> /etc/bashrc \
 && echo export JASPERINC=/usr/include/jasper/ >> /etc/bashrc \
 && echo export JASPERLIB=/usr/lib64/ >> /etc/bashrc \
 && echo export LD_LIBRARY_PATH="/usr/lib64/openmpi/lib" >> /etc/bashrc \
 && echo export PATH="/usr/lib64/openmpi/bin:$PATH" >> /etc/bashrc \
 && echo setenv LDFLAGS "-lm" >> /etc/csh.cshrc \
 && echo setenv NETCDF "/wrf/netcdf_links" >> /etc/csh.cshrc \
 && echo setenv JASPERINC "/usr/include/jasper/" >> /etc/csh.cshrc \
 && echo setenv JASPERLIB "/usr/lib64/" >> /etc/csh.cshrc \
 && echo setenv LD_LIBRARY_PATH "/usr/lib64/openmpi/lib" >> /etc/csh.cshrc \
 && echo setenv PATH "/usr/lib64/openmpi/bin:$PATH" >> /etc/csh.cshrc
#
# Set up netcdf
#
RUN mkdir /wrf/netcdf_links \
 && ln -sf /usr/include/openmpi-x86_64/ /wrf/netcdf_links/include \
 && ln -sf /usr/lib64/openmpi/lib /wrf/netcdf_links/lib

ENV LD_LIBRARY_PATH /usr/lib64/openmpi/lib
ENV PATH  /usr/lib64/openmpi/bin:$PATH

#
# set up ssh configuration
COPY ssh_config /root/.ssh/config
RUN mkdir -p /root/.openmpi
COPY default-mca-params.conf /root/.openmpi/mca-params.conf

RUN mkdir -p /var/run/sshd \
    && ssh-keygen -A \
    && sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/g' /etc/ssh/sshd_config \
    && sed -i 's/#RSAAuthentication yes/RSAAuthentication yes/g' /etc/ssh/sshd_config \
    && sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config \
    && ssh-keygen -f /root/.ssh/id_rsa -t rsa -N '' \
    && chmod 600 /root/.ssh/config \
    && chmod 700 /root/.ssh \
    && cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys
#
VOLUME /wrf
CMD ["/bin/bash"]
