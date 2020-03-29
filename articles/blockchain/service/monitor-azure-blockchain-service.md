---
title: Monitorowanie usługi Azure Blockchain Service (ABS)
description: Monitorowanie usługi Azure Blockchain za pośrednictwem usługi Azure Monitor
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293253"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitoruj usługę Azure Blockchain za pośrednictwem usługi Azure Monitor  

Ponieważ klienci uruchamiają scenariusze łańcucha bloków klasy produkcyjnej w usłudze Azure Blockchain Service (ABS), kluczowe znaczenie ma monitorowanie zasobów pod kątem dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania generowane przez usługę Azure Blockchain Service i jak można używać różnych funkcji i integracji usługi Azure Monitor do analizowania i ostrzegania, aby zarządzać środowiskami klasy produkcyjnej.  

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Usługa Azure Blockchain Service tworzy dane monitorowania przy użyciu usługi Azure Monitor, która jest usługą monitorowania pełnego stosu na platformie Azure, która zapewnia pełny zestaw funkcji do monitorowania zasobów platformy Azure. Aby uzyskać więcej informacji na temat usługi Azure Monitor, zobacz [Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource).
 

Poniższe sekcje opierają się na tym artykule, opisując określone dane zebrane z usługi Azure Blockchain Service i podając przykłady konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitorowanie danych zebranych z usługi Azure Blockchain Service  

Usługa Azure Blockchain service zbiera ten sam rodzaj danych monitorowania, co inne zasoby platformy Azure, które są opisane w [obszarze Monitorowanie danych](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) z zasobów platformy Azure. Zobacz [monitorowanie danych usługi Azure Blockchain Service, aby](#monitor-azure-blockchain-service-data-reference) uzyskać szczegółowe informacje na temat dzienników i metryk utworzonych przez usługę Azure Blockchain Service.

Strona omówienia w witrynie Azure portal dla każdego zasobu członkowskiego usługi Azure Blockchain Service zawiera krótki widok transakcji, w tym żądań obsługiwanych i przetworzonych bloków. Niektóre z tych danych są zbierane automatycznie i dostępne do analizy po utworzeniu zasobu członkowskiego usługi Azure Blockchain Service, podczas gdy można włączyć zbieranie dodatkowych danych z dodatkową konfiguracją.

## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne  

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale należy utworzyć ustawienie diagnostyczne do zbierania dzienników zasobów lub przesyłania dalej ich poza usługą Azure Monitor. Zobacz [Tworzenie ustawień diagnostycznych do zbierania dzienników platformy i metryk na platformie Azure, aby](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) uzyskać szczegółowy proces tworzenia ustawienia diagnostycznego przy użyciu portalu Azure, interfejsu wiersza polecenia lub programu PowerShell.

Podczas tworzenia ustawienia diagnostycznego, należy określić, które kategorie dzienników do zbierania. Kategorie usługi Azure Blockchain service są wymienione poniżej.

**Dzienniki proxy blockchain** - Wybierz kategorię, jeśli chcesz monitorować dzienniki proxy NGNIX. Wszystkie szczegóły transakcji odbiorcy są dostępne do celów inspekcji i debugowania.  

**Dzienniki aplikacji blockchain** — wybierz kategorię, aby uzyskać dzienniki aplikacji blockchain hostowane przez usługę zarządzaną. Na przykład dla członka ABS-Kworum te dzienniki będą dzienniki z kworum się.  

**Żądania metryki:** Wybierz opcję, aby zbierać dane metryki z usługi Azure Cosmos DB do miejsc docelowych w ustawieniu diagnostycznym, które są zbierane automatycznie w metrykach platformy Azure. Zbieranie danych metryk za pomocą dzienników zasobów do analizowania obu rodzajów danych razem i do wysyłania danych metryk poza usługą Azure Monitor.

## <a name="analyze-metric-data"></a>Analizowanie danych metrycznych  

Możesz analizować metryki usługi Azure Blockchain za pomocą Eksploratora metryk, przejdź do karty Metryki w sekcji Monitorowanie w bloku zasobów ABS. Zobacz [Wprowadzenie do Eksploratora metryk platformy Azure, aby](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) uzyskać szczegółowe informacje na temat korzystania z narzędzia. Pełne metryki dla usługi Azure Blockchain service znajdują się w metrykach standardowych usługi Azure Blockchain Service w obszarze nazw.

Wymiar **węzła** można użyć podczas dodawania filtru lub dzielenia metryk, które zasadniczo zawiera wartości metryki dla węzłów transakcji i węzłów walidatora członka ABS.

## <a name="analyze-log-data"></a>Analizowanie danych dzienników

Oto kilka zapytań, które można wprowadzić na pasku wyszukiwania dzienników, aby ułatwić monitorowanie członków usługi Azure Blockchain Service. Te zapytania działają z [nowym językiem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Aby zbadać warunki błędu w dziennikach aplikacji Blockchain, użyj poniższej kwerendy:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Aby zbadać warunki błędu w dziennikach proxy łańcucha bloków, użyj poniższej kwerendy  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Filtry czasu dostępne w dziennikach platformy Azure umożliwiają filtrowanie kwerendy dla określonego zakresu czasu.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Monitorowanie danych usługi Azure Blockchain Service  

Ten artykuł zawiera odwołanie do danych dziennika i metryki zebranych do analizy wydajności i dostępności usługi Azure Blockchain.  

### <a name="resource-logs"></a>Dzienniki zasobów

Wszystkie dzienniki zasobów współużytkują wspólny schemat najwyższego poziomu z kilkoma unikatowymi właściwościami specyficznymi dla usługi blockchain. Poniżej znajduje się artykuł Schemat [dzienników zasobów najwyższego poziomu](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema), szczegóły dotyczące określonych właściwości usługi Azure Blockchain Service są omówione poniżej  

W poniższej tabeli wymieniono właściwości dzienników serwera proxy usługi Azure Blockchain, gdy są one zbierane w dziennikach usługi Azure Monitor lub usłudze Azure Storage.  


| Nazwa właściwości  | Opis |
|:---|:---|
| time | Data i godzina (UTC) po wykonaniu operacji. |
| Resourceid  | Zasób usługi Azure Blockchain Service, dla którego są włączone dzienniki.  |
| category  |W przypadku usługi Azure Blockchain wartościami, które są możliwe, są **dzienniki proxy** i **applicationlogs**. |
| operationName  | Nazwa operacji reprezentowanej przez to zdarzenie.   |
| Poziom dziennika  | Domyślnie usługa Azure Blockchain Service umożliwia poziom dziennika **informacji.**   |
| Lokalizacja węzła  | Region platformy Azure, w którym wdrożono element członkowski łańcucha bloków.  |
| Nazwa łańcucha bloków  | Nazwa węzła członka usługi Azure Blockchain, na którym wykonywana jest operacja.   |
| Etretoda  | Metoda, która jest wywoływana przez podstawowy protokół blockchain, w Kworum, może być eth_sendTransactions, eth_getBlockByNumber itp.  |
| Agent  | Agent użytkownika, który działa w imieniu użytkownika, takich jak przeglądarka internetowa Mozilla, Edge itp. Przykładami wartości są: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| Code   | kody błędów HTTP. Zazwyczaj 4XX i 5XX są warunki błędu.  |
| WęzełHost  | Nazwa DNS węzła.   |
| Nazwa pliku RequestMethodName | Metoda HTTP wywoływana, możliwe wartości w tym miejscu są PUT dla create member, GET do uzyskania szczegółów istniejącego elementu członkowskiego, DELETE dla usuń członka, PATCH do aktualizacji elementu członkowskiego.   |
| Nazwanika łańcucha bloków  | Nazwa członka usługi Azure Blockchain Service podana przez użytkownika.  |
| Konsorcjum | Nazwa konsorcjum podana przez użytkownika.   |
| Remote  | Adres IP klienta, do którego nadchodzi żądanie.  |
| Rozmiar żądania  | Rozmiar żądania złożonego w bajtach.  |
| Czas żądania  | Czas trwania żądania w milisekundach.|




W poniższej tabeli wymieniono właściwości dzienników aplikacji azure blockchain.


| Nazwa właściwości  | Opis |
|:---|:---|
| time | Data i godzina (UTC) po wykonaniu operacji. |
| Resourceid  | Zasób usługi Azure Blockchain Service, dla którego są włączone dzienniki.|
| category  |W przypadku usługi Azure Blockchain możliwa wartość to **proxylogi** i **dzienniki aplikacji.**  |
| operationName  | Nazwa operacji reprezentowanej przez to zdarzenie.   |
| Poziom dziennika  | Domyślnie usługa Azure Blockchain Service umożliwia poziom dziennika **informacji.**   |
| Lokalizacja węzła  | Region platformy Azure, w którym wdrożono element członkowski łańcucha bloków.  |
| Nazwa łańcucha bloków  | Nazwa węzła członka usługi Azure Blockchain, na którym wykonywana jest operacja.   |
| BlockchainMessage (BlockchainMessage)    | To pole będzie zawierać dziennik aplikacji Blockchain, który jest dziennikami zwykłych danych. W przypadku ABS-Kworum miałoby to dzienniki kworum. Zawiera informacje o tym, jaki typ wpisu dziennika jest to, że jest informacyjny, błąd, ostrzeżenie i ciąg, który daje więcej informacji na temat akcji wykonywane.   |
| Identyfikator dzierżawy    | Dzierżawa usługi Azure Blockchain w poszczególnych regionach. Formatem tego pola https://westlake-rp-prodjest . <region>.cloudapp.azure.com, w którym region określa region platformy Azure wdrożonego elementu członkowskiego.       |
| SourceSystem   | System wypełnia dzienniki, w tym przypadku jest to **Azure**.    |



### <a name="metrics"></a>Metryki

W poniższych tabelach wymieniono metryki platformy zebrane dla usługi Azure Blockchain Service. Wszystkie metryki są przechowywane w metrykach standardowych **usługi Azure Blockchain Service** w obszarze nazw.

Aby uzyskać listę wszystkich obsługiwanych metryk usługi Azure Monitor (w tym usługi Azure Blockchain Service), zobacz [metryki obsługiwane przez usługę Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)

### <a name="blockchain-metrics"></a>Dane blockchain

Poniższa tabela określa listę metryk łańcucha bloków, które są zbierane dla zasobu członkowskiego usługi Azure Blockchain Service.


| Nazwa metryki | Jednostka  |  Typ agregacji| Opis   |
|---|---|---|---|
| Oczekujące transakcje   | Liczba  |  Średnia | Liczba transakcji oczekujących na wydobycie.   |
| Przetworzone bloki   | Liczba  | Suma  |  Liczba bloków przetwarzanych w każdym przedziale czasu. Obecnie rozmiar bloku wynosi 5 sekund, dlatego w ciągu minuty każdy węzeł przetworzy 12 bloków i 60 bloków w 5 minut.   |
|Przetworzone transakcje    | Liczba  | Suma  | Liczba transakcji przetworzonych w bloku.    |
|Transakcje w kolejce    |  Liczba | Średnia  | Liczba transakcji, których nie można natychmiast wydobyć. Może to być spowodowane tym, że przybyli poza kolejnością, a przyszła czeka na przedejście poprzedniej transakcji. Lub może to być dwie transakcje mają ten sam numer używany tylko raz (nonce) i tej samej wartości gazu, stąd druga nie może być wydobywana.   |

### <a name="connection-metrics"></a>Metryki połączeń  

W poniższej tabeli wymieniono różne metryki połączenia, które są zbierane dla zasobu członkowskiego usługi Azure Blockchain Service. Są to metryki serwera proxy NGINX.


| Nazwa metryki | Jednostka  |  Typ agregacji| Opis |
|---|---|---|---|
| Akceptowane połączenia   | Liczba  |  Suma | Całkowita liczba zaakceptowanych połączeń klientów.   |
| Aktywne połączenia  | Liczba  | Średnia  |  Bieżąca liczba aktywnych połączeń klientów, w tym połączenia oczekujące.    |
|Obsługiwane połączenia    | Liczba  | Suma  | Całkowita liczba obsługiwanych połączeń. Ogólnie rzecz biorąc wartość parametru jest taka sama jak akceptowane połączenia, chyba że pewne limity zasobów zostały osiągnięte.     |
|Obsługiwane żądania     |  Liczba | Suma  | Całkowita liczba żądań klientów.  |


### <a name="performance-metrics"></a>Metryki wydajności

W poniższej tabeli wymieniono metryki wydajności, które są zbierane dla każdego z węzłów zasobu członkowskiego łańcucha bloków platformy Azure.  


| Nazwa metryki | Jednostka  |  Typ agregacji| Opis   |
|---|---|---|---|
| Procent użycia procesora CPU   | Procentowe  |  Maks. | Procent użycia procesora CPU.     |
| Bajty odczytu we/wy   | Kilobajtów   | Suma  |  Suma bajtów odczytu we/wy we wszystkich węzłach zasobu członkowskiego łańcucha bloków.      |
|Bajty zapisu we/wy     | Kilobajtów   | Suma  | Suma we/wy zapisuje bajty we wszystkich węzłach zasobu członkowskiego łańcucha bloków.     |
|Limit pamięci       |  Gigabajtów   | Średnia    | Maksymalna ilość pamięci dostępnej dla procesu łańcucha bloków na węzeł. |
|Użycie pamięci     | Gigabajtów  |  Średnia | Ilość używanej pamięci uśredniona we wszystkich węzłach.  |
| Procent użycia pamięci     | Procentowe   | Średnia  |  Procent używanej pamięci uśredniona we wszystkich węzłach.       |
|Użycie pamięci masowej      | Gigabajtów   | Średnia  | GB używanego miejsca do magazynowania uśrednił we wszystkich węzłach.       |


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) do przechwytywania i przekształcania danych łańcucha bloków do usługi Azure Event Grid.
