# Introduction
Packages in Ubuntu are like app stores. You install packages from central repository. Repositories reside in what is called mirrors. There are mirros in multiple geographic
locations and your Ubuntu server usually subscribes to the one nearest. Package management tools such as ```apt``` (Advanced Package Tool)
allows you to list, search, install and remove packages. It also takes care of dependencies, meaning when installing a package, 
it automatically detects dependencies and installs them for you.


Traditionally each distribution (meaning each Linux distribution, Ubuntu being one of them) has its own package format,
and its own utilities to manage them. Ubuntu utilizes **Debian packages** (with package names ending in .deb)
as the main package format, which Ubuntu inherits from the Debian distribution (Ubuntu is forked from Debian, which means that it uses Debian as its foundation)
Ubuntu and Debian utilize ```apt``` and ```dpkg``` command to manage packages.
On the other hand distributions such as CentOS and Red Hat use RPM packages for their distributions and ```dnf``` command to manage them.


# Debian packages
They are easy to use and handle dependency resolution for you. However they present some challenges and major drawbacks.
First, the majority of the distribution is made up of Debian packages. This means that the Linux kernel, system packages, libraries
and security updates are all Debian packages that are installed when you install Ubuntu server.
When you install security updates, Debian packages are installed.

The reason this maybe a problem is that other software you will be installing, such as Apache, MariaDB, and so on,
are also Debian packages, and may conflict with system packages when one package requires a pre-requisite that conflicts with the pre-requisite
of another package. For instance Apache may require an imaginary logging Debian package with version 1.0, while MariaDB requires the same
package with version 1.2. Since you can only install one Debian package with one version, you can't satisfy the pre-requisites of 
both Apache and MariaDB.

This means that to install a newer versions of Apache and MariaDB, you will have to wait for new version of Ubuntu altogether.

**Universal packages** are a new concept for Linux, and are intended to be a single package format that multiple distributions recognize.
The idea is that a developer only has to compile one package (instead of separate packages for each distribution)
and users would only need to download a single package regardless of their chosen Linux flavor. The next section will discuss a type of
universal package called Snaps.

# Snap packages
The competing technologies for universal packages include Flatpack, AppImage and Snap packages.

Canonical, the makers of Ubuntu, understand the pain points that developers and users experience
and have been making a great effort to change how packages are managed.
The type of universal package they have developed to address these concerns is known as the **Snap package**

Like all universal packages, Snap packages (or more simply Snaps) have no impact on the underlying Debian packages at all
and are completely independent, thus removing the possibility of conflicts with your system packages. This also allows
you to have a newer version of an application installed.

# Installing and removing software

**APT** or **Advanced Package Tool** is a suite of tools that allows us to install, remove, and update Debian packages.
There are various sub-commands that make up this suite. The most popular variation of the ```apt``` command
is ```apt install```. For example the following command will install ```nginx```

```bash
sudo apt install nginx
```

You can install multiple packages at once

```bash
sudo apt install <package1> <package2> <package3>
```

To answer yes to all questions by default when installing a package

```bash
sudo apt install -y <package>
```

When installing a package ```apt``` will look up your local cache for the package. So you have to constantly update your local cache of package names
in order for ```apt``` locate the package you are looking for
Below updates list of repositories, not the package themselves

```
sudo apt update
```

To remove a package

```
sudo apt remove <package>
```

To remove a package together with its configurations usually stored in /etc directory

```
sudo apt remove --purge <package>
```

To search for package

```
sudo apt search <keyword>
```

To view the details of a package

```
apt-cache show nginx
```

## Managing Snap packages with snap

To find a package

```
snap find <keyword>
```

To install

```
sudo snap install <package>
```

To remove

```
sudo snap remove <package>
```

To update a package

```
sudo snap refresh <package>
```

To update all packages

```
sudo snap refresh
```



