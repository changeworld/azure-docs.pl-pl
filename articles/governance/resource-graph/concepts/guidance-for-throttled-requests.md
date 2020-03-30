---
title: Wskazówki dotyczące ograniczonych żądań
description: Dowiedz się, aby grupować, rozłożenie, podział na strony i kwerendy równolegle, aby uniknąć żądań są ograniczane przez usługę Azure Resource Graph.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259853"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Wskazówki dotyczące ograniczania żądań w grafie zasobów platformy Azure

Podczas tworzenia programowego i częstego korzystania z danych programu Azure Resource Graph należy wziąć pod uwagę, jak ograniczanie wpływa na wyniki kwerend. Zmiana sposobu, w jaki dane są wymagane może pomóc i Organizacji uniknąć ograniczania i utrzymania przepływu danych w odpowiednim czasie o zasoby platformy Azure.

W tym artykule omówiono cztery obszary i wzorce związane z tworzeniem zapytań w grafie zasobów platformy Azure:

- Omówienie nagłówków ograniczeń
- Grupowanie zapytań
- Rozkładanie zapytań
- Wpływ paginacji

## <a name="understand-throttling-headers"></a>Omówienie nagłówków ograniczeń

Usługa Azure Resource Graph przydziela numer przydziału dla każdego użytkownika na podstawie przedziału czasu. Na przykład użytkownik może wysyłać co najwyżej 15 zapytań w każdym 5-sekundowym oknie bez ograniczania. Wartość przydziału jest określana przez wiele czynników i może ulec zmianie.

W każdej odpowiedzi na kwerendę program Azure Resource Graph dodaje dwa nagłówki ograniczania przepustowości:

- `x-ms-user-quota-remaining`(int): Pozostały przydział zasobów dla użytkownika. Ta wartość jest mapowana do liczby zapytań.
- `x-ms-user-quota-resets-after`(hh:mm:ss): Czas trwania do czasu zresetowania zużycia przydziału przez użytkownika.

Aby zilustrować, jak działają nagłówki, przyjrzyjmy się odpowiedzi na `x-ms-user-quota-remaining: 10` `x-ms-user-quota-resets-after: 00:00:03`zapytanie, która ma nagłówek i wartości i .

- W ciągu najbliższych 3 sekund, co najwyżej 10 zapytań mogą być przesyłane bez ograniczania.
- W ciągu 3 `x-ms-user-quota-remaining` sekund, `x-ms-user-quota-resets-after` wartości i `15` `00:00:05` zostaną zresetowane do i odpowiednio.

Aby wyświetlić przykład używania nagłówków do _wycofywania_ żądań kwerend, zobacz przykład w [aplikacji Kwerenda w trybie równoległym](#query-in-parallel).

## <a name="grouping-queries"></a>Grupowanie zapytań

Grupowanie zapytań według subskrypcji, grupy zasobów lub pojedynczego zasobu jest bardziej efektywne niż równoległość zapytań. Koszt przydziału większej kwerendy jest często mniejszy niż koszt przydziału wielu małych i docelowych zapytań. Zaleca się, aby rozmiar grupy był mniejszy niż _300_.

- Przykład słabo zoptymalizowanego podejścia

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Przykład #1 zoptymalizowanego podejścia grupowego

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Przykład #2 zoptymalizowanego podejścia grupowania w celu uzyskania wielu zasobów w jednej kwerendzie

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Rozkładanie zapytań

Ze względu na sposób ograniczania jest wymuszane, zaleca się kwerendy, które mają być rozłożone. Oznacza to, że zamiast wysyłać 60 zapytań w tym samym czasie, rozłożenie zapytań na cztery 5-sekundowe okna:

- Harmonogram kwerend nie z rozłożonych w rozłożenie

  | Liczba zapytań         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Przedział czasu (s) | 0-5 | 5-10 | 10-15 | 15-20 |

- Harmonogram zapytań rozłożonych

  | Liczba zapytań         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Przedział czasu (s) | 0-5 | 5-10 | 10-15 | 15-20 |

Poniżej znajduje się przykład przestrzegania nagłówków ograniczania przepustowości podczas wykonywania zapytań o program Azure Resource Graph:

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

### <a name="query-in-parallel"></a>Kwerenda równolegle

Mimo że grupowanie jest zalecane w przypadku równoległości, istnieją czasy, w których nie można łatwo zgrupować zapytań. W takich przypadkach można podjąć kwerendy usługi Azure Resource Graph, wysyłając wiele zapytań w sposób równoległy. Poniżej znajduje się przykład jak _cofnąć_ na podstawie ograniczania nagłówków w takich scenariuszach:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

## <a name="pagination"></a>Dzielenie na strony

Ponieważ usługa Azure Resource Graph zwraca co najwyżej 1000 wpisów w odpowiedzi na pojedyncze zapytanie, może być konieczne [odtworzenie](./work-with-data.md#paging-results) zapytań na strony, aby uzyskać kompletny zestaw danych, którego szukasz. Jednak niektórzy klienci usługi Azure Resource Graph obsługują podział na strony innego niż inni.

- Zestaw SDK języka C#

  Podczas korzystania z ResourceGraph SDK, należy obsługiwać podział na strony, przekazując token skip zwracany z poprzedniej odpowiedzi kwerendy do następnej kwerendy na strony. Ten projekt oznacza, że musisz zbierać wyniki ze wszystkich połączeń na strony i łączyć je razem na końcu. W takim przypadku każde wysłane zapytanie na strony ma jeden przydział zapytania:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
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

- Azure CLI / Azure PowerShell

  W przypadku korzystania z interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell zapytania do programu Azure Resource Graph są automatycznie nagminnie pobierane co najwyżej 5000 wpisów. Wyniki kwerendy zwracają połączoną listę wpisów ze wszystkich wywołań na strony. W takim przypadku, w zależności od liczby wpisów w wyniku kwerendy, pojedyncza kwerenda na stronie może zużywać więcej niż jeden przydział zapytania. Na przykład w poniższym przykładzie pojedyncze uruchomienie kwerendy może zużywać maksymalnie pięć przydziałów zapytań:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Nadal się dławić?

Jeśli po wykonaniu powyższych zaleceń otrzymujesz ograniczenie, skontaktuj [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)się z zespołem pod adresem .

Podaj następujące szczegóły:

- Twój konkretny przypadek użycia i sterownik biznesowy wymaga wyższego limitu ograniczania przepustowości.
- Do ilu zasobów masz dostęp? Ile z nich jest zwracanych z pojedynczej kwerendy?
- Jakie rodzaje zasobów Cię interesują?
- Jaki jest twój wzorzec zapytania? X zapytania na Y sekundy itp.

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [kwerendach startowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [kwerendach zaawansowanych](../samples/advanced.md).
- Dowiedz się więcej o [eksplorowanie zasobów](explore-resources.md).