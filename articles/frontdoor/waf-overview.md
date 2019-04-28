---
title: Zapora aplikacji sieci web platformy Azure dla platformy Azure drzwiami frontowymi co to jest? (Wersja zapoznawcza)
description: Dowiedz się jak usługa Azure zapory aplikacji sieci web dla usługi Azure drzwiami frontowymi chroni aplikacje sieci web przed złośliwymi atakami.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 17cf6629aca6c73bc96e4cf0c172a2e87a7aafb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459360"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door-preview"></a>Zapora aplikacji sieci web platformy Azure dla platformy Azure drzwiami frontowymi co to jest? (Wersja zapoznawcza)

Zapora aplikacji sieci web platformy Azure (WAF) (wersja zapoznawcza) zapewnia scentralizowaną ochronę aplikacji sieci web, które są globalnie dostarczane za pomocą usługi Azure drzwiami frontowymi. Jest on zaprojektowany i wykonywane w celu ochrony usługi sieci web przed typowe luki w zabezpieczeniach i luk w zabezpieczeniach, a także zapewnić usługi o wysokiej dostępności dla użytkowników oprócz ułatwienia spełnienia wymagań dotyczących zgodności.

> [!IMPORTANT]
> Zapora aplikacji sieci web platformy Azure (WAF) Azure drzwiami frontowymi jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez Umowa dotycząca poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aplikacje internetowe coraz częściej cele złośliwe ataki, takie jak odmowa usługi powodzie, atakami polegającymi na iniekcji SQL i ataki z użyciem skryptów między witrynami. Te złośliwe ataki, może spowodować, że usługa przestoju i utraty danych, stanowią znaczne zagrożenie dla właścicieli aplikacji sieci web.

Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na poziomie wielu warstw topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Ponadto rozwiązanie zapory aplikacji sieci Web może reagować na zagrożenia bezpieczeństwa szybciej przez poprawki znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast zapobiegających poszczególnych aplikacjach sieci web.

Zapora aplikacji sieci Web dla drzwiami frontowymi jest rozwiązaniem globalnych i scentralizowane. Jest ono wdrożone sieci platformy Azure lokalizacjach na całym świecie, a każde żądanie przychodzące dla ZAPORĄ aplikacji sieci web, dostarczanych przez drzwiami frontowymi jest kontrolowane na granicy sieci. Zezwala na zapory aplikacji sieci Web zapobiec złośliwe ataki blisko źródła ataku, zanim wejdzie do sieci wirtualnej i oferuje globalny ochrony na dużą skalę bez obniżania oczekiwanego poziomu wydajności. Zasady zapory aplikacji sieci Web można łatwo łączyć do dowolnego profilu drzwiami frontowymi w ramach subskrypcji, a nowe zasady można wdrożyć w ciągu kilku minut, co pozwala szybko reagować na zmieniające się wzorców zagrożeń.

![Zapora aplikacji sieci web platformy Azure](./media/waf-overview/web-application-firewall-overview.png)

Można również włączyć Azure zapory aplikacji sieci Web z usługą Application Gateway. Aby uzyskać więcej informacji, zobacz [zapory aplikacji sieci Web](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>Zasady zapory aplikacji sieci Web i reguł

Można skonfigurować zasady zapory aplikacji sieci Web i skojarzyć te zasady do co najmniej jeden drzwiami frontowymi Frontony do ochrony. Zasady zapory aplikacji sieci Web składa się z dwóch typów reguł zabezpieczeń:
- Reguły niestandardowe, które były edytowane przez klienta.
- zestawy reguł zarządzanego, znajdujących się na kolekcję usługi Azure managed wstępnie skonfigurowany zestaw reguł. Gdy oba są obecne, reguły niestandardowe są wykonywane przed wykonaniem reguły w zestawie reguł zarządzanych. Reguła składa się z warunkiem dopasowania, priorytet i akcji. Obsługiwane typy akcji to: Zezwalaj na, BLOK, DZIENNIKÓW i PRZEKIEROWANIA. Można utworzyć w pełni dostosowane zasady, które spełnia Twoje wymagania ochrony konkretnej aplikacji, łącząc zarządzanych, jak i niestandardowe reguły.

Reguły zasad są wykonywane w kolejności ustalonej według priorytetów, których priorytet jest unikatowa liczba całkowita, która definiuje kolejność wykonywania reguły. Mniejszą liczbą całkowitą wartość oznacza wyższy priorytet, a te są oceniane przed regułami o wyższej wartości liczby całkowitej. Po dopasowaniu regułę odpowiednich akcji, która została zdefiniowana w regule jest stosowany do żądania. Po przetworzeniu takiego dopasowania reguły o niższych priorytetach nie są przetwarzane dalej.

Aplikacja sieci web dostarczanych przez drzwiami frontowymi może mieć tylko jedną zasadę zapory aplikacji sieci Web skojarzony z nim w danym momencie. Jednak może mieć konfiguracji drzwiami frontowymi bez żadnych zasad zapory aplikacji sieci Web skojarzone z nią. Jeśli zasady zapory aplikacji sieci Web jest obecny, jest replikowany do wszystkich naszych lokalizacjach w celu zapewnienia spójności w zasadach zabezpieczeń na całym świecie.

## <a name="waf-modes"></a>Tryby zapory aplikacji sieci Web

Zasady zapory aplikacji sieci Web można skonfigurować do uruchamiania w następujących dwóch trybach:

- **Tryb wykrywania:** Po uruchomieniu w trybie wykrywania, zapory aplikacji sieci Web nie ma innych akcji inne niż monitorów i rejestruje żądanie i jego dopasowane reguły zapory aplikacji sieci Web do dzienników zapory aplikacji sieci Web. Można włączyć rejestrowanie danych diagnostycznych dla drzwiami frontowymi (przy użyciu portalu, można to osiągnąć, przechodząc do **diagnostyki** sekcji w witrynie Azure portal).

- **Tryb zapobiegania:** Po skonfigurowaniu do pracy w trybie zapobiegania zapory aplikacji sieci Web ma określoną akcję, jeśli żądanie jest zgodny z regułą, a jeśli zostanie znalezione dopasowanie, nie dodatkowe reguły o niższym priorytecie są oceniane. Wszelkie żądania dopasowane również są rejestrowane w dziennikach zapory aplikacji sieci Web.

## <a name="waf-actions"></a>Akcje zapory aplikacji sieci Web

Zapora aplikacji sieci Web klienci mogą wybrać do uruchomienia z jedną z akcji, gdy żądanie spełnia warunki reguły:
- **Zezwalaj na:**  Żądanie przechodzi przez zaporę aplikacji sieci Web i są przekazywane do zaplecza. Żadne dodatkowe niższy priorytet reguł, można zablokować tego żądania.
- **Blok:** Żądanie jest zablokowany, a Zapora aplikacji sieci Web wysyła odpowiedź do klienta bez przekazywania żądania do zaplecza.
- **Dziennik:**  Żądanie jest rejestrowane w dziennikach zapory aplikacji sieci Web i zapory aplikacji sieci Web kontynuuje oceny niższy priorytet reguły.
- **Przekieruj:** Zapora aplikacji sieci Web przekierowuje żądanie do określonego identyfikatora URI. Określony identyfikator URI jest ustawienie poziomie zasad. Po skonfigurowaniu, wszystkie żądania, które odpowiadają **przekierowania** akcji, które będą wysyłane do tego identyfikatora URI.


## <a name="waf-rules"></a>Reguły zapory aplikacji sieci Web

Zasady zapory aplikacji sieci Web może składać się z dwóch typów reguł zabezpieczeń — niestandardowe reguły, opracowane przez klienta i zestawów reguł zarządzanego, usługi Azure managed wstępnie skonfigurowany zestaw reguł.

### <a name="custom-authored-rules"></a>Niestandardowe reguły utworzone
Niestandardowe reguły zapory aplikacji sieci Web można skonfigurować w następujący sposób:

- **Zezwalaj na adresów IP, listy i listy zablokowanych:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci web, w oparciu o listę adresów IP klientów lub zakres adresów IP. Typy adresów IPv4 i IPv6 są obsługiwane. Tej listy można skonfigurować tak, aby zablokować lub zezwolić na te żądania, jeśli źródłowy adres IP odpowiada adresu IP na liście.

- **Kontrola dostępu na podstawie geograficznych:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci web na podstawie kodu kraju skojarzony adres IP klienta.

- **Kontrola dostępu na podstawie parametrów HTTP:** Można skonfigurować niestandardowe reguły na podstawie ciągu dopasowania parametry żądania HTTP/HTTPS, takich jak ciągi zapytań, argumenty POST, identyfikator URI żądania, nagłówek żądania i treści żądania.

- **Żądanie kontroli dostępu na podstawie metody:** Można skonfigurować niestandardowe reguły na podstawie metody żądania HTTP, żądania GET, PUT lub HEAD.

- **Ograniczenie rozmiaru:** Można skonfigurować reguły niestandardowe, w oparciu o długości określonych części żądań, takich jak ciągu zapytania identyfikatora Uri, lub w treści żądania.

- **Szybkość, ograniczając reguły:** Reguły kontroli stawka jest ograniczenie nietypowe duży ruch z dowolnego adresu IP klienta. Możesz skonfigurować wartości progowej liczby żądań sieci web z adresu IP klienta dozwolone w okresie jednej minuty. To różni się od adresu IP oparte na liście dozwolonych/zablokowanych reguła niestandardowa, która umożliwia albo wszystkie lub bloki, które wszystkie żądania z adresu IP klienta. Ograniczanie szybkości można łączyć z warunki dopasowań dodatkowe, takie jak parametry HTTP (S) dopasowania dla formantu szybkości poziomu szczegółowości.


### <a name="azure-managed-rule-sets"></a>Zestawy reguł zarządzanych platformy Azure

Zestawy reguł zarządzanego usługi Azure umożliwiają łatwe do wdrożenia ochrony przed wspólny zbiór zagrożenia bezpieczeństwa. Ponieważ takie zestawy reguł są zarządzane przez platformę Azure, zasady są aktualizowane zgodnie z potrzebami, aby zapewnić ochronę przed nowych sygnatur ataku. W publicznej wersji zapoznawczej usługi Azure managed domyślny zestaw reguł zawiera reguły na następujące kategorie zagrożeń:

- Wykonywanie skryptów między witrynami
- Ataki języka Java
- Włączenie pliku lokalnego
- Ataki polegające na iniekcji PHP
- Wykonanie polecenia zdalnego
- Dołączenie pliku zdalnego
- Utrwalanie sesji
- Ochrona przed atakami polegającymi na iniekcji SQL

Numer wersji zestawu domyślne reguły powoduje zwiększenie po dodaniu nowych sygnatur ataku do zestawu reguł.
Domyślny zestaw reguł jest domyślnie włączone w trybie wykrywania w zasadach zapory aplikacji sieci Web. Można wyłączyć lub włączyć poszczególnych reguł w ramach domyślny zestaw reguł do wymagań aplikacji. Można również ustawić określone akcje (Zezwalaj/BLOK/przekierowanie/LOG) dla każdej reguły. Domyślnym działaniem jest bloku. Ponadto reguły niestandardowe można skonfigurować w ramach jednych zasad zapory aplikacji sieci Web, jeśli chcesz pominąć dowolną ze wstępnie skonfigurowanych reguł w domyślnych zestawem reguł.
Reguły niestandardowe są zawsze stosowane przed wyznaczeniem zasad w domyślnych zestawem reguł. Jeśli żądanie jest zgodny z niestandardową regułę, odpowiednich akcji reguły są stosowane, a żądanie jest zablokowany lub przekazywane do zaplecza bez wywołanie wszelkie dalsze niestandardowe reguły lub reguły w domyślnych zestawem reguł. Ponadto istnieje możliwość można usunąć domyślnego zestawu reguł z zasad zapory aplikacji sieci Web.


## <a name="configuration"></a>Konfigurowanie
W publicznej wersji zapoznawczej:
- Konfigurowanie i wdrażanie wszystkich typów reguł zapory aplikacji sieci Web jest w pełni obsługiwany przy użyciu interfejsów API REST, szablonów usługi Azure Resource Manager i programu Azure PowerShell.
- Za pomocą witryny Azure portal, można skonfigurować lub wyświetlać tylko usługi Azure managed domyślne zestawem reguł.

## <a name="monitoring"></a>Monitorowanie

Monitorowanie zapory aplikacji sieci Web na wejściu jest zintegrowana z usługą Azure Monitor w celu śledzenia alertów i łatwego monitorowania trendów ruchu.

## <a name="pricing"></a>Cennik

W publicznej wersji zapoznawczej każde miesięczne użycie, skojarzone z zapory aplikacji sieci Web dla drzwiami frontowymi bezpłatnie i nie zostanie obciążona.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).

