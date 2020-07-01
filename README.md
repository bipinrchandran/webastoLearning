# Webasto ONE PORTAL

Welcome to the code repository of the project Webasto ONE PORTAL

## Setup guide

### Clone Repository to get your local instance

The following steps need to be taken, to setup the git structure for Webasto ONE PORTAL. For convinience, you can create a local variable, which shows to the local project directory:

```bash
export WEBASTOONEPORTAL_BASE_DIR={PROJECT_DIR}/Webasto
```

Navigate to you Webasto ONE PORTAL project directory and perform the following steps:

```bash
cd $WEBASTOONEPORTAL_BASE_DIR
mkdir gitrepo
cd gitrepo
git clone https://intranet.valtech.com/bb/scm/wbo/oneportal.git
```


On Windows, you'll probably get an error saying the filename/path is too long when creating the files from git.

1. Open gpedit.msc
In the Local Group Policy Editor, in the left-hand pane, drill down to Computer Configuration > Administrative Templates > System > Filesystem. On the right, find the “Enable win32 long paths” item and double-click it and enable this option and restart windows.
2. In your git command shell, set the following:
git config --system core.longpaths true


### Setting up SAP Commerce

Documentation is located [here](https://help.sap.com/viewer/product/SAP_COMMERCE/1905/en-US)

This sections describes how to setup a SAP Commerce instance with the Webasto-specific extensions.

#### Download & unzip SAP Commerce 1905

[Download SAP Commerce 1905](https://www.dropbox.com/s/icrqvoi9mac2xxz/CXCOMM190500P_0-70004140.ZIP?dl=0). 

You should now have a ZIP-file called CXCOMM1905XY.ZIP on your machine.

Unzip the hybris folder of the archive to _$WEBASTOONEPORTAL_BASE_DIR_ by typing these commands

```bash
unzip $DOWNLOAD_DIR/CXCOMM190500P_0-70004140.ZIP 'hybris/*' -d $WEBASTOONEPORTAL_BASE_DIR
```

A new folder has been created in _$WEBASTOONEPORTAL_BASE_DIR_ called **hybris** and the content will be copied into it.

#### Link Webasto extensions

In order to customize SAP Commerce, the Webasto extensions need to be linked to SAP Commerce by creating symbolic links. Starting from _$WEBASTOONEPORTAL_BASE_DIR_ perform the following steps (the folder do not exist yet):

For Mac OSX/Linux

```bash
ln -s $WEBASTOONEPORTAL_BASE_DIR/gitrepo/oneportal/core-customize/hybris/bin/custom $WEBASTOONEPORTAL_BASE_DIR/hybris/bin/custom
ln -s $WEBASTOONEPORTAL_BASE_DIR/gitrepo/oneportal/core-customize/hybris/config $WEBASTOONEPORTAL_BASE_DIR/hybris/config
```

For Windows

```bash
mklink /d hybris\config $WEBASTOONEPORTAL_BASE_DIR\core-customize\hybris\config
mklink /d hybris\bin\custom $WEBASTOONEPORTAL_BASE_DIR\core-customize\hybris\bin\custom
```
Your Hybris instance is now set up. 

### Setup a dockerized MySQL Database

#### Install Docker & Docker Compose

For Mac OSX

Use brew to install

```bash
brew cask install docker
```

For Windows

[Download](https://docs.docker.com/docker-for-windows/) Docker and follow the instructions

#### Run Docker

The Webasto Git repository comes with a docker image for a MySQL Database. To start it do the following:

```bash
cd $WEBASTOONEPORTAL_BASE_DIR/gitrepo/oneportal/misc
docker-compose up -d
#If you receive the error "ERROR: client version 1.22 is too old. Minimum supported API version is 1.24, please upgrade your client to a newer version", open the file docker-compose.yml in the folder /misc and replace version : '2' with version: '2.1'
 
# If you want to check, wether your docker db is running type the following
docker ps
 
# Use this command, when you want to stop the container
docker kill webasto_mysql
 
# In order to start a stopped container type this
docker start webasto_mysql
```

Congratulations, your dockerized DB is now setup!
For more information visit [https://docs.docker.com/](https://docs.docker.com/)

### Install Addons for Webservices

In order to provide webservice endpoints for spartacus, you need to install the cmsoccaddon and the acceleratorwebservicesaddon. Navigate to the platform directory and use ant to install it.

```bash
cd hybris/bin/platform
. ./setantenv.sh
 
ant addoninstall -Daddonnames="cmsoccaddon,acceleratorwebservicesaddon" -DaddonStorefront.ycommercewebservices=webastooneportalwebservices
```

#### Startup SAP Commerce 

If your database is running, your SAP Commerce is now ready to startup. Starting from _$WEBASTOONEPORTAL_BASE_DIR_, type these commands:

```bash
cd hybris/bin/platform
 
. ./setantenv.sh
ant customize
ant clean initialize && ./hybrisserver.sh debug
```

After initializing and starting up, your SAP Commerce instance is now up and running. You can access the HAC via:

[https://localhost:9002/hac ](https://localhost:9002/hac)

Voila!

### Setting up Spartacus Storefront

See official [Documentation](https://sap.github.io/cloud-commerce-spartacus-storefront-docs/)

[Slack Channel](https://spartacus-storefront.slack.com) for questions & troubleshooting 

The Webasto instance does not come with a maintained integrated storefront anymore. It uses the the Spartacus frontend from SAP instead. 

#### Prerequisites

Angular CLI: 8.0.0 or later, < 9.0.0: [https://cli.angular.io/](https://cli.angular.io/)

node.js: 10.14.1 or later, < 12: [https://nodejs.org/en/download/](https://nodejs.org/en/download/)

yarn: 1.15 or later: [https://yarnpkg.com/lang/en/docs/install/#mac-stable](https://yarnpkg.com/lang/en/docs/install/#mac-stable)

For Mac you can use the following commands to install

```bash
brew install yarn
brew install node@10
brew install angular-cli
```

To start Spartacus for the first time, do the following steps

```bash
cd $WEBASTO_BASE_DIR/gitrepo/oneportal/webasto/js-storefront/webastooneportal-spa
npm install
yarn start --ssl
```

Angluar compiles automatically after every code change. 

npm install must only be executed after changing the package.json file in the spartacus root directoy.

Enjoy and Good Luck :)




#### Upgrading Spartacus (1.2.0)

Run the following lines in your terminal within your /webasto/gitrepo/oneportal folder:
```bash
brew upgrade yarn
brew upgrade node@10
brew upgrade angular-cli
```
And 

```bash
yarn install
yarn start --ssl
```