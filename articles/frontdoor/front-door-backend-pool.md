---
title: Usługa Azure drzwiami frontowymi — zaplecza i pule zaplecza | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jakie są wewnętrznej bazy danych i pul zaplecza dla konfiguracji drzwi wejściowe.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 64d96d54b323d634703301e48cdaa28fa875fbbc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958802"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Pule zaplecza aplikacji i wewnętrznej bazy danych w usłudze Azure Service drzwi
W tym artykule opisano różne koncepcje dotyczące jak mapować wdrażania aplikacji za pomocą drzwiami frontowymi. Również Wyjaśnijmy, możesz z przodu różne warunki drzwi biblioteki konfiguracją na całym zaplecza aplikacji znaczenie.

## <a name="backend-pool"></a>Pula zaplecza
Puli zaplecza z przodu drzwi odnosi się do zestawu równoważnych serwerach zaplecza, które mogą odbierać ruch dla aplikacji tego samego typu. Innymi słowy jest logiczną grupą wystąpień aplikacji w całym świecie mogą odbierać ruch tego samego, która może odpowiadać na nie dzięki oczekiwane zachowanie. Te zaplecza zwykle są wdrażane w różnych regionach lub w ramach tego samego regionu. Ponadto te zaplecza są całkowicie w trybie wdrożenia aktywne-aktywne, lub można zdefiniować jako konfiguracja aktywny/pasywny.

Puli zaplecza definiuje również, jak różne zaplecza powinien wszystkie oceniane pod kątem ich kondycji za pomocą sondy kondycji, i odpowiednio jak Równoważenie obciążenia między zapleczem powinno mieć miejsce.

### <a name="health-probes"></a>Sondy kondycji
Drzwiami frontowymi wysyła okresowe żądania sondowania HTTP/HTTPS do każdego skonfigurowanego zaplecza w celu określenia odległości i kondycję każdego zaplecza, aby załadować saldo żądań użytkowników końcowych. Ustawienia sondy kondycji dla puli zaplecza definiują, jak firma Microsoft sondowania stanu kondycji dla aplikacji zaplecza. Dostępne dla konfiguracji Równoważenie obciążenia sieciowego są następujące ustawienia:

1. **Ścieżka**: Ścieżka adresu URL, w których będą wysyłane żądania sondowania do dla wszystkich zaplecza w puli zaplecza. Na przykład, jeśli jeden z zaplecza jest `contoso-westus.azurewebsites.net` i ścieżka jest równa `/probe/test.aspx`, wówczas środowisk drzwiami frontowymi, zakładając, że ustawiono protokołu HTTP, wyśle żądanie sondy kondycji do http://contoso-westus.azurewebsites.net/probe/test.aspx. 
2. **Protokół**: Określa, czy będą wysyłane żądania sondy kondycji z wejściu do zaplecza za pośrednictwem protokołu HTTP lub HTTPS.
3. **Interwał (w sekundach)**: to pole określa częstotliwość sondy kondycji do zaplecza, czyli odstępach czasu, w którym środowisk drzwiami frontowymi wyśle sondę. Uwaga — Jeśli potrzebujesz szybszych przejścia w tryb failover, wartość tego pola niższą wartość. Jednak im niższa wartość bardziej sondę kondycji woluminu, który otrzyma zaplecza. Aby poznać ilość sondy, ile drzwi spowoduje wygenerowanie na zaplecza, Przyjrzyjmy się przykładowi. Teraz załóżmy, że, interwał jest ustawiany na 30 sekund wiąże się z około 90 drzwi wejściowe lub w środowiskach POP globalnie. Tak, każdy z zaplecza otrzyma około o 3 – 5 sondowania żądań na sekundę.

Odczyt [sond kondycji](front-door-health-probes.md) Aby uzyskać szczegółowe informacje.

### <a name="load-balancing-settings"></a>Ustawienia równoważenia obciążenia
Ustawienia równoważenia obciążenia dla puli zaplecza, zdefiniuj sposób możemy ocenić sond kondycji dla podejmowania decyzji o wewnętrznej bazy danych jako poprawny stan, a także jak musimy załadować saldo ruch między różnych zaplecza w puli zaplecza. Dostępne dla konfiguracji Równoważenie obciążenia sieciowego są następujące ustawienia:

1. **Przykładowy rozmiar**: Ta właściwość określa, jak wiele przykładów sond kondycji należy wziąć pod uwagę oceny kondycji wewnętrznej bazy danych.
2. **Rozmiar próbki pomyślne**: Ta właściwość określa, czy wielkość próby jak wyjaśniono powyżej, jak wiele przykładów potrzebujemy pod kątem powodzenia do wywoływania zaplecza jako w dobrej kondycji. 
</br>Na przykład, załóżmy, że dla Twojego drzwiami frontowymi ustawiono sondy kondycji *interwał* 30 sekund, *przykładowy rozmiar* jest ustawiona na "5" i *rozmiar próbki pomyślne* jest ustawiona na "3". A następnie co ta konfiguracja oznacza, że to, że za każdym razem, gdy Oceniamy sondy kondycji do zaplecza, przyjrzymy się pięć ostatnich przykłady, które będzie obejmujące ostatnich 150 sekund (5 * 30 s) i chyba że istnieją 3 lub więcej z tych sond pomyślne firma Microsoft uzna tylnej koniec złej kondycji. Załóżmy, że wystąpiły tylko dwa sondy pomyślne, dlatego firma Microsoft oznaczy wewnętrznej bazy danych o złej kondycji. Przy następnym Uruchamiamy oceny 3 okaże się pomyślnie WE pięć ostatnich sond, następnie możemy oznaczyć wewnętrznej bazy danych jako w dobrej kondycji ponownie.
3. **Czułość opóźnienia (dodatkowe opóźnienie)**: pole czułości opóźnienie definiuje, czy chcesz drzwiami frontowymi wysyłać żądania do zaplecza, które są w zakresie poufności pod względem pomiaru opóźnienia lub na żądania najbliższy wewnętrznej bazy danych. Odczyt [najmniejszego opóźnienia na podstawie metody routingu opartego na](front-door-routing-methods.md#latency) na wejściu dowiedzieć się więcej.

## <a name="backend"></a>Zaplecze
Zapleczem jest równoznaczne z wystąpienia wdrożenia aplikacji w regionie. Drzwiami frontowymi obsługuje zarówno platformy Azure, jak i zaplecza spoza platformy Azure, a więc region, w tym miejscu tylko nie jest ograniczona do regionów platformy Azure, ale można też lokalnego centrum danych lub wystąpienie aplikacji, w niektórych innych chmur.

Zaplecza aplikacji, w kontekście drzwi do przodu, odnosi się do nazwy hosta lub publiczny adres IP aplikacji, która może obsługiwać żądania klientów. Tak zaplecza nie należy mylić z warstwy usługi bazy danych lub warstwę magazynu itp., ale raczej powinny być traktowane jako publicznym punktem końcowym zaplecza aplikacji.

Po dodaniu zaplecza w puli zaplecza usługi drzwiami frontowymi należy wypełnić następujące informacje:

1. **Typ hosta zaplecza**: typ zasobu, które chcesz dodać. Drzwiami frontowymi obsługuje automatyczne odnajdowanie zaplecza aplikacji z usługi app service, usługi w chmurze lub magazynu. Inny zasób na platformie Azure lub nawet zaplecza spoza platformy Azure, wybierz opcję "Niestandardowy host". Uwaga — Podczas konfigurowania interfejsów API nie weryfikuje, czy wewnętrznej bazy danych jest dostępny ze środowisk drzwiami frontowymi zamiast tego należy się upewnić, że zaplecza jest osiągalna drzwiami frontowymi. 
2. **Nazwa hosta subskrypcji i zaplecze oparte na**: Jeśli nie wybrano "Niestandardowy host" dla typu hosta wewnętrznej bazy danych, a następnie należy określić zakres szczegółów i wybrać wewnętrzną bazą danych, wybierając odpowiednią subskrypcję i odpowiednią nazwę hosta wewnętrznej bazy danych przez użytkownika interfejs.
3. **Nagłówek hosta zaplecza**: wartość nagłówka hosta wysyłana do wewnętrznej bazy danych dla każdego żądania. Odczyt [nagłówek hosta zaplecza](#hostheader) Aby uzyskać szczegółowe informacje.
4. **Priorytet**: priorytetów można przypisać do różnych zaplecza, gdy chcesz użyć podstawowa usługa zaplecza dla całego ruchu, a wykonywanie kopii zapasowych w przypadku podstawowej lub kopii zapasowej zaplecza są niedostępne. Przeczytaj więcej na temat [priorytet](front-door-routing-methods.md#priority).
5. **Waga**: Jeśli chcesz dystrybuować ruch w zestawie zaplecza, równomiernie lub według wagi współczynniki można przypisać wagi do różnych zaplecza. Przeczytaj więcej na temat [wagi](front-door-routing-methods.md#weighted).


### <a name = "hostheader"></a>Nagłówek hosta wewnętrznej bazy danych

Żądania przekazywane za wejściu do wewnętrznej bazy danych ma pola nagłówka hosta, który używa wewnętrznej bazy danych w celu pobrania zasobu docelowego. Wartość dla tego pola zazwyczaj pochodzi z identyfikatora URI wewnętrznej bazy danych, a także ma hosta i portu. Na przykład żądania dotyczącego `www.contoso.com` będzie miał nagłówek hosta `www.contoso.com`. Jeśli konfigurujesz wewnętrzną bazą danych przy użyciu witryny Azure portal, domyślną wartość, która jest wypełniana dla tego pola jest nazwą hosta w wewnętrznej bazie danych. Na przykład, jeśli jest zaplecza `contoso-westus.azurewebsites.net`, a następnie w witrynie Azure portal będzie automatycznie wypełniony wartość nagłówka hosta zaplecza `contoso-westus.azurewebsites.net`. 
</br>Jednak jeśli przy użyciu szablonów usługi Resource Manager, lub inny mechanizm i są to ustawienie nie zostanie to pole jawnie drzwiami frontowymi wysyła przychodzącą nazwę hosta jako wartość nagłówka hosta. Na przykład, jeśli żądanie zostało wystosowane `www.contoso.com`, a zapleczem `contoso-westus.azurewebsites.net` następnie za pomocą puste pole nagłówka hosta zaplecza drzwiami frontowymi ustawi nagłówek hosta jako `www.contoso.com`.

Większość zaplecza aplikacji (takich jak Web Apps, Blob Storage i usług w chmurze) wymagają nagłówek hosta był zgodny z domeną wewnętrznej bazy danych. Jednak hosta serwera sieci Web, który kieruje wewnętrzną bazą danych ma inną nazwę hosta, takich jak www.contoso.azurefd.net. Jeśli wewnętrznej bazy danych, którą konfigurujesz wymaga nagłówka hosta, aby dopasować nazwę hosta w wewnętrznej bazie danych, należy upewnić się, że nagłówek hosta wewnętrznej bazy danych ma również nazwę hosta w wewnętrznej bazie danych.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurowanie zaplecza nagłówek hosta dla wewnętrznej bazy danych
Pole "Nagłówek hosta dla wewnętrznej bazy danych" można skonfigurować dla zaplecza w sekcji puli zaplecza.

1. Otwórz zasób usługi drzwi wejściowe, a następnie kliknij pulę zaplecza, zawierającej wewnętrznej bazy danych do skonfigurowania.

2. Dodaj zaplecze, jeśli nie dodano żadnego lub edytować istniejący. Pole "Nagłówek hosta dla wewnętrznej bazy danych" może być ustawiona na wartość niestandardową lub pole pozostanie puste, co oznacza, że nazwa hosta dla żądania przychodzącego będzie służyć jako wartość nagłówka hosta.



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie drzwiami frontowymi](quickstart-create-front-door.md).
- Dowiedz się, [działania drzwiami frontowymi](front-door-routing-architecture.md).