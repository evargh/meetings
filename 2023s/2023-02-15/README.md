- In the beginning, there was chroot
  - 1979, Bill Joy
  - https://en.wikipedia.org/wiki/Chroot
  - https://en.wikipedia.org/wiki/Chroot#Limitations
  - Preparing the rootfs
    - docker run $image && docker export $container > rootfs.tar && mkdir rootfs && tar xvf rootfs.tar --directory=rootfs && chroot rootfs /bin/bash
    - debootstrap
    - https://www.youtube.com/watch?v=gMpldbcMHuI
- Fakeroot
- Namespaces
  - https://windsock.io/introducing-the-namespaces/
  - https://lwn.net/Articles/531114/
  - User namespaces
    - UID/GID mapping
    - map-current-user, map-root-user
    - cat /proc/$$/uid_map (inside-ns, outside-ns, range)
    - > Eric Biederman has put a lot of effort into making the user namespaces implementation safe and correct. However, the changes wrought by this work are subtle and wide ranging. Thus, it may happen that user namespaces have some as-yet unknown security issues that remain to be found and fixed in the future.
  - pid namespace
    - sudo unshare -p -f --mount-proc=$PWD/rootfs/proc chroot rootfs /bin/bash
    - PID 1 behavior
      - Reep zombie children
  - Mount namespace
    - cat /proc/self/mountinfo
  - Network namespace
    - ip route, ip link, iptables --list-rules
    - https://blog.quarkslab.com/digging-into-linux-namespaces-part-1.html
- Cgroups
- Security considerations
  - Be root inside user namespace, talk to files from mount namespace
    - Docker access == root
  - https://lwn.net/Articles/652468/
  - "Privilege escalation is necessary for containerization!"
    - https://singularity-admindoc.readthedocs.io/en/latest/security.html
  - OCI runtime
    - Podman
  - The Singularity/Apptainer approach
    - https://apptainer.org/docs/user/main/security.html