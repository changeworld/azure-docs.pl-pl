---
title: Co to jest zapora aplikacji sieci Web platformy Azure w drzwiach frontowych platformy Azure?
description: Dowiedz się, jak zapora aplikacji sieci Web platformy Azure w usłudze Azure Front Door chroni aplikacje internetowe przed złośliwymi atakami.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: c8ff1849668d5effe15b6c25d00f3965a17b8e3e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77915643"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Zapora aplikacji sieci Web platformy Azure w drzwiach frontowych platformy Azure

Zapora aplikacji sieci Web azure (WAF) w usłudze Azure Front Door zapewnia scentralizowaną ochronę aplikacji sieci web. WAF chroni twoje usługi internetowe przed typowymi exploitami i lukami w zabezpieczeniach. Zapewnia wysoką dostęp do usługi dla użytkowników i pomaga spełnić wymagania dotyczące zgodności.

WAF na drzwiach frontowych to globalne i scentralizowane rozwiązanie. Jest wdrażany w lokalizacjach brzegowych sieci platformy Azure na całym świecie. Aplikacje internetowe z włączoną funkcją WAF sprawdzają każde przychodzące żądanie dostarczane przez drzwi wejściowe na krawędzi sieci. 

WAF zapobiega złośliwym atakom w pobliżu źródeł ataków, zanim wejdą one do sieci wirtualnej. Otrzymujesz globalną ochronę na dużą skalę bez utraty wydajności. Zasady WAF łatwo łączy się z dowolnym profilem drzwi frontowych w subskrypcji. Nowe reguły można wdrożyć w ciągu kilku minut, dzięki czemu można szybko reagować na zmieniające się wzorce zagrożeń.

![Zapora aplikacji sieci Web platformy Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Polityka i zasady WAF

Można skonfigurować zasady WAF i skojarzyć tę zasadę z co najmniej jednym frontołów drzwi frontowych dla ochrony. Zasady WAF składają się z dwóch typów reguł zabezpieczeń:

- reguły niestandardowe, które są autorstwa klienta.

- zarządzane zestawy reguł, które są kolekcją wstępnie skonfigurowanego zestawu reguł zarządzanych przez platformę Azure.

Gdy oba są obecne, reguły niestandardowe są przetwarzane przed przetworzeniem reguł w zestawie reguł zarządzanych. Reguła jest wykonana z warunku dopasowania, priorytetu i akcji. Obsługiwane typy akcji to: ZEZWALAJ, BLOKUJ, LOG i PRZEKIEROWANIE. Można utworzyć w pełni dostosowane zasady, które spełniają określone wymagania dotyczące ochrony aplikacji, łącząc reguły zarządzane i niestandardowe.

Reguły w ramach zasad są przetwarzane w kolejności priorytetu. Priorytet jest unikatową całkowitej liczby, która definiuje kolejność reguł do przetworzenia. Mniejsza wartość całkowita oznacza wyższy priorytet i te reguły są oceniane przed regułami o wyższej wartości całkowitej. Po dopasowaniu reguły do żądania jest stosowana odpowiednia akcja zdefiniowana w regule. Po przetworzeniu takiego dopasowania reguły o niższych priorytetach nie są dalej przetwarzane.

Aplikacja internetowa dostarczana przez drzwiami frontowymi może mieć tylko jedną zasadę WAF skojarzoną z nią naraz. Jednak można mieć konfigurację drzwi frontowych bez żadnych zasad WAF skojarzonych z nim. Jeśli zasady WAF jest obecny, jest replikowany do wszystkich naszych lokalizacji brzegowych w celu zapewnienia spójnych zasad zabezpieczeń na całym świecie.

## <a name="waf-modes"></a>Tryby WAF

Zasady WAF można skonfigurować tak, aby działały w następujących dwóch trybach:

- **Tryb wykrywania:** Po uruchomieniu w trybie wykrywania WAF nie podejmuje żadnych innych działań niż monitory i rejestruje żądanie i jego dopasowaną regułę WAF do dzienników WAF. Diagnostykę rejestrowania drzwi przednich można włączyć. Korzystając z portalu, przejdź do sekcji **Diagnostyka.**

- **Tryb zapobiegania:** W trybie zapobiegania WAF wykonuje określoną akcję, jeśli żądanie pasuje do reguły. Jeśli zostanie znaleziony mecz, nie są oceniane żadne dalsze reguły o niższym priorytecie. Wszystkie dopasowane żądania są również rejestrowane w dziennikach WAF.

## <a name="waf-actions"></a>Akcje WAF

Klienci WAF mogą wybrać uruchomienie jednej z akcji, gdy żądanie jest zgodne z warunkami reguły:

- **Zezwól na:**  Żądanie przechodzi przez WAF i jest przekazywane do zaplecza. Żadne dalsze reguły o niższym priorytecie nie mogą blokować tego żądania.
- **Blok:** Żądanie jest zablokowane i WAF wysyła odpowiedź do klienta bez przekazywania żądania do zaplecza.
- **Dziennik:**  Żądanie jest rejestrowane w dziennikach WAF i WAF kontynuuje ocenę reguł o niższym priorytecie.
- **Przekierowanie:** WAF przekierowuje żądanie do określonego identyfikatora URI. Określony identyfikator URI jest ustawieniem poziomu zasad. Po skonfigurowaniu wszystkie żądania pasujące do akcji **Przekierowanie** zostaną wysłane do tego identyfikatora URI.

## <a name="waf-rules"></a>Zasady WAF

Zasady WAF mogą składać się z dwóch typów reguł zabezpieczeń — reguł niestandardowych, utworzonych przez klienta i zarządzanych zestawów reguł, wstępnie skonfigurowanego zestawu reguł zarządzanych przez platformę Azure.

### <a name="custom-authored-rules"></a>Niestandardowe reguły tworzenia

Reguły niestandardowe można skonfigurować w następujący sposób:

- **Lista zablokowanych i lista zablokowanych adresów IP:** Dostęp do aplikacji internetowych można kontrolować na podstawie listy adresów IP klienta lub zakresów adresów IP. Obsługiwane są zarówno typy adresów IPv4, jak i IPv6. Tę listę można skonfigurować tak, aby blokować lub zezwalać na te żądania, w których źródłowy adres IP jest zgodny z adresem IP na liście.

- **Kontrola dostępu oparta na danych geograficznych:** Możesz kontrolować dostęp do aplikacji sieci web na podstawie kodu kraju skojarzonego z adresem IP klienta.

- **Kontrola dostępu oparta na parametrach HTTP:** Reguły można oprzeć na dopasowaniaach ciągów w parametrach żądania HTTP/HTTPS.  Na przykład ciągi zapytań, args POST, Identyfikator URI żądania, Nagłówek żądania i Treść żądania.

- **Zażądaj kontroli dostępu opartej na metodzie:** Reguły oparte na metodzie żądania HTTP żądania. Na przykład GET, PUT lub HEAD.

- **Ograniczenie rozmiaru:** Reguły można oprzeć na długości określonych części żądania, takich jak ciąg zapytania, Uri lub treść żądania.

- **Zasady ograniczania stawek:** Reguła kontroli szybkości ma na celu ograniczenie nieprawidłowego ruchu z dowolnego adresu IP klienta. Można skonfigurować próg liczby żądań sieci web dozwolonych z adresu IP klienta w czasie jednej minuty. Ta reguła różni się od reguły niestandardowej opartej na liście ADRESÓW IP, która zezwala na wszystkie lub blokuje wszystkie żądania z adresu IP klienta. Limity szybkości mogą być łączone z dodatkowymi warunkami dopasowania, takimi jak dopasowania parametrów HTTP(S) do kontroli szybkości szczegółowości.

### <a name="azure-managed-rule-sets"></a>Zestawy reguł zarządzanych przez platformę Azure

Zestawy reguł zarządzanych przez platformę Azure zapewniają łatwy sposób wdrażania ochrony przed typowym zestawem zagrożeń bezpieczeństwa. Ponieważ takie zestawy reguł są zarządzane przez platformę Azure, reguły są aktualizowane zgodnie z potrzebami, aby chronić przed nowymi podpisami ataków. Domyślny zestaw reguł zarządzanych przez platformę Azure zawiera reguły dotyczące następujących kategorii zagrożeń:

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
Domyślny zestaw reguł jest domyślnie włączony w trybie wykrywania w zasadach WAF. Można wyłączyć lub włączyć poszczególne reguły w ramach domyślnego zestawu reguł, aby spełnić wymagania aplikacji. Można również ustawić określone akcje (ALLOW/BLOCK/REDIRECT/LOG) dla danej reguły.

Czasami może być konieczne pominięcie niektórych atrybutów żądania z oceny WAF. Typowym przykładem są tokeny wstawione w usłudze Active Directory, które są używane do uwierzytelniania. Można skonfigurować listę wykluczeń dla reguły zarządzanej, grupy reguł lub dla całego zestawu reguł.  

Domyślną akcją jest zablokowanie. Ponadto reguły niestandardowe można skonfigurować w tej samej zasadie WAF, jeśli chcesz pominąć dowolną wstępnie skonfigurowaną regułę w domyślnym zestawie reguł.

Reguły niestandardowe są zawsze stosowane przed oszacowaniem reguł w domyślnym zestawie reguł. Jeśli żądanie jest zgodne z regułą niestandardową, stosowana jest odpowiednia akcja reguły. Żądanie jest zablokowane lub przekazywane do zaplecza. Żadne inne reguły niestandardowe ani reguły w domyślnym zestawie reguł nie są przetwarzane. Można również usunąć domyślny zestaw reguł z zasad WAF.

### <a name="bot-protection-rule-set-preview"></a>Zestaw reguł ochrony bota (wersja zapoznawcza)

Można włączyć zarządzany zestaw reguł ochrony bota do podejmowania akcji niestandardowych na żądaniach znanych kategorii botów. 

Obsługiwane są trzy kategorie botów: Bad, Good i Unknown. Podpisy botów są zarządzane i dynamicznie aktualizowane przez platformę WAF.

Złe boty obejmują boty ze złośliwych adresów IP i botów, które sfałszowały ich tożsamość. Złośliwe adresy IP pochodzą z kanału informacyjnego Microsoft Threat Intelligence i są aktualizowane co godzinę. [Inteligentny wykres zabezpieczeń](https://www.microsoft.com/security/operations/intelligence) zasila usługę Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Usługę Azure Security Center.

Dobre boty obejmują sprawdzone wyszukiwarki. Nieznane kategorie obejmują dodatkowe grupy botów, które zidentyfikowały się jako boty. Na przykład analizator rynku, pobieranie danych i agentów zbierania danych. 

Nieznane boty są klasyfikowane za pośrednictwem opublikowanych agentów użytkownika bez dodatkowej weryfikacji. Można ustawić akcje niestandardowe, aby blokować, zezwalać, rejestrować lub przekierowywać dla różnych typów botów.

![Zestaw reguł ochrony botów](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Zestaw reguł ochrony bota jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli ochrona bota jest włączona, przychodzące żądania zgodne z regułami botów są rejestrowane w dzienniku FrontdoorWebApplicationFirewallLog. Możesz uzyskać dostęp do dzienników WAF z konta magazynu, centrum zdarzeń lub analizy dzienników.

## <a name="configuration"></a>Konfigurowanie

Można skonfigurować i wdrożyć wszystkie typy reguł WAF przy użyciu witryny Azure portal, interfejsy API REST, szablonów usługi Azure Resource Manager i programu Azure PowerShell.

## <a name="monitoring"></a>Monitorowanie

Monitorowanie waf w drzwiach frontowych jest zintegrowane z usługą Azure Monitor, aby śledzić alerty i łatwo monitorować trendy ruchu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Zaporze aplikacji sieci Web w usłudze Azure Application Gateway](../ag/ag-overview.md)