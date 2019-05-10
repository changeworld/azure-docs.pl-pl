---
title: Przegląd Standard platformy Azure przed atakami DDoS Protection | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure DDoS Protection.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 41e9d88df49d153089e6dc7a12c5873ccc167279
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209458"
---
# <a name="azure-ddos-protection-standard-overview"></a>Omówienie usługi Azure DDoS Protection standardowe

Ataki typu „rozproszona odmowa usługi” (Distributed Denial of Service, DDoS) należą do największych obaw związanych z dostępnością i zabezpieczeniami wśród klientów, którzy planują przeniesienie swoich aplikacji do chmury. Atak DDoS polega na próbie wyczerpania zasobów aplikacji, przez co aplikacja staje się niedostępna dla zwykłych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu.

Usługa Azure DDoS protection, w połączeniu z najlepszych rozwiązań projektowania aplikacji zapewniają ochronę przed atakami DDoS. Usługa Azure DDoS protection zapewnia następujących warstwach usługi:

- **Podstawowe**: Automatycznie włączone w ramach platformy Azure. Zawsze włączone monitorowanie ruchu, a także w czasie rzeczywistym ograniczenie typowych ataków na poziomie sieci, należy podać tych samych mechanizmów obronnych wartościowa usług online firmy Microsoft. Cały skali globalnej sieci platformy Azure może służyć do dystrybucji, a także zmniejszyć ruch ataku w regionach. Ochrony praw dla protokołów IPv4 i IPv6 Azure [publiczne adresy IP](virtual-network-public-ip-address.md).
- **Standardowa**: Udostępnia dodatkowe ograniczenia możliwości w porównaniu z warstwa podstawowa usług, które są specjalnie przeznaczone specjalnie do zasobów usługi Azure Virtual Network. Standard ochrony przed atakami DDoS jest proste włączyć i nie wymaga żadnych zmian w aplikacji. Za pomocą dedykowanego monitorowania i ruchu algorytmów uczenia maszynowego dostosowanych zasad ochrony. Zasady są stosowane do publicznych adresów IP skojarzonych z zasobami wdrożonymi w sieciach wirtualnych, takich jak usługa Azure Load Balancer, Azure Application Gateway i wystąpień usługi Azure Service Fabric, ale ta ochrona nie ma zastosowania do środowiska usługi App Service. W czasie rzeczywistym danych telemetrycznych jest dostępna za pośrednictwem usługi Azure Monitor widoków, podczas ataku i historii. Analizy ograniczania ryzyka ataków zaawansowane są dostępne za pomocą ustawień diagnostycznych. Ochrona warstwy aplikacji można dodać za pomocą [zapory aplikacji sieci Web usługi Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub dzięki zainstalowaniu 3rd zapory innych firm w witrynie Azure Marketplace. Ochrony praw dla protokołów IPv4 i IPv6 Azure [publiczne adresy IP](virtual-network-public-ip-address.md).

![Platforma Azure a Podstawowa ochrona przed atakami DDoS Standardowa (Standard)](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy ataków DDoS, które zmniejsza Standard ochrony przed atakami DDoS

Standard ochrony przed atakami DDoS pozwala ograniczyć następujące rodzaje ataków:

- **Ataki pomiarowej**: Celem ataku jest zalać warstwy sieciowej znaczną ilość ruchu pozornie uzasadnione. Zawiera powodzie, powodzie wzmocnienia i innych powodzie sfałszowane pakietów UDP. Standard ochrony przed atakami DDoS zmniejsza tych potencjalnych ataków wielu gigabajtów, przejmowania i czyszczenia danych, w skali globalnej sieci platformy Azure, automatycznie.
- **Protokół ataków**: Te ataki obiekt docelowy renderowania niedostępne przez wykorzystanie słabości w warstwie 3 i stosu protokołu 4 warstwy. Obejmuje, SYN ataków powódź odbicie ataków i inne ataki protokołu. Standard ochrony przed atakami DDoS zmniejsza te ataki rozróżnianiu ruchu złośliwego i uzasadnione, interakcji z klientem i blokowania złośliwego ruchu. 
- **Ataki zasobu (aplikacji) w warstwie**: Te ataki docelowych, pakiety aplikacji sieci web, w celu zakłócania przesyłania danych między hostami. Przed atakami obejmują HTTP naruszeniami protokołu, SQL iniekcji, skryptów między witrynami i inne ataki warstwy 7. Skorzystaj z Kalkulatora [zapory aplikacji sieci web Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), przed atakami DDoS Protection Standard, aby zapewnić ochronę przed atakami, te. Ponadto są dostępne w ofert zapory aplikacji sieci web innych firm [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Standard ochrony przed atakami DDoS chroni zasoby w sieci wirtualnej, w tym publiczne adresy IP skojarzone z maszyn wirtualnych, usługi równoważenia obciążenia i bramy aplikacji. W połączeniu z zapory aplikacji sieci web Application Gateway, Standard ochrony przed atakami DDoS może zapewnić pełne warstwy 3 do warstwy 7 możliwości ograniczania ryzyka.

## <a name="ddos-protection-standard-features"></a>Funkcje warstwy standardowa ochrony przed atakami DDoS

![Funkcje przed atakami DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Usługa DDoS Protection standardowe funkcje obejmują:

- **Integracja platformy natywnej:** Natywnie zintegrowana z platformą Azure. Obejmuje konfigurację za pomocą witryny Azure portal. Standard ochrony przed atakami DDoS uwzględnia zasoby i konfiguracji zasobów.
- **Kompleksowa ochrona:** Uproszczona konfiguracja chroni wszystkie zasoby w sieci wirtualnej tak szybko, jak standardowy ochrony przed atakami DDoS jest włączona. Brak definicji interwencji lub użytkownika jest wymagana. Standard ochrony przed atakami DDoS natychmiast i automatycznie minimalizuje skuteczność ataku, po jego wykryciu.
- **Zawsze włączone monitorowanie ruchu:** Wzorców ruchu dla twojej aplikacji są monitorowane 24 godzin dziennie, 7 dni w tygodniu, wyszukiwanie wskaźników ataki DDoS. Środki zaradcze odbywa się po przekroczeniu zasady ochrony.
- **Dostrajanie adaptacyjne:** Profilowanie ruchu inteligentne uzyskuje informacje o ruchu danej aplikacji wraz z upływem czasu i wybiera i aktualizuje profil, który jest najbardziej odpowiedni dla Twojej usługi. Profil, który dostosowuje się zgodnie z ruchem zmienia się wraz z upływem czasu.
- **Ochrona wielowarstwowych:** Zapewnia ochronę przed atakami DDoS pełnego stosu, gdy jest używane z zapory aplikacji sieci web.
- **Skaluj rozbudowane środki zaradcze:** Ponad 60 ataku różnych typów można zminimalizować globalnego pojemność, aby zapewnić ochronę przed największych znane ataki DDoS.
- **Analiza ataku:** Uzyskuj szczegółowe raporty co pięć minut w trakcie ataku oraz pełne podsumowanie po jego zakończeniu. Stream środki zaradcze przepływ rejestruje zarządzania informacjami i zdarzeniami zabezpieczeń w trybie offline (SIEM) system niemalże w czasie rzeczywistym podczas ataku.
- **Metryki ataku:** Skrócona metryki z każdego ataku są dostępne za pośrednictwem usługi Azure Monitor.
- **Alertowanie ataku:** Alerty można skonfigurować na początku i Zatrzymaj atak i czasie trwania ataku przy użyciu ataku wbudowanych metryk. Alerty zintegrować operacyjne oprogramowania, takie jak dzienniki, Splunk, usługi Azure Storage, poczty E-mail i witryny Azure portal Microsoft Azure Monitor.
- **Gwarancja kosztów:** Transfer danych i aplikacji skalowalnego w poziomie zniżki w ramach udokumentowane ataki DDoS.

## <a name="ddos-protection-standard-mitigation"></a>Środki zaradcze Standard ochrony przed atakami DDoS

Standard ochrony przed atakami DDoS monitoruje wykorzystanie rzeczywisty ruch i stale porównuje ją z progów zdefiniowane w zasadach przed atakami DDoS. Po przekroczeniu progu ruchu ataki DDOS jest inicjowany automatycznie. Gdy ruch zwraca niższy od wartości progowej, środki zaradcze zostaną usunięte.

![Ograniczanie ryzyka](./media/ddos-protection-overview/mitigation.png)

Podczas środki zaradcze ruch wysyłany do chronionego zasobu jest przekierowywane przez usługi DDoS protection i kilka są sprawdzane, takie jak następujące testy:

- Upewnij się, pakiety są zgodne ze specyfikacją internet i nie są źle sformułowane.
- Korzystać z klienta w celu określenia, jeśli ruch jest potencjalnie fałszywych pakietów (np.: SYN uwierzytelniania lub plik Cookie SYN i upuszczając pakietów dla źródła ponowne przesłanie go).
- Limit szybkości pakietów, jeśli żadnej innej metody wymuszania mogą być wykonywane.

Ochrona przed atakami DDoS blokuje ruch ataku i przekazuje pozostały ruch do jego przeznaczenia. W ciągu kilku minut wykrywanie ataków otrzymasz powiadomienie za pomocą metryk usługi Azure Monitor. Konfigurowanie rejestrowania w telemetrii Standard ochrony przed atakami DDoS, może zapisywać dzienniki dostępne opcje do przyszłej analizy. Dane metryk w usłudze Azure Monitor w przypadku ochrony przed atakami DDoS w warstwie standardowa są przechowywane przez 30 dni.

Firma Microsoft nawiązała współpracę z [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, gdzie można wygenerować ruchu względem włączona ochrona przed atakami DDoS publicznych adresów IP dla symulacji. Symulacja chmury punktu przerwania pozwala na:

- Sprawdzić, jak Microsoft Azure przed atakami DDoS Protection Standard chroni Twoje zasoby platformy Azure przed atakami DDoS
- Optymalizowanie procesu reagowania na zdarzenia w ramach ataków DDoS
- Dokumentowanie zgodności przed atakami DDoS
- Szkolenie zespołom zabezpieczeń sieci

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie standardu ochrony przed atakami DDoS](manage-ddos-protection.md)
