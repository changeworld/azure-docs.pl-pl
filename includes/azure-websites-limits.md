| Zasób | Bezpłatna | Współdzielona (wersja zapoznawcza) | Podstawowa | Standardowa (Standard) | Premium </th> |
| --- | --- | --- | --- | --- | --- |
| [Aplikacje internetowe, mobilne i interfejsu API](https://azure.microsoft.com/services/app-service/) na [planu usługi App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Nieograniczona liczba<sup>2</sup> |Nieograniczona liczba<sup>2</sup> |Nieograniczona liczba<sup>2</sup> |
| [Aplikacje logiki](https://azure.microsoft.com/services/app-service/logic/) na [planu usługi App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 na każdy rdzeń |20 na każdy rdzeń |
| [Plan usługi App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 na region |10 dla grupy zasobów |100 dla każdej grupy zasobów |100 dla każdej grupy zasobów |100 dla każdej grupy zasobów |
| Typ wystąpienia obliczeniowe |Udostępniona |Udostępniona |W wersji dedykowanej<sup>3</sup> |W wersji dedykowanej<sup>3</sup> |W wersji dedykowanej<sup>3</sup></p> |
| [Skalowalny w poziomie](../articles/app-service/web-sites-scale.md) (maksymalna liczba wystąpień) |1 udostępnionego |1 udostępnionego |3 w wersji dedykowanej<sup>3</sup> |10 w wersji dedykowanej<sup>3</sup> |(50 w środowisku ASE) w wersji dedykowanej 20<sup>3,4</sup> |
| Magazyn<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| Czas Procesora (5 min)<sup>6</sup> |3 minuty |3 minuty |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność zgodnie ze standardowymi stawkami |Bez ograniczeń, płatność zgodnie ze standardowymi stawkami |
| Czas Procesora (dzień)<sup>6</sup> |60 minut |240 minut |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność zgodnie ze standardowymi stawkami |Bez ograniczeń, płatność zgodnie ze standardowymi stawkami |
| Pamięć (1 godzina) |1024 MB na plan usługi App Service |1024 MB dla aplikacji |ND |ND |ND |
| Przepustowość |165 MB |Nieograniczona [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) zastosowania |Bez ograniczeń, transfer danych, które obowiązują stawki jak za |Bez ograniczeń, transfer danych, które obowiązują stawki jak za |Bez ograniczeń, transfer danych, które obowiązują stawki jak za |
| Architektura aplikacji |32-bitowa |32-bitowa |32-bitowy/64-bitowy |32-bitowy/64-bitowy |32-bitowy/64-bitowy |
| Za wystąpienie usługi sieci Web Sockets<sup>7</sup> |5 |35 |350 |Nieograniczona liczba |Nieograniczona liczba |
| Współbieżne [debugera połączeń](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) na aplikację |1 |1 |1 |5 |5 |
| [Poddomena azurewebsites.NET z protokołem SSL i protokołu FTP/S](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Domena niestandardowa](../articles/app-service/app-service-web-tutorial-custom-domain.md) pomocy technicznej | |X |X |X |X |
| Domena niestandardowa [Obsługa protokołu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Nieograniczone połączenia SNI SSL |Nieograniczone połączenia SNI SSL i 1 połączenie IP SSL połączeń |Nieograniczone połączenia SNI SSL i 1 połączenie IP SSL połączeń |
| Zintegrowana usługa równoważenia obciążenia | |X |X |X |X |
| [Zawsze włączone](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Zaplanowane kopie zapasowe](../articles/app-service/web-sites-backup.md) | | | | Zaplanowane kopie zapasowe co 2 godziny, maksymalnie 12 kopii zapasowych dziennie (Ręczne + zaplanowane) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (Ręczne + zaplanowane) |
| [Automatyczne skalowanie](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [Zadania Webjob](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Usługa Azure Scheduler](https://azure.microsoft.com/services/scheduler/) pomocy technicznej | |X |X |X |X |
| [Monitorowanie punktu końcowego](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Miejsca przejściowe](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Domeny niestandardowe na aplikację</a> | |500 |500 |500 |500 |
| Umowa SLA | |<p> |99,9% |99.95%<sup>10</sup> |99.95%<sup>9</sup> |

<sup>1</sup>aplikacji i limitami przydziału magazynów są na plan usługi App Service, chyba że zaznaczono inaczej.  
<sup>2</sup>rzeczywista liczba aplikacje obsługujące na tych maszynach zależy działanie aplikacji, rozmiaru wystąpienia maszyny i odpowiednie wykorzystanie zasobów.  
<sup>3</sup>dedykowane wystąpienia mogą mieć różne rozmiary. Zobacz [App Service — ceny](https://azure.microsoft.com/pricing/details/app-service/) Aby uzyskać więcej informacji.  
<sup>4</sup>w warstwie premium zapewnia do 50 oblicza wystąpienia (zależnie od dostępności) oraz 500 GB miejsca na dysku, przy użyciu środowisk usługi App Service i 20 wystąpień obliczeniowych i 250 GB miejsca inny sposób.  
<sup>5</sup>limit magazynu jest łączny rozmiar zawartości dla wszystkich aplikacji w ten sam plan usługi App Service. Więcej opcji magazynu są dostępne w [środowiska App Service Environment](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>te zasoby są ograniczone przez zasoby fizyczne na dedykowanych wystąpieniach (rozmiar wystąpienia i liczby wystąpień).  
<sup>7</sup>skalowanie aplikacji w warstwie podstawowa do dwóch wystąpień, masz 350 równoczesnych połączeń dla każdego z dwóch wystąpień.  
<sup>8</sup>uruchamiaj niestandardowe pliki wykonywalne i/lub skrypty na żądanie, według harmonogramu albo w sposób ciągły jako zadanie w tle w ramach usługi App Service wystąpienia usługi. Do ciągłego wykonywania zadań w sieci Web jest wymagana opcja Zawsze włączona. Do wykonywania zadań w sieci Web według harmonogramu jest wymagana usługa Azure Scheduler w warstwie Bezpłatna lub Standardowa. Nie ma wstępnie zdefiniowanego limitu liczby zadań Webjob, która może działać w wystąpieniu usługi App Service, ale istnieją praktycznych limitów, które są zależne od kodu aplikacji próbuje zrobić.   
<sup>9</sup>SLA 99,95% udostępniana w przypadku wdrożeń korzystających z wielu wystąpień z usługi Azure Traffic Manager skonfigurowaną dla trybu failover.  

