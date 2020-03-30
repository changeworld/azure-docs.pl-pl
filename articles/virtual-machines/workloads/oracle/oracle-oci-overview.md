---
title: Integracja platformy Microsoft Azure z infrastrukturą Oracle Cloud | Dokumenty firmy Microsoft
description: Dowiedz się więcej o rozwiązaniach, które integrują aplikacje Oracle działające na platformie Microsoft Azure z bazami danych w oracle cloud infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: e1249913300be532cc6514f1478bbc6f4183c001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300557"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Rozwiązania oracle z aplikacjami integrujące microsoft azure i oracle cloud infrastructure (wersja zapoznawcza)

Firmy Microsoft i Oracle nawiązały współpracę w celu zapewnienia łączności między chmurami o niskich opóźnieniach i dużej przepustowości, co pozwala na korzystanie z najlepszych z obu chmur. 

Korzystając z tej łączności między chmurami, można podzielić wielowarstwową aplikację do uruchamiania warstwy bazy danych w oracle cloud infrastructure (OCI) i aplikacji i innych warstw na platformie Microsoft Azure. Środowisko jest podobne do uruchamiania całego stosu rozwiązań w jednej chmurze. 

> [!IMPORTANT]
> Ta funkcja między chmurami jest obecnie w wersji zapoznawczej i [obowiązują ograniczenia.](#region-availability) Aby ustanowić łączność o małym opóźnieniu między platformą Azure i OCI, twoja subskrypcja platformy Azure musi najpierw zostać włączona dla tej możliwości. Aby zapisać się do wersji zapoznawczej, należy wypełnić ten krótki [formularz ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Nie możesz korzystać z tej funkcji, dopóki nie otrzymasz wiadomości e-mail z potwierdzeniem. Możesz również skontaktować się z przedstawicielem firmy Microsoft, aby włączyć tę wersję zapoznawczą. Dostęp do możliwości w wersji zapoznawczej jest zależny od dostępności i ograniczony przez firmę Microsoft według własnego uznania. Wypełnienie ankiety nie gwarantuje dostępu. Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie powinna być używana dla obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Szczegółowe informacje można znaleźć w [dodatkowych warunkach użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) w wersji zapoznawczej platformy Microsoft Azure. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

Jeśli chcesz wdrożyć rozwiązania Oracle w całości w infrastrukturze platformy Azure, zobacz [obrazy maszyn wirtualnych Oracle i ich wdrożenie na platformie Microsoft Azure.](oracle-vm-solutions.md)

## <a name="scenario-overview"></a>Omówienie scenariusza

Łączność między chmurami zapewnia rozwiązanie do uruchamiania wiodących w branży aplikacji Oracle i własnych aplikacji niestandardowych na maszynach wirtualnych platformy Azure, a jednocześnie korzysta z zalet hostowanych usług baz danych w OCI. 

Aplikacje, które można uruchamiać w konfiguracji między chmurami obejmują:

* Pakiet e-biznesowy
* JD Edwards EnterpriseOne
* Peoplesoft
* Aplikacje Oracle Retail
* Zarządzanie finansami Oracle Hyperion

Poniższy diagram jest omówienie wysokiego poziomu połączonego rozwiązania. Dla uproszczenia diagram pokazuje tylko warstwę aplikacji i warstwę danych. W zależności od architektury aplikacji rozwiązanie może zawierać dodatkowe warstwy, takie jak warstwa sieci web na platformie Azure. Aby uzyskać więcej informacji, zobacz następujące sekcje.

![Omówienie rozwiązania OCI platformy Azure](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Dostępność regionów 

Łączność między chmurami jest ograniczona do następujących regionów:
* Azure East US (eastus) & OCI Ashburn (Stany Zjednoczone Na wschodzie)
* Azure UK South (uksouth) & OCI London (Wielka Brytania Południowa)
* Azure Canada Central (canadacentral) & OCI Toronto (Kanada Południowo-Wschodnia)
* Azure Europa Zachodnia (westeurope) & OCI Amsterdam (Holandia Północno-Zachodnia)

## <a name="networking"></a>Obsługa sieci

Klienci korporacyjni często decydują się na dywersyfikację i wdrażanie obciążeń w wielu chmurach z różnych powodów biznesowych i operacyjnych. Aby zdywersyfikować, klienci łączą sieci w chmurze za pomocą Internetu, sieci VPN IPSec lub korzystania z rozwiązania do łączności bezpośredniej dostawcy chmury za pośrednictwem sieci lokalnej. Wzajemne połączenia z sieciami w chmurze mogą wymagać znacznych inwestycji w czas, pieniądze, projektowanie, zaopatrzenie, instalację, testowanie i operacje. 

Aby sprostać tym wyzwaniom klientów, firmy Oracle i Microsoft umożliwiły zintegrowane środowisko wielochmurowe. Sieć między chmurami jest ustanawiana przez połączenie obwodu [usługi ExpressRoute](../../../expressroute/expressroute-introduction.md) na platformie Microsoft Azure z obwodem [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) w OCI. Ta łączność jest możliwa, gdy lokalizacja komunikacji równorzędnej usługi Azure ExpressRoute znajduje się w pobliżu lub w tej samej lokalizacji komunikacji równorzędnej co usługa OCI FastConnect. Ta konfiguracja umożliwia bezpieczną i szybką łączność między dwiema chmurami bez konieczności korzystania z pośredniego dostawcy usług.

Za pomocą usługi ExpressRoute i FastConnect klienci mogą równorzędować sieć wirtualną na platformie Azure z wirtualną siecią w chmurze w OCI, pod warunkiem że prywatna przestrzeń adresowa IP nie nakłada się na siebie. Komunikacja równorzędna dwóch sieci umożliwia zasobom w sieci wirtualnej komunikowanie się z zasobem w sieci wirtualnej chmury OCI tak, jakby były one w tej samej sieci.

## <a name="network-security"></a>Bezpieczeństwo sieci

Bezpieczeństwo sieci jest kluczowym elementem każdej aplikacji korporacyjnej i ma kluczowe znaczenie dla tego rozwiązania z wieloma chmurami. Każdy ruch przechodzący przez usługi ExpressRoute i FastConnect przechodzi przez sieć prywatną. Ta konfiguracja umożliwia bezpieczną komunikację między siecią wirtualną platformy Azure a siecią chmury wirtualnej Oracle. Nie trzeba podawać publiczny adres IP do żadnych maszyn wirtualnych na platformie Azure. Podobnie, nie potrzebujesz bramy internetowej w OCI. Cała komunikacja odbywa się za pośrednictwem prywatnego adresu IP maszyn.

Ponadto można skonfigurować [listy zabezpieczeń](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) w wirtualnej sieci w chmurze OCI i reguły zabezpieczeń (dołączone do [grup zabezpieczeń sieci](../../../virtual-network/security-overview.md)platformy Azure). Użyj tych reguł do kontrolowania ruchu przepływającego między komputerami w sieciach wirtualnych. Reguły zabezpieczeń sieci mogą być dodawane na poziomie komputera, na poziomie podsieci, a także na poziomie sieci wirtualnej.
 
## <a name="identity"></a>Tożsamość

Tożsamość jest jednym z głównych filarów partnerstwa między Microsoft i Oracle. Wykonano znaczną pracę w celu zintegrowania [usługi Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) z [usługą Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Usługa Azure AD to chmurowa usługa zarządzania tożsamościami i dostępem firmy Microsoft w chmurze. Pomaga użytkownikom zalogować się i uzyskać dostęp do różnych zasobów. Usługa Azure AD umożliwia również zarządzanie użytkownikami i ich uprawnieniami.

Obecnie ta integracja umożliwia zarządzanie w jednej centralnej lokalizacji, która jest usługa Azure Active Directory. Usługa Azure AD synchronizuje wszelkie zmiany w katalogu z odpowiednim katalogiem Oracle i jest używana do logowania jednokrotnego do rozwiązań Oracle w różnych chmurach.

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do [sieci między chmurami](configure-azure-oci-networking.md) między platformą Azure a OCI. 

Aby uzyskać więcej informacji i oficjalnych dokumentów na temat OCI, zobacz dokumentację [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
