---
title: Przykłady konfiguracji routera klienta ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera przykłady konfiguracji routera dla routerów Cisco i Juniper.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: d6ea716f-d5ee-4a61-92b0-640d6e7d6974
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 83a7da2db537a3c900e90432455d59e8ac56d917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850686"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Przykłady konfiguracji routera do konfigurowania i zarządzania nią translatora adresów Sieciowych
Ta strona zawiera przykłady konfiguracji translatora adresów Sieciowych dla Cisco ASA i Juniper SRX routery serii. Te powinny być przykłady tylko do celów informacyjnych i nie mogą być używane, ponieważ jest. Możesz pracować z dostawcą pozwoli uzyskać odpowiedni konfiguracji sieci. 

> [!IMPORTANT]
> Przykłady na tej stronie mają być wyłącznie w celu uzyskania wskazówek. Należy skontaktować się z zespołu sprzedaży / techniczne z dostawcą i sieci zespołu pozwoli uzyskać odpowiedni konfiguracji zgodnie z potrzebami. Microsoft nie będzie obsługiwał problemy związane z konfiguracji opisanych w tej strony. Należy się z dostawcą urządzenia pomocy technicznej.
> 
> 

* Poniższe przykłady konfiguracji routera mają zastosowanie do komunikacji równorzędnych publicznej Azure i firmy Microsoft. Nie należy skonfigurować translatora adresów Sieciowych dla platformy Azure prywatnej komunikacji równorzędnej. Przegląd [komunikacji równorzędnych ExpressRoute](expressroute-circuit-peerings.md) i [wymagania ExpressRoute NAT](expressroute-nat.md) więcej szczegółów.

* Należy użyć oddzielnych pule adresów IP translatora adresów Sieciowych dla łączności z Internetem i ExpressRoute. Przy użyciu tej samej puli IP translatora adresów Sieciowych przez internet oraz ExpressRoute spowoduje asymetrycznego routingu i utratę łączności.


## <a name="cisco-asa-firewalls"></a>Cisco ASA zapór
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Konfiguracja PAWEŁ ruch z sieci klienta do firmy Microsoft
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

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Konfiguracja PAWEŁ ruch od firmy Microsoft do klienta sieci

**Interfejsy i kierunek:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Konfiguracja:**

Pula translacji adresów Sieciowych:

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
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Tworzenie nadmiarowych interfejsów Ethernet dla klastra
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
* Zaufania strefy do sieci wewnętrznej i niezaufanej sieci zewnętrznych ukierunkowane routery brzegowe
* Przypisz odpowiednich interfejsów do strefy
* Zezwalaj usługom na interfejsach

    zabezpieczeń strefy {{zaufania strefy zabezpieczeń {hosta-— ruch przychodzący {systemu services {ping;                   } protokołów {bgp;                   interfejsy}} {reth0.100;               {}} niezaufanej strefy zabezpieczeń {hosta-— ruch przychodzący {systemu services {ping;                   } protokołów {bgp;                   interfejsy}} {reth1.100;               }           }       }   }


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
* Utwórz dwie pule translatora adresów Sieciowych. Jeden posłuży do NAT ruchu wychodzącego do firmy Microsoft i innych firmy Microsoft do klienta.
* Utwórz reguły NAT ruchu odpowiednich
  
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

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Konfigurowanie protokołu BGP do anonsowania prefiksów selektywnego w każdym kierunku
Zapoznaj się próbek w [przykłady konfiguracji Routing ](expressroute-config-samples-routing.md) strony.

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

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje znajdują się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

