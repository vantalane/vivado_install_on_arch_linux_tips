

# vivado_install_on_arch_linux_tips
Some tips and tricks I found and used while installing various Vivado versions on Arch Linux.

I'm writing these down for myself, hopefully it'll help anyone that encounters the same issue.

## Context

I wanted to install Vivado 2023.1 on Arch. I had previously installed:\
2020.2\
2022.2\
2023.2\
2024.1\
2024.2\
...back when I was using Kubuntu on a separate drive, which run perfectly fine after I switched over to Arch.
But for some reason I couldn't run the xinstall file of 2023.1, providing arguments such as batch  (./xsetup -b) wouldn't change anything.



## My problem:

After making sure I have tried just installing all the jre* and jdk* gobbledigooks, I kept getting the following:

```
./xsetup 
This is a fresh install.
INFO Could not detect the display scale (hDPI).
       If you are using a high resolution monitor, you can set the insaller scale factor like this: 
       export XINSTALLER_SCALE=2
       setenv XINSTALLER_SCALE 2
Exception in thread "main" java.lang.ExceptionInInitializerError
        at com.xilinx.installer.api.InstallerLauncher.<clinit>(Unknown Source)
Caused by: java.lang.NullPointerException: Cannot invoke "jdk.internal.platform.CgroupInfo.getMountPoint()" because "anyController" is null
        at java.base/jdk.internal.platform.cgroupv2.CgroupV2Subsystem.getInstance(CgroupV2Subsystem.java:80)
        at java.base/jdk.internal.platform.CgroupSubsystemFactory.create(CgroupSubsystemFactory.java:114)
        at java.base/jdk.internal.platform.CgroupMetrics.getInstance(CgroupMetrics.java:177)
        at java.base/jdk.internal.platform.SystemMetrics.instance(SystemMetrics.java:29)
        at java.base/jdk.internal.platform.Metrics.systemMetrics(Metrics.java:58)
        at java.base/jdk.internal.platform.Container.metrics(Container.java:43)
        at jdk.management/com.sun.management.internal.OperatingSystemImpl.<init>(OperatingSystemImpl.java:182)
        at jdk.management/com.sun.management.internal.PlatformMBeanProviderImpl.getOperatingSystemMXBean(PlatformMBeanProviderImpl.java:280)
        at jdk.management/com.sun.management.internal.PlatformMBeanProviderImpl$3.nameToMBeanMap(PlatformMBeanProviderImpl.java:199)
        at java.management/sun.management.spi.PlatformMBeanProvider$PlatformComponent.getMBeans(PlatformMBeanProvider.java:195)
        at java.management/java.lang.management.ManagementFactory.getPlatformMXBean(ManagementFactory.java:687)
        at java.management/java.lang.management.ManagementFactory.getOperatingSystemMXBean(ManagementFactory.java:389)
        at com.xilinx.installer.utils.s.<clinit>(Unknown Source)
        ... 1 more

```


## Solutions:

If you haven't yet, definitely get the following pkgs from AUR:
- ncurses5-compat-libs
- libxcrypt-compat

This'll prevent your Vivado installer from getting stuck just before finishing off the install.


### Try different ENV variables

The installer shell script starts up a java based installer. In my case, java version of my system had some compatibility issues with Cgroup metrics. Thus try an older java version:
```
sudo pacman -S jre8-openjdk

```
Change java home to it and add following options:
```
export X_JAVA_HOME=/usr/lib/jvm/java-8-openjdk
export _JAVA_OPTIONS="-XX:+UnlockExperimentalVMOptions -XX:-UseContainerSupport"
./xsetup
```
This method is what I've done. 

### Try AUR Xilinx Vivado 
If you haven't seen it yet, there is a AUR package that can install Vivado such that it can be managed by PACMAN. Try this if you have a large and spacious root partition that can handle the size of a .tar.gz of your wanted Vivado install file,uncompressed .tar.gz of it and the Vivado that is actually installed on your system. Read it up on the wiki.


### If you can run xinstall, try
If only the GUI is broken (seems to be the case for some), try installing it in batch mode. Seems that others could install it that way without a GUI. Not me, though.
