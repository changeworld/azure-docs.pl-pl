---
title: Wskazówki dotyczące żądania ograniczone
description: Naucz się tworzyć lepsze zapytania w celu uniknięcia żądań do usługi Azure Resource wykresu z ograniczane.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276901"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Wskazówki dotyczące żądania ograniczone w wykresie zasobów platformy Azure

Podczas tworzenia programowe i częste użycie danych wykresu zasobów platformy Azure, brany pod uwagę przewidzieć wpływ na sposób ograniczania wyników zapytania. Modyfikowanie danych w sposób, jest wymagany może pomóc uniknąć ograniczane oraz obsługi przepływu aktualne dane dotyczące zasobów platformy Azure organizacji.

W tym artykule opisano cztery obszary i wzorce dotyczące tworzenia kwerend w wykresie zasobów platformy Azure:

- Omówienie ograniczania nagłówki
- Przetwarzanie wsadowe, zapytania
- Różnicowanie zapytań
- Wpływ dzielenia na strony

## <a name="understand-throttling-headers"></a>Omówienie ograniczania nagłówki

Wykres zasobów platformy Azure przydziela limit przydziału liczby dla każdego użytkownika, w oparciu o przedział czasu. Na przykład użytkownik może wysłać co najwyżej 15 zapytań w ramach każdego okna 5-sekundowego bez ograniczane. Wartość limitu przydziału zależy od wielu czynników i może ulec zmianie.

W każdej odpowiedzi na kwerendę wykres zasobów Azure dodaje dwa nagłówki ograniczania przepustowości:

- `x-ms-user-quota-remaining` (int): Pozostały limit przydziału zasobów dla użytkownika. Ta wartość jest mapowany na liczby zapytań.
- `x-ms-user-quota-resets-after` (: mm: ss): Czas trwania dopóki nie zostanie zresetowane przez użytkownika limit przydziału użycia.

Aby zilustrować, jak działają nagłówki, Przyjrzyjmy się odpowiedzi na zapytanie zawiera nagłówek i wartości `x-ms-user-quota-remaining: 10` i `x-ms-user-quota-resets-after: 00:00:03`.

- W następnym 3 sekundach co najwyżej 10 kwerendy mogą być składane, nie będzie ograniczona.
- W 3 sekundach, wartości `x-ms-user-quota-remaining` i `x-ms-user-quota-resets-after` zostaną zresetowane do `15` i `00:00:05` odpowiednio.

Aby wyświetlić przykład użycia nagłówki, aby _wycofywania_ dla żądań zapytań, zobacz przykład w [zapytania równolegle](#query-in-parallel).

## <a name="batching-queries"></a>Przetwarzanie wsadowe, zapytania

Przetwarzanie wsadowe, zapytania według subskrypcji, grupy zasobów lub poszczególnych zasobów jest bardziej wydajne niż przekształcają zapytania. Koszt przydziału większych zapytania często jest mniejszy niż koszt przydziału wiele zapytań małych, jak i docelowym. Rozmiar partii jest zalecane jest mniejsza niż _300_.

- Przykład źle zoptymalizowane metody

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Przykład #1 zoptymalizowane podejścia przetwarzania wsadowego

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Przykład #2 zoptymalizowane podejścia przetwarzania wsadowego

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Różnicowanie zapytań

Ze względu na sposób ograniczania są wymuszane, zaleca się samodzielnym zapytań. Oznacza to, że zamiast wysyłać 60 zapytania w tym samym czasie, przesunąć zapytania do czterech 5-sekundowego systemu windows:

- Harmonogram rozłożona inne niż zapytania

  | Liczba zapytań         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Interwał (s) | 0-5 | 5-10 | 10-15 | 15-20 |

- Rozłożona harmonogram zapytania

  | Liczba zapytań         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Interwał (s) | 0-5 | 5-10 | 10-15 | 15-20 |

Poniżej przedstawiono przykład uwzględnienie nagłówki ograniczania przepustowości podczas wykonywania zapytań względem grafu zasobów platformy Azure:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Zapytania równoległe

Mimo że zaleca się przetwarzanie wsadowe, za pośrednictwem przetwarzania równoległego, istnieją razy gdzie zapytań nie może łatwo partii. W takich przypadkach można zbadać wykres zasobów platformy Azure, wysyłając wielu zapytań w sposób równoległy. Poniżej znajduje się przykład jak _wycofywania_ oparte na ograniczanie nagłówków w takich scenariuszach:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginacja

Ponieważ wykres zasobów platformy Azure zwraca maksymalnie 1000 wpisów w odpowiedzi na jedno zapytanie, może być konieczne [stronicowanie](./work-with-data.md#paging-results) zapytań w taki sposób, aby uzyskać pełny zestaw danych, czego szukasz. Jednak niektórzy klienci wykres zasobów platformy Azure obsługuje podział na strony inaczej niż inne.

- Zestaw SDK języka C#

  Korzystając z zestawu SDK ResourceGraph, będzie konieczna Obsługa dzielenia na strony, przekazując token pomijania zwracanych z poprzedniej odpowiedzi na zapytanie do następnego zapytania z podziałem na strony. W tym projekcie oznacza, że musisz Zbieraj wyniki z wszystkich wywołań z podziałem na strony i połączyć je na końcu. W przypadku każdego zapytania z podziałem na strony, które wysyłasz przyjmuje jeden przydział zapytania:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Wiersza polecenia platformy Azure / Azure PowerShell

  Korzystając z wiersza polecenia platformy Azure lub programu Azure PowerShell, zapytania, aby wykres zasobów platformy Azure są automatycznie z podziałem na strony można pobrać maksymalnie 5000 wpisów. Wyniki zapytania zwraca listę połączoną z wpisów z wszystkich wywołań z podziałem na strony. W takich przypadkach w zależności od liczby wpisów w wyniku zapytania pojedynczego zapytania z podziałem na strony może zużywać więcej niż jeden przydział zapytania. Na przykład w poniższym przykładzie pojedynczego uruchamiania zapytania może zużywać maksymalnie pięć przydział zapytania:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Nadal ograniczona?

Jeśli jesteś ograniczany po wykonywaniu powyższe zalecenia, skontaktuj się z zespołem pod adresem [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Należy podać następujące dane:

- Wybrany sterownik przypadków użycia i biznesowych potrzebuje wyższy limit ograniczania przepustowości.
- Jak wiele zasobów masz dostęp do? Ile są zwracane z pojedynczego zapytania?
- Co to są typy zasobów możesz zainteresowani?
- Co to jest Twoja wzorzec zapytania? X zapytań na sekundę Y itp.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz język używany w [początkowego zapytania](../samples/starter.md).
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md).
- Dowiedz się, jak [zapoznaj się z zasobami](explore-resources.md).