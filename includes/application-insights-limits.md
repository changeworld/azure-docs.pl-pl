Istnieją pewne ograniczenia liczby metryk i zdarzeń na aplikację (czyli na klucz instrumentacji). Ograniczenia zależą od wybranego [planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

| Zasób | Limit domyślny | Uwaga
| --- | --- | --- |
| Łączna ilość danych na dzień | 100 GB | Ilość danych możesz zmniejszyć, ustawiając limit. Jeśli potrzebujesz większej ilości danych, należy zwiększyć limit w portalu do 1000 GB. O pojemności maksymalnej ponad 1000 GB, wysyłania wiadomości e-mail do AIDataCap@microsoft.com.
| Ilość bezpłatnych danych na miesiąc<br/> (Podstawowe planu cenowego) | 1 GB | Opłaty za dodatkowe dane są naliczane za gigabajt.
| Ograniczanie przepływności | Zdarzenia 32 KB/s | Limit jest mierzony przez minutę.
| Przechowywanie danych | 90 dni | Ten zasób jest przeznaczony na potrzeby [wyszukiwania](../articles/application-insights/app-insights-diagnostic-search.md), [analizy](../articles/application-insights/app-insights-analytics.md) i [Eksploratora metryk](../articles/application-insights/app-insights-metrics-explorer.md).
| Przechowywanie szczegółowych wyników [wieloetapowego testu dostępności](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 dni | Ten zasób zapewnia szczegółowe wyniki każdego kroku.
| Maksymalnego rozmiaru zdarzenia | 64 K | 
| Długość nazwy właściwości i metryki | 150 | Zobacz [wpisz schematy](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość ciągu wartości właściwości | 8192 | Zobacz [wpisz schematy](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość komunikatu śledzenia i wyjątku | 10 K | Zobacz [wpisz schematy](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Liczba [testów dostępności](../articles/application-insights/app-insights-monitor-web-app-availability.md) na aplikację | 100 |
| [Profiler](../articles/application-insights/app-insights-profiler.md) przechowywania danych | 5 dni |
| [Profiler](../articles/application-insights/app-insights-profiler.md) danych wysyłanych dziennie | 10 GB |

Aby uzyskać informacje, zobacz [Informacje o zarządzaniu cenami i limitami przydziału dla usługi Application Insights](../articles/application-insights/app-insights-pricing.md).

