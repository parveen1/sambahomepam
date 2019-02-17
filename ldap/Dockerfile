# Version: ldapgroup
# name parveen
# ldapserver with all database edt.org
# -------------------------------------
FROM fedora:27
LABEL author="@parveen ldapserver"
LABEL description="ldapserver edt.org cn dataDB"
RUN dnf -y install procps openldap-clients openldap-servers
RUN mkdir /opt/docker
COPY * /opt/docker/
RUN chmod +x /opt/docker/install.sh /opt/docker/startup.sh
WORKDIR /opt/docker
EXPOSE 389
CMD ["/opt/docker/startup.sh"]


