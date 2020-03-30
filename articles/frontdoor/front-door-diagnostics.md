---
title: Monitorowanie metryk i dzienników w drzwiach frontowych platformy Azure| Dokumenty firmy Microsoft
description: W tym artykule opisano różne metryki i dzienniki dostępu, które obsługują usługi Azure Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471731"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitorowanie metryk i dzienników w drzwiach frontowych platformy Azure

Korzystając z usługi Azure Front Door, można monitorować zasoby w następujący sposób:

- **Metryki**. Usługa Azure Front Door ma obecnie siedem metryk do wyświetlania liczników wydajności.
- **Dzienniki**. Dzienniki aktywności i diagnostyki umożliwiają zapisywanie lub przetwarzanie danych z zasobów w celu monitorowania wydajności, dostępu i innych danych.

### <a name="metrics"></a>Metryki

Metryki są funkcją dla niektórych zasobów platformy Azure, które umożliwiają wyświetlanie liczników wydajności w portalu. Dostępne są dane dotyczące drzwi wejściowych:

| Metryka | Metryka Nazwa wyświetlana | Jednostka | Wymiary | Opis |
| --- | --- | --- | --- | --- |
| Liczba żądań | Liczba żądań | Liczba | Stan http</br>Grupa HttpStatus</br>Region klienta</br>Kraj klienta | Liczba żądań klientów obsługiwanych przez drzwi wejściowe.  |
| Rozmiar żądania | Rozmiar żądania | Bajty | Stan http</br>Grupa HttpStatus</br>Region klienta</br>Kraj klienta | Liczba bajtów wysyłanych jako żądania od klientów do drzwi wejściowych. |
| Rozmiar odpowiedzi | Rozmiar odpowiedzi | Bajty | Stan http</br>Grupa HttpStatus</br>Region klienta</br>Kraj klienta | Liczba bajtów wysłanych jako odpowiedzi z drzwi wejściowych do klientów. |
| TotalLatency (TotalLatency) | Całkowite opóźnienie | Milisekund | Stan http</br>Grupa HttpStatus</br>Region klienta</br>Kraj klienta | Czas obliczony na podstawie żądania klienta odebranego przez drzwi wejściowe, dopóki klient nie potwierdzi ostatniego bajtu odpowiedzi z drzwiami frontowymi. |
| ZapleczeRequestCount | Liczba żądań wewnętrznej bazy danych | Liczba | Stan http</br>Grupa HttpStatus</br>Zaplecze | Liczba żądań wysłanych z drzwi frontowych do zaplecza. |
| BackendRequestLatency (BackendRequestLatency) | Opóźnienie żądania wewnętrznej bazy danych | Milisekund | Zaplecze | Czas obliczony od momentu wysłania żądania przez drzwi frontowe do wewnętrznej bazy danych do momentu odebranie przez drzwi wejściowe ostatniego bajtu odpowiedzi z wewnętrznej bazy danych. |
| BackendHealthPercentage | Procent kondycji wewnętrznej bazy danych | Wartość procentowa | Zaplecze</br>BackendPool (BackendPool) | Procent pomyślnych sond kondycji od drzwi frontowych do zaplecza. |
| Konto WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Liczba | PolicyName</br>Rulename</br>Akcja | Liczba żądań klientów przetworzonych przez zabezpieczenia warstwy aplikacji drzwiami frontowymi. |

## <a name="activity-logs"></a><a name="activity-log"></a>Dzienniki aktywności

Dzienniki aktywności zawierają informacje o operacjach wykonywanych na drzwiach frontowych. Określają one również, co, kto i kiedy dla operacji zapisu (umieścić, opublikować lub usunąć) podjęte na drzwiach frontowych.

>[!NOTE]
>Dzienniki aktywności nie obejmują operacji odczytu (get). Nie obejmują one również operacji, które można wykonać przy użyciu witryny Azure portal lub oryginalnego interfejsu API zarządzania.

Dostęp do dzienników aktywności w drzwiach frontowych lub wszystkich dzienników zasobów platformy Azure w usłudze Azure Monitor. Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie drzwi frontowych.
2. Wybierz **dziennik aktywności**.

    ![Dziennik aktywności](./media/front-door-diagnostics/activity-log.png)

3. Wybierz zakres filtrowania, a następnie wybierz pozycję **Zastosuj**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Dzienniki diagnostyczne
Dzienniki diagnostyczne zawierają zaawansowane informacje o operacjach i błędach, które są ważne dla inspekcji i rozwiązywania problemów. Dzienniki diagnostyczne różnią się od dzienników aktywności.

Dzienniki aktywności zapewniają wgląd w operacje wykonywane na zasoby platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane zasób. Aby uzyskać więcej informacji, zobacz [Dzienniki diagnostyczne usługi Azure Monitor](../azure-monitor/platform/platform-logs-overview.md).

![Dzienniki diagnostyczne](./media/front-door-diagnostics/diagnostic-log.png)

Aby skonfigurować dzienniki diagnostyczne dla drzwi ekwiomowych:

1. Wybierz drzwi frontowe platformy Azure.

2. Wybierz **pozycję Ustawienia diagnostyczne**.

3. Wybierz **włącz diagnostykę**. Archiwizuj dzienniki diagnostyczne wraz z metrykami na konto magazynu, przesyłaj je strumieniowo do centrum zdarzeń lub wysyłaj do dzienników usługi Azure Monitor.

Drzwi frontowe obecnie udostępnia dzienniki diagnostyczne (wsadowe co godzinę). Dzienniki diagnostyczne zapewniają indywidualne żądania interfejsu API z każdego wpisu o następującym schemacie:

| Właściwość  | Opis |
| ------------- | ------------- |
| Nazwa backendhostname | Jeśli żądanie było przekazywane do wewnętrznej bazy danych, to pole reprezentuje nazwa hosta wewnętrznej bazy danych. To pole będzie puste, jeśli żądanie zostało przekierowane lub przekazane do regionalnej pamięci podręcznej (gdy buforowanie jest włączone dla reguły routingu). |
| Stan pamięci podręcznej | W przypadku scenariuszy buforowania to pole definiuje trafienie/pominięcie pamięci podręcznej w punkcie POP |
| ClientIp | Adres IP klienta, który złożył żądanie. Jeśli w żądaniu był nagłówek X-Forwarded-For, adres IP klienta jest pobierany z tego samego. |
| Port klienta | Port IP klienta, który złożył żądanie. |
| HttpMethod (httpmethod) | Metoda HTTP używana przez żądanie. |
| Kod HttpStatus | Kod stanu HTTP zwrócony z serwera proxy. |
| Szczegóły httpStatusDetails | Wynikowy stan na żądanie. Znaczenie tej wartości ciągu można znaleźć w tabeli odwołanie status. |
| Wersja httpwersacjowa | Typ żądania lub połączenia. |
| POP | Krótka nazwa krawędzi, na której wylądował wniosek. |
| RequestBytes (Żądaj bajtów) | Rozmiar komunikatu żądania HTTP w bajtach, w tym nagłówki żądań i treść żądania. |
| Requesturi | Identyfikator URI odebranego żądania. |
| Odpowiedzi Bajty | Bajty wysyłane przez serwer wewnętrznej bazy danych jako odpowiedź.  |
| Nazwa reguł routingu | Nazwa reguły routingu, do którą dopasowane żądanie. |
| SecurityProtocol (właśc. | Wersja protokołu TLS/SSL używana przez żądanie lub null, jeśli nie ma szyfrowania. |
| Tarcza WysłałaToOriginShield | Pole logiczne reprezentujące, jeśli w pierwszym środowisku wystąpiła chybienia w pamięci podręcznej, a żądanie zostało wysłane do regionalnej pamięci podręcznej. Zignoruj to pole, jeśli reguła routingu jest przekierowaniem lub gdy nie ma włączonego buforowania. |
| CzasTaken | Czas trwania akcji w milisekundach. |
| ŚledzenieReferencja | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez drzwiami frontowymi, również wysyłane jako nagłówek X-Azure-Ref do klienta. Wymagane do wyszukiwania szczegółów w dziennikach dostępu dla określonego żądania. |
| Useragent | Typ przeglądarki, który był używany przez klienta. |

**Uwaga:** W przypadku różnych konfiguracji routingu i zachowań ruchu niektóre pola, takie jak nazwa wiersza kopii zapasowejHostname, cacheStatus, sentToOriginShield i pole POP mogą odpowiadać różnymi wartościami. Poniższa tabela wyjaśnia różne wartości, te pola będą miały dla różnych scenariuszy:

| Scenariusze | Liczba wpisów dziennika | POP | Nazwa backendhostname | Tarcza WysłałaToOriginShield | Stan pamięci podręcznej |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Reguła routingu bez włączeniu buforowania | 1 | Kod przyp. | Zaplecze, w którym żądanie zostało przekazane | False | CONFIG_NOCACHE |
| Reguła routingu z włączoną buforowaniem. Pamięć podręczna trafiona na krawędzi POP | 1 | Kod przyp. | Pusty | False | Hit |
| Reguła routingu z włączoną buforowaniem. Cache miss na krawędzi POP, ale cache hit w nadrzędnej pamięci podręcznej POP | 2 | 1. Kod EDGE POP</br>2. Kod POP pamięci podręcznej nadrzędnej | 1. Nadrzędna nazwa hosta pamięci podręcznej POP</br>2. Pusty | 1. Prawda</br>2. Fałsz | 1. MISS</br>2. PARTIAL_HIT |
| Reguła routingu z włączoną buforowaniem. Pominięcie pamięci podręcznej zarówno w pamięci podręcznej krawędzi, jak i w pamięci podręcznej nadrzędnej POP | 2 | 1. Kod EDGE POP</br>2. Kod POP pamięci podręcznej nadrzędnej | 1. Nadrzędna nazwa hosta pamięci podręcznej POP</br>2. Zaplecze, które pomaga wypełnić pamięć podręczną | 1. Prawda</br>2. Fałsz | 1. MISS</br>2. MISS |


## <a name="next-steps"></a>Następne kroki

- [Tworzenie profilu drzwi przednich](quickstart-create-front-door.md)
- [Jak działają drzwi wejściowe](front-door-routing-architecture.md)
