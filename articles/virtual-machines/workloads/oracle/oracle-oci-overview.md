---
title: Integracja Microsoft Azure z usługą Oracle Cloud Infrastructure | Microsoft Docs
description: Informacje o rozwiązaniach, które integrują aplikacje Oracle działające w Microsoft Azure z bazami danych w infrastrukturze chmury firmy Oracle (OCI).
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
ms.openlocfilehash: 4628955998ab8b289a429cdfb85e23f7f97b0b40
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101432"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Rozwiązania aplikacji Oracle integrujące Microsoft Azure i infrastrukturę chmurową Oracle (wersja zapoznawcza)

Firmy Microsoft i Oracle współpracują w celu zapewnienia małych opóźnień i wysokiej przepływności łączności między chmurami, co pozwala korzystać z zalet najlepszych chmur. 

Korzystając z tej łączności między chmurami, można podzielić aplikację wielowarstwową w celu uruchomienia warstwy bazy danych w infrastrukturze chmury firmy Oracle (OCI) oraz aplikacji i innych warstw na Microsoft Azure. Środowisko jest podobne do uruchamiania całego stosu rozwiązań w pojedynczej chmurze. 

> [!IMPORTANT]
> Ta funkcja między chmurami jest obecnie dostępna w wersji zapoznawczej i [obowiązują ograniczenia](#preview-limitations). Aby włączyć łączność z małymi opóźnieniami między platformą Azure a OCI, Twoja subskrypcja platformy Azure musi być wyświetlona na liście dla tej funkcji. Musisz zarejestrować się w wersji zapoznawczej, wykonując ten krótki [formularz ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Nie możesz korzystać z tej możliwości, dopóki nie otrzymasz wiadomości e-mail z potwierdzeniem. Możesz również skontaktować się z przedstawicielem firmy Microsoft w celu włączenia tej wersji zapoznawczej. Dostęp do funkcji wersji zapoznawczej podlega dostępności i jest ograniczony przez firmę Microsoft w ramach własnego uznania. Zakończenie ankiety nie gwarantuje dostępu. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie powinna być używana na potrzeby obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

Jeśli interesuje się wdrażanie rozwiązań Oracle w całości w infrastrukturze platformy Azure, zobacz [obrazy maszyn wirtualnych Oracle i ich wdrożenie na Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Omówienie scenariusza

Łączność między chmurami zapewnia rozwiązanie umożliwiające uruchamianie aplikacji wiodących w branży firmy Oracle oraz własnych aplikacji niestandardowych na maszynach wirtualnych platformy Azure, a jednocześnie korzysta z zalet hostowanych usług bazy danych w systemie OCI. 

Aplikacje, które można uruchomić w konfiguracji obejmującej wiele chmur, obejmują:

* Pakiet E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikacje sieci sprzedaży firmy Oracle
* Zarządzanie finansowe Oracle Hyperion

Poniższy diagram przedstawia ogólny przegląd połączonego rozwiązania. Dla uproszczenia diagram pokazuje tylko warstwę aplikacji i warstwę danych. W zależności od architektury aplikacji rozwiązanie może zawierać dodatkowe warstwy, takie jak warstwa sieci Web na platformie Azure. Aby uzyskać więcej informacji zobacz następujące sekcje.

![Omówienie rozwiązania OCI na platformie Azure](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Łączność między chmurami w wersji zapoznawczej jest ograniczona do regionu Wschodnie stany USA (Wschodnie) i OCI Ashburn (US-Ashburn-1).

## <a name="networking"></a>Networking

Klienci korporacyjni często decydują się na zróżnicowanie i wdrażaniu obciążeń dla wielu chmur w różnych sytuacjach firmy i operacyjnych. W celu zróżnicowania klienci korzystają z Internetu, sieci VPN IPSec lub bezpośredniego rozwiązania do łączności dostawcy w chmurze za pośrednictwem sieci lokalnej. Połączenia między sieciami w chmurze mogą wymagać znaczących inwestycji w miarę czasu, pieniędzy, projektowania, zaopatrzenia, instalacji, testowania i operacji. 

Aby rozwiązać te problemy dla klientów, oprogramowanie Oracle i firma Microsoft umożliwiły zintegrowane środowisko z obsługą chmury. Sieci w chmurze są nawiązywane przez połączenie obwodu usługi [ExpressRoute](../../../expressroute/expressroute-introduction.md) w Microsoft Azure z obwodem usługi [FASTCONNECT](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) w systemie OCI. To połączenie jest możliwe, gdy lokalizacja komunikacji równorzędnej usługi Azure ExpressRoute znajduje się w pobliżu lub w tej samej lokalizacji komunikacji równorzędnej co OCI FastConnect. Ta konfiguracja umożliwia bezpieczną i szybką łączność między dwiema chmurami bez konieczności pośredniego dostawcy usług.

Korzystając z ExpressRoute i FastConnect, klienci mogą połączyć się z siecią wirtualną na platformie Azure z wirtualną siecią chmurową w systemie OCI, pod warunkiem, że przestrzeń prywatnych adresów IP nie nakłada się na siebie. Komunikacja równorzędna dwóch sieci umożliwia zasobom w sieci wirtualnej komunikowanie się z zasobem w sieci wirtualnej OCI, tak jakby znajdowały się one w tej samej sieci.

## <a name="network-security"></a>Bezpieczeństwo sieci

Zabezpieczenia sieci to kluczowy składnik dowolnej aplikacji korporacyjnej, który stanowi centralne rozwiązanie w chmurze. Każdy ruch przechodzący przez ExpressRoute i FastConnect przechodzi przez sieć prywatną. Ta konfiguracja umożliwia bezpieczną komunikację między siecią wirtualną platformy Azure a siecią chmurą wirtualną firmy Oracle. Nie musisz podawać publicznego adresu IP do żadnych maszyn wirtualnych na platformie Azure. Podobnie nie jest potrzebna Brama internetowa w OCI. Cała komunikacja odbywa się za pośrednictwem prywatnego adresu IP maszyn.

Ponadto można skonfigurować [listy zabezpieczeń](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) w sieci i reguł zabezpieczeń w chmurze wirtualnej OCI (dołączone do [sieciowych grup zabezpieczeń](../../../virtual-network/security-overview.md)platformy Azure). Za pomocą tych reguł można kontrolować ruch przepływający między maszynami w sieciach wirtualnych. Reguły zabezpieczeń sieci można dodawać na poziomie komputera, na poziomie podsieci, a także na poziomie sieci wirtualnej.
 
## <a name="identity"></a>Tożsamość

Tożsamość jest jednym z podstawowych filarów powiązania między firmą Microsoft i bazą danych Oracle. W celu zintegrowania [usługi Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) z usługą [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) została wykonana znaczna pracy. Azure AD to usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft. Ułatwia użytkownikom logowanie się i dostęp do różnych zasobów. Usługa Azure AD umożliwia również zarządzanie użytkownikami i ich uprawnieniami.

Obecnie ta Integracja umożliwia zarządzanie w jednej centralnej lokalizacji, która jest Azure Active Directory. Usługa Azure AD synchronizuje wszelkie zmiany w katalogu z odpowiednim katalogiem Oracle i służy do logowania jednokrotnego do wielochmurowych rozwiązań firmy Oracle.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z międzychmurową [siecią](configure-azure-oci-networking.md) między platformą Azure a OCI. 

Aby uzyskać więcej informacji i oficjalny dokument dotyczący OCI, zobacz dokumentację w [chmurze firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
