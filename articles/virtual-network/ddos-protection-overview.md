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
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: 705f69f9143e3d2b27a3099f340218aaa12931f8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="azure-ddos-protection-standard-overview"></a>Omówienie usługi Azure Standard ochrony przed atakami DDoS

Rozproszone "odmowa usługi" (DDoS) są niektóre największej dostępności i zabezpieczeń dotyczy ukierunkowane klientów, które są przenoszone swoje aplikacje w chmurze. Próbuje takiego ataku na wyczerpanie zasobów aplikacji, co aplikacja niedostępne do autoryzowanych użytkowników. Może być celem ataków DDoS dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Ochrona przed atakami DDoS Azure, w połączeniu z najlepszymi rozwiązaniami projektowania aplikacji zapewniają ochronę przed atakami DDoS. Ochrona przed atakami DDos Azure zapewnia następujące warstwy usług:

- **Podstawowe**: włączane automatycznie w ramach platformy Azure, bez dodatkowych opłat. Monitorowanie ruchu zawsze włączone i w czasie rzeczywistym zmniejszenie typowych ataków na poziomie sieci, mają tego samego zabezpieczenia, wykorzystywane przez usługi online firmy Microsoft. Cały skali globalnej sieci platformy Azure może służyć do rozpowszechniania i ograniczyć ruch ataku w regionach. Podano ochrony dla protokołów IPv4 i IPv6 Azure [publiczne adresy IP](virtual-network-public-ip-address.md).
- **Standardowe**: udostępnia dodatkowe ograniczenie możliwości w porównaniu z warstwy podstawowej usługi, które są dopasowane do zasobów sieci wirtualnej platformy Azure. Standardowa ochrony przed atakami DDoS jest proste włączyć i nie wymaga żadnych zmian w aplikacji. Zasady ochrony są dopasowane za pośrednictwem dedykowanych monitorowania i ruchu algorytmów uczenia maszynowego. Zasady są stosowane na publiczne adresy IP skojarzone z zasobami wdrożonymi w sieci wirtualnej, takie jak wystąpienia usługi równoważenia obciążenia Azure, Azure Application Gateway i sieci szkieletowej usług Azure. Dane telemetryczne w czasie rzeczywistym jest dostępna za pośrednictwem widoków Azure Monitor, podczas ataku i historii. Ochrona warstwy aplikacji można dodać za pomocą [zapory aplikacji sieci Web z bramy aplikacji Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ochrony praw IPv4 Azure [publiczne adresy IP](virtual-network-public-ip-address.md).

![Standard ochrony przed atakami DDoS Azure](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy ataków DDoS zmniejsza standardowe ochrony przed atakami DDoS

Ochrona przed atakami DDoS — standardowe ograniczania następujące rodzaje ataków:

- **Ataki pomiarowej**: celem ataku jest wypełniania warstwy sieci o rozległe pozornie potrzebnego ruchu. Zawiera powodzie, powodzie wzmocnienia i innych powodzie sfałszowane pakietów UDP. Ochrona przed atakami DDoS — standardowe zmniejsza tych potencjalnych ataków wielu gigabajtów przez pochłaniające i czyszczenia danych, o skali globalnej sieci platformy Azure, automatycznie.
- **Protokół ataków**: ataki renderowania docelowy niedostępny, wykorzystując osłabienie warstwy 3 i 4 warstwy stosu protokołu. Zawiera on, SYN powódź ataków, ataki odbicia, a inne ataki protokołu. Standardowa ochrony przed atakami DDoS zmniejsza tego rodzaju ataki rozróżnianie między ruchu złośliwego i uzasadnionych interakcji z klienta i blokowania złośliwego ruchu. 
- **Ataki zasobów (aplikacja) w warstwie**: ataki target pakiety aplikacji sieci web, aby przerwać przekazywania danych między hostami. Ataków należą HTTP naruszeń protokołu, SQL iniekcji skryptów krzyżowych i inne ataki warstwy 7. Użyj platformy Azure [zapory aplikacji sieci web Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), przy użyciu DDoS ochrony Standard, aby zapewnić ochronę przed atakami te. Ponadto są dostępne w ofert zapory aplikacji sieci web innych firm [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Ochrona przed atakami DDoS — standardowe chroni zasobów w sieci wirtualnej, włączając publiczne adresy IP skojarzone z maszynami wirtualnymi, usługi równoważenia obciążenia i bramy aplikacji. W połączeniu z zapory aplikacji sieci web Application Gateway, standardowe ochrony przed atakami DDoS zapewniają pełną warstwy 3 do 7 ograniczenie możliwości warstwy.

## <a name="ddos-protection-standard-features"></a>Funkcje ochrony przed atakami DDoS — standardowe

![Funkcje DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Ochrona przed atakami DDoS — standardowe funkcje:

- **Integracja platformy Native:** natywnie zintegrowane na platformie Azure. Obejmuje konfiguracji za pomocą portalu Azure. Standardowa ochrony przed atakami DDoS rozumie, wszystkie zasoby i konfiguracji zasobu.
- **Ochrona gotowe:** konfiguracji uproszczony natychmiast chroni wszystkie zasoby w sieci wirtualnej, jak standardowe ochrony przed atakami DDoS jest włączona. Brak definicji interwencji lub użytkownika jest wymagana. Ochrona przed atakami DDoS — standardowe natychmiast i automatycznie zmniejsza ataku, po jego wykryciu.
- **Monitorowanie ruchu zawsze włączone:** Twoich wzorców ruchu aplikacji są monitorowane 24 godziny na dobę, 7 dni w tygodniu, wyszukiwanie wskaźników atakami DDoS. Środki zaradcze odbywa się po przekroczeniu zasad ochrony.
- **Dostrajanie adaptacyjną:** profilowania inteligentnego ruchu uzyskuje informacje o ruchu aplikacji wraz z upływem czasu i wybiera i aktualizuje profil, który jest najbardziej odpowiednie dla Twojej usługi. Profil można dostosować zgodnie z ruchem zmienia się wraz z upływem czasu.
- **Warstwy 3 do ochrony warstwy 7:** zapewnia ochrona przed atakami DDoS pełnego stosu, gdy jest używany z zapory aplikacji sieci web.
- **Ograniczenie dużej skali:** można zminimalizować przez 60 ataku różnych typów, globalne pojemność, ochronę przed największy znane atakami DDoS.
- **Ataki metryki:** Summarized metryki na każdym ataki są dostępne za pośrednictwem Monitora Azure.
- **Alerty ataku:** alertów można skonfigurować na rozpoczęcie i zakończenie ataku i w czasie trwania ataku, za pomocą wbudowanych ataku metryki. Alerty zintegrować oprogramowanie operacyjne, takie jak Microsoft Azure Log Analytics, Splunk usługi Azure Storage, poczty E-mail i portalu Azure.
- **Koszt gwarancji:** transferu danych i aplikacji skalowalnych w poziomie zniżki w ramach ataków DDoS udokumentowane.

## <a name="ddos-protection-standard-mitigation"></a>Środki zaradcze standardowe ochrony przed atakami DDoS

Ochrona przed atakami DDoS — standardowe monitoruje ruch rzeczywiste użycie i stale porównuje ją z progów zdefiniowane w zasadzie DDoS. Po przekroczeniu progu ruchu DDoS ograniczenie jest inicjowany automatycznie. Gdy ruch zwraca poniżej progu, ograniczenie zostanie usunięty.

![Środki zaradcze](./media/ddos-protection-overview/mitigation.png)

Podczas środki zaradcze ruch wysyłany do chronionego zasobu jest przekierowywane przez usługę ochrony przed atakami DDoS i kilka są sprawdzane, takie jak następujące testy:

- Upewnij się, pakiety są zgodne z specyfikacji internetowych i nie są źle sformułowane.
- Interakcje z klienta w celu określenia, jeśli ruch jest potencjalnie fałszywych pakietów (np.: SYN uwierzytelniania lub plik Cookie SYN lub poprzez upuszczenie pakietów dla źródła ponownego przesłania go).
- Limit szybkości pakietów, jeśli żadnej innej metody wymuszania mogą być wykonywane.

Ochrona przed atakami DDoS blokuje ruch ataku i przekazuje ruch pozostałych do jego przeznaczenia. W ciągu kilku minut wykrywanie ataków użytkownik jest powiadamiany, przy użyciu metryk Azure Monitor. Konfigurowanie rejestrowania na standardowe ochrony przed atakami DDoS telemetrii, może zapisać dzienniki dostępne opcje dla przyszłych analizy. Dane w monitorze Azure do ochrony przed atakami DDoS — standardowe są przechowywane przez 30 dni.

Microsoft współpracuje z [chmury firmy BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, których można wygenerować ruchu względem włączona ochrona przed atakami DDoS symulacje publicznych adresów IP. Symulacji punktu przerwania chmury umożliwia:

- Sprawdź poprawność jak Microsoft Azure przed atakami DDoS ochrony Standard chroni zasobów platformy Azure przed atakami DDoS
- Optymalizacja procesu odpowiedzi na zdarzenia w obszarze ataków DDoS
- Zgodność DDoS dokumentu
- Uczenia zespołów zabezpieczeń sieci

## <a name="next-steps"></a>Kolejne kroki

- [Skonfiguruj Standard ochrony przed atakami DDoS](manage-ddos-protection.md)