---
title: 'Azure ExpressRoute: przykłady konfiguracji routera — translator adresów sieciowych'
description: Ta strona zawiera przykłady konfiguracji routera dla routerów Cisco i Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: ef2fd40db422c459ca966e802344ef45f7ec01de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072109"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Przykłady konfiguracji routera do konfigurowania translatora adresów sieciowych i zarządzania nim

Ta strona zawiera przykłady konfiguracji NAT dla routerów serii Cisco ASA i Juniper SRX podczas pracy z usługą ExpressRoute. Są one przeznaczone wyłącznie do próbek i nie mogą być używane w stanie, w jakim są. Można współpracować z dostawcą, aby wymyślić odpowiednie konfiguracje dla sieci.

> [!IMPORTANT]
> Przykłady na tej stronie mają być przeznaczone wyłącznie do wskazówek. Musisz współpracować z dostawcą sprzedaży / zespół techniczny i zespół sieci, aby wymyślić odpowiednie konfiguracje, aby spełnić Twoje potrzeby. Firma Microsoft nie będzie obsługiwać problemów związanych z konfiguracjami wymienionymi na tej stronie. W celu uzyskania pomocy technicznej należy skontaktować się z dostawcą urządzenia.
> 
> 

* Poniższe przykłady konfiguracji routera dotyczą komunikacji równorzędnej usługi Azure Public i Microsoft. Nie można skonfigurować translatora adresów sieciowych dla prywatnej komunikacji równorzędnej platformy Azure. Przejrzyj [wymagania dotyczące komunikacji równorzędnej usługi ExpressRoute](expressroute-circuit-peerings.md) [i usługi ExpressRoute NAT, aby](expressroute-nat.md) uzyskać więcej informacji.

* Musisz używać oddzielnych pul ADRESÓW IP NAT dla łączności z Internetem i programem ExpressRoute. Korzystanie z tej samej puli adresów IP NAT w Internecie i ubrać w program ExpressRoute spowoduje asymetryczne routingu i utratę łączności.


## <a name="cisco-asa-firewalls"></a>Zapory Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Konfiguracja PAT dla ruchu z sieci klienta do firmy Microsoft
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

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Konfiguracja PAT dla ruchu z firmy Microsoft do sieci klienta

**Interfejsy i kierunek:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Konfiguracji:**

Pula NAT:

    object network outbound-PAT
        host <NAT-IP>

Serwer docelowy:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Grupa obiektów dla adresów IP klientów

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Polecenia NAT:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Routery serii Juniper SRX
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


### <a name="2-create-two-security-zones"></a>2. Tworzenie dwóch stref zabezpieczeń
* Strefa zaufania dla sieci wewnętrznej i strefy nieufności dla routerów edge z widokiem na sieć zewnętrzną
* Przypisywanie odpowiednich interfejsów do stref
* Zezwalaj na usługi na interfejsach

    bezpieczeństwa { strefy { security-zone Trust { host-inbound-traffic { system-services { ping;                   } protokoły { bgp;                   } } interfejsy { reth0.100;               } } strefa zabezpieczeń Nieufność { host-ruch przychodzący { system-services { ping;                   } protokoły { bgp;                   } } interfejsy { reth1.100;               }           }       }   }


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


### <a name="4-configure-nat-policies"></a>4. Konfigurowanie zasad NAT
* Utwórz dwie pule NAT. Jeden będzie używany do ruchu NAT wychodzących do firmy Microsoft i innych od firmy Microsoft do klienta.
* Tworzenie reguł na nat odpowiedniego ruchu
  
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

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Skonfiguruj BGP do reklamowania prefiksów selektywnych w każdym kierunku
Zapoznaj się z przykładami na stronie [Przykłady konfiguracji routingu.](expressroute-config-samples-routing.md)

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

