# Installing Snort 3 on Ubuntu: A Complete Guide  

Snort, the legendary open-source Intrusion Detection and Prevention System (IDS/IPS), has gained significant improvements with the release of Snort 3. Its enhanced performance, modular design, and user-friendly configuration make it a favorite for cybersecurity professionals.  

This guide walks you through the step-by-step process of installing Snort 3 on Ubuntu. Whether you're setting it up for learning or defending your network, let’s get started!  

---

## **Why Choose Snort 3?**  

Snort 3 is a game-changer in intrusion detection. Here's why you should care:  

1. **Modern Architecture:** Built for multi-threading and scalability.  
2. **Flexibility:** YAML-based configuration is intuitive and easy to manage.  
3. **Extensibility:** Supports plugins and custom scripts for advanced use cases.  

Now, let’s turn this excitement into action!  

---

## **Step-by-Step Installation Process**   

1. Before installing Snort 3, ensure your system is up to date to avoid compatibility issues.  

```bash
sudo apt update && sudo apt upgrade -y
```
2. Organize your installation by creating a dedicated directory for Snort.  

```bash
mkdir snort
cd snort
```
3. Snort 3 requires several libraries and tools. Install them all with the following command:  

```bash
sudo apt-get install -y build-essential autotools-dev libdumbnet-dev libluajit-5.1-dev \
libpcap-dev zlib1g-dev pkg-config libhwloc-dev cmake liblzma-dev openssl libssl-dev \
cpputest libsqlite3-dev libtool uuid-dev git autoconf bison flex libcmocka-dev \
libnetfilter-queue-dev libunwind-dev libmnl-dev ethtool libjemalloc-dev
```
### **1. Install PCRE (Perl-Compatible Regular Expressions)**  
PCRE is crucial for pattern matching, a core part of Snort's detection engine.  

```bash
cd ~/snort
wget https://sourceforge.net/projects/pcre/files/pcre/8.45/pcre-8.45.tar.gz
tar -xzvf pcre-8.45.tar.gz
cd pcre-8.45
./configure
make
sudo make install
```
### **2. Install Gperftools**  

Gperftools enhances Snort’s performance monitoring and debugging capabilities.  

```bash
cd ~/snort
wget https://github.com/gperftools/gperftools/releases/download/gperftools-2.9.1/gperftools-2.9.1.tar.gz
tar -xzvf gperftools-2.9.1.tar.gz
cd gperftools-2.9.1
./configure
make
sudo make install
```

### **3. Install Ragel**  

Ragel generates finite state machines used in Snort's parsing processes.  

```bash
cd ~/snort
wget http://www.colm.net/files/ragel/ragel-6.10.tar.gz
tar -xzvf ragel-6.10.tar.gz
cd ragel-6.10
./configure
make
sudo make install
```

### **4. Download (But Don’t Install) Boost C++ Libraries**  

Boost provides critical C++ utilities used in Snort's operation.  

```bash
cd ~/snort
wget https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.gz
tar -xvzf boost_1_77_0.tar.gz
```

### **5. Install Hyperscan**  

Hyperscan is a high-performance regex matching library, essential for Snort's pattern matching.  

```bash
cd ~/snort
wget https://github.com/intel/hyperscan/archive/refs/tags/v5.4.2.tar.gz
tar -xzvf v5.4.2.tar.gz
mkdir ~/snort/hyperscan-5.4.2-build
cd hyperscan-5.4.2-build/
cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DBOOST_ROOT=~/snort/boost_1_77_0/ ../hyperscan-5.4.2
make
sudo make install
```

### **6. Install Flatbuffers**  

Flatbuffers enable efficient serialization for Snort.  

```bash
cd ~/snort
wget https://github.com/google/flatbuffers/archive/refs/tags/v2.0.0.tar.gz -O flatbuffers-v2.0.0.tar.gz
tar -xzvf flatbuffers-v2.0.0.tar.gz
mkdir flatbuffers-build
cd flatbuffers-build
cmake ../flatbuffers-2.0.0
make
sudo make install
```

### **7. Install Data Acquisition (DAQ)**  

DAQ manages packet capture for Snort, ensuring smooth traffic analysis.  

```bash
cd ~/snort
wget https://github.com/snort3/libdaq/archive/refs/tags/v3.0.13.tar.gz -O libdaq-3.0.13.tar.gz
tar -xzvf libdaq-3.0.13.tar.gz
cd libdaq-3.0.13
./bootstrap
./configure
make
sudo make install
```

### **8. Update Shared Libraries**
```bash
sudo ldconfig
```

### **9. Download and Install Snort 3**  

Download the latest version of Snort 3. Please note that the version number (`3.5.2.0` in this example) may change over time. Check the [Snort GitHub Releases](https://github.com/snort3/snort3/releases) for the most up-to-date version and adjust the commands accordingly.  

```bash
cd ~/snort
wget https://github.com/snort3/snort3/archive/refs/tags/3.5.2.0.tar.gz -O snort3-3.5.2.0.tar.gz
tar -xzvf snort3-3.5.2.0.tar.gz
cd snort3-3.5.2.0
./configure_cmake.sh --prefix=/usr/local --enable-jemalloc
cd build
make
sudo make install
```


### **10. Verify Snort Installation**  

Now your Snort 3 installation should be complete. To verify, check the Snort version:  

```bash
/usr/local/bin/snort -v
```

### **11. Test Snort with Default Configuration**  

To test your Snort installation, use the default configuration file by running the following command:  

```bash
sudo snort -c /usr/local/etc/snort/snort.lua
```

### **12. Disable Receive Offload for IPS/IDS**  

When configuring Snort for both IDS and IPS, it's essential to disable both generic and large receive offload on your network adapter for optimal performance.  

1. First, check your network adapter's name:  

```bash
ip a
```

2. Then, check the current offload settings for your network adapter by running:

```bash
sudo ethtool -k eth0 | grep receive-offload
```
(Note: Replace `eth0` with the name of your network adapter, if necessary.)

To disable Large Receive Offload (LRO) and Generic Receive Offload (GRO), modify the `ethtool` service configuration. Follow these steps:

1. Open the `ethtool.service` file in a text editor:  

```bash
sudo nano /lib/systemd/ethtool.service
```
2. Add the following configuration to disable LRO and GRO, replacing `<network adapter>` with the name of your network adapter (e.g., eth0):
```bash
[Unit]
Description=Ethtool Configuration for Network Interface

[Service]
Requires=network.target
Type=oneshot
ExecStart=/sbin/ethtool -K <network adapter> gro off
ExecStart=/sbin/ethtool -K <network adapter> lro off

[Install]
WantedBy=multi-user.target
```

After modifying the `ethtool` service configuration, enable and start the service to apply the changes:

1. Enable the service to start on boot:

```bash
sudo systemctl enable ethtool
```
2. Start the service
```bash
sudo service ethtool start
```

### **1. Create a Local Rules File**  

Now, let's create a local rules file where you can add your custom Snort rules:

1. Create the `local.rules` file in the Snort configuration directory:

```bash
sudo mkdir /usr/local/etc/rules
sudo nano /usr/local/etc/rules/local.rules
```
This will create the local.rules file, where you can add your custom detection rules for Snort.

Let's create a simple Snort rule that generates an alert whenever an ICMP (Internet Control Message Protocol) packet is detected on the network.
```bash
alert icmp any any -> any any (msg:"ICMP Detected"; sid:1000001; )
```
Save and exit the editor (CTRL+X, then Y to confirm, and Enter to save).

**Explanation of the Rule:**

- `alert`: This is the action to take when the rule is triggered (in this case, generate an alert).
- `icmp`: This specifies the protocol that we are inspecting (ICMP).
- `any any -> any any`: This means the rule will match any ICMP packet, regardless of the source or destination IP address or port.
- `msg:"ICMP Detected"`: This is the message that will appear in the alert when this rule is triggered.
- `sid:1000001`: This is the unique Snort ID (SID) assigned to this rule. It helps to uniquely identify the rule.

