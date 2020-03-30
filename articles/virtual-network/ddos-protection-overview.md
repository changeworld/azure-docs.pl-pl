---
title: Omówienie standardu ochrony przed atakami DDoS platformy Azure
description: Dowiedz się więcej o usłudze Azure DDoS Protection.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: f1dd33425a57689974fc98a28724adf7b130ab40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536347"
---
# <a name="azure-ddos-protection-standard-overview"></a>Omówienie standardu ochrony przed atakami DDoS platformy Azure

Ataki typu „rozproszona odmowa usługi” (Distributed Denial of Service, DDoS) należą do największych obaw związanych z dostępnością i zabezpieczeniami wśród klientów, którzy planują przeniesienie swoich aplikacji do chmury. Atak DDoS próbuje wyczerpać zasoby aplikacji, dzięki czemu aplikacja jest niedostępna dla legalnych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu.

Ochrona DDoS platformy Azure w połączeniu z najlepszymi rozwiązaniami w zakresie projektowania aplikacji zapewniają ochronę przed atakami DDoS. Ochrona przed atakami DDoS platformy Azure udostępnia następujące warstwy usług:

- **Podstawowe:** Automatycznie włączone jako część platformy Azure. Zawsze włączone monitorowanie ruchu i łagodzenie typowych ataków na poziomie sieci w czasie rzeczywistym zapewniają taką samą obronę, z której korzystają usługi online firmy Microsoft.Cała skala globalnej sieci platformy Azure może służyć do dystrybucji i ograniczania ruchu ataków w różnych regionach.Ochrona jest dostępna dla [publicznych adresów IP](virtual-network-public-ip-address.md)IPv4 i IPv6 Platformy Azure.
- **Standard:** Zapewnia dodatkowe możliwości ograniczania ryzyka w warstwie usługi Basic, które są dostrojone specjalnie do zasobów sieci wirtualnej platformy Azure. Standard ochrony przed atakami DDoS jest prosty w możliwości i nie wymaga żadnych zmian w aplikacji. Zasady ochrony są dostosowywane za pośrednictwem dedykowanych algorytmów monitorowania ruchu i uczenia maszynowego. Zasady są stosowane do publicznych adresów IP skojarzonych z zasobami wdrożonymi w sieciach wirtualnych, takich jak Azure Load Balancer, Azure Application Gateway i Azure Service Fabric, ale ta ochrona nie ma zastosowania do środowisk usługi App Service.Dane telemetryczne w czasie rzeczywistym są dostępne za pośrednictwem widoków usługi Azure Monitor podczas ataku i historii. Rozbudowa analizy łagodzenia ataków są dostępne za pomocą ustawień diagnostycznych. Ochronę warstwy aplikacji można dodać za pośrednictwem [Zapory aplikacji azure bramy aplikacji sieci Web](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub instalując zaporę innej firmy z usługi Azure Marketplace. Ochrona jest dostępna dla [publicznych adresów IP](virtual-network-public-ip-address.md)IPv4 i IPv6 Platformy Azure.

|Funkcja                                         |DDoS Protection Basic                 |Standard ochrony DDoS                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Aktywne monitorowanie ruchu & zawsze po wykryciu |Tak                                   |Tak                                           |
|Automatyczne łagodzenie ataków                    |Tak                                   |Tak                                           |
|Gwarancja dostępności                          |Region świadczenia usługi Azure                          |Aplikacja                                   |
|Zasady łagodzenia zmiany klimatu                             |Dostrojony do woluminu regionu ruchu platformy Azure |Dostrojony do natężenia ruchu w aplikacji          |
|Metryki & alerty                                |Nie                                    |Metryki ataków w czasie rzeczywistym & dzienniki diagnostyczne za pośrednictwem monitora platformy Azure                                 |
|Raporty dotyczące łagodzenia zmiany klimatu                              |Nie                                    |Raporty dotyczące łagodzenia ataków                |
|Dzienniki przepływu ograniczającego zagrożenie                            |Nie                                    |Strumień dziennika NRT dla integracji z SIEM           |
|Dostosowywanie zasad łagodzenia zmiany klimatu                 |Nie                                    |Zaangażuj ekspertów DDoS                           |
|Pomoc techniczna                                         |Najlepszy wysiłek                           |Dostęp do ekspertów DDoS podczas aktywnego ataku|
|Umowa SLA                                             |Region świadczenia usługi Azure                          |Gwarancja aplikacji & ochrona kosztów       |
|Cennik                                         |Bezpłatna                                  |Miesięczne użycie & oparte na                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy ataków DDoS, które ogranicza standard ochrony przed atakami DDoS

Standard ochrony przed atakami DDoS może ograniczyć następujące typy ataków:

- **Ataki wolumetryczne**: Celem ataku jest zalania warstwy sieci znaczną ilością pozornie uzasadnionego ruchu. Obejmuje powodzie UDP, powodzie wzmacniające i inne powodzie sfałszowane. Standard ochrony przed atakami DDoS zmniejsza te potencjalne ataki wielo gigabajtowe, absorbując je i szorując automatycznie za pomocą globalnej skali sieci platformy Azure.
- **Ataki protokołu:** Ataki te sprawiają, że cel jest niedostępny, wykorzystując słabość stosu protokołów warstwy 3 i warstwy 4. Obejmuje ataki powodziowe SYN, ataki refleksyjne i inne ataki protokołów. Standard ochrony przed atakami DDoS łagodzi te ataki, rozróżniając złośliwy i legalny ruch, w interakcja z klientem i blokując złośliwy ruch. 
- **Ataki warstwy zasobów (aplikacji):** Ataki te są ukierunkowane na pakiety aplikacji sieci web, aby zakłócić przesyłanie danych między hostami. Ataki obejmują naruszenia protokołu HTTP, iniekcję SQL, skrypty między witrynami i inne ataki warstwy 7. Użyj Zapory aplikacji sieci Web, takiej jak [zapora aplikacji sieci Web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)usługi Azure Application Gateway, a także standard ochrony przed atakami DDoS, aby zapewnić ochronę przed tymi atakami. Istnieją również oferty zapory aplikacji sieci Web innych firm dostępne w [portalu Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)

Standard ochrony przed atakami DDoS chroni zasoby w sieci wirtualnej, w tym publiczne adresy IP skojarzone z maszynami wirtualnymi, modułami równoważenia obciążenia i bramami aplikacji. W połączeniu z zaporą aplikacji sieci web bramy aplikacji lub zaporą aplikacji sieci web innej firmy wdrożoną w sieci wirtualnej z publicznym adresem IP standard ochrony przed atakami DDoS może zapewnić pełną funkcję ograniczania poziomu od warstwy 3 do warstwy 7.

## <a name="ddos-protection-standard-features"></a>Funkcje standardu ochrony przed atakami DDoS

![Funkcja DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Funkcje standardu ochrony przed atakami DDoS obejmują:

- **Natywna integracja platformy:** Natywnie zintegrowane z platformą Azure. Obejmuje konfigurację za pośrednictwem witryny Azure portal. Standard ochrony przed atakami DDoS rozumie twoje zasoby i konfigurację zasobów.
- **Ochrona pod klucz:** Uproszczona konfiguracja natychmiast chroni wszystkie zasoby w sieci wirtualnej, gdy tylko zostanie włączony standard ochrony przed atakami DDoS. Nie jest wymagana żadna interwencja ani definicja użytkownika. Standard ochrony przed atakami DDoS natychmiast i automatycznie zmniejsza atak po jego wykryciu.
- **Zawsze włączone monitorowanie ruchu:** Wzorce ruchu aplikacji są monitorowane 24 godziny na dobę, 7 dni w tygodniu, szukając wskaźników ataków DDoS. Łagodzenie jest wykonywane po przekroczeniu zasad ochrony.
- **Strojenie adaptacyjne:** Inteligentne profilowanie ruchu uczy się ruchu aplikacji w czasie i wybiera i aktualizuje profil, który jest najbardziej odpowiedni dla twojej usługi. Profil dostosowuje się wraz ze zmianami ruchu w czasie.
- **Ochrona wielowarstwowa:** Zapewnia pełną ochronę DDoS stosu, gdy jest używana z zaporą aplikacji sieci web.
- **Rozbudowana skala łagodzenia:** Ponad 60 różnych typów ataków można złagodzić, z globalnym pojemność, aby chronić przed największymi znanymi atakami DDoS.
- **Analiza ataków:** Otrzymuuj szczegółowe raporty w odstępach pięciominutowych podczas ataku i pełne podsumowanie po zakończeniu ataku. Przepływ ograniczania strumienia loguje się do systemu zarządzania informacjami i zdarzeniami w trybie offline (SIEM) w celu monitorowania w czasie zbliżonym do rzeczywistego podczas ataku.
- **Wskaźniki ataku:** Podsumowane metryki z każdego ataku są dostępne za pośrednictwem usługi Azure Monitor.
- **Ostrzeganie o ataku:** Alerty można skonfigurować na początku i na zatrzymaniu ataku oraz w czasie trwania ataku, przy użyciu wbudowanych metryk ataku. Alerty są integrowane z oprogramowaniem operacyjnym, takim jak dzienniki monitorów platformy Microsoft Azure, splunk, usługa Azure Storage, poczta e-mail i portal Azure.
- **Gwarancja kosztów:** Transfer danych i skalowanie aplikacji kredytów usługi dla udokumentowanych ataków DDoS.

## <a name="ddos-protection-standard-mitigation"></a>Łagodzenie standardu ochrony DDoS

Standard ochrony przed atakami DDoS monitoruje rzeczywiste wykorzystanie ruchu i stale porównuje je z progami zdefiniowanymi w zasadach DDoS. Po przekroczeniu progu ruchu, środki zaradcze DDoS jest inicjowany automatycznie. Gdy ruch zwraca poniżej progu, środki zaradcze są usuwane.

![Środki zaradcze](./media/ddos-protection-overview/mitigation.png)

Podczas ograniczania ryzyka ruch wysyłany do chronionego zasobu jest przekierowywane przez usługę ochrony DDoS i przeprowadzane są kilka kontroli, takich jak następujące kontrole:

- Upewnij się, że pakiety są zgodne ze specyfikacjami internetowymi i nie są zniekształcone.
- Interakcja z klientem w celu ustalenia, czy ruch jest potencjalnie sfałszowanym pakietem (np.
- Pakiety limitu szybkości, jeśli nie można wykonać żadnej innej metody wymuszania.

Usługa DDoS Protection blokuje ruch w ramach ataku, a pozostały ruch przekazuje do miejsca docelowego. W ciągu kilku minut od wykrycia ataku otrzymasz powiadomienie przy użyciu metryk usługi Azure Monitor. Konfigurując rejestrowanie danych telemetrycznych standardu ochrony przed atakami DDoS, można zapisywać dzienniki do dostępnych opcji dla przyszłej analizy. Dane metryki w usłudze Azure Monitor for DDoS Protection Standard są przechowywane przez 30 dni.

Firma Microsoft nawiązała współpracę z [usługą BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) w celu utworzenia interfejsu, w którym można generować ruch na publicznych adresach IP z obsługą ochrony przed atakami DDoS dla symulacji. Symulacja chmury programu BreakPoint umożliwia:

- Sprawdzanie poprawności, w jaki sposób standard ochrony przed atakami DDoS platformy Microsoft Azure chroni zasoby platformy Azure przed atakami DDoS
- Optymalizacja procesu reagowania na incydenty w przypadku ataku DDoS
- Zgodność z dokumentami DDoS
- Szkolenie zespołów bezpieczeństwa sieci

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie standardu ochrony przed atakami DDoS](manage-ddos-protection.md)
