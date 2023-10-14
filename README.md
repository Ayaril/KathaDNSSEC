# DNSSEC implementation with Katharà

My network scheme is made of the following zones:
  - root
  - *.com
    - *.google.com
  - *.net
    - *.test.net
      - *.tzone.test.net

## Device configuration
**Named.conf.*** defines each zone for which the server is responsible and provides configuration information per zone
The configuration file of each DNS server consist of: 
  - Zone
  - Domain
  - Class (omitted, by default setted as "Internet class")
  - Type (master, slave, hint)
  - File

Each server have to implement **root zone** as hint type server and with **"."** domain.

For each server there is a **Database file** that begins with a **SOA (Start Of Authority)** record.
DB files include the following data:
  - **TTL**:
    Time to Live specifies the amount of time a DNS record can be cached by a resolver or intermediary DNS server.
  - **SOA**:
    The SOA record defines administrative information about the zone, including the primary nameserver, the email address of the administrator, and various timing values
  - **Serial**:
    A version number for the zone file. Increment this value whenever you make changes to the zone.
  - **Refresh time**:
    How often secondary DNS servers should check for updates (in seconds)
  - **Retry time**:
    How often secondary DNS servers should retry if the refresh fails (in seconds)
  - **Expire time**:
    The maximum time a secondary DNS server should keep the zone data if it cannot refresh (in seconds)
  - **Negative cache TTL**

  - **NS**:
    Name server records specify the authoritative DNS servers for the zone
  - **A**:
    Address records map hostnames to IP addresses
  - **MX**:
    Mail Exchange records specify the mail servers responsible for receiving email on behalf of the domain.
    
They also include additional data regarding devices in the same zone.

**Statup files** are necessary to configure devices to send and receive correctly ping requests.

## DNS configuration 
### Master configuration
Each authority DNS server creates two pair of keys, a private used to digitally sign each record in the zone and a public published on the internet used to verify the signatures.

In this environtment the DNSSEC extension will be configurated in the "test.net" domain
In named.conf.options we have to set:
  > dnssec-enable yes;
  > dnssec-validation yes;
  > dnssec-lookaside auto;
  > key-directory "/var/cache/bind/keys";

To create the keys we have to move in:
 > cd /var/cache/bind/keys
and execute the commands:
> dnssec-keygen -a RSASHA256 -b 1280 test.net
> dnssec-keygen -a RSASHA256 -b 2048 -f KSK test.net
Those commands will create two files named Ktest.net.*.key and two files named Ktest.net.*.private

## Testing
The testing of the network can be made with the command **dig**, a lookup utility used to query DNS servers and retrieve DNS information for domain names

