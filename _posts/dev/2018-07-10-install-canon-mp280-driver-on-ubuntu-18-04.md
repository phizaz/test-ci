---
layout: post
title: Install Canon MP280 Driver on Ubuntu 18.04
categories: dev
tags: ubuntu
---

You need **libtiff4** which cannot be installed via apt from [here ](https://mun-steiner.de/wordpress/index.php/linux/scannen-und-drucken/canon-software-pixma-mg5250/libtiff4-erforderlich/)(direct link: <http://old-releases.ubuntu.com/ubuntu/pool/universe/t/tiff3/libtiff4_3.9.7-2ubuntu1_amd64.deb>)

Install the libtiff4 package:

```
dpkg -i libtiff4_3.9.7-2ubuntu1_amd64.deb
```

You also need **libpng12–0** which will not be found in the apt-get from [here](https://packages.ubuntu.com/xenial/amd64/libpng12-0/download)(direct link: <http://mirrors.kernel.org/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb>)

Install the libpng12–0 package:

```
dpkg -i libpng12-0_1.2.54-1ubuntu1_amd64.deb
```

Now, install the dependencies of the driver:

```
apt install libatk1.0-0 libgtk2.0-0 libpango1.0-0
```

You can download the driver from <http://support-in.canon-asia.com/contents/IN/EN/0100301402.html>.

After extracting the archive, you will see:

```
packages
resources
install.sh
```

Just go to the packages directly, you will see:

```
cnijfilter-common_3.40-1_amd64.deb
cnijfilter-common_3.40-1_i386.deb
cnijfilter-mp280series_3.40-1_amd64.deb
cnijfilter-mp280series_3.40-1_i386.deb
```

I will assume that you use **amd64** architecture. Go ahead install both of the **common** and the **mp280series** packages.

```
dpkg -i cnijfilter-common_3.40-1_amd64.deb
dpkg -i cnijfilter-mp280series_3.40-1_amd64.deb
```

That should be all!