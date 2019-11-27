---
title: Co to jest Zapora aplikacji sieci Web platformy Azure na platformie Azure front-drzwi?
description: Dowiedz się, jak Zapora aplikacji sieci Web platformy Azure w usłudze Azure front-drzwi chroni aplikacje sieci Web przed złośliwymi atakami.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: b646035f6a952f679059abab86d94179f447f9ff
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406210"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Zapora aplikacji sieci Web platformy Azure na platformie Azure

Zapora aplikacji sieci Web platformy Azure (WAF) na platformie Azure front-drzwi zapewnia scentralizowaną ochronę aplikacji sieci Web, które są globalnie dostarczane przy użyciu usług frontonu platformy Azure. Jest ona zaprojektowana i obsługiwana tak, aby chronić Twoje usługi internetowe przed typowymi lukami w zabezpieczeniach oraz zapewnić wysoką dostępność usług dla użytkowników i pomóc w spełnieniu wymogów dotyczących zgodności.

WAF na wierzchu drzwi to globalne i scentralizowane rozwiązanie. Jest ona wdrażana w lokalizacjach brzegowych sieci platformy Azure na całym świecie i każde żądanie przychodzące dla aplikacji sieci Web obsługującej WAF, dostarczone przez tylne drzwi, jest sprawdzane na granicy sieci. Pozwala to WAF zapobieganiu złośliwych ataków blisko źródeł ataków przed wprowadzeniem ich do sieci wirtualnej i oferuje globalną ochronę na dużą skalę bez utraty wydajności. Zasady WAFymi można łatwo połączyć z dowolnym profilem drzwi do przodu w ramach subskrypcji. w ciągu kilku minut można wdrożyć nowe reguły, co pozwala szybko reagować na zmiany wzorców zagrożeń.

![Zapora aplikacji sieci Web platformy Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Zasady i reguły WAF

Można skonfigurować zasady WAF i skojarzyć te zasady z co najmniej jednym frontonem czołowym dla ochrony. Zasady WAF obejmują dwa typy reguł zabezpieczeń:

- Reguły niestandardowe, które zostały utworzone przez klienta.

- zarządzane zestawy reguł, które są zbiorem wstępnie skonfigurowanych zestawów reguł zarządzanego przez platformę Azure.

Gdy obie są obecne, reguły niestandardowe są przetwarzane przed przetworzeniem reguł w zarządzanym zestawie reguł. Reguła zawiera warunek dopasowania, priorytet i akcję. Obsługiwane typy akcji to: Zezwalaj, Blokuj, Rejestruj i Przekieruj. Można utworzyć w pełni dostosowane zasady spełniające określone wymagania dotyczące ochrony aplikacji przez połączenie reguł zarządzanych i niestandardowych.

Reguły w ramach zasad są przetwarzane w kolejności priorytetów, w której priorytet jest unikatową liczbą całkowitą, która definiuje kolejność przetwarzania reguł. Mniejsza wartość całkowita oznacza wyższy priorytet i są oceniane przed regułami o wyższej wartości całkowitej. Po dopasowaniu reguły odpowiednia akcja zdefiniowana w regule zostanie zastosowana do żądania. Gdy takie dopasowanie zostanie przetworzone, reguły o niższych priorytetach nie są przetwarzane więcej.

Aplikacja sieci Web dostarczana przez tylne drzwi może mieć tylko jedną zasadę WAFą skojarzoną z nim jednocześnie. Istnieje jednak możliwość skonfigurowania drzwi z przodu bez żadnych skojarzonych z nimi zasad WAF. Jeśli zasady WAF są obecne, zostaną zreplikowane do wszystkich naszych lokalizacji brzegowych, aby zapewnić spójność zasad zabezpieczeń na całym świecie.

## <a name="waf-modes"></a>Tryby WAF

Zasady WAF można skonfigurować tak, aby były uruchamiane w następujących dwóch trybach:

- **Tryb wykrywania:** Po uruchomieniu w trybie wykrywania usługa WAF nie przyjmuje żadnych innych akcji innych niż monitory i rejestruje żądanie i zgodną regułę WAF z dziennikami WAF. Można włączyć diagnostykę rejestrowania dla drzwi zewnętrznych (w przypadku korzystania z portalu można to osiągnąć, przechodząc do sekcji **Diagnostyka** w Azure Portal).

- **Tryb zapobiegania:** Gdy program jest skonfigurowany do uruchamiania w trybie zapobiegania, WAF wykonuje określoną akcję, jeśli żądanie jest zgodne z regułą i jeśli zostanie znalezione dopasowanie, nie są oceniane dalsze reguły o niższym priorytecie. Wszystkie dopasowane żądania są również rejestrowane w dziennikach WAF.

## <a name="waf-actions"></a>Akcje WAF

WAF klienci mogą wybrać uruchamianie z jednej z akcji, gdy żądanie spełnia warunki reguły:

- **Zezwalaj:**  Żądanie przechodzi przez WAF i jest przekazywane do zaplecza. Żadne dalsze reguły o niższym priorytecie nie mogą blokować tego żądania.
- **Blokuj:** Żądanie jest blokowane i WAF wysyła odpowiedź do klienta bez przesyłania dalej żądania do zaplecza.
- **Dziennik:**  Żądanie jest rejestrowane w dziennikach WAF, a WAF kontynuuje szacowanie reguł o niższym priorytecie.
- **Przekierowanie:** WAF przekierowuje żądanie do określonego identyfikatora URI. Określony identyfikator URI jest ustawieniem poziomu zasad. Po skonfigurowaniu wszystkie żądania zgodne z akcją **przekierowania** zostaną wysłane do tego identyfikatora URI.

## <a name="waf-rules"></a>Reguły WAF

Zasady WAFymi mogą składać się z dwóch typów reguł zabezpieczeń — niestandardowych reguł, utworzonych przez klienta i zarządzane zestawy reguł, zarządzane wstępnie przez platformę Azure.

### <a name="custom-authored-rules"></a>Niestandardowe reguły utworzone

Niestandardowe reguły WAF można skonfigurować w następujący sposób:

- **Lista dozwolonych adresów IP i lista zablokowanych:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci Web na podstawie listy adresów IP klientów lub zakresów adresów IP. Obsługiwane są zarówno typy adresów IPv4, jak i IPv6. Tę listę można skonfigurować do blokowania lub zezwalania na te żądania, w przypadku których źródłowy adres IP jest zgodny z adresem IP na liście.

- **Kontrola dostępu oparta na geograficznym:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci Web w oparciu o kod kraju skojarzony z adresem IP klienta.

- **Kontrola dostępu oparta na parametrach http:** Można skonfigurować niestandardowe reguły na podstawie ciągów, które pasują do parametrów żądania HTTP/HTTPS, takich jak ciągi zapytań, argumenty POST, identyfikator URI żądania, nagłówek żądania i treść żądania.

- **Zażądaj kontroli dostępu opartej na metodzie:** Reguły niestandardowe można skonfigurować na podstawie metody żądania HTTP żądania, takiej jak GET, PUT lub szef.

- **Ograniczenie rozmiaru:** Można skonfigurować niestandardowe reguły na podstawie długości określonych części żądania, takich jak ciąg zapytania, identyfikator URI lub treść żądania.

- **Reguły ograniczania szybkości:** Reguła kontroli częstotliwości polega na ograniczeniu nietypowego natężenia ruchu z dowolnego adresu IP klienta. W czasie trwania jednej minuty można skonfigurować próg liczby żądań sieci Web dozwolonych przez adres IP klienta. Jest to odrębne względem reguły typu "Zezwól/Blokuj" na podstawie listy adresów IP, która zezwala na wszystkie żądania z adresu IP klienta lub blokuje je. Ograniczanie szybkości może być połączone z dodatkowymi warunkami dopasowania, takimi jak parametry HTTP (S) pasujące do kontroli stopnia szczegółowości.

### <a name="azure-managed-rule-sets"></a>Zestawy reguł zarządzane przez platformę Azure

Zestawy reguł zarządzane przez platformę Azure zapewniają łatwy sposób wdrażania ochrony przed wspólnym zbiorem zagrożeń bezpieczeństwa. Ponieważ takie zestaw reguł jest zarządzany przez platformę Azure, reguły są aktualizowane w razie potrzeby w celu ochrony przed nowymi sygnaturami ataków. W publicznej wersji zapoznawczej zarządzana przez platformę Azure reguła domyślna obejmuje reguły dotyczące następujących kategorii zagrożeń:

- Skrypty między lokacjami
- Ataki Java
- Włączenie lokalnego pliku
- Ataki w języku PHP
- Zdalne wykonywanie poleceń
- Zdalne Dołączanie plików
- Utrwalanie sesji
- Ochrona przed atakami polegającymi na iniekcji SQL
- Atakujący protokołu

Numer wersji domyślnego zestawu reguł zostanie zwiększony, gdy nowe podpisy ataków zostaną dodane do zestawu reguł.
Domyślny zestaw reguł jest domyślnie włączony w trybie wykrywania w zasadach WAF. Można wyłączyć lub włączyć poszczególne reguły w ramach domyślnego zestawu reguł, aby spełniały wymagania aplikacji. Można również ustawić określone akcje (Zezwalaj/Blokuj/REDIRECT/LOG) dla każdej reguły. Domyślną akcją jest blokowanie. Ponadto reguły niestandardowe można skonfigurować w ramach tych samych zasad WAF, jeśli chcesz pominąć wszystkie wstępnie skonfigurowane reguły w domyślnym zestawie reguł.
Reguły niestandardowe są zawsze stosowane przed oceną reguł w domyślnym zestawie reguł. Jeśli żądanie jest zgodne z regułą niestandardową, stosowana jest odpowiednia akcja reguły, a żądanie jest blokowane lub przenoszone przez program do zaplecza bez wywołania dalszych reguł niestandardowych lub reguł w zestawie reguł domyślnych. Ponadto istnieje możliwość usunięcia domyślnego zestawu reguł z zasad WAFymi.


### <a name="bot-protection-rule-set-preview"></a>Zestaw reguł ochrony bot (wersja zapoznawcza)

Zarządzany zestaw reguł ochrony bot można włączyć dla WAF, aby wykonywał niestandardowe akcje na żądaniach ze znanych kategorii bot. Istnieją trzy kategorie bot: zła botów, dobry botów i nieznany botów. Podpisy bot są zarządzane i dynamicznie aktualizowane przez platformę WAF. Złośliwe adresy IP dla nieprawidłowych botów są źródłem ze źródła analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center. Dobre botów obejmują sprawdzone aparaty wyszukiwania. Nieznane kategorie obejmują dodatkowe grupy bot. Można ustawić akcje niestandardowe w celu blokowania, zezwalania, rejestrowania lub przekierowywania dla różnych typów botów.

![Zestaw reguł ochrony bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli ochrona bot jest włączona, przychodzące żądania zgodne z regułami bot są rejestrowane w dzienniku FrontdoorWebApplicationFirewallLog. Użytkownik może uzyskać dostęp do dzienników WAF z konta magazynu, centrum zdarzeń lub usługi log Analytics.

## <a name="configuration"></a>Konfigurowanie

Konfigurowanie i wdrażanie wszystkich typów reguł WAF jest w pełni obsługiwane przy użyciu Azure Portal, interfejsów API REST, szablonów Azure Resource Manager i Azure PowerShell.

## <a name="monitoring"></a>Monitorowanie

Monitorowanie pod kątem WAFów w drzwiach zewnętrznych jest zintegrowane z Azure Monitor do śledzenia alertów i łatwego monitorowania trendów ruchu.

## <a name="next-steps"></a>Następne kroki

- Informacje o [zaporze aplikacji sieci Web w usłudze Azure Application Gateway](../ag/ag-overview.md)
