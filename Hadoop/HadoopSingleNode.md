# Single Node Setup

*Materials*: A computer with Linux installed + Access to the Internet
*Explanation*: Hadoop supports Linux better than Windows, therefore install or use a machine that has Linux installed on it (i.e. Ubuntu 16.04 64-bit) on the computer (go to [[Ubuntu installation tutorial]]). The computer must have access to the internet because we need to install some software online.


# Pre-installation
 
The pre-installation phrase includes: creating a new user account, installing two software (JAVA and SSH).
*Explanation*: It is recommended to create a separate user account for Hadoop in order to isolate Hadoop file system from the Linux file system. Java is the main prerequisite for Hadoop. SSH is required when doing operations (e.g. copying files) across different computers of a Hadoop cluster.

# Creating a new user account
** Open a terminal (Ctrl+Alt+T) and type in the following commands:
<pre>
<code class="ruby">
sudo useradd -m hduser -s /bin/bash 
# Purpose: Creat a new user account named ¨hduser¨. 
# ¨sudo¨ means run the command with the security privileges of the superuser. 
# ¨-m¨ means creating the user's home directory if it does not exist. 
# ¨-s /bin/bash¨ means using ¨/bin/bash¨ as the user's login shell.

sudo passwd hduser # Set password for the new account. 


sudo adduser hduser sudo # Add hduser as a superuser

sudo reboot # Restart the computer
</code>
</pre>
Note: You can use other names for this new account (not necessarily use ¨hduser¨). However keep in mind that, in a Hadoop cluster, the new user account on each computer must have the same name (e.g. ¨hduser¨).
** Log in using the new account => Open a terminal (Ctrl+Alt+T):

![](../images/AccountCreated.png)

** Update the resource of software using the following command:
<pre>
<code class="ruby">
sudo apt-get update
</code>
</pre>
Note: Ubuntu uses ¨apt-get¨ command to install software (e.g. JAVA, SSH) online. The online resource of software must be updated, otherwise, some software may not be found.
# Installing JAVA
** The following command checks whether whether JAVA has already been installed:
<pre><code class="ruby">
java -version
</code></pre>

![Java not installed](../images/JAVA-not-installed.png)


The above figure shows that JAVA has not been installed.
** Install JAVA using the following commands:
<pre><code class="ruby">
sudo apt-get install openjdk-8-jre openjdk-8-jdk # Install Java Runtime Environment (JRE) and Java Development Kit (JDK)
</code></pre>
Note: This command may not be successful because ¨openjdk-8-jre and openjdk-8-jdk may have no installation candidate¨. In this case, please check the homepage of openjdk and install the latest version.
** Add the JAVA path to the environment path
JAVA path is added by editing the file ¨~/.bashrc¨ using the following command:
<pre><code class="ruby">
sudo gedit ~/.bashrc
</code></pre>
Add *export JAVA_HOME=/usr* to the first line of ¨~/.bashrc¨. 
Save and close the file ¨~/.bashrc¨.
Run the following command to activate the changes:
<pre><code class="ruby">
source ~/.bashrc
</code></pre>
** Check the installation of java again:
<pre><code class="ruby">
java -version
</code></pre>
!JAVA-installed.png!
The above figure shows that JAVA has be installed successfully.
# Installing SSH
** The following command checks whether whether SSH has already been installed:
<pre><code class="ruby">
ssh localhost
</code></pre>
!SSH-not-installed.png!
The above figure shows that SSH has not been installed.
** Install SSH using the following command:
<pre><code class="ruby">
sudo apt-get install openssh-server rsync
</code></pre>
Note: *openssh-server* offers a very handy function *ssh-copy-id* (see [[Cluster Setup]]) to exchange SSH keys between two computers. *rsync* is a fast, versatile, remote (and local) file-copying tool. In a Hadoop cluster, there are file-copying operations between computers. 
** Check the installation of SSH again:
<pre><code class="ruby">
ssh localhost
</code></pre>
!SSH-installed-need-password.png! 
The above image shows that ssh has been installed, however, passphrase is required in order to ssh to the localhost.
** If we want to ssh to localhost without a passphrase, execute the following commands:
<pre><code class="ruby">
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
# (ssh-keygen) means generating keys for ssh.
# (-t rsa) sets the type of key as rsa.
# (-P '') provids '' as the passphrase.
# (-f ~/.ssh/id_rsa) specifies the filename of the key file as (~/.ssh/id_rsa).  
# For more information, please refer to: http://linux.die.net/man/1/ssh-keygen
</code></pre>
!SSH-keygen.png!
<pre><code class="ruby">
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
</code></pre>
!SSH-authorized-keys.png!
<pre><code class="ruby">
chmod 0600 ~/.ssh/authorized_keys
</code></pre>
** Check the installation of SSH again:
!SSH-installed.png!
The above figure shows that SSH has been installed successfully and we can ssh to the localhost without a passphrase.
Up to now, we have finished the pre-installation, next we will introduce the steps of Hadoop installation. 

h2. Hadoop installation

# Download a recent stable release from one of the "Apache Download Mirrors":http://hadoop.apache.org/releases.html
!Hadoop-miroor.png!
We download the Hadoop2.7.2 binary (do not download the source because it needs to be compiled before being used).
!Hadoop-downloaded.png!
It can be seen that the compressed file of Hadoop2.7.2 is download to the folder ¨Downloads¨.
# Uncompress, move and rename the Hadoop file using the following commands:
<pre><code class="ruby">
sudo tar -zxf ~/Downloads/hadoop-2.7.2.tar.gz -C /usr/local
#-z(gzip), -x(extract), -f(file), -C(directory)
cd /usr/local/
sudo mv ./hadoop-2.7.2/ ./hadoop
sudo chown -R hduser ./hadoop
</code></pre>
!Hadoop-renamed.png!
# Add the Hadoop path to the environment path:
<pre><code class="ruby">
sudo gedit ~/.bashrc
</code></pre>
Add the following line to the file ¨~/.bashrc¨.
<pre><code class="ruby">
export PATH=$PATH:/usr/local/hadoop/bin:/usr/local/hadoop/sbin
export HADOOP_HOME=/usr/local/hadoop
</code></pre>
Save and close the file ¨~/.bashrc¨.
Run the following command to activate the changes:
<pre><code class="ruby">
source ~/.bashrc
</code></pre>
Up to now, the Hadoop for Signal Node Setup is finished. Next we will check whether Hadoop is installed properly, and then run "some examples given by the official website":https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html.

h2. Hadoop testing

# Check the version of Hadoop
<pre>
<code class="ruby">
/usr/local/hadoop/bin/hadoop version
</code>
</pre>
!Hadoop-version.png!
# Run the given example ¨grep¨
<pre>
<code class="ruby">
cd /usr/local/hadoop/ # Go to the given directory
mkdir input # Make folder ¨input¨
ls
cp etc/hadoop/*.xml input
ls input/ # Display the content
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep input output 'dfs[a-z.]+'
cat output/*
</code>
</pre>
!Hadoop-grep-runing.png!
!Hadoop-grep-result.png!
