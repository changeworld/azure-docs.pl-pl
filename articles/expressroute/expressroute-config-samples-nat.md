---
title: Konfiguracja routera przykłady - NAT - Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera przykłady konfiguracji routera dla routerów Cisco i Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: ccee0f0c01119ebbfb5ba9c5980ee006a555a399
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367612"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Przykłady konfiguracji routera, aby skonfigurować i zarządzać nimi translatora adresów Sieciowych

Ta strona zawiera przykłady konfiguracji translatora adresów Sieciowych dla Cisco ASA i Juniper SRX serii routerów, podczas pracy z usługą ExpressRoute. Te powinny być przykłady tylko do celów informacyjnych i nie może być używany, ponieważ jest. Możesz pracować z dostawcą, co pozwoli uzyskać odpowiednich konfiguracji sieci.

> [!IMPORTANT]
> Przykłady na tej stronie są przeznaczone wyłącznie w przypadku orientacji. Należy skontaktować się z dostawcą zespołu sprzedaży / technicznych i zespołowi sieci, co pozwoli uzyskać odpowiednie konfiguracje do własnych potrzeb. Microsoft nie będzie obsługiwał problemy związane z konfiguracji opisanych na tej stronie. Musisz skontaktować się z dostawcą urządzenia problemów.
> 
> 

* Poniższe przykłady konfiguracji routera mają zastosowanie do komunikacji równorzędnej Azure publicznej i firmy Microsoft. Nie musisz skonfigurować translatora adresów Sieciowych dla prywatnej komunikacji równorzędnej Azure. Przegląd [komunikacje równorzędne usługi ExpressRoute](expressroute-circuit-peerings.md) i [wymagania translatora adresów Sieciowych ExpressRoute](expressroute-nat.md) Aby uzyskać więcej informacji.

* Łączność z Internetem i usługi ExpressRoute, należy użyć oddzielnych pul adresów IP translatora adresów Sieciowych. Przy użyciu tej samej puli adresów IP translatora adresów Sieciowych dla Internetu i usługi ExpressRoute spowoduje asymetryczny routing i utraty łączności.


## <a name="cisco-asa-firewalls"></a>Cisco ASA zapory
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Konfiguracja osobisty token dostępu dla ruchu z sieci klienta do firmy Microsoft
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Konfiguracja osobisty token dostępu dla ruchu z firmy Microsoft do sieci klienta

**Interfejsy i kierunek:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Konfiguracja:**

Pula NAT:

    object network outbound-PAT
        host <NAT-IP>

Serwer docelowy:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Grupy obiektów dla adresów IP klienta

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Polecenia translatora adresów Sieciowych:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX serii routery
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Tworzenie nadmiarowego interfejsów Ethernet dla klastra
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Utwórz dwie strefy zabezpieczeń
* Zaufania strefy sieci wewnętrznej i godny dla zewnętrznych sieci skierowany routery graniczne
* Przypisz odpowiednie interfejsy do strefy
* Zezwalaj usługom na interfejsach

    zabezpieczenia {strefy {zaufania strefie zabezpieczeń {hosta — — ruch przychodzący {systemu usług {ping;                   } protokołów {bgp;                   interfejsy}} {reth0.100;               {}} Godny strefy zabezpieczeń {hosta — — ruch przychodzący {systemu usług {ping;                   } protokołów {bgp;                   interfejsy}} {reth1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Tworzenie zasad zabezpieczeń między strefami
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
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
            from-zone Untrust to-zone Trust {
                policy allow-any {
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
    }


### <a name="4-configure-nat-policies"></a>4. Konfigurowanie zasad translatora adresów Sieciowych
* Utwórz dwie pule translatora adresów Sieciowych. Jeden będzie służyć do NAT ruchu wychodzącego do firmy Microsoft i innych firmy Microsoft do klienta.
* Tworzenie reguły NAT ruchu odpowiednich
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Konfigurowanie protokołu BGP, aby anonsować selektywne prefiksy w każdym kierunku
Zobacz przykłady w [przykłady konfiguracji routingu](expressroute-config-samples-routing.md) strony.

### <a name="6-create-policies"></a>6. Tworzenie zasad
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Kolejne kroki
Szczegółowe informacje znajdują się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

