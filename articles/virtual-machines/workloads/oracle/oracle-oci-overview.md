---
title: Integrowanie platformy Microsoft Azure przy użyciu infrastruktury Chmurowej Oracle | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o rozwiązaniach, które integrują aplikacje Oracle działającymi w systemie Microsoft Azure z bazami danych w infrastrukturze chmury Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 711fce9627537e68171c3b170121900d6b14ca1e
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743653"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Rozwiązania aplikacji Oracle, integracji Microsoft Azure i infrastruktury chmury Oracle (wersja zapoznawcza)

Firmy Microsoft i Oracle wspólnie opracowały małe opóźnienia i wysoką przepływność połączenia wielu chmur, co pozwala korzystać z zalet najlepsze cechy obu chmurach. 

Korzystając z tego połączenia wielu chmur, można podzielić wielowarstwową aplikację do uruchamiania na Oracle infrastruktury chmury (OCI) warstwę bazy danych i aplikacji oraz innych warstw w systemie Microsoft Azure. Proces jest podobny do uruchamiania stosu całego rozwiązania w chmurze pojedynczego. 

> [!IMPORTANT]
> Ta funkcja wielu chmur, jest obecnie w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

Jeśli interesuje Cię we wdrażaniu rozwiązań Oracle w całości w infrastrukturze platformy Azure, zobacz [obrazów maszyn wirtualnych firmy Oracle i ich wdrażania w systemie Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Omówienie scenariusza

Łączność między chmurą zapewnia rozwiązanie umożliwiające uruchamianie aplikacji wiodące w branży firmy Oracle i własnych aplikacjach niestandardowych na maszynach wirtualnych platformy Azure podczas cieszą się korzyściami z hostowaną bazą danych usług w OCI. 

Aplikacje, które można uruchomić w konfiguracji o wielu chmur, obejmują:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikacji handlu detalicznego Oracle
* Zarządzania finansami Hyperion Oracle

Poniższy diagram jest ogólne omówienie rozwiązania połączonej. Dla uproszczenia na diagramie przedstawiono warstwy aplikacji i warstwy danych. W zależności od architecutre aplikacji rozwiązania mogą obejmować dodatkowych warstw, np. warstwa sieci web na platformie Azure. Aby uzyskać więcej informacji zobacz następujące sekcje.

![Azure — Omówienie rozwiązania OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Łączność między chmurą w wersji zapoznawczej jest ograniczona do regionu Azure wschodnie stany USA (eastus) i OCI Ashburn region (us-ashburn-1).

## <a name="networking"></a>Networking

Klienci korporacyjni często wybrać Zróżnicuj i wdrażanie obciążeń za pośrednictwem wielu chmur dla różnych firm i przyczyn operacyjnych. Aby zróżnicować, klientom łączenia sieci chmury, za pośrednictwem Internetu, IPSec VPN lub przy użyciu rozwiązania połączenie bezpośrednie dostawcy w chmurze za pośrednictwem sieci lokalnej. Połączeń w sieciach w chmurze może wymagać znaczących inwestycji w czasie, pieniądze, projektowania, zamówień, instalacji, testowania i operacji. 

Aby rozwiązać te problemy klientów, Oracle i Microsoft włączono integrację wielu chmur. Połączenia sieciowe między chmurami zostanie nawiązane połączenie [ExpressRoute](../../../expressroute/expressroute-introduction.md) obwodu na platformie Microsoft Azure przy użyciu [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) obwód w OCI. To połączenie jest możliwe, gdzie lokalizacji komunikacji równorzędnej usługi ExpressRoute systemu Azure jest w sąsiedztwie lub w tej samej lokalizacji komunikacji równorzędnej, OCI FastConnect. Ta konfiguracja umożliwia bezpieczne, szybkie łączność między dwie chmury bez konieczności korzystania z dostawcą usług pośrednich.

Przy użyciu usługi ExpressRoute i FastConnect, klienci mogą komunikacji równorzędnej sieci wirtualnej na platformie Azure z sieci wirtualnej chmury w OCI, pod warunkiem, że przestrzeń prywatnych adresów IP nienakładającego. Komunikacja równorzędna dwóch sieci umożliwia zasobu w sieci wirtualnej do komunikowania się z zasobem w sieci wirtualnej chmury OCI tak, jakby były one w tej samej sieci.

## <a name="network-security"></a>Bezpieczeństwo sieci

Zabezpieczenia sieci jest kluczowym składnikiem aplikacji przedsiębiorstwa i to centralny punkt tego rozwiązania wielu chmur. Wszelkie dane przesyłane za pośrednictwem usługi ExpressRoute i FastConnect przekazuje za pośrednictwem sieci prywatnej. Taka konfiguracja pozwala na potrzeby bezpiecznej komunikacji między siecią wirtualną platformy Azure i sieci wirtualnej chmury programu Oracle. Nie trzeba podać publicznego adresu IP do maszyn wirtualnych na platformie Azure. Podobnie nie potrzebujesz bramy internetowej w OCI. Cała komunikacja odbywa się za pośrednictwem prywatny adres IP używany przez maszyny.

Ponadto możesz skonfigurować [list zabezpieczeń](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) na reguły zabezpieczeń i sieci wirtualnej chmury OCI (dołączony do platformy Azure [sieciowe grupy zabezpieczeń](../../../virtual-network/security-overview.md)). Użyj tych reguł do sterowania ruchem odbywającym się między maszynami w sieciach wirtualnych. Można dodać reguły zabezpieczeń sieci na poziomie komputera, na poziomie podsieci, a także na poziomie sieci wirtualnej.
 
## <a name="identity"></a>Tożsamość

Tożsamość jest jednym z filarów core współpracy między firmami Microsoft i Oracle. Dużo pracy zostały wykonane w celu zintegrowania [Oracle Usługa zarządzania tożsamościami w chmurze](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) przy użyciu [usługi Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Usługa Azure AD to usługa firmy Microsoft oparte na chmurze tożsamości i dostępu do zarządzania. Pomaga użytkownikom, zaloguj się i uzyskać dostęp do różnych zasobów. Usługa Azure AD umożliwia również zarządzanie użytkownikami i ich uprawnienia.

Obecnie ta integracja pozwala na zarządzanie w jednej centralnej lokalizacji, która jest usługi Azure Active Directory. Usługa Azure AD synchronizuje wszelkie zmiany w katalogu z odpowiadający mu katalog bazy danych Oracle i jest używany do logowania jednokrotnego do wielu chmur rozwiązaniach Oracle.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji i oficjalne dokumenty dotyczące OCI zobacz [w chmurze firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentacji.
