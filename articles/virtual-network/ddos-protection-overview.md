---
title: Przegląd Standard platformy Azure przed atakami DDoS Protection | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure DDoS Protection.
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
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: f25da8c1eedc31209a67ae05aef9dded45b706e0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962406"
---
# <a name="azure-ddos-protection-standard-overview"></a>Omówienie usługi Azure DDoS Protection standardowe

Rozproszone atakom typu odmowa usługi (DDoS) przedstawiono niektóre z największych problemów dostępność i bezpieczeństwo połączonego z klientów, którzy są przenoszone do ich aplikacji w chmurze. Próby ataków DDoS na wyczerpanie zasobów aplikacji, że aplikacja staje się niedostępny dla uprawnionych użytkowników. Ataki DDoS mogą być przeznaczone dla dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Usługa Azure DDoS protection, w połączeniu z najlepszych rozwiązań projektowania aplikacji zapewniają ochronę przed atakami DDoS. Usługa Azure DDoS protection zapewnia następujących warstwach usługi:

- **Podstawowe**: automatycznie włączone w ramach platformy Azure. Zawsze włączone monitorowanie ruchu, a także w czasie rzeczywistym ograniczenie typowych ataków na poziomie sieci, należy podać tych samych mechanizmów obronnych wartościowa usług online firmy Microsoft. Cały skali globalnej sieci platformy Azure może służyć do dystrybucji, a także zmniejszyć ruch ataku w regionach. Ochrony praw dla protokołów IPv4 i IPv6 Azure [publiczne adresy IP](virtual-network-public-ip-address.md).
- **Standardowa**: udostępnia dodatkowe ograniczenia możliwości w porównaniu z warstwa podstawowa usług, które są specjalnie przeznaczone specjalnie do zasobów usługi Azure Virtual Network. Standard ochrony przed atakami DDoS jest proste włączyć i nie wymaga żadnych zmian w aplikacji. Za pomocą dedykowanego monitorowania i ruchu algorytmów uczenia maszynowego dostosowanych zasad ochrony. Zasady są stosowane do publicznych adresów IP skojarzonych z zasobami wdrożonymi w sieciach wirtualnych, takich jak wystąpienia usługi Azure Load Balancer, Azure Application Gateway i Azure Service Fabric. W czasie rzeczywistym danych telemetrycznych jest dostępna za pośrednictwem usługi Azure Monitor widoków, podczas ataku i historii. Analizy ograniczania ryzyka ataków zaawansowane są dostępne za pomocą ustawień diagnostycznych. Ochrona warstwy aplikacji można dodać za pomocą [zapory aplikacji sieci Web usługi Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub dzięki zainstalowaniu 3rd zapory innych firm w witrynie Azure Marketplace. Ochrony praw IPv4 Azure [publiczne adresy IP](virtual-network-public-ip-address.md).

![Platforma Azure a Podstawowa ochrona przed atakami DDoS Standardowa (Standard)](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy ataków DDoS, które zmniejsza Standard ochrony przed atakami DDoS

Standard ochrony przed atakami DDoS pozwala ograniczyć następujące rodzaje ataków:

- **Ataki pomiarowej**: celem ataku jest zalać warstwy sieciowej znaczną ilość ruchu pozornie uzasadnione. Zawiera powodzie, powodzie wzmocnienia i innych powodzie sfałszowane pakietów UDP. Standard ochrony przed atakami DDoS zmniejsza tych potencjalnych ataków wielu gigabajtów, przejmowania i czyszczenia danych, w skali globalnej sieci platformy Azure, automatycznie.
- **Protokół ataków**: te ataki obiekt docelowy renderowania niedostępne przez wykorzystanie słabości w warstwie 3 i stosu protokołu warstwy 4. Obejmuje, SYN ataków powódź odbicie ataków i inne ataki protokołu. Standard ochrony przed atakami DDoS zmniejsza te ataki rozróżnianiu ruchu złośliwego i uzasadnione, interakcji z klientem i blokowania złośliwego ruchu. 
- **Ataki zasobu (aplikacji) w warstwie**: te ataki docelowych, pakiety aplikacji sieci web, w celu zakłócania przesyłania danych między hostami. Przed atakami obejmują HTTP naruszeniami protokołu, SQL iniekcji, skryptów między witrynami i inne ataki warstwy 7. Skorzystaj z Kalkulatora [zapory aplikacji sieci web Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), przed atakami DDoS Protection Standard, aby zapewnić ochronę przed atakami, te. Ponadto są dostępne w ofert zapory aplikacji sieci web innych firm [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Standard ochrony przed atakami DDoS chroni zasoby w sieci wirtualnej, w tym publiczne adresy IP skojarzone z maszyn wirtualnych, usługi równoważenia obciążenia i bramy aplikacji. W połączeniu z zapory aplikacji sieci web Application Gateway, Standard ochrony przed atakami DDoS może zapewnić pełne warstwy 3 do warstwy 7 możliwości ograniczania ryzyka.

## <a name="ddos-protection-standard-features"></a>Funkcje warstwy standardowa ochrony przed atakami DDoS

![Funkcje przed atakami DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Usługa DDoS Protection standardowe funkcje obejmują:

- **Integracja platformy natywnej:** natywnie zintegrowane na platformie Azure. Obejmuje konfigurację za pomocą witryny Azure portal. Standard ochrony przed atakami DDoS uwzględnia zasoby i konfiguracji zasobów.
- **Kompleksowa ochrona:** uproszczony Konfiguracja chroni wszystkie zasoby w sieci wirtualnej tak szybko, jak standardowy ochrony przed atakami DDoS jest włączona. Brak definicji interwencji lub użytkownika jest wymagana. Standard ochrony przed atakami DDoS natychmiast i automatycznie minimalizuje skuteczność ataku, po jego wykryciu.
- **Zawsze włączone monitorowanie ruchu:** Twoich wzorców ruchu aplikacji mają być monitorowane 24 godzin dziennie, 7 dni w tygodniu, wyszukiwanie wskaźników ataki DDoS. Środki zaradcze odbywa się po przekroczeniu zasady ochrony.
- **Dostrajanie adaptacyjne:** ruchu inteligentne profilowanie uzyskuje informacje o ruchu danej aplikacji wraz z upływem czasu i wybiera i aktualizuje profil, który jest najbardziej odpowiedni dla Twojej usługi. Profil, który dostosowuje się zgodnie z ruchem zmienia się wraz z upływem czasu.
- **Ochrona wielowarstwowych:** zapewnia ochronę przed atakami DDoS pełnego stosu, gdy jest używane z zapory aplikacji sieci web.
- **Skaluj rozbudowane środki zaradcze:** 60 za pośrednictwem ataku różnych typów można zminimalizować globalnego pojemność, aby zapewnić ochronę przed największych znane ataki DDoS.
- **Ataki analytics:** uzyskiwanie szczegółowych raportów w przyrostach co 5 minut podczas ataku i zapoznać się ze wszystkimi po zakończeniu ataku. Stream środki zaradcze przepływ rejestruje zarządzania informacjami i zdarzeniami zabezpieczeń w trybie offline (SIEM) system niemalże w czasie rzeczywistym podczas ataku.
- **Ataki metryki:** Summarized metryki z każdego ataku są dostępne za pośrednictwem usługi Azure Monitor.
- **Alerty ataku:** alerty można skonfigurować na początku i Zatrzymaj atak i czasie trwania ataku przy użyciu ataku wbudowanych metryk. Alerty zintegrować operacyjne oprogramowania, takie jak Microsoft Azure Log Analytics, Splunk, usługi Azure Storage, poczty E-mail i witryny Azure portal.
- **Gwarancja kosztów:** transferu danych i udokumentowane ataków DDoS, środki na usługi skalowania w poziomie aplikacji.

## <a name="ddos-protection-standard-mitigation"></a>Środki zaradcze Standard ochrony przed atakami DDoS

Standard ochrony przed atakami DDoS monitoruje wykorzystanie rzeczywisty ruch i stale porównuje ją z progów zdefiniowane w zasadach przed atakami DDoS. Po przekroczeniu progu ruchu ataki DDOS jest inicjowany automatycznie. Gdy ruch zwraca niższy od wartości progowej, środki zaradcze zostaną usunięte.

![Środki zaradcze](./media/ddos-protection-overview/mitigation.png)

Podczas środki zaradcze ruch wysyłany do chronionego zasobu jest przekierowywane przez usługi DDoS protection i kilka są sprawdzane, takie jak następujące testy:

- Upewnij się, pakiety są zgodne ze specyfikacją internet i nie są źle sformułowane.
- Interakcji z klientem w celu ustalenia, czy ruch jest potencjalnie fałszywych pakietów (np.: SYN uwierzytelniania lub plik Cookie SYN i upuszczając pakietów dla źródła ponowne przesłanie go).
- Limit szybkości pakietów, jeśli żadnej innej metody wymuszania mogą być wykonywane.

Ochrona przed atakami DDoS blokuje ruch ataku i przekazuje pozostały ruch do jego przeznaczenia. W ciągu kilku minut wykrywanie ataków otrzymasz powiadomienie za pomocą metryk usługi Azure Monitor. Konfigurowanie rejestrowania w telemetrii Standard ochrony przed atakami DDoS, może zapisywać dzienniki dostępne opcje do przyszłej analizy. Dane metryk w usłudze Azure Monitor w przypadku ochrony przed atakami DDoS w warstwie standardowa są przechowywane przez 30 dni.

Firma Microsoft nawiązała współpracę z [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, gdzie można wygenerować ruchu względem włączona ochrona przed atakami DDoS publicznych adresów IP dla symulacji. Symulacja chmury punktu przerwania pozwala na:

- Sprawdzić, jak Microsoft Azure przed atakami DDoS Protection Standard chroni Twoje zasoby platformy Azure przed atakami DDoS
- Optymalizowanie procesu reagowania na zdarzenia w ramach ataków DDoS
- Dokumentowanie zgodności przed atakami DDoS
- Szkolenie zespołom zabezpieczeń sieci

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie standardu ochrony przed atakami DDoS](manage-ddos-protection.md)
