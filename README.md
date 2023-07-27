# repeaterTakFlow
Node-Red Flow to forward Repeater coverage from RepeaterBook database to TAK via multicast or TAKServer. (used in conjunction with Ampledatas node-red-contrib-tak).


![nodered](/repeaterTakFlow.png?raw=true "nodered")

![atak](/atak.png?raw=true "atak")

![itak](/itak1.png?raw=true "itak")


REQUIREMENTS:

-Node-Red

-node-red-contrib-tak https://github.com/snstac/node-red-contrib-tak

-TAKServer (or Multicast supported devices)

--------------------------------------------------------------

SETUP NODE-RED:

-https://nodered.org/docs/getting-started/

-options include installing node-red on your local PC, a Raspberry Pi or similar single board computer or mini PC, deploying node-red on a cloud server or virtual private server (VPS), Docker Container, or local virtual environment. After installing node-red you should be able to go to the node-red dashboard at http://*nodeRedIPaddress:1880 you may have to open appropriate ports (1880) to allow devices to access the node-red dashboard.

-ensure you install the "node-red-contrib-tak" node. If not there should be a prompt when you import repeaterTakFlow in Node-Red that there are nodes that need to be installed and will automatically install them for you if you allow. In the event that isnt the case, in Node-Red: Menu (3 horizontal lines) > Manage palette > Install > Search "node-red-contrib-tak" > Install > Install

---------------------------------------------------------------

IMPORT .JSON FLOW TO NODE RED:

-in GitHub: click on "repeaterTakFlow.json" > click on the download icon "Download raw file" > note where the "repeaterTakFlow.json" file downloaded to, default is in your Downloads folder.

-in Node-Red: click on menu icon (3 horizontal lines top right) > click on "Import" > click on "select a file to import" > go to Downloads folder and click on "repeaterTakFlow.json" > Upload > Import

ALTERNATIVELY..

-you can just copy the whole "repeaterTakFlow.json" code from GitHub and paste it into the Node-Red Import Clipboard.

----------------------------------------------------------------

CONFIGURE TAKSERVER:

-TAKServer can be hosted on various platforms: Raspberry Pi, Mini PCs, your local PC, Cloud Servers or Virtual Private Servers (VPS), Docker Containers, or Virtual Environments. Regardless, if you're looking for instructions on setting up your TAKServer I recommend ATAKHQs guides https://www.atakhq.com/en/tak-server.

-Configuring the TCP (TAKServer) node in Node-Red repeaterTakFlow, Input your TAKServer IP and Port, and checkbox whether you're using SSL/TLS or not. If you are using SSL/TLS, ensure you upload you TAKServer certificates and key that are located in the directory of your TAKServer that stores all client certificates/keys (ie: ubuntu docker container is "/opt/tak/certs/files"). I Suggest creating a TAK client "node-red" to be used specifically for Node-Reds handling of forwarding data to TAK. In your certificates file you will want to copy and upload "node-red.pem" as the "Certificate", "node-red.key" as the "Private Key" and "ca.pem" as the "CA Certificate" in the TLS Configuration Properties in the TCP (TAKServer) node in Node-Red. You will also need to enter the Passphrase for your TAKServer truststore, this can be found in your TAKServers CoreConfig.xml file. Default is "atakatak".

----------------------------------------------------------------

USING THE REPEATERTAKFLOW:

-in ATAK: Menu (3 horizontal lines) > Drawing Tools > *choose square > *tap your 3 points to create your box > *select center CoT marker of your box (default name is Rectangle 1) > *click bottom right waypoint/menu icon in radial menu > change shape name to: Repeaters > Send > Broadcast > repeater data will return as CoT icons inside your square. In the remarks section of each CoT returned will be additional data on the repeater.

-in iTAK: Draw > *select polygon (the not circle shape) > select > *create a square around your area of interest > *change name to: Repeater > Share > Share with Contacts > Share With All > repeater data will return as CoT icons inside your square. In the remarks section of each CoT returned will be additional data on the repeater.

----------------------------------------------------------------

HOW THE REPEATERTAKFLOW WORKS:

-TCP (TAKServer) node sends CoT traffic to TAK node to turn into a readable JSON format.

-TAK node sends CoT as JSON to filter data node.

-filter data node constantly reads and waits til a CoT marker includes "repeaters" or "Repeaters" as the callsign in which it grabs the plotted locations of the square shape as the maximum and minimum range to get the repeater data for.

-http req nodes send a http GET to request data from RepeaterBook API.

-f1 node filters the returned data from RepeaterBook, because you cant request specific location of repeaters through the API, it returns every repeater and has to be filtered from this node, only passing on repeaters within Lat / Lon range from the box shape CoT.

-repeater json node formats a readable CoT to be passed to TAK node to turn into a XML CoT that TAK can understand.

-TAK turns the json data to CoT and pushes it via TCP (TAKServer) or UDP depending on what is chosen.

-------------------------------------------------------------------

TROUBLESHOOTING / KNOWN ISSUES:

-in the event the Node-Red service explodes and you lose connection to the Node-Red UI and cant reconnect, you may need to restart your Node-Red service

$ sudo service node-red restart

