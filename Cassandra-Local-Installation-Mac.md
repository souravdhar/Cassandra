
1. Create local directory where you need to keep your Cassandra Binaries

mkdir -p ~/Documents/Cassandra/


2. Download the Cassandra package whatever in the latest one. I have downloaded 3.11.3. There are various ways to
download the package and curl is one of those

curl -O http://www-us.apache.org/dist/cassandra/3.5/apache-cassandra-3.11.3-bin.tar.gz
tar xzvf apache-cassandra-3.11.3-bin.tar.gz


3. In case we need to upgrade Cassandra in the future, let’s create a symbolic link to the Cassandra 3.5 directory.
This will keep is from having to change environment variables later on.

ln -s ~/opt/packages/cassandra/apache-cassandra-3.5 ~/opt/cassandra


4. We want the ability to execute Cassandra commands from any directory so we will add Cassandra to our system PATH.
Open your bash profile with your preferred text editor. I am using Sublime Text.

vi ~/.bash_profile


5. Now add the following to the bash profile file.

# Cassandra
if [ -d "$HOME/opt/cassandra" ]; then
    export PATH="$PATH:$HOME/opt/cassandra/bin"
fi


6. Source your bash profile.

source ~/.bash_profile


7. Verify Cassandra installation.

cassandra -v
# expected output:
# 3.5


Note: If you have previous Cassandra installation which is not removed properly from .cassandra folder delete it accordingly before starting Cassandra

rm -rf ./cassandra