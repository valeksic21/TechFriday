# mssql-server-rhel
# Maintainers: Vjekoslav Aleksić (valeksic21 on GitHub)
# GitRepo: https://github.com/valeksic21/TechFriday

# Base OS layer: latest RHEL 7
FROM registry.access.redhat.com/rhel7/rhel:latest


### Atomic/OpenShift Labels - https://github.com/projectatomic/ContainerApplicationGenericLabels
LABEL name="valeksic21/TechFriday/mssql" \
      vendor="valeksic21" \
      version="14.0" \
      release="1" \
      summary="MS SQL Server Developer Edition" \
      description="MS SQL Server is used for TechFriday demo" \
### Required labels above - recommended below
      url="https://www.microsoft.com/en-us/sql-server/" \
      run='docker run --name ${NAME} \
        -e ACCEPT_EULA=Y -e MSSQL_SA_PASSWORD=Pa55w0rD \
        -p 1433:1433 \
        -d  ${IMAGE}' \
      io.k8s.description="MS SQL Server is used for TechFriday demo" \
      io.k8s.display-name="MS SQL Server Developer Edition"


# For environment variables list, see https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-configure-environment-variables
# If you want to specify LCID, you may not set here. You should specify with running a docker. ``docker run -e "MSSQL_LCID=1041" <image>``
ARG SA_PASSWORD='Pa55w0rD'
ARG TCP_PORT=1433
ARG PID=Developer
ENV ACCEPT_EULA=Y MSSQL_PID=$PID MSSQL_SA_PASSWORD=$SA_PASSWORD MSSQL_TCP_PORT=$TCP_PORT

# Install latest mssql-server package
# You don't have to register subscription if you build docker image on registered RHEL machine.
# If you build on other machines, please fill in Red Hat subscription name and password and uncomment the below command.
RUN subscription-manager register --username valeksic28 --password Eugen28$ --auto-attach
RUN yum install -y curl
RUN REPOLIST=rhel-7-server-rpms,packages-microsoft-com-mssql-server-2017,packages-microsoft-com-prod && \
    curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo && \
    curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo && \
    yum install -y --disablerepo "*" --enablerepo ${REPOLIST} --setopt=tsflags=nodocs mssql-server mssql-server-agent mssql-server-fts mssql-tools unixODBC-devel && \
    yum clean all && \
    rm -rf /var/cache/yum
   
COPY uid_entrypoint /opt/mssql-tools/bin/

RUN mkdir -p /var/opt/mssql && \
    chmod -R g=u /var/opt/mssql /etc/passwd

### Containers should not run as root as a good practice
USER 10001

# Default SQL Server TCP/Port
EXPOSE $TCP_PORT

ENV PATH $PATH:/opt/mssql-tools/binTH $PATH:/opt/mssql-tools/bin

### user name recognition at runtime w/ an arbitrary uid - for OpenShift deployments
ENTRYPOINT [ "uid_entrypoint" ]

CMD /opt/mssql/bin/sqlservr
