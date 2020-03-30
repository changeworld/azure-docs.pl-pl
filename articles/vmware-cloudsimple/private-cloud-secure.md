---
title: Rozwiązania Azure VMware firmy CloudSimple — bezpieczna chmura prywatna
description: W tym artykule opisano, jak zabezpieczyć rozwiązania Azure VMware przez chmurę prywatną CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565982"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Jak zabezpieczyć środowisko chmury prywatnej

Zdefiniuj kontrolę dostępu opartą na rolach (RBAC) dla usługi CloudSimple, portalu CloudSimple i chmury prywatnej z platformy Azure.  Użytkownicy, grupy i role dostępu do vCenter of Private Cloud są określane przy użyciu funkcji SSO VMware.  

## <a name="rbac-for-cloudsimple-service"></a>Usługa RBAC for CloudSimple

Tworzenie usługi CloudSimple wymaga roli **właściciela** lub **współautora** w ramach subskrypcji platformy Azure.  Domyślnie wszyscy właściciele i współautorzy mogą utworzyć usługę CloudSimple i uzyskać dostęp do portalu CloudSimple do tworzenia chmur prywatnych i zarządzania nimi.  Tylko jedna usługa CloudSimple można utworzyć na region.  Aby ograniczyć dostęp do określonych administratorów, wykonaj poniższą procedurę.

1. Tworzenie usługi CloudSimple w nowej **grupie zasobów** w witrynie Azure portal
2. Określ RBAC dla grupy zasobów.
3. Kupowanie węzłów i używanie tej samej grupy zasobów co usługa CloudSimple

Tylko użytkownicy, którzy mają uprawnienia **właściciela** lub **współautora** w grupie zasobów zobaczą usługę CloudSimple i uruchomią portal CloudSimple.

Aby uzyskać więcej informacji na temat funkcji RBAC, zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure.](../role-based-access-control/overview.md)

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC dla private cloud vCenter

Domyślny `CloudOwner@cloudsimple.local` użytkownik jest tworzony w domenie SSO vCenter podczas tworzenia chmury prywatnej.  Użytkownik CloudOwner ma uprawnienia do zarządzania vCenter. Dodatkowe źródła tożsamości są dodawane do vCenter SSO dla udzielania dostępu do różnych użytkowników.  Wstępnie zdefiniowane role i grupy są umniejszane w centrum vCenter, którego można użyć do dodania dodatkowych użytkowników.

### <a name="add-new-users-to-vcenter"></a>Dodawanie nowych użytkowników do centrum vCenter

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md) dla **cloudowner\@cloudsimple.local** użytkownika w chmurze prywatnej.
2. Zaloguj się do vCenter przy użyciu **cloudowner\@cloudsimple.local**
3. [Dodaj użytkowników jednokrotnego logowania vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Dodawanie użytkowników do [grup logowania jednokrotnego vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Aby uzyskać więcej informacji na temat wstępnie zdefiniowanych ról i grup, zobacz [CloudSimple Private Cloud model uprawnień VMware vCenter](learn-private-cloud-permissions.md) artykułu.

### <a name="add-new-identity-sources"></a>Dodawanie nowych źródeł tożsamości

Możesz dodać dodatkowych dostawców tożsamości dla domeny SSO vCenter w chmurze prywatnej.  Dostawcy tożsamości zapewniają uwierzytelnianie, a grupy SSO vCenter zapewniają autoryzację dla użytkowników.

* [Użyj usługi Active Directory jako dostawcy tożsamości](set-vcenter-identity.md) w usłudze Private Cloud vCenter.
* [Używanie usługi Azure AD jako dostawcy tożsamości](azure-ad.md) w usłudze Private Cloud vCenter

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md) dla **cloudowner\@cloudsimple.local** użytkownika w chmurze prywatnej.
2. Zaloguj się do vCenter przy użyciu **cloudowner\@cloudsimple.local**
3. Dodaj użytkowników z dostawcy tożsamości do [grup logowania jednokrotnego vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Bezpieczna sieć w środowisku private cloud

Bezpieczeństwo sieci środowiska private cloud jest kontrolowane przez zabezpieczenie dostępu do sieci i kontrolowanie ruchu sieciowego między zasobami.

### <a name="access-to-private-cloud-resources"></a>Dostęp do zasobów chmury prywatnej

Private Cloud vCenter i dostęp do zasobów odbywa się za pośrednictwem bezpiecznego połączenia sieciowego:

* **[Połączenie usługi ExpressRoute](on-premises-connection.md)**. Usługa ExpressRoute zapewnia bezpieczne połączenie o dużej przepustowości i małych opóźnieniach ze środowiska lokalnego.  Korzystanie z połączenia umożliwia usługom lokalnym, sieciom i użytkownikom dostęp do centrum wirtualnego private cloud.
* **[Brama sieci VPN między lokacjami](vpn-gateway.md)**. Sieć VPN typu lokacja lokacja umożliwia dostęp do zasobów chmury prywatnej z lokalnego środowiska za pośrednictwem bezpiecznego tunelu.  Można określić, które sieci lokalne mogą wysyłać i odbierać ruch sieciowy do chmury prywatnej.
* **[Brama sieci VPN typu punkt-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Użyj połączenia sieci VPN typu punkt-lokacja, aby uzyskać szybki zdalny dostęp do centrum wirtualnego private cloud.

### <a name="control-network-traffic-in-private-cloud"></a>Sterowanie ruchem sieciowym w chmurze prywatnej

Tabele zapory i reguły kontrolują ruch sieciowy w chmurze prywatnej.  Tabela zapory umożliwia kontrolowanie ruchu sieciowego między siecią źródłową lub adresem IP a siecią docelową lub adresem IP na podstawie kombinacji reguł zdefiniowanych w tabeli.

1. Tworzenie [tabeli zapory](firewall.md#add-a-new-firewall-table).
2. [Dodaj reguły](firewall.md#create-a-firewall-rule) do tabeli zapory.
3. [Dołącz tabelę zapory do sieci VLAN/podsieci](firewall.md#attach-vlans-subnet).
