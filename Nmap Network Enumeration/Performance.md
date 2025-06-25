
significant role when we need to scan an extensive network or are dealing with low network bandwidth

## Timeouts

When Nmap sends a packet, it takes some time (`Round-Trip-Time` - `RTT`) to receive a response from the scanned port. Generally, `Nmap` starts with a high timeout (`--min-RTT-timeout`) of 100ms.

#### Default Scan

```shell-session
sudo nmap 10.129.2.0/24 -F
```

#### Optimized RTT

```shell-session

sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms
```

we can conclude that setting the initial RTT timeout (`--initial-rtt-timeout`) to too short a time period may cause us to overlook hosts.

## Max Retries

 increase scan speed is by specifying the retry rate of sent packets (`--max-retries`)
The default value is `10`, but we can reduce it to `0`

```shell-session
sudo nmap 10.129.2.0/24 -F | grep "/tcp" | wc -l
```

```shell-session
sudo nmap 10.129.2.0/24 -F --max-retries 0 | grep "/tcp" | wc -l
```

|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.0/24`|Scans the specified target network.|
|`-F`|Scans top 100 ports.|
|`--max-retries 0`|Sets the number of retries that will be performed during the scan.|

## Rates

When setting the minimum rate (`--min-rate <number>`) for sending packets, we tell `Nmap` to simultaneously send the specified number of packets. It will attempt to maintain the rate accordingly.


#### Default Scan

```shell-session
sudo nmap 10.129.2.0/24 -F -oN tnet.default
```
~29s

#### Optimized Scan
```shell-session
sudo nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300
```

~9s


|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.0/24`|Scans the specified target network.|
|`-F`|Scans top 100 ports.|
|`-oN tnet.minrate300`|Saves the results in normal formats, starting the specified file name.|
|`--min-rate 300`|Sets the minimum number of packets to be sent per second.|


#### Default Scan - Found Open Ports

```shell-session
cat tnet.default | grep "/tcp" | wc -l

23
```

#### Optimized Scan - Found Open Ports

```shell-session
cat tnet.minrate300 | grep "/tcp" | wc -l

23
```

## Timing

The default timing template used when we have defined nothing else is the normal (`-T 3`).

`Nmap` offers six different timing templates (`-T <0-5>`) for us to use. These values (`0-5`) determine the aggressiveness of our scans.

- `-T 0` / `-T paranoid`
- `-T 1` / `-T sneaky`
- `-T 2` / `-T polite`
- `-T 3` / `-T normal`
- `-T 4` / `-T aggressive`
- `-T 5` / `-T insane`

The exact used options with their values we can find here: [https://nmap.org/book/performance-timing-templates.html](https://nmap.org/book/performance-timing-templates.html)

#### Default Scan

```shell-session
sudo nmap 10.129.2.0/24 -F -oN tnet.default 

<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 32.44 seconds
```

#### Insane Scan

```shell-session
sudo nmap 10.129.2.0/24 -F -oN tnet.T5 -T 5

<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 18.07 seconds
```

|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.0/24`|Scans the specified target network.|
|`-F`|Scans top 100 ports.|
|`-oN tnet.T5`|Saves the results in normal formats, starting the specified file name.|
|`-T 5`|Specifies the insane timing template.|

#### Default Scan - Found Open Ports

```shell-session
cat tnet.default | grep "/tcp" | wc -l

23
```

#### Insane Scan - Found Open Ports

```shell-session
cat tnet.T5 | grep "/tcp" | wc -l

23
```

More information about scan performance we can find at [https://nmap.org/book/man-performance.html](https://nmap.org/book/man-performance.html)


