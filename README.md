
# NAME

user-repo - Apache module for personal svn/git repositories

# SYNOPSIS

svnuser-new-repo <*reponame*>  
gituser-new-repo <*reponame*>  
svnuser-import-repo ~/<*reponame*>  
gituser-import-repo ~/<*reponame*>  

# DESCRIPTION

This is a module for the apache web server that allows users to create, import
and manage their own svn or git repositories, in a sense similar to
*userdir* which provides web directories for the users. The addresses of the
repositories may depend on how the module is configured, although the default is
that repositories become accessible as:

* http://{serveraddress}/svn/~{owneruser}/{reponame}
* http://{user}@{serveraddress}/git/~{owneruser}/{reponame}

where {owneruser} is the login name of the repository owner, {user} is the
login name of the user that accesses the repository and {reponame} is the name
given to the repository when created. Due to a strange behavior of git, when
cloning the repository it is important to include the user name {user} in the
URL, otherwise pushes might fail. With these personal repositories, access can
be granted to anyone, they do not need to have a user account in the server.
Each person administers and assigns permissions to their own repositories, all
of which is very simple as explained below.

The personal repositories are not activated by default to all users, so anyone
interested in having them should make a request to the system administrator.

The actual repository files are stored in **~/public_svn** or **~/public_git**,
accessible to the owner so that these can be later moved to another server
and/or removed.

Do NOT copy repositories directly to **~/public_svn** or **~/public_git** and do
NOT create repositories using *svnadmin* or *git* *--bare*. There are specific
commands for creating and importing repositories, these have to be used in
order to guaranty that everything is setup correctly. The usage instructions
are the following:

## Creating new repositories

Simply issue one of the commands depending if it is svn or git:

* svnuser-new-repo <*reponame*>
* gituser-new-repo <*reponame*>

## Importing an existing repository

First copy the existing repository to your home in the server such that it
resides in the directory ~/{reponame}. Then issue one of the following
commands depending on the case:

* svnuser-import-repo ~/<*reponame*>
* gituser-import-repo ~/<*reponame*>

## Setting permissions to repositories

For all the user's svn repositories, the permissions are set in the file
*~/public_svn/.svn-authz* (for information regarding the format and usage go
to [http://svnbook.red-bean.com/en/1.6/svn.serverconfig.pathbasedauthz.html]).

For git, each repository has its own file to set the permissions
*~/public_git/{reponame}/.htaccess*. It is straightforward to configure for read
only or read and write access just by looking at the file created with each
repository.

## Granting access to external users

External users need to be added to the files *~/public_svn/.htpasswd* or
*~/public_git/.htpasswd*. For instructions on how to do this see man
htpasswd(1).

## Configuring users for personal repositories

Unlike userdir, by default users are not enabled for personal repositories. To
initialize a user, the system administrator needs to issue the command
svnuser-init-user or gituser-init-user followed by the user. The initialization
command creates a group for the user that the apache server can access, creates
the repositories directory with the appropriate permissions, adds the user to
the module configuration and finally it restarts the apache server.


# INSTALLATION

Currently this module is designed for debian based distributions. The preferred
way of installation is to use the deb package, making sure that all dependencies
are also installed, for example install using gdebi.

The deb package can be easily created from the source. First you need to install
devscripts, cmake and pandoc. Then the package is built by running

$ cmake . && make deb

If everything works as expected, you will find the deb package in the current
directory.


# COPYRIGHT

The MIT License (MIT)

Copyright (c) 2014-present, Mauricio Villegas <mauricio_ville@yahoo.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
