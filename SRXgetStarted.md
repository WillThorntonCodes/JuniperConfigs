##Basic configuration to setup a Juniper SRX firewall for lab use
```
system {
    host-name srx-firewall;
    name-server {
        8.8.8.8;
    }
}
## CONFIGURE SECURITY NAT, POLICES, AND ZONES
security {
    nat {
        source {
            rule-set INTERNET {
                from zone trust;
                to zone untrust;
                rule INTERNET_RULE {
                    match {
                        source-address 192.168.3.1/24;
                        destination-address 0.0.0.0/0;
                    }
                    then {              
                        source-nat {
                            interface;
                        }
                    }
                }
            }
        }
    }
    policies {
        from-zone trust to-zone untrust {
            policy allow-all {
                match {
                    source-address any;
                    destination-address any;
                    application any;
                }
                then {
                    permit;
                }
            }
        }
    }
    zones {                             
        security-zone trust {
            host-inbound-traffic {
                system-services {
                    all;
                }
            }
            interfaces {
                ge-0/0/0.0;
            }
        }
        security-zone untrust {
            host-inbound-traffic {
                system-services {
                    all;
                }
            }
            interfaces {
                ge-0/0/1.0;
            }
        }
    }
}
## CONFIGURE INTERFACES
interfaces {                            
    ge-0/0/0 {
        unit 0 {
            family inet {
                address 192.168.3.1/24; ## TRUST INTERFACE IP
            }
        }
    }
    ge-0/0/1 {
        unit 0 {
            family inet {
                dhcp; ## UNTRUST INTERFACE IP (I USE DHCP IN MY LAB)
            }
        }
    }
    fxp0 {
        unit 0 {
            family inet {
                address 192.168.0.1/24; ## MANAGEMENT IP
            }
        }
    }
}
routing-options {                       
    static {
        route 0.0.0.0/0 next-hop 192.*.*.1; ## NEEDED TO POINT INTERNET TRAFFIC TO ISP GATEWAY
    }
}
```
