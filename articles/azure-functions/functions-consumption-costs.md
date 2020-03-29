---
title: Szacowanie kosztów planu zużycia w usłudze Azure Functions
description: Dowiedz się, jak lepiej oszacować koszty, które mogą być ponoszone podczas uruchamiania aplikacji funkcji w planie zużycia na platformie Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963992"
---
# <a name="estimating-consumption-plan-costs"></a>Szacowanie kosztów planu zużycia

Obecnie istnieją trzy typy planów hostingu dla aplikacji, która działa w usłudze Azure Functions, przy czym każdy plan ma swój własny model cenowy: 

| Planowanie | Opis |
| ---- | ----------- |
| [**Zużycie**](functions-scale.md#consumption-plan) | Opłata jest naliczana tylko za czas działania aplikacji funkcji. Ten plan zawiera[bezpłatną stronę cen] [dotacji]na podstawie subskrypcji.|
| [**Premium**](functions-scale.md#premium-plan) | Zapewnia te same funkcje i mechanizm skalowania jako plan zużycia, ale z zwiększoną wydajnością i dostępem do sieci wirtualnej. Koszt zależy od wybranej warstwy cenowej. Aby dowiedzieć się więcej, zobacz [Plan usługi Azure Functions Premium](functions-premium-plan.md). |
| [**Dedykowane (usługa aplikacji)**](functions-scale.md#app-service-plan) <br/>(podstawowy poziom lub wyższy) | Jeśli musisz uruchomić w dedykowanych maszynach wirtualnych lub w izolacji, użyj obrazów niestandardowych lub chcesz użyć nadmiaru pojemności planu usługi App Service. Używa [zwykłych rozliczeń planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/). Koszt zależy od wybranej warstwy cenowej.|

Wybrano plan, który najlepiej obsługuje wydajność funkcji i wymagania kosztowe. Aby dowiedzieć się więcej, zobacz [Skala i hosting usług Azure Functions](functions-scale.md).

Ten artykuł dotyczy tylko planu zużycia, ponieważ ten plan powoduje koszty zmienne. W tym artykule zastępuje się często zadawanymi [pytaniami dotyczącymi rozliczania kosztów planu zużycia.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

Funkcje trwałe można również uruchomić w planie zużycia. Aby dowiedzieć się więcej o kwestiach związanych z kosztami podczas korzystania z funkcji trwałych, zobacz [Rozliczanie funkcji trwałych](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Koszty planu zużycia

*Koszt* wykonania wykonania pojedynczej funkcji jest mierzony w *GB sekund.* Koszt wykonania jest obliczany przez połączenie jego użycia pamięci z czasem wykonywania. Funkcja, która działa dłużej kosztuje więcej, podobnie jak funkcja, która zużywa więcej pamięci. 

Należy wziąć pod uwagę przypadek, w którym ilość pamięci używanej przez funkcję pozostaje stała. W takim przypadku obliczanie kosztu jest proste mnożenie. Na przykład załóż, że funkcja zużywała 0,5 GB przez 3 sekundy. Następnie koszt wykonania `0.5GB * 3s = 1.5 GB-seconds`jest . 

Ponieważ użycie pamięci zmienia się wraz z czasem, obliczenia są zasadniczo integralną częścią użycia pamięci w czasie.  System wykonuje to obliczenie, pobierając próbki użycia pamięci procesu (wraz z procesami podrzędnymi) w regularnych odstępach czasu. Jak wspomniano na [stronie cennika,]użycie pamięci jest zaokrąglane w górę do najbliższego zasobnika o rozmiarze 128 MB. Gdy proces jest używany 160 MB, opłata jest naliczana za 256 MB. Obliczenia uwzględnia współbieżność, która jest wiele równoczesnych wykonań funkcji w tym samym procesie.

> [!NOTE]
> Podczas gdy użycie procesora CPU nie jest bezpośrednio brane pod uwagę w koszcie wykonania, może mieć wpływ na koszt, gdy wpływa na czas wykonywania funkcji.

## <a name="other-related-costs"></a>Inne powiązane koszty

Podczas szacowania całkowity koszt uruchamiania funkcji w dowolnym planie, należy pamiętać, że funkcja środowiska wykonawczego używa kilku innych usług platformy Azure, które są rozliczane oddzielnie. Podczas obliczania cen dla aplikacji funkcji, wszelkie wyzwalacze i powiązania, które masz, które integrują się z innymi usługami platformy Azure wymagają tworzenia i płacenia za te dodatkowe usługi. 

W przypadku funkcji uruchomionych w planie zużycia całkowity koszt jest kosztem wykonania funkcji, a także kosztem przepustowości i dodatkowych usług. 

Podczas szacowania całkowitych kosztów aplikacji funkcji i powiązanych usług użyj [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Powiązany koszt | Opis |
| ------------ | ----------- |
| **Konto magazynu** | Każda aplikacja funkcji wymaga, aby mieć skojarzone [konto usługi Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts)ogólnego przeznaczenia, które jest [rozliczane oddzielnie.](https://azure.microsoft.com/pricing/details/storage/) To konto jest używane wewnętrznie przez środowisko wykonawcze Functions, ale można go również używać dla wyzwalaczy magazynu i powiązań. Jeśli nie masz konta magazynu, zostanie utworzone podczas tworzenia aplikacji funkcji. Aby dowiedzieć się więcej, zobacz [Wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Funkcje opiera się na [usługi Application Insights,](../azure-monitor/app/app-insights-overview.md) aby zapewnić środowisko monitorowania wysokiej wydajności dla aplikacji funkcji. Chociaż nie jest to wymagane, należy [włączyć integrację usługi Application Insights](functions-monitoring.md#enable-application-insights-integration). Bezpłatna dotacja danych telemetrycznych jest uwzględniana co miesiąc. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Przepustowość sieci** | Nie płacisz za transfer danych między usługami platformy Azure w tym samym regionie. Jednak można ponieść koszty wychodzących transferów danych do innego regionu lub poza platformą Azure. Aby dowiedzieć się więcej, zobacz [Szczegóły cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Zachowania wpływające na czas wykonywania

Następujące zachowania funkcji może mieć wpływ na czas wykonywania:

+ **Wyzwalacze i powiązania:** Czas do odczytu danych wejściowych i zapisu danych [wyjściowych](functions-triggers-bindings.md) do powiązania funkcji jest liczony jako czas wykonywania. Na przykład gdy funkcja używa powiązania danych wyjściowych do zapisu wiadomości do kolejki magazynu platformy Azure, czas wykonywania obejmuje czas przeznaczony do zapisania wiadomości do kolejki, który jest uwzględniony w obliczaniu kosztu funkcji. 

+ **Wykonanie asynchroniczne:** czas oczekiwania funkcji na wyniki żądania asynchronii (w`await` języku C#) jest liczony jako czas wykonywania. Obliczanie GB-second opiera się na godzinie rozpoczęcia i zakończenia funkcji i użycia pamięci w tym okresie. To, co dzieje się w tym czasie pod względem aktywności procesora, nie jest uwzględniane w obliczeniach. Można zmniejszyć koszty podczas operacji asynchronicznych przy użyciu [funkcji trwałych](durable/durable-functions-overview.md). Nie są rozliczane za czas spędzony w awaits w funkcji orkiestratora.

## <a name="view-execution-data"></a>Wyświetlanie danych wykonania

Na [fakturze](/azure/billing/billing-download-azure-invoice)można wyświetlić dane związane z kosztami **łącznych wykonań - Funkcje** i **Czas wykonania - Funkcje**, wraz z rzeczywistymi kosztami naliczanych. Jednak te dane faktury są agregacji miesięcznej dla okresu faktury za poprzednie. 

Aby lepiej zrozumieć wpływ na koszty funkcji, można użyć usługi Azure Monitor do wyświetlania metryk związanych z kosztami generowanych obecnie przez aplikacje funkcji. Aby uzyskać te dane, można użyć [eksploratora metryk usługi Azure Monitor](../azure-monitor/platform/metrics-getting-started.md) w [witrynie Azure portal] lub interfejsów API REST.

### <a name="monitor-metrics-explorer"></a>Monitoruj eksploratora metryk

Użyj [Eksploratora metryk usługi Azure Monitor,](../azure-monitor/platform/metrics-getting-started.md) aby wyświetlić dane związane z kosztami dla aplikacji funkcji planu zużycia w formacie graficznym. 

1. U góry [witryny Azure portal] w **usługach wyszukiwania, zasobów i dokumentów** wyszukuj `monitor` i wybierz pozycję Monitor **w** obszarze **Usługi**.

1. Po lewej stronie wybierz **pozycję Metryki** > **Wybierz zasób,** a następnie użyj ustawień poniżej obrazu, aby wybrać aplikację funkcyjną.

    ![Wybieranie zasobu aplikacji funkcji](media/functions-consumption-costing/select-a-resource.png)

      
    |Ustawienie  |Sugerowana wartość  |Opis  |
    |---------|---------|---------|
    | Subskrypcja    |  Twoja subskrypcja  | Subskrypcja za pomocą aplikacji funkcji.  |
    | Grupa zasobów     | Twoja grupa zasobów  | Grupa zasobów zawierająca aplikację funkcji.   |
    | Typ zasobu     |  App Services | Aplikacje funkcyjne są wyświetlane jako wystąpienia usług aplikacji w Monitorze. |
    | Zasób     |  Twoja aplikacja funkcji  | Aplikacja funkcji do monitorowania.        |

1. Wybierz pozycję **Zastosuj,** aby wybrać aplikację funkcyjną jako zasób do monitorowania.

1. Z **metryki**wybierz pozycję **Liczba wykonania funkcji** i **Suma** dla **agregacji**. Spowoduje to dodanie sumy liczby wykonań w wybranym okresie do wykresu.

    ![Definiowanie metryki aplikacji funkcji do dodania do wykresu](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Wybierz **dodaj metrykę** i powtórz kroki 2-4, aby dodać **jednostki wykonywania funkcji** do wykresu. 

Wynikowy wykres zawiera sumy dla obu metryk wykonywania w wybranym zakresie czasu, który w tym przypadku jest dwie godziny.

![Wykres liczby wykonań funkcji i jednostek wykonawczych](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Ponieważ liczba jednostek wykonania jest o wiele większa niż liczba wykonania, wykres pokazuje tylko jednostki wykonania.

Ten wykres pokazuje łącznie 1,11 miliarda `Function Execution Units` zużytych w okresie dwóch godzin, mierzonych w mb-milisekundach. Aby przekonwertować na GB-sekundy, podziel przez 1024000. W tym przykładzie aplikacja `1110000000 / 1024000 = 1083.98` funkcji używane GB sekund. Możesz wziąć tę wartość i pomnożyć przez bieżącą cenę czasu wykonania na[stronie cennik] [funkcji,]co daje koszt tych dwóch godzin, zakładając, że użyłeś już żadnych bezpłatnych dotacji czasu wykonania. 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Narzędzie [interfejsu wiersza polecenia platformy Azure](/cli/azure/) ma polecenia do pobierania metryk. Funkcji interfejsu wiersza polecenia można używać z lokalnego środowiska poleceń lub bezpośrednio z portalu przy użyciu [usługi Azure Cloud Shell](../cloud-shell/overview.md). Na przykład następujące polecenie [az monitor metryki zwraca](/cli/azure/monitor/metrics#az-monitor-metrics-list) dane godzinowe w tym samym okresie czasu używanym wcześniej.

Pamiętaj, aby `<AZURE_SUBSCRIPTON_ID>` zastąpić identyfikator subskrypcji platformy Azure z uruchomionym poleceniem.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

To polecenie zwraca ładunek JSON, który wygląda jak w poniższym przykładzie:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Ta szczególna `2019-09-11T21:46` `2019-09-11T23:18`odpowiedź pokazuje, że od do , aplikacja używane 1110000000 MB-milisekundy (1083.98 GB sekund).

## <a name="determine-memory-usage"></a>Określanie użycia pamięci

Jednostki wykonywania funkcji są kombinacją czasu wykonywania i użycia pamięci, co sprawia, że jest to trudna metryka dla zrozumienia użycia pamięci. Dane pamięci nie są metryką obecnie dostępną za pośrednictwem usługi Azure Monitor. Jednak jeśli chcesz zoptymalizować użycie pamięci aplikacji, można użyć danych licznika wydajności zebranych przez usługa Application Insights.  

Jeśli jeszcze tego nie zrobiono, [włącz usługę Application Insights w aplikacji funkcji](functions-monitoring.md#enable-application-insights-integration). Po włączeniu tej integracji można [zbadać te dane telemetryczne w portalu](functions-monitoring.md#query-telemetry-data).  

W obszarze **Monitorowanie**wybierz **pozycję Dzienniki (Analytics)** następnie skopiuj następującą kwerendę telemetryczną i wklej ją do okna kwerendy i wybierz pozycję **Uruchom**. Ta kwerenda zwraca całkowite użycie pamięci w każdym próbkowaniu.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Wyniki wyglądają jak w poniższym przykładzie:

| sygnatura czasowa \[UTC\]          | name          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 AM | Bajty prywatne | 209,932,288 |
| 9/12/2019, 1:06:14\.994 AM | Bajty prywatne | 212,189,184 |
| 9/12/2019, 1:06:30\.010 AM | Bajty prywatne | 231,714,816 |
| 9/12/2019, 1:07:15\.040 AM | Bajty prywatne | 210,591,744 |
| 9/12/2019, 1:12:16\.285 AM | Bajty prywatne | 216,285,184 |
| 9/12/2019, 1:12:31\.376 AM | Bajty prywatne | 235,806,720 |

## <a name="function-level-metrics"></a>Metryki na poziomie funkcji

Usługa Azure Monitor śledzi metryki na poziomie zasobów, który dla funkcji jest aplikacją funkcji. Integracja usługi Application Insights emituje metryki na podstawie funkcji. Oto przykładowe zapytanie analityczne, aby uzyskać średni czas trwania funkcji:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | średniaDurationMilliseconds |
|----------------------------|-----------------------------|
| KolejkiTrętowanie 200000 | 16\.087                     |
| KolejkiTrętarka MaxDurationMs | 90\.249                     |
| MinDurationM queueTrigger | 8\.522                      |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o aplikacjach funkcji monitorowania](functions-monitoring.md)

[strona z cenami]:https://azure.microsoft.com/pricing/details/functions/
[Portal Azure]: https://portal.azure.com
