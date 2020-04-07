---
title: Co to jest zapora aplikacji sieci Web platformy Azure w usłudze Azure CDN?
description: Dowiedz się, jak zapora aplikacji sieci Web platformy Azure w usłudze Azure CDN chroni aplikacje internetowe przed złośliwymi atakami.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 28cf8d9fd60cc6fc158812aa0a1dff3a4b0dced1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754282"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Zapora aplikacji sieci Web platformy Azure w sieci dostarczania zawartości platformy Azure

Zapora aplikacji sieci Web azure (WAF) w usłudze Azure Content Delivery Network (CDN) firmy Microsoft zapewnia scentralizowaną ochronę zawartości sieci Web. WAF chroni twoje usługi internetowe przed typowymi exploitami i lukami w zabezpieczeniach. Zapewnia wysoką dostęp do usługi dla użytkowników i pomaga spełnić wymagania dotyczące zgodności.

> [!IMPORTANT]
> WAF na platformie Azure CDN firmy Microsoft jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usługi w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa WAF na platformie Azure CDN to globalne i scentralizowane rozwiązanie. Jest wdrażany w lokalizacjach brzegowych sieci platformy Azure na całym świecie. WAF zatrzymuje złośliwe ataki w pobliżu źródeł ataku, zanim dotrą do twojego pochodzenia. Otrzymujesz globalną ochronę na dużą skalę bez utraty wydajności. 

Zasady WAF łatwo łączy się z dowolnego punktu końcowego usługi CDN w ramach subskrypcji. Nowe reguły można wdrożyć w ciągu kilku minut, dzięki czemu można szybko reagować na zmieniające się wzorce zagrożeń.

![Zapora aplikacji sieci Web platformy Azure](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>Polityka i zasady WAF

Można skonfigurować zasady WAF i skojarzyć tę zasadę z co najmniej jednym punktem końcowym sieci CDN w celu ochrony. Zasady WAF składają się z dwóch typów reguł zabezpieczeń:

- reguły niestandardowe, które można utworzyć.

- zarządzane zestawy reguł, które są kolekcją wstępnie skonfigurowanych reguł zarządzanych przez platformę Azure.

Gdy oba są obecne, reguły niestandardowe są przetwarzane przed przetworzeniem reguł w zestawie reguł zarządzanych. Reguła jest wykonana z warunku dopasowania, priorytetu i akcji. Obsługiwane typy akcji to: *ZEZWALAJ,* *BLOKUJ,* *DZIENNIKA*i *PRZEKIEROWANIE*. Można utworzyć w pełni dostosowane zasady, które spełniają określone wymagania dotyczące ochrony aplikacji, łącząc reguły zarządzane i niestandardowe.

Reguły w ramach zasad są przetwarzane w kolejności priorytetu. Priorytet to unikatowy numer, który definiuje kolejność reguł do przetworzenia. Mniejsze liczby są wyższy priorytet i te reguły są oceniane przed reguły o większej wartości. Po dopasowaniu reguły do żądania jest stosowana odpowiednia akcja zdefiniowana w regule. Po przetworzeniu takiego dopasowania reguły o niższych priorytetach nie są dalej przetwarzane.

Aplikacja sieci web hostowana w usłudze Azure CDN może mieć tylko jedną zasadę WAF skojarzoną z nią w czasie. Jednak można mieć punkt końcowy usługi CDN bez żadnych zasad WAF skojarzonych z nim. Jeśli zasady WAF jest obecny, jest replikowany do wszystkich naszych lokalizacji brzegowych w celu zapewnienia spójnych zasad zabezpieczeń na całym świecie.

## <a name="waf-modes"></a>Tryby WAF

Zasady WAF można skonfigurować tak, aby działały w następujących dwóch trybach:

- *Tryb wykrywania:* Po uruchomieniu w trybie wykrywania WAF nie podejmuje żadnych innych działań niż monitory i rejestruje żądanie i jego dopasowaną regułę WAF do dzienników WAF. Diagnostykę rejestrowania drzwi przednich można włączyć. Korzystając z portalu, przejdź do sekcji **Diagnostyka.**

- *Tryb zapobiegania:* W trybie zapobiegania WAF podejmuje określoną akcję, jeśli żądanie pasuje do reguły. Jeśli zostanie znaleziony mecz, nie są oceniane żadne dalsze reguły o niższym priorytecie. Wszystkie dopasowane żądania są również rejestrowane w dziennikach WAF.

## <a name="waf-actions"></a>Akcje WAF

Można wybrać jedną z następujących akcji, gdy żądanie odpowiada warunkom reguły:

- *Zezwól:* Żądanie przechodzi przez WAF i jest przekazywane do zaplecza. Żadne dalsze reguły o niższym priorytecie nie mogą blokować tego żądania.
- *Blok:* Żądanie jest zablokowane i WAF wysyła odpowiedź do klienta bez przekazywania żądania do zaplecza.
- *Dziennik:* Żądanie jest rejestrowane w dziennikach WAF i WAF kontynuuje ocenę reguł o niższym priorytecie.
- *Przekierowanie:* WAF przekierowuje żądanie do określonego identyfikatora URI. Określony identyfikator URI jest ustawieniem poziomu zasad. Po skonfigurowaniu wszystkie żądania pasujące do akcji *Przekierowanie* są wysyłane do tego identyfikatora URI.

## <a name="waf-rules"></a>Zasady WAF

Zasady WAF mogą składać się z dwóch typów reguł zabezpieczeń:

- *reguły niestandardowe:* reguły, które tworzysz samodzielnie 
- *zarządzane zestawy reguł:* wstępnie skonfigurowany zestaw reguł zarządzanych przez platformę Azure

### <a name="custom-rules"></a>Reguły niestandardowe

Reguły niestandardowe mogą mieć reguły dopasowania i reguły kontroli stawek.

Można skonfigurować następujące niestandardowe reguły dopasowania:

- *Lista zablokowanych adresów IP i lista zablokowanych:* Można kontrolować dostęp do aplikacji internetowych na podstawie listy adresów IP klienta lub zakresów adresów IP. Obsługiwane są zarówno typy adresów IPv4, jak i IPv6. Tę listę można skonfigurować tak, aby blokować lub zezwalać na te żądania, w których źródłowy adres IP jest zgodny z adresem IP na liście.

- *Kontrola dostępu oparta na danych geograficznych:* możesz kontrolować dostęp do aplikacji internetowych na podstawie kodu kraju skojarzonego z adresem IP klienta.

- *Kontrola dostępu oparta na parametrach HTTP: Reguły*można oprzeć na dopasowaniaach ciągów w parametrach żądań HTTP/HTTPS.  Na przykład ciągi zapytań, args POST, Identyfikator URI żądania, Nagłówek żądania i Treść żądania.

- *Kontrola dostępu oparta na metodzie żądania:* Reguły są oparte na metodzie żądania HTTP żądania. Na przykład GET, PUT lub HEAD.

- *Ograniczenie rozmiaru:* Reguły można oprzeć na długości określonych części żądania, takich jak ciąg zapytania, Uri lub treść żądania.

Reguła kontroli szybkości ogranicza nienormalnie duży ruch z dowolnego adresu IP klienta.

- *Reguły ograniczania szybkości:* Można skonfigurować próg liczby żądań internetowych dozwolonych z adresu IP klienta w czasie trwania jednej minuty. Ta reguła różni się od reguły niestandardowej opartej na liście ADRESÓW IP, która zezwala na wszystkie lub blokuje wszystkie żądania z adresu IP klienta. Limity szybkości mogą być łączone z dodatkowymi warunkami dopasowania, takimi jak dopasowania parametrów HTTP(S) do kontroli szybkości szczegółowości.

### <a name="azure-managed-rule-sets"></a>Zestawy reguł zarządzanych przez platformę Azure

Zestawy reguł zarządzanych przez platformę Azure zapewniają łatwy sposób wdrażania ochrony przed typowym zestawem zagrożeń bezpieczeństwa. Ponieważ te zestawy reguł są zarządzane przez platformę Azure, reguły są aktualizowane zgodnie z potrzebami, aby chronić przed nowymi podpisami ataków. Zarządzany domyślny zestaw reguł platformy Azure zawiera reguły dotyczące następujących kategorii zagrożeń:

- Użycie skryptów między witrynami
- Ataki Java
- Dołączanie do plików lokalnych
- Ataki wtrysku PHP
- Zdalne wykonywanie polecenia
- Dołączanie pliku zdalnego
- Utrwalenie sesji
- Ochrona przed atakami polegającymi na iniekcji SQL
- Atakujący protokołu

Numer wersji domyślnego zestawu reguł zwiększa się, gdy nowe podpisy ataku są dodawane do zestawu reguł.
Domyślny zestaw reguł jest domyślnie włączony w trybie *wykrywania* w zasadach WAF. Można wyłączyć lub włączyć poszczególne reguły w ramach domyślnego zestawu reguł, aby spełnić wymagania aplikacji. Można również ustawić określone akcje (ALLOW/BLOCK/REDIRECT/LOG) dla danej reguły. Domyślną akcją dla zarządzanego domyślnego zestawu reguł jest *Block*.

Reguły niestandardowe są zawsze stosowane przed oszacowaniem reguł w domyślnym zestawie reguł. Jeśli żądanie jest zgodne z regułą niestandardową, stosowana jest odpowiednia akcja reguły. Żądanie jest zablokowane lub przekazywane do zaplecza. Żadne inne reguły niestandardowe ani reguły w domyślnym zestawie reguł nie są przetwarzane. Można również usunąć domyślny zestaw reguł z zasad WAF.

## <a name="configuration"></a>Konfigurowanie

Można skonfigurować i wdrożyć wszystkie typy reguł WAF przy użyciu witryny Azure portal, interfejsy API REST, szablonów usługi Azure Resource Manager i programu Azure PowerShell.

## <a name="monitoring"></a>Monitorowanie

Monitorowanie w ramach usługi WAF za pomocą usługi CDN jest zintegrowane z usługą Azure Monitor w celu śledzenia alertów i łatwego monitorowania trendów ruchu.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie zasad WAF za pomocą usługi Azure CDN przy użyciu portalu Azure](waf-cdn-create-portal.md)
