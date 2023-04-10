# openpilot navigation (without Prime)
Are you a cheapskate and don't want to fork up $16/mo for comma prime lite, and want to use navigation (or soon to be navigate on openpilot) on your comma three for free? Well, this is for you!

<i>Word of caution, you will need some technical knowhow to set this up.</i>

<b>Prerequisites:</b>
* Mapbox Token (how do I get this?)
* Cloudflare Account* 
* Domain Name* 
* SSH access to your comma three
* Some technical knowledge

<i>* not necessary, but extremely convenient</i>

## Usage

### Obtaining a Mapbox Token 
1. Head over to <a href="https://mapbox.com">Mapbox.com</a> and create an account.
2. Copy your public token.
3. Done!

### Setting up a CloudFlare Tunnel 
<i>If you want to just hotspot into your comma three, or use a dedicated IP, feel free to skip this step.</i>

1. Head over to <a href="https://cloudflare.com">cloudflare.com</a> and create an account.
2. Add your domain to your CloudFlare account with "add site", and point ns.
3. Click on `Zero Trust` in the navigation panel on the left.
4. On the new page, click `Access`, then `Tunnels` in the navigation panel on the left.
5. Click on `Create new tunnel`.
6. Give the tunnel a name you'd like, then click save.
7. SSH into your comma three, and install cloudflared. 
    1. Download the latest cloudflared by running,
    ```bash
    sudo mount -o rw,remount /; cd /data; wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-aarch64.rpm; sudo apt install ./cloudflared-linux-aarch64.rpm
    ```
    2. After this installs, return to CloudFlare.
8. Copy the command listed under `If you already have cloudflared installed on your machine:` in CloudFlare, and paste it into your comma three.
9. Click "next" in CloudFlare. Enter an optional subdomain, and select the domain you added previously to CloudFlare. You can set this for example, to `nav.domain.com`. 
10. Under "Service", select `HTTP` as the Type, and type `http://localhost:8000` into the text field.
11. Click `save tunnel`.
12. You should be able to navigate to this URL, and be presented with a CloudFlare host down page. This means it's working!

### Setting up your comma three
1. SSH into your comma three.
2. Get your Mapbox public token ready. Modify the `/data/continue.sh` file, and add the following line anywhere in the file. 
```
export MAPBOX_TOKEN=<your mapbox token here, please remote the <> from this>
```
2. Paste the following command, 
```bash
cd /data/selfdrive/navd/; wget https; mkdir templates; wget https
```
3. Modify the following file, `/data/selfdrive/manager/process_config.py`, and paste the following somewhere in the `procs` list with proper indentation, 
```python
PythonProcess("navsrv", "selfdrive.navd.navsrv", offroad=True),
```
4. Modify the following file, `/data/selfdrive/navd/templates/index.html`, and paste your Mapbox token into the part that says, 
```javascript
const ACCESS_TOKEN = 'YOUR MAPBOX TOKEN HERE';
```
5. Save the file.
6. Run `sudo systemctl restart comma`
7. If you've followed the instructions properly, you should be able to access this page now at the URL you set earlier in the CloudFlare setup process. If you didn't use a CloudFlare tunnel, and opted for tethering, simply navigate to `http://<commathreeip>:8000`. 

### How to use
Once you've followed the above instructions on setting this up, you should be able to navigate to this webserver anytime the C3 is online and connected to the internet. When on the webserver, you can search for an address, or paste a Google Maps or Apple Maps URL into the appropriate field.

`Set Route` - This will send the route to your comma three.<br>
`Reset` - This will reset the form so you can search for a new route.<br>
`Clear Navigation` - This will remove the current navigation route from your comma three.
