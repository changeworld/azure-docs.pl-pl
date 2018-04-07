---
title: Omówienie standardowe ochrony przed atakami DDoS Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze Azure ochrona przed atakami DDoS.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: bb19baae1bc03633b7646fd2255ca910532595c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-ddos-protection-standard-overview"></a>Omówienie usługi Azure Standard ochrony przed atakami DDoS

Rozproszone "odmowa usługi" (DDoS) są jednym z największych problemów dostępności i zabezpieczeń ukierunkowane klientów przenoszenie swoje aplikacje w chmurze. Próbuje takiego ataku na wyczerpanie zasobów aplikacji, co aplikacja niedostępne do autoryzowanych użytkowników. Może być celem ataków DDoS dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Ochrona przed atakami DDoS Azure, w połączeniu z najlepszymi rozwiązaniami projektowania aplikacji zapewniają ochronę przed atakami DDoS. Ochrona przed atakami DDos Azure zapewnia następujące warstwy usług: 

- **Podstawowa ochrona przed atakami DDoS Azure**: włączane automatycznie w ramach platformy Azure, bez dodatkowych opłat. Zawsze włączone ruchu monitorowania i w czasie rzeczywistym zmniejszenie typowych ataków na poziomie sieci zapewnia tego samego zabezpieczenia, wykorzystywane przez usługi online firmy Microsoft. Cały skali globalnej sieci platformy Azure może służyć do rozpowszechniania i ograniczyć ruch ataku w regionach. Podano ochrony dla protokołów IPv4 i IPv6 Azure [publiczne adresy IP](virtual-network-public-ip-address.md).
- **Standardowa ochrony przed atakami DDoS Azure** zapewnia dodatkowe ograniczenie możliwości dostosowana specjalnie do zasobów sieci wirtualnej platformy Azure. To proste włączyć i nie wymaga żadnych zmian w aplikacji. Zasady ochrony są dopasowane za pomocą dedykowanego ruchu monitorowania i komputer, algorytmów uczenia i zastosowane do publicznych adresów IP skojarzonych z zasobami wdrożonymi w sieci wirtualne, takie jak usługi równoważenia obciążenia Azure, Azure Application Gateway i Azure Wystąpienia usługi sieć szkieletowa usług. Dane telemetryczne w czasie rzeczywistym jest dostępna za pośrednictwem widoków Azure Monitor, podczas ataku i historii. Ochronę warstwy aplikacji można dodać za pomocą [zapory aplikacji sieci Web bramy aplikacji](https://azure.microsoft.com/services/application-gateway). Ochrony praw IPv4 Azure [publiczne adresy IP](virtual-network-public-ip-address.md). 

![Standard ochrony przed atakami DDoS Azure](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

> [!IMPORTANT]
> Standardowa ochrony przed atakami DDoS Azure jest obecnie w wersji zapoznawczej. Zapewnia ochronę zasobów platformy Azure, który ma Azure publicznego adresu IP skojarzonego, takich jak maszyny wirtualne, usługi równoważenia obciążenia i bramy aplikacji. Musisz [zarejestrować](http://aka.ms/ddosprotection) usługi przed włączeniem ochrony przed atakami DDoS — standardowe dla Twojej subskrypcji. Po zarejestrowaniu zespołu Azure DDoS skontaktuje się i prowadzi użytkownika przez proces aktywacji. Ochrona przed atakami DDoS — standardowe są dostępne w wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie centralnej nam, Europa Północna, Europa Zachodnia, Japonia Zachodnia, Japonia Wschodnia, Azja Wschodnia i Azja południowo-wschodnia regionów tylko. Podczas udostępniania wersji zapoznawczej są nie naliczane opłaty za korzystanie z usługi.

Zachęcamy do wypróbowania DDoS ochrony standardowe w środowiskach rozwoju, testów lub produkcji. Aby przesłać opinię dotyczącą komfort pracy, należy korzystać z następujących zasobów:
- [Ochrona przed atakami DDoS Azure na Forum usługi Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Ochrona przed atakami DDoS Azure na MSDN Forum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Ochrona przed atakami DDos Azure w przepełnienie stosu](https://stackoverflow.com/tags/azure-ddos/info)

W przypadku problemów dotyczących obsługi, możesz [Otwórz bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md). Podczas ochrony przed atakami DDoS — standardowe jest w wersji zapoznawczej, jest obsługiwane w sposób optymalny.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy ataków DDoS zmniejsza standardowe ochrony przed atakami DDoS

Ochrona przed atakami DDoS — standardowe ograniczania ataki tego rodzaju:

- **Ataki pomiarowej**: celem ataku jest wypełniania warstwy sieci o rozległe pozornie potrzebnego ruchu. Zawiera powodzie, powodzie wzmocnienia i innych powodzie sfałszowane pakietów UDP. Standardowa ochrony przed atakami DDoS zmniejsza tych potencjalnych ataków wielu gigabajtów przez pochłaniające i czyszczenia danych, wykorzystując skali globalnej sieci platformy Azure, automatycznie. 
- **Protokół ataków**: ataki renderować element docelowy niedostępny dzięki wykorzystaniu osłabienie warstwy 3 i 4 warstwy stosu protokołu. Zawiera on, SYN powódź ataków, ataki odbicia, a inne ataki protokołu. Standardowe ochrony przed atakami DDoS zmniejsza zagrożenie tego rodzaju ataki rozróżnianie między ruchu złośliwego i uzasadnionych interakcji z klienta i blokowania złośliwego ruchu. 
- **Ataki w warstwie aplikacji**: ataki target pakiety aplikacji sieci web do zakłócać przekazywania danych między hostami. Zawiera HTTP naruszeń protokołu, SQL iniekcji skryptów krzyżowych i inne ataki warstwy 7. Użyj platformy Azure [zapory aplikacji sieci web Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), przy użyciu DDoS ochrony Standard, aby zapewnić ochronę przed atakami te. 

Ochrona przed atakami DDoS — standardowe chroni zasobów w sieci wirtualnej, włączając publiczne adresy IP skojarzone z maszynami wirtualnymi, usługi równoważenia obciążenia i bramy aplikacji. W połączeniu z zapory aplikacji sieci web Application Gateway, standardowe ochrony przed atakami DDoS zapewniają pełną warstwy 3 do 7 ograniczenie możliwości warstwy.

## <a name="ddos-protection-standard-features"></a>Funkcje ochrony przed atakami DDoS — standardowe

![Funkcje DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

Ochrona przed atakami DDoS — standardowe funkcje: 

- **Integracja platformy Native:** natywnie zintegrowane Azure i zawiera konfiguracji za pomocą portalu Azure i programu PowerShell. Standardowa ochrony przed atakami DDoS rozumie, wszystkie zasoby i konfiguracji zasobu.
- **Monitorowanie ruchu zawsze włączone:** Twoich wzorców ruchu aplikacji są monitorowane 24 godziny na dobę, 7 dni w tygodniu, wyszukiwanie wskaźników atakami DDoS. Środki zaradcze odbywa się po przekroczeniu zasad ochrony.
- **Ochrona gotowe:** konfiguracji uproszczony natychmiast chroni wszystkie zasoby w sieci wirtualnej, jak standardowe ochrony przed atakami DDoS jest włączona. Brak definicji interwencji lub użytkownika jest wymagana. Ochrona przed atakami DDoS — standardowe natychmiast i automatycznie zmniejsza ataku, po jego wykryciu.
- **Dostrajanie adaptacyjną:** profilowania inteligentnego ruchu uzyskuje informacje o ruchu aplikacji wraz z upływem czasu i wybiera i aktualizuje profil, który jest najbardziej odpowiednie dla Twojej usługi. Profil można dostosować zgodnie z ruchem zmienia się wraz z upływem czasu.
- **Warstwy 3 do ochrony warstwy 7:** zapewnia ochrona przed atakami DDoS pełnego stosu, gdy jest używany z bramy aplikacji.
- **Ograniczenie dużej skali:** można zminimalizować przez 60 ataku różnych typów, globalne pojemność, ochronę przed największy znane atakami DDoS. 
- **Ataki metryki:** Summarized metryki na każdym ataki są dostępne za pośrednictwem Monitora Azure.
- **Alerty ataku:** alertów można skonfigurować na rozpoczęcie i zakończenie ataku i w czasie trwania ataku, za pomocą wbudowanych ataku metryki. Alerty zintegrować oprogramowanie operacyjne, takie jak Microsoft Azure Log Analytics, Splunk usługi Azure Storage, poczty E-mail i portalu Azure.
- **Koszt gwarancji:** transferu danych i aplikacji skalowalnych w poziomie zniżki w ramach ataków DDoS udokumentowane.

## <a name="ddos-protection-standard-mitigation"></a>Środki zaradcze standardowe ochrony przed atakami DDoS

Usługa ochrony przed atakami DDoS firmy Microsoft monitoruje ruch rzeczywiste użycie i stale porównuje ją z progów zdefiniowane w zasadzie DDoS. Po przekroczeniu progu ruchu DDoS ograniczenie jest inicjowany automatycznie. Gdy ruch zwraca poniżej progu, ograniczenie zostanie usunięty.

Podczas środki zaradcze ruch wysyłany do chronionego zasobu jest przekierowywane przez usługę ochrona przed atakami DDoS i kilka testy są wykonywane. Kontrole zwykle wykonanie następujących działań:

- Upewnij się, pakiety są zgodne z specyfikacji internetowych i nie są źle sformułowane.
- Interakcje z klienta w celu określenia, jeśli ruch jest potencjalnie fałszywych pakietów (np.: SYN uwierzytelniania lub plik Cookie SYN lub poprzez upuszczenie pakietów dla źródła ponownego przesłania go).
- Limit szybkości pakietów, jeśli żadnej innej metody wymuszania mogą być wykonywane.

Bloki ochrony przed atakami DDoS ataku ruchu i przekazuje pozostały ruch do jego przeznaczenia. W ciągu kilku minut wykrywanie ataków użytkownik jest powiadamiany, przy użyciu metryk Azure Monitor. Konfigurowanie rejestrowania na standardowe ochrony przed atakami DDoS telemetrii, może zapisać dzienniki dostępne opcje dla przyszłych analizy. Dane w monitorze Azure dla ochrony przed atakami DDoS — Standard obecnie są przechowywane przez 30 dni.

Klientów, aby symulować własnych ataków DDoS nie jest zalecane. Zamiast tego, klienci mogą użyć kanału pomocy technicznej aby żądania DDoS ataki symulacji wykonywane przez sieć platformy Azure. Inżynier skontaktuje się z rozmieszczania szczegóły takiego ataku (portów, protokołów, docelowych adresów IP) i Rozmieść czas, aby zaplanować uruchomienie testu.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o zarządzaniu przy użyciu standardowych ochrony przed atakami DDoS [programu Azure PowerShell](ddos-protection-manage-ps.md) lub [portalu Azure](ddos-protection-manage-portal.md).
