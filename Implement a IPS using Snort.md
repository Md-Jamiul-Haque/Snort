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



