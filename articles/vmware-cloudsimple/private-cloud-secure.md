---
title: Azure VMware Solutions (Automatyczna synchronizacja) — bezpieczna synchronizacja w chmurze prywatnej
description: Opisuje, jak zabezpieczyć chmurę prywatną rozwiązań Azure VMware (Automatyczna synchronizacja)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020084"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Jak zabezpieczyć środowisko chmury prywatnej do automatycznej synchronizacji

Zdefiniuj funkcję kontroli dostępu opartej na rolach (RBAC) dla usługi automatycznej synchronizacji, portalu automatycznej synchronizacji i automatycznej synchronizacji chmury prywatnej na platformie Azure. Użytkownicy, grupy i role umożliwiające dostęp do programu vCenter w chmurze prywatnej do automatycznej synchronizacji są określane przy użyciu logowania jednokrotnego w oprogramowaniu VMware. 

## <a name="rbac-for-avs-service"></a>Usługa RBAC dla usługi automatycznej synchronizacji

Tworzenie usługi automatycznej synchronizacji wymaga roli **właściciela** lub **współautora** w ramach subskrypcji platformy Azure. Domyślnie wszyscy właściciele i Współautorzy mogą utworzyć usługę automatycznej synchronizacji i uzyskać dostęp do portalu automatycznej synchronizacji w celu utworzenia i zarządzania chmurą prywatną. Dla każdego regionu można utworzyć tylko jedną usługę automatycznej synchronizacji. Aby ograniczyć dostęp do określonych administratorów, wykonaj poniższą procedurę.

1. Utwórz usługę automatycznej synchronizacji w nowej **grupie zasobów** na Azure Portal
2. Określ wartość RBAC dla grupy zasobów.
3. Kup węzły i Użyj tej samej grupy zasobów co usługa automatycznej synchronizacji

Tylko użytkownicy, którzy mają uprawnienia **właściciela** lub **współautora** w grupie zasobów, zobaczą usługę automatycznej synchronizacji i uruchamiają Portal automatycznej synchronizacji.

Aby uzyskać więcej informacji na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC na potrzeby automatycznej synchronizacji usługi vCenter w chmurze prywatnej

Domyślny `CloudOwner@AVS.local` użytkownika jest tworzony w domenie programu vCenter logowania jednokrotnego, gdy zostanie utworzona chmura prywatna. Użytkownik CloudOwner ma uprawnienia do zarządzania programem vCenter. Do programu vCenter SSO są dodawane dodatkowe źródła tożsamości w celu udzielenia dostępu innym użytkownikom. Wstępnie zdefiniowane role i grupy są konfigurowane w programie vCenter, który może służyć do dodawania kolejnych użytkowników.

### <a name="add-new-users-to-vcenter"></a>Dodawanie nowych użytkowników do programu vCenter

1. [Eskalować uprawnienia](escalate-private-cloud-privileges.md) dla **CloudOwner@AVS.local** użytkownika w chmurze prywatnej automatycznej synchronizacji.
2. Zaloguj się do programu vCenter przy użyciu **CloudOwner@AVS.local**
3. [Dodaj użytkowników korzystających z logowania](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)jednokrotnego w programie vCenter.
4. Dodawanie użytkowników do [grup programu vCenter Logowanie jednokrotne](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Aby uzyskać więcej informacji na temat wstępnie zdefiniowanych ról i grup, zobacz artykuł [Automatyczna synchronizacja chmury prywatnej model uprawnień oprogramowania VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Dodaj nowe źródła tożsamości

Możesz dodać dodatkowych dostawców tożsamości dla domeny vCenter SSO z chmury prywatnej automatycznej synchronizacji. Dostawcy tożsamości udostępniają uwierzytelnianie i grupy vCenter logowania jednokrotnego zapewniają autoryzację dla użytkowników.

* [Użyj Active Directory jako dostawcy tożsamości](set-vcenter-identity.md) w przypadku automatycznej synchronizacji programu vCenter w chmurze prywatnej.
* [Korzystanie z usługi Azure AD jako dostawcy tożsamości](azure-ad.md) w przypadku automatycznej synchronizacji programu vCenter w chmurze prywatnej

1. [Eskalować uprawnienia](escalate-private-cloud-privileges.md) dla **CloudOwner@AVS.local** użytkownika w chmurze prywatnej automatycznej synchronizacji.
2. Zaloguj się do programu vCenter przy użyciu **CloudOwner@AVS.local**
3. Dodaj użytkowników z dostawcy tożsamości do [grup usługi vCenter Logowanie jednokrotne](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Zabezpieczanie sieci w środowisku chmury prywatnej do automatycznej synchronizacji

Automatyczna synchronizacja zabezpieczeń sieci środowiska chmury prywatnej jest kontrolowana przez zabezpieczenie dostępu do sieci i sterowanie ruchem sieciowym między zasobami.

### <a name="access-to-avs-private-cloud-resources"></a>Dostęp do automatycznej synchronizacji zasobów chmury prywatnej

Automatyczna synchronizacja chmury prywatnej usługa vCenter i dostęp do zasobów odbywa się za pośrednictwem bezpiecznego połączenia sieciowego:

* **[Połączenie ExpressRoute](on-premises-connection.md)** . ExpressRoute zapewnia bezpieczne połączenie o wysokiej przepustowości i małych opóźnieniach ze środowiska lokalnego. Użycie połączenia umożliwia lokalnym usługom, sieciom i użytkownikom uzyskiwanie dostępu do narzędzia do automatycznej synchronizacji w chmurze prywatnej.
* **[Brama sieci VPN typu lokacja-lokacja](vpn-gateway.md)** . Połączenie sieci VPN typu lokacja-lokacja umożliwia dostęp do zasobów chmury prywatnej w wersji zachodzącej ze źródeł lokalnych za pośrednictwem bezpiecznego tunelu. Należy określić, które sieci lokalne mogą wysyłać i odbierać ruch sieciowy w chmurze prywatnej automatycznej synchronizacji.
* **[Brama sieci VPN typu punkt-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Używanie połączenia sieci VPN typu punkt-lokacja do szybkiego dostępu zdalnego do programu vCenter w chmurze prywatnej chmury.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Kontrolowanie ruchu sieciowego w chmurze prywatnej automatycznej synchronizacji

Tabele i reguły zapory kontrolują ruch sieciowy w chmurze prywatnej automatycznej synchronizacji. Tabela zapory pozwala kontrolować ruch sieciowy między siecią źródłową lub adresem IP a siecią docelową lub adresem IP na podstawie kombinacji reguł zdefiniowanych w tabeli.

1. Utwórz [tabelę zapory](firewall.md#add-a-new-firewall-table).
2. [Dodaj reguły](firewall.md#create-a-firewall-rule) do tabeli zapory.
3. [Dołącz tabelę zapory do sieci VLAN/podsieci] (firewall. MD # Attach-vlanssubnet.
