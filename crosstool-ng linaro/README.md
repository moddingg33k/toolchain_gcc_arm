Building Crosstool-NG


Install needed packages:

	sudo apt-get install build-essential libncurses5-dev
	sudo apt-get install automake libtool bison flex texinfo
	sudo apt-get install gawk curl cvs subversion gcj-jdk
	sudo apt-get install libexpat1-dev python-dev


Install Crosstool-NG locally using "–local" option

	tar xjf crosstool-ng-linaro-1.13.1-4.8-2014.04.tar.bz2
	cd crosstool-ng-linaro-1.13.1-4.8-2014.04
	./configure --local
	make
	make install

Crosstool-NG comes with a set of ready-made configuration files for various typical setups called samples. They can be listed by running:

	./ct-ng list-samples


Use the ARM EABI cross compiler for use on linux. Configure and produce this toolchain by issuing:

	./ct-ng arm-unknown-linux-gnueabi

Reﬁne your conﬁguration by running the menuconﬁg interface:

	./ct-ng menuconfig

Build Crosstool-NG:

	 ./ct-ng build


Alternative Download:

	http://crosstool-ng.org/download/crosstool-ng/
