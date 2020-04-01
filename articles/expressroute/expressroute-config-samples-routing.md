---
title: 'Azure ExpressRoute: przykłady konfiguracji routera'
description: Ta strona zawiera próbki konfiguracji routera dla routerów Cisco i Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 5304aefaf3ad70bb552b4b0d1b26fcce9867c9c0
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397747"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Przykłady konfiguracji routera do konfigurowania routingu i zarządzania nim
Ta strona zawiera przykłady konfiguracji interfejsu i routingu dla routerów z serii Cisco IOS-XE i Juniper MX podczas pracy z usługą ExpressRoute. Są one przeznaczone wyłącznie do próbek i nie mogą być używane w stanie, w jakim są. Można współpracować z dostawcą, aby wymyślić odpowiednie konfiguracje dla sieci. 

> [!IMPORTANT]
> Przykłady na tej stronie mają być przeznaczone wyłącznie do wskazówek. Musisz współpracować z dostawcą sprzedaży / zespół techniczny i zespół sieci, aby wymyślić odpowiednie konfiguracje, aby spełnić Twoje potrzeby. Firma Microsoft nie będzie obsługiwać problemów związanych z konfiguracjami wymienionymi na tej stronie. W celu uzyskania pomocy technicznej należy skontaktować się z dostawcą urządzenia.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Ustawienia MMU i TCP w interfejsach routera
* Jednostki MTU dla interfejsu usługi ExpressRoute to 1500, co jest typową domyślną jednostką MTU dla interfejsu Ethernet na routerze. Jeśli router nie ma domyślnie innej jednostki MTU, nie ma potrzeby określania wartości w interfejsie routera.
* W przeciwieństwie do bramy sieci VPN platformy Azure nie trzeba określać usługi TCP MSS dla obwodu usługi ExpressRoute.

Poniższe przykłady konfiguracji routera dotyczą wszystkich komunikacji równorzędnej. Przejrzyj wymagania [dotyczące komunikacji równorzędnej usługi ExpressRoute](expressroute-circuit-peerings.md) i [routingu usługi ExpressRoute,](expressroute-routing.md) aby uzyskać więcej informacji na temat routingu.


## <a name="cisco-ios-xe-based-routers"></a>Routery z systemem Cisco IOS-XE
Przykłady w tej sekcji dotyczą dowolnego routera z rodziną systemu operacyjnego IOS-XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurowanie interfejsów i podkonfekcji
Na każdym routerze, z który łączysz się z firmą Microsoft, wymagany jest interfejs podrzędny na komunikację równorzędną. Interfejs podrzędny można zidentyfikować za pomocą identyfikatora sieci VLAN lub skumulowanej pary identyfikatorów sieci VLAN i adresu IP.

**Definicja interfejsu Dot1Q**

Ten przykład zawiera definicję pod-interfejsu dla podpołącza z jednym identyfikatorem sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definicja interfejsu QinQ**

Ten przykład zawiera definicję pod-interfejsu dla podpołącza z dwoma identyfikatorami sieci VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli jest używany, pozostaje taki sam we wszystkich elementach równorzędnych. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy na komunikację równorzędną. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Konfigurowanie sesji eBGP
Należy skonfigurować sesję Protokołu BGP z firmą Microsoft dla każdej komunikacji równorzędnej. Poniższy przykład umożliwia skonfigurowanie sesji BGP z firmą Microsoft. Jeśli adres IPv4 używany dla interfejsu podrzędnego był a.b.c.d, adres IP sąsiada BGP (Microsoft) będzie a.b.c.d+1. Ostatni oktet adresu IPv4 sąsiada protokołu IPv4 protokołu BGP będzie zawsze liczbą parzysty.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurowanie prefiksów, które mają być reklamowane podczas sesji BGP
Router można skonfigurować tak, aby anonsował wybrane prefiksy firmie Microsoft. Możesz to zrobić za pomocą poniższego przykładu.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mapy tras
Za pomocą map tras i list prefiksów można filtrować prefiksy propagowane do sieci. Możesz użyć poniższego przykładu, aby wykonać zadanie. Upewnij się, że masz odpowiednią konfigurację list prefiksów.

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

### <a name="5-configuring-bfd"></a>5. Konfigurowanie BFD

BfD można skonfigurować w dwóch miejscach. Jeden na poziomie interfejsu, a drugi na poziomie BGP. Poniższy przykład dotyczy interfejsu QinQ. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Routery z serii Juniper MX
Przykłady w tej sekcji dotyczą wszystkich routerów serii Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurowanie interfejsów i podkonfekcji

**Definicja interfejsu Dot1Q**

Ten przykład zawiera definicję pod-interfejsu dla podpołącza z jednym identyfikatorem sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

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

Ten przykład zawiera definicję pod-interfejsu dla podpołącza z dwoma identyfikatorami sieci VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli jest używany, pozostaje taki sam we wszystkich elementach równorzędnych. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy na komunikację równorzędną. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

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
Należy skonfigurować sesję Protokołu BGP z firmą Microsoft dla każdej komunikacji równorzędnej. Poniższy przykład umożliwia skonfigurowanie sesji BGP z firmą Microsoft. Jeśli adres IPv4 używany dla interfejsu podrzędnego był a.b.c.d, adres IP sąsiada BGP (Microsoft) będzie a.b.c.d+1. Ostatni oktet adresu IPv4 sąsiada protokołu IPv4 protokołu BGP będzie zawsze liczbą parzysty.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurowanie prefiksów, które mają być reklamowane podczas sesji BGP
Router można skonfigurować tak, aby anonsował wybrane prefiksy firmie Microsoft. Możesz to zrobić za pomocą poniższego przykładu.

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


### <a name="4-route-policies"></a>4. Zasady tras
Za pomocą map tras i list prefiksów można filtrować prefiksy propagowane do sieci. Możesz użyć poniższego przykładu, aby wykonać zadanie. Upewnij się, że masz odpowiednią konfigurację list prefiksów.

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

### <a name="4-configuring-bfd"></a>4. Konfigurowanie BFD
BfD można skonfigurować tylko w sekcji protokołu BGP.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje znajdują się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).



