---
title: Przykłady konfiguracji routera — usługi Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera przykłady konfiguracji routera dla routerów Cisco i Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2d7fb060896de8df266489451a11ba343760c747
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367476"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Przykłady konfiguracji routera, aby skonfigurować routingu oraz zarządzanie nim
Ta strona zawiera interfejs i przykłady konfiguracji routingu dla Cisco IOS-XE i Juniper MX serii routerów, podczas pracy z usługą ExpressRoute. Te powinny być przykłady tylko do celów informacyjnych i nie może być używany, ponieważ jest. Możesz pracować z dostawcą, co pozwoli uzyskać odpowiednich konfiguracji sieci. 

> [!IMPORTANT]
> Przykłady na tej stronie są przeznaczone wyłącznie w przypadku orientacji. Należy skontaktować się z dostawcą zespołu sprzedaży / technicznych i zespołowi sieci, co pozwoli uzyskać odpowiednie konfiguracje do własnych potrzeb. Microsoft nie będzie obsługiwał problemy związane z konfiguracji opisanych na tej stronie. Musisz skontaktować się z dostawcą urządzenia problemów.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Ustawienia MTU i TCP MSS na interfejsy routera
* Rozmiar jednostki MTU interfejsu usługi ExpressRoute jest 1500, który jest typowy domyślny rozmiar jednostki MTU interfejsu Ethernet na routerze. Chyba że router ma inny rozmiar jednostki MTU domyślnie, nie ma potrzeby określić wartość na interfejsie routera.
* W odróżnieniu od bramą Azure VPN MSS TCP dla obwodu usługi ExpressRoute nie trzeba określać.

Poniższe przykłady konfiguracji routera mają zastosowanie do wszystkich połączeń komunikacji równorzędnej. Przegląd [komunikacje równorzędne usługi ExpressRoute](expressroute-circuit-peerings.md) i [wymagania dotyczące routingu usługi ExpressRoute](expressroute-routing.md) więcej informacji na temat routingu.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE na podstawie routery
Przykłady w tej sekcji mają zastosowanie w przypadku dowolnego router, na którym uruchomiono rodziny systemów operacyjnych XE dla systemu IOS.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurowanie w interfejsach i podrzędne
Będzie wymagać interfejsu sub, na połączenie komunikacji równorzędnej każdego routera, który możesz połączyć się z firmą Microsoft. Interfejs podsieci, mogą zostać zidentyfikowane z Identyfikatorem sieci VLAN lub pary skumulowany identyfikatorów sieci VLAN i adresu IP.

**Definicja interfejsu Dot1Q**

W tym przykładzie przedstawiono definicję interfejsu podrzędną podrzędny interfejs za pomocą pojedynczego identyfikatora sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adres IPv4 zawsze jest liczbą nieparzystą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definicja interfejsu QinQ**

W tym przykładzie przedstawiono definicję interfejsu podrzędną podrzędny interfejs za pomocą dwóch identyfikatorów sieci VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli używany pozostaje taka sama we wszystkie komunikacje równorzędne. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adres IPv4 zawsze jest liczbą nieparzystą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Konfigurowanie sesje eBGP
Należy skonfigurować sesji protokołu BGP dla każdego wystąpienia komunikacji równorzędnej z firmą Microsoft. Poniższy przykład umożliwia konfigurowanie sesji protokołu BGP z firmą Microsoft. Jeśli adres IPv4, używane dla interfejsu sub a.b.c.d, adres IP sąsiada protokołu BGP (Microsoft) będzie podsieci a.b.c.d+1. Ostatni oktet adres IPv4 sąsiada protokołu BGP będą zawsze liczbą parzystą.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurowanie prefiksów do anonsowania za pośrednictwem sesji protokołu BGP
Można skonfigurować router w taki sposób, aby anonsować wybierz prefiksów do firmy Microsoft. Możesz to zrobić korzystając z poniższego przykładu.

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
Możesz użyć mapy tras i listy prefiks prefiksy filtrowania rozpropagowane w Twojej sieci. Poniższy przykład służy do wykonania zadania. Upewnij się, że Instalator wyświetla odpowiedni prefiks.

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
Przykłady w tej sekcji dotyczą wszystkie routery z serii Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurowanie w interfejsach i podrzędne

**Definicja interfejsu Dot1Q**

W tym przykładzie przedstawiono definicję interfejsu podrzędną podrzędny interfejs za pomocą pojedynczego identyfikatora sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adres IPv4 zawsze jest liczbą nieparzystą.

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

W tym przykładzie przedstawiono definicję interfejsu podrzędną podrzędny interfejs za pomocą dwóch identyfikatorów sieci VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli używany pozostaje taka sama we wszystkie komunikacje równorzędne. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adres IPv4 zawsze jest liczbą nieparzystą.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. Konfigurowanie sesje eBGP
Należy skonfigurować sesji protokołu BGP dla każdego wystąpienia komunikacji równorzędnej z firmą Microsoft. Poniższy przykład umożliwia konfigurowanie sesji protokołu BGP z firmą Microsoft. Jeśli adres IPv4, używane dla interfejsu sub a.b.c.d, adres IP sąsiada protokołu BGP (Microsoft) będzie podsieci a.b.c.d+1. Ostatni oktet adres IPv4 sąsiada protokołu BGP będą zawsze liczbą parzystą.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurowanie prefiksów do anonsowania za pośrednictwem sesji protokołu BGP
Można skonfigurować router w taki sposób, aby anonsować wybierz prefiksów do firmy Microsoft. Możesz to zrobić korzystając z poniższego przykładu.

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
Możesz użyć mapy tras i listy prefiks prefiksy filtrowania rozpropagowane w Twojej sieci. Poniższy przykład służy do wykonania zadania. Upewnij się, że Instalator wyświetla odpowiedni prefiks.

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

