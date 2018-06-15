---
title: Przykłady konfiguracji routera klienta ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera przykłady konfiguracji routera dla routerów Cisco i Juniper.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850770"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Przykłady konfiguracji routera do konfigurowania i zarządzania nią routingu
Ta strona zawiera interfejs i przykłady konfiguracji routingu dla systemu IOS-XE Cisco i Juniper MX routerów serii. Te powinny być przykłady tylko do celów informacyjnych i nie mogą być używane, ponieważ jest. Możesz pracować z dostawcą pozwoli uzyskać odpowiedni konfiguracji sieci. 

> [!IMPORTANT]
> Przykłady na tej stronie mają być wyłącznie w celu uzyskania wskazówek. Należy skontaktować się z zespołu sprzedaży / techniczne z dostawcą i sieci zespołu pozwoli uzyskać odpowiedni konfiguracji zgodnie z potrzebami. Microsoft nie będzie obsługiwał problemy związane z konfiguracji opisanych w tej strony. Należy się z dostawcą urządzenia pomocy technicznej.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Ustawienia MTU i TCP MSS w interfejsach routera
* Rozmiar jednostki MTU interfejsu ExpressRoute jest 1500, która jest typowy domyślny rozmiar jednostki MTU interfejsu Ethernet na routerze. Jeśli router domyślnie ma inny rozmiar jednostki MTU, jest niepotrzebna określenie wartości w interfejsie routera.
* W przeciwieństwie do bramy sieci VPN platformy Azure MSS TCP dla obwodu usługi ExpressRoute nie trzeba określić.

Poniższe przykłady konfiguracji routera mają zastosowanie do wszystkich komunikacji równorzędnych. Przegląd [komunikacji równorzędnych ExpressRoute](expressroute-circuit-peerings.md) i [wymagania dotyczące routingu usługi ExpressRoute](expressroute-routing.md) uzyskać więcej informacji o routingu.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE na podstawie routery
Przykłady w tej sekcji dotyczą żadnych router, na którym uruchomiono rodziny systemów operacyjnych IOS XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurowanie w interfejsach i podrzędne
Konieczne będzie interfejs sub na równorzędna każdego routera, gdy nawiązujesz połączenie do firmy Microsoft. Interfejs podsieci można zidentyfikować z Identyfikatorem sieci VLAN lub pary skumulowany identyfikatorów sieci VLAN i adresu IP.

**Definicja interfejsu Dot1Q**

W tym przykładzie przedstawiono definicję interfejsu podrzędnego interfejsu podrzędnego z jednego identyfikatora sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adresu IPv4 zawsze jest liczbą nieparzystą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definicja interfejsu QinQ**

W tym przykładzie przedstawiono definicję interfejsu podrzędnego interfejsu podrzędnego z dwóch identyfikatorów sieci VLAN. Zewnętrzne identyfikator sieci VLAN (s-tag), jeśli używana jest taka sama we wszystkich komunikacji równorzędnych. Wewnętrzny identyfikator sieci VLAN (c znacznik) jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adresu IPv4 zawsze jest liczbą nieparzystą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Konfigurowanie sesji eBGP
Należy skonfigurować sesję protokołu BGP z firmy Microsoft dla każdej komunikacji równorzędnej. Poniższy przykład umożliwia skonfigurować sesję protokołu BGP z firmą Microsoft. Jeśli adres IPv4 dla interfejsu sub a.b.c.d, adres IP sąsiadów BGP (Microsoft) będzie a.b.c.d+1. Ostatni oktet adresu IPv4 sąsiadów BGP będą zawsze miały liczbą parzystą.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurowanie prefiksy anonsowanie sesji BGP
Można skonfigurować router tak, aby anonsowania prefiksów wybierz do firmy Microsoft. Możesz to zrobić przy użyciu poniższych przykładowych.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mapuje trasy
Można użyć mapy trasy i prefiksu list prefiksy filtru propagowane do sieci. Poniższy przykład służy do wykonywania tego zadania. Upewnij się, że Instalator wyświetla odpowiedni prefiks.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Routery serii juniper MX
Przykłady w tej sekcji dotyczą wszystkie routery serii Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurowanie w interfejsach i podrzędne

**Definicja interfejsu Dot1Q**

W tym przykładzie przedstawiono definicję interfejsu podrzędnego interfejsu podrzędnego z jednego identyfikatora sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adresu IPv4 zawsze jest liczbą nieparzystą.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definicja interfejsu QinQ**

W tym przykładzie przedstawiono definicję interfejsu podrzędnego interfejsu podrzędnego z dwóch identyfikatorów sieci VLAN. Zewnętrzne identyfikator sieci VLAN (s-tag), jeśli używana jest taka sama we wszystkich komunikacji równorzędnych. Wewnętrzny identyfikator sieci VLAN (c znacznik) jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adresu IPv4 zawsze jest liczbą nieparzystą.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Konfigurowanie sesji eBGP
Należy skonfigurować sesję protokołu BGP z firmy Microsoft dla każdej komunikacji równorzędnej. Poniższy przykład umożliwia skonfigurować sesję protokołu BGP z firmą Microsoft. Jeśli adres IPv4 dla interfejsu sub a.b.c.d, adres IP sąsiadów BGP (Microsoft) będzie a.b.c.d+1. Ostatni oktet adresu IPv4 sąsiadów BGP będą zawsze miały liczbą parzystą.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurowanie prefiksy anonsowanie sesji BGP
Można skonfigurować router tak, aby anonsowania prefiksów wybierz do firmy Microsoft. Możesz to zrobić przy użyciu poniższych przykładowych.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Mapuje trasy
Można użyć mapy trasy i prefiksu list prefiksy filtru propagowane do sieci. Poniższy przykład służy do wykonywania tego zadania. Upewnij się, że Instalator wyświetla odpowiedni prefiks.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje znajdują się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

