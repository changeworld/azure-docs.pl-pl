---
title: Monitorowanie metryk i dzienników w usłudze Azure Front-drzwiczk | Microsoft Docs
description: W tym artykule opisano różne metryki i dzienniki dostępu obsługiwane przez usługę Azure Front drzwiczk
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
ms.openlocfilehash: 229706ff91b776363d3e9de080e02cee5edf9c77
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677892"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitorowanie metryk i dzienników w usłudze Azure Front-drzwiczk

Za pomocą usługi Azure Front drzwiczk można monitorować zasoby w następujący sposób:

- **Metryki**. Drzwi frontonu platformy Azure mają obecnie siedem metryk do wyświetlania liczników wydajności.
- **Dzienniki**. Dzienniki działań i diagnostyki umożliwiają zapisywanie lub zużywanie wydajności, dostępu i innych danych z zasobów na potrzeby monitorowania.

### <a name="metrics"></a>Metryki

Metryki to funkcja niektórych zasobów platformy Azure, która umożliwia wyświetlanie liczników wydajności w portalu. Dostępne są następujące metryki czołowych drzwi:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Wymiary | Opis |
| --- | --- | --- | --- | --- |
| requestCount | Liczba żądań | Liczba | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba żądań klientów obsłużonych przez tylne drzwi.  |
| RequestSize | Rozmiar żądania | Szybkość | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako żądania od klientów do przednich drzwi. |
| ResponseSize | Rozmiar odpowiedzi | Szybkość | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako odpowiedzi z pierwszych drzwi do klientów. |
| TotalLatency | Łączne opóźnienie | ) | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Czas, który został obliczony przez żądanie klienta odebrane przez tylne drzwi do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z drzwi przednich. |
| BackendRequestCount | Liczba żądań wewnętrznej bazy danych | Liczba | Wartości httpStatus</br>HttpStatusGroup</br>Danych | Liczba żądań wysyłanych z przednich drzwi do frontonu. |
| BackendRequestLatency | Opóźnienie żądania wewnętrznej bazy danych | ) | Danych | Czas, jaki upłynął od momentu wysłania żądania przez drzwi do zaplecza do momentu odebrania ostatniego bajtu odpowiedzi z zaplecza do przodu. |
| BackendHealthPercentage | Procent kondycji zaplecza | Procent | Danych</br>Ustawień httpsettings elementu | Procent pomyślnych sond kondycji z czołowych drzwi do frontonu. |
| WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Liczba | policyName</br>RuleName</br>Działanie | Liczba żądań klientów przetworzonych przez zabezpieczenia warstwy aplikacji dla drzwi z przodu. |

## <a name="activity-log"></a>Dzienniki aktywności

Dzienniki aktywności zawierają informacje o operacjach wykonywanych w usłudze front-drzwi. Określają one również elementy, kto i kiedy dla operacji zapisu (Put, post lub Delete) podejmowane w usłudze front-drzwi.

>[!NOTE]
>Dzienniki aktywności nie obejmują operacji odczytu (Get). Nie obejmują one również operacji wykonywanych przy użyciu Azure Portal lub oryginalnego interfejsu API zarządzania.

Uzyskaj dostęp do dzienników aktywności w usłudze front-drzwi lub wszystkich dzienników zasobów platformy Azure w Azure Monitor. Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie z drzwiami czołowymi.
2. Wybierz pozycję **Dziennik aktywności**.

    ![Dziennik aktywności](./media/front-door-diagnostics/activity-log.png)

3. Wybierz zakres filtrowania, a następnie wybierz pozycję **Zastosuj**.

## <a name="diagnostic-logging"></a>Dzienniki diagnostyczne
Dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji i rozwiązywania problemów. Dzienniki diagnostyczne różnią się od dzienników aktywności.

Dzienniki aktywności zapewniają wgląd w operacje wykonywane w zasobach platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane przez zasób. Aby uzyskać więcej informacji, zobacz [Azure monitor dzienników diagnostycznych](../azure-monitor/platform/resource-logs-overview.md).

![Dzienniki diagnostyczne](./media/front-door-diagnostics/diagnostic-log.png)

Aby skonfigurować dzienniki diagnostyczne dla usługi front-drzwi:

1. Wybierz swoją usługę Azure front-drzwi.

2. Wybierz pozycję **Ustawienia diagnostyczne**.

3. Wybierz pozycję **Włącz diagnostykę**. Archiwizuj dzienniki diagnostyczne wraz z metrykami na koncie magazynu, przesyłaj strumieniowo do centrum zdarzeń lub wysyłaj je do dzienników Azure Monitor.

Usługa front-drzwi udostępnia teraz dzienniki diagnostyczne (wsadowe co godzinę). Dzienniki diagnostyczne zapewniają pojedyncze żądania interfejsu API za pomocą każdego wpisu, który ma następujący schemat:

| Właściwość  | Opis |
| ------------- | ------------- |
| clientIp | Adres IP klienta, który wykonał żądanie. |
| clientPort | Port IP klienta, który wykonał żądanie. |
| httpMethod | Metoda HTTP używana przez żądanie. |
| HttpStatusCode | Kod stanu HTTP zwrócony z serwera proxy. |
| HttpStatusDetails | Stan wynikający z żądania. Znaczenie tej wartości ciągu można znaleźć w tabeli odwołania do stanu. |
| httpVersion | Typ żądania lub połączenia. |
| RequestBytes | Rozmiar komunikatu żądania HTTP w bajtach, w tym nagłówki żądań i treść żądania. |
| RequestUri | Identyfikator URI odebranego żądania. |
| ResponseBytes | Bajty wysłane przez serwer wewnętrznej bazy danych jako odpowiedź.  |
| RoutingRuleName | Nazwa reguły routingu, która pasuje do żądania. |
| To elementu SecurityProtocol | Wersja protokołu TLS/SSL używana przez żądanie lub wartość null, jeśli nie ma szyfrowania. |
| TimeTaken | Czas trwania akcji (w milisekundach). |
| UserAgent | Typ przeglądarki używany przez klienta |
| TrackingReference | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi, również wysyłany jako nagłówek X-Azure-ref do klienta. Wymagane do wyszukiwania szczegółowych informacji w dziennikach dostępu dla określonego żądania. |

## <a name="next-steps"></a>Następne kroki

- [Tworzenie profilu frontu drzwi](quickstart-create-front-door.md)
- [Jak działają tylne drzwi](front-door-routing-architecture.md)
