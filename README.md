Python Shiny Local Hosting & Deployment
---
This tutorial will show some neccessay steps to host and deploy a python shiny app on a local linux server.

## 1. Set up a Python Shiny Environment
* Create a conda virtual environment

    ``` conda create -n shiny python=3.12 ```
* Install Python Shiny

    ``` pip install shiny ```
## 2. Write the Shiny App
* For this example, we will use `app.py` in `shiny_app` directory.
## 3. Deploy to Shiny Server
* Refer to the official [documentation](https://shiny.posit.co/py/docs/deploy-on-prem.html) for an open source option to set up shiny Server. 
### 3.1. Install shiny server
* Follow instructions started from Step 3 of this [official link](https://posit.co/download/shiny-server/?_gl=1*ogqd4o*_ga*MTQ2Mzk0Mzc5OC4xNzIyMjkwOTM4*_ga_8QJS108GF1*MTcyMjMwMzAxNy40LjEuMTcyMjMwMzAzNS4wLjAuMA..*_ga_2C0WZ1JHG0*MTcyMjMwMzAxNy40LjEuMTcyMjMwNDQ5OS4wLjAuMA..) to install Shiny Server.
* Command lines to download and install Shiny Server for Ubuntu

    ``` sudo apt-get install gdebi-core ```

    ``` wget https://download3.rstudio.org/ubuntu-18.04/x86_64/shiny-server-1.5.22.1017-amd64.deb ```

    ``` sudo gdebi shiny-server-1.5.22.1017-amd64.deb ```

### 3.2. Configure Python
* Clear out the contents of `/srv/shiny-server` and replace it with `shiny_app`. For example, 
    * `/srv/shiny-server/shiny_app/app.y` will be serverd from `http://callisto:3838/shiny_app` (assuming `callisto` is the hostname of your ubuntu server).
 
* Point shiny server to an absolute path to a virtual environment, e.g., `/home/usr/anaconda3/envs/shiny`. Edit the file `/etc/shiny-server/shiny-server.conf`(root privileges are required). Add a line with `python /home/usr/anaconda3/envs/shiny`. Change the user after `run_as` to your user name (this step might be not needed). The modified `shiny-server.conf` may look like this:

    ```
    # Use system python3 to run Shiny apps
    python /home/usr/anaconda3/envs/shiny;

    # Instruct Shiny Server to run applications as the user "ichxw"
    run_as ichxw;

    # Define a server that listens on port 3838
    server {
    listen 3838;

    # Define a location at the base URL
    location / {

        # Host the directory of Shiny Apps stored in this directory
        site_dir /srv/shiny-server/;

        # Log all Shiny output to files in this directory
        log_dir /var/log/shiny-server;

        # When a user visits the base URL rather than a particular application,
        # an index of the applications available in this directory will be shown.
        directory_index on;
    }
    }
    ```
### 3.3. Restart and Test
* Restart the ubuntu server to apply the changes in `/etc/shiny-server/shiny-server.conf`:

    ``` sudo reboot ```
* Find log files in `/var/log/shiny-server.log` and `/var/log/shiny-server/*.log`.

## 4. Advanced Guidelines
* Refer to this [link](https://docs.posit.co/shiny-server/?_gl=1*b350di*_ga*MTQ2Mzk0Mzc5OC4xNzIyMjkwOTM4*_ga_8QJS108GF1*MTcyMjMwMzAxNy40LjEuMTcyMjMwMzAzNS4wLjAuMA..#installation) for advanced guidelines.
### 4.1. Stopping and starting
* As stated in the guideline: _The installer will automatically deploy the necessary scripts to ensure that Shiny Server is started automatically on boot. When possible, we use `systemd` or `Upstart` to manage the shiny-server service_.
* Start/restart or stop the server mannually

    ``` sudo systemctl start shiny-server ```

    ``` sudo systemctl stop shiny-server ```

    ``` sudo systemctl restart shiny-server ```
* Check the status of the shiny-server service

    ``` sudo systemctl status shiny-server ```
* Enable or disablethe Shiny Server to run automatically at boot time:

    ``` sudo systemctl enable shiny-server ``` 

    ``` sudo systemctl disable shiny-server ```
