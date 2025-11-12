# Ultimate guide
This guide is based off the [original one by Matthew Schultz](https://github.com/MatthewSchultz/Install-Rails-on-WSL) and [this one from GoRails](https://gorails.com/setup/windows/11).
It also considers you are installing rails >= 7.x.

## Step 1 - WSL

On Windows 11 it is easier to just do:
```bash
wsl --install
```

We are going with the default distro (Ubuntu) for simplicity.

You can also see what other distros you can install by doing:
```bash
wsl --list --online
```

And install your preferred one with:
```bash
wsl --install <Distribution Name>
```

Finish the installation and reboot your PC. Then run `sudo apt update && sudo apt upgrade` to start getting packages.
You can search for "Ubuntu" in the Windows Start Menu anytime to open the Ubuntu terminal.

## Step 2 - Install dependencies for Ruby

The first step is to install dependencies for compiling Ruby.

Open your Terminal and run the following commands to install them.
```bash
sudo apt-get update
sudo apt install build-essential rustc libssl-dev libyaml-dev zlib1g-dev libgmp-dev curl zlib1g-dev libssl-dev libreadline-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev
sudo apt install imagemagick
sudo apt install graphviz
```

Here we install a bunch of dependencies, of which libxml2-dev is particularly important for nokogiri.

The second line installs imagemagick, a critical image-processing library. If you know you don't need this it's ok to omit, but it's a common enough library I suggest installing it.

The third line installs graphviz, a graphical generation utility. Not sure how important this is, but I use it often.

### Setup QOL Tools

```bash
sudo apt-get install tmux tty-clock htop cowsay postgresql-client fortune
```

These are somewhat optional, but make living in semi-linux bearable.

## Step 3 - Install Ruby
Next, we'll install Ruby using a version manager called Mise. This allows you to easily update Ruby and switch between versions anytime.

```bash
curl https://mise.run | sh
echo 'eval "$(~/.local/bin/mise activate)"' >> ~/.bashrc
source ~/.bashrc
```
Then install Ruby with Mise:

```bash
mise use --global ruby@3
```

Confirm that Ruby is installed and works:

```bash
ruby --version
#=> 3.4.7
```

You also want to ensure you're using the latest version of Rubygems.

```bash
gem update --system
```

## Step 4 - Installing Node

Optionally, if you plan to use Node.js for handling assets, you can use Mise to install Node as well.

```bash
mise use --global node@24.11.0
```

```bash
node -v
#=> 24.11.0
```

## Step 5 - Setup GIT

```bash
git config --global color.ui true
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR@EMAIL.com"
ssh-keygen -t ed25519 -C "YOUR@EMAIL.com"
```

The next step is to take the newly generated SSH key and add it to your Github or Bitbucket account. You want to copy and paste the output of the following command and paste it here.

```bash
cat ~/.ssh/id_ed25519.pub
```

Once you've done this, you can check and see if it worked:

```bash
ssh -T git@github.com
```

or

```bash
ssh -T git@bitbucket.org
```

You should get a message like this:

```bash
Hi your_name! You've successfully authenticated, but GitHub does not provide shell access.
```

## Step 6 - Because we Can

```bash
gem install lolcat
```

Helps make sure gems are working anyways.......not really. I just find lolcat funny.

## Step 7 - Installing Rails

Choose the version of Rails you want to install:

```bash
gem install rails -v 8.1.1
```

> Note: If you want to use [Solidus](https://github.com/solidusio/solidus), the latest supported version at the time of writing is 7.2. For your consideration.

Now that you've installed Rails, you can run the rails -v command to make sure you have everything installed correctly:

```bash
rails -v
# Rails 8.1.1
```

If you get a different result for some reason, it means your environment may not be setup properly.

## Step 8 - Setting up PostgreSQL

For PostgreSQL, we're going to add a new repository to easily install a recent version of Postgres.

```bash
sudo apt install postgresql libpq-dev
sudo service postgresql start
```

You'll need to start postgresql each time you load your WSL environment.

The postgres installation doesn't setup a user for you, so you'll need to follow these steps to create a user with permission to create databases. Feel free to replace zam with your username.

```bash
sudo -u postgres createuser zam -s
# If you would like to set a password for the user, you can do the following
sudo -u postgres psql
postgres=# \password zam
```

## Step 9 - Get to Project Directory

Do `cd /mnt/<windows full path>` to get to the project directory. Project directories ARE case sensitive, use linux folder seperation, do not use the colon notation for drives, and the drive letter is lowercased, so `C:\Users\You\Projects\SomeFolder\` becomes `/mnt/c/Users/You/Projects/SomeFolder`.

## Step 10 - Test it out
Let's create your first Rails application on Windows!

```bash
rails new myapp -d postgresql -c tailwindcss
```

With this, we are creating a project using postgres as the DB and tailwind as the CSS framework.

Then, move into the application directory
```bash
cd myapp
```

If you setup Postgres with a username/password, modify the config/database.yml file to contain the username/password that you specified above.

Create the database
```bash
rake db:create
```

Start your server
```bash
rails server
```

You can now visit http://localhost:3000 to view your new website!
