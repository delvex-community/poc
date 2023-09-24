FROM registry.access.redhat.com/ubi8/ubi-init
RUN dnf install httpd -y
VOLUME /sys/fs/cgroup
EXPOSE 80
CMD ["/sbin/init"]

