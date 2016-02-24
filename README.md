# nftablesctl
This is a run control script and a systemd service file to load, unload, save and test nftables rules.

###Setup
Copy the RC script to the '/usr/sbin' directory and make it executable.
```
#> cp nftablesctl /usr/sbin/
#> chmod 755 /usr/sbin/nftablesctl
```
Now copy the sysytemd service file into the right place.
```
#> cp nftables.service /usr/lib/systemd/system/
```

###Initial Tests
Load the ipv4 standard filter that comes with the nftables package.
```
#> nftablesctl load -c /etc/nftables/ipv4-filter
```
Now save our general firewall file.
```
#> nftablesctl save
Overwrite nftables rules storage '/etc/nftables/firewall.nft' (y/N): y
```
For test purpose add a new output filter rule that drops packets to ip address 8.8.4.4.
```
#> nft insert rule filter output ip daddr 8.8.4.4 counter drop
```
The result can be checked via the list command.
```
#> nftablesctl list
table ip filter {
        chain input {
                type filter hook input priority 0; policy accept;
        }

        chain forward {
                type filter hook forward priority 0; policy accept;
        }

        chain output {
                type filter hook output priority 0; policy accept;
                ip daddr 8.8.4.4 counter packets 0 bytes 0 drop 
        }
}
```
Now save and reload the firewall.nft file again.
```
#> nftablesctl save -n
Overwrite nftables rules storage '/etc/nftables/firewall.nft'.
#> nftablesctl load
Loading nftables rules from file '/etc/nftables/firewall.nft'.
```
