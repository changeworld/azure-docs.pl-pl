---
title: Uzyskiwanie dostępu do rozwiązań VMware (Automatyczna synchronizacja) z platformy Azure w środowisku lokalnym
description: Uzyskiwanie dostępu do rozwiązań VMware platformy Azure (Automatyczna synchronizacja) z sieci lokalnej za pośrednictwem zapory
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 09b25dbdc8fc13c40ccd89b2cfd78611cedaac9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024470"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Uzyskiwanie dostępu do własnego środowiska chmury prywatnej i aplikacji z poziomu lokalnego

Połączenie można skonfigurować z sieci lokalnej w celu automatycznej synchronizacji przy użyciu usługi Azure ExpressRoute lub sieci VPN typu lokacja-lokacja. Uzyskaj dostęp do narzędzia do automatycznej synchronizacji w chmurze prywatnej oraz wszelkich obciążeń uruchamianych w chmurze prywatnej automatycznej synchronizacji przy użyciu połączenia. Można kontrolować, które porty są otwierane w ramach połączenia, za pomocą zapory w sieci lokalnej. W tym artykule omówiono niektóre typowe wymagania dotyczące portów aplikacji. W przypadku innych aplikacji zapoznaj się z dokumentacją aplikacji dotyczącą wymagań portów.

## <a name="ports-required-for-accessing-vcenter"></a>Porty wymagane do uzyskania dostępu do programu vCenter

Aby można było uzyskać dostęp do Menedżera automatycznej synchronizacji chmurowej usługi vCenter i NSX-T, porty zdefiniowane w poniższej tabeli muszą być otwarte na zaporze lokalnej. 

| Port       | Źródło                           | Cel                      | Przeznaczenie                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Lokalne serwery DNS          | Automatyczna synchronizacja serwerów DNS w chmurze prywatnej        | Wymagane do przeszukiwania DNS przez *AZ. AVS.io* do automatycznej synchronizacji serwerów DNS w chmurze prywatnej z sieci lokalnej.     |
| 53 (UDP)   | Automatyczna synchronizacja serwerów DNS w chmurze prywatnej        | Lokalne serwery DNS          | Wymagane do przesyłania dalej DNS wyszukiwania lokalnych nazw domen z funkcji automatycznej synchronizacji chmurowej programu vCenter do lokalnych serwerów DNS. |
| 80 (TCP)   | Sieć lokalna              | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Wymagane do przekierowywania adresu URL programu vCenter z *protokołu HTTP* do *protokołu HTTPS*.                                                         |
| 443 (TCP)  | Sieć lokalna              | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Wymagane w celu uzyskania dostępu do Menedżera vCenter i NSX-T z sieci lokalnej.                                           |
| 8000 (TCP) | Sieć lokalna              | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Wymagany do vMotion maszyn wirtualnych z lokalnego do automatycznej synchronizacji chmury prywatnej.                                          |
| 8000 (TCP) | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Sieć lokalna              | Wymagany do vMotion maszyn wirtualnych z chmury prywatnej do wersji lokalnej.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porty wymagane do korzystania z lokalnej usługi Active Directory jako źródła tożsamości

Aby skonfigurować lokalną usługi Active Directory jako źródło tożsamości w ramach automatycznej synchronizacji chmury prywatnej, należy otworzyć porty zdefiniowane w tabeli. Zobacz temat [Korzystanie z usługi Azure AD jako dostawcy tożsamości dla programu vCenter w ramach automatycznej synchronizacji wersji zapoznaj się z chmurą prywatną](https://docs.azure.cloudsimple.com/azure-ad/) .

| Port         | Źródło                           | Cel                                         | Przeznaczenie                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Automatyczna synchronizacja serwerów DNS w chmurze prywatnej        | Lokalne serwery DNS                             | Wymagane w celu przeprowadzenia przeszukiwania DNS lokalnych nazw domen usługi Active Directory z wersji próbnej wersji załączonej do lokalnych serwerów DNS.        |
| 389 (TCP/UDP) | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Lokalne kontrolery domeny usługi Active Directory     | Wymagane w przypadku komunikacji z protokołem LDAP z programu reautomatyczna synchronizacja chmurowych serwerów vCenter do kontrolerów domeny usługi Active Directory na potrzeby uwierzytelniania użytkowników.              |
| 636 (TCP)     | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Lokalne kontrolery domeny usługi Active Directory     | Wymagane w celu komunikacji przy użyciu protokołu Secure LDAP (LDAPs) z usługi automatycznej synchronizacji chmury prywatnej programu vCenter Server z kontrolerami domeny Active Directory na potrzeby uwierzytelniania użytkowników. |
| 3268 (TCP)    | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Lokalne serwery wykazu globalnego usługi Active Directory | Wymagane na potrzeby komunikacji z protokołem LDAP w wdrożeniach z wielodomenowym kontrolerem.                                                                      |
| 3269 (TCP)    | Automatyczna synchronizacja sieci zarządzania chmurą prywatną | Lokalne serwery wykazu globalnego usługi Active Directory | Wymagane w przypadku komunikacji LDAPs w wdrożeniach z wielodomenowym kontrolerem.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Typowe porty wymagane do uzyskania dostępu do obciążeń maszyn wirtualnych

Dostęp do obciążeń maszyn wirtualnych działających w ramach automatycznej synchronizacji prywatnej chmury wymaga otwarcia portów na zaporze lokalnej. W poniższej tabeli przedstawiono niektóre typowe porty wymagane i ich przeznaczenia. Wymagania dotyczące portów specyficzne dla aplikacji można znaleźć w dokumentacji aplikacji.

| Port         | Źródło                         | Cel                          | Przeznaczenie                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Sieć lokalna            | Automatyczna synchronizacja sieci obciążeń chmur prywatnych       | Bezpieczny dostęp powłoki do maszyn wirtualnych z systemem Linux działających w ramach automatycznej synchronizacji chmury prywatnej.            |
| 3389 (TCP)    | Sieć lokalna            | Automatyczna synchronizacja sieci obciążeń chmur prywatnych       | Pulpit zdalny do maszyn wirtualnych z systemem Windows uruchomiony w ramach automatycznej synchronizacji chmury prywatnej.               |
| 80 (TCP)      | Sieć lokalna            | Automatyczna synchronizacja sieci obciążeń chmur prywatnych       | Uzyskaj dostęp do dowolnych serwerów sieci Web wdrożonych na maszynach wirtualnych działających w ramach automatycznej synchronizacji chmury prywatnej.      |
| 443 (TCP)     | Sieć lokalna            | Automatyczna synchronizacja sieci obciążeń chmur prywatnych       | Uzyskaj dostęp do dowolnych bezpiecznych serwerów sieci Web wdrożonych na maszynach wirtualnych działających w ramach automatycznej synchronizacji chmury prywatnej. |
| 389 (TCP/UDP) | Automatyczna synchronizacja sieci obciążeń chmur prywatnych | Lokalna sieć usługi Active Directory | Przyłączanie maszyn wirtualnych obciążeń systemu Windows do lokalnej domeny usługi Active Directory.     |
| 53 (UDP)      | Automatyczna synchronizacja sieci obciążeń chmur prywatnych | Sieć lokalna                  | Dostęp usługi DNS do obciążeń maszyn wirtualnych do lokalnych serwerów DNS.       |

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Nawiązywanie połączenia z siecią lokalną za pomocą usługi Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Konfigurowanie sieci VPN typu lokacja-lokacja z poziomu lokalnego](https://docs.azure.cloudsimple.com/vpn-gateway/)
