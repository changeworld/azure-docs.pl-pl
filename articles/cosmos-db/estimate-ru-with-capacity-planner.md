---
title: Szacowanie kosztów przy użyciu planowania pojemności usługi Azure Cosmos DB
description: Planista pojemności usługi Azure Cosmos DB umożliwia oszacowanie przepływności (RU/s) wymaganej i kosztu obciążenia. W tym artykule opisano sposób używania nowej wersji planisty zdolności produkcyjnych do szacowania przepływności i wymaganych kosztów.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707632"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Szacowanie usług RU/s przy użyciu planowania pojemności usługi Azure Cosmos DB

Konfigurowanie baz danych i kontenerów usługi Azure Cosmos z odpowiednią ilością aprowizowanej przepływności lub [jednostek żądań (RU/s)](request-units.md)dla obciążenia ma zasadnicze znaczenie dla optymalizacji kosztów i wydajności. W tym artykule opisano, jak użyć [planowania pojemności usługi](https://cosmos.azure.com/capacitycalculator/) Azure Cosmos DB, aby uzyskać oszacowanie wymaganych usług RU/s i kosztów obciążenia. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Jak oszacować przepływność i koszt za pomocą planowania pojemności usługi Azure Cosmos DB

Planer pojemności może być używany w dwóch trybach.

|**Tryb**  |**Opis**  |
|---------|---------|
|Podstawowa (Basic)|Zapewnia szybkie, wysokiego poziomu RU / s i kosztorys. W tym trybie przyjęto domyślne ustawienia usługi Azure Cosmos DB dla zasad indeksowania, spójności i innych parametrów. <br/><br/>Użyj trybu podstawowego do szybkiego szacowania wysokiego poziomu podczas oceny potencjalnego obciążenia do uruchomienia w usłudze Azure Cosmos DB.|
|Zaawansowane|Zapewnia bardziej szczegółowe ru/s i kosztoszustradywność, z możliwością dostrojenia dodatkowych ustawień — indeksowania zasad, poziomu spójności i innych parametrów, które wpływają na koszt i przepływność. <br/><br/>Tryb zaawansowany należy używać podczas szacowania ru/s dla nowego projektu lub chcesz bardziej szczegółowego oszacowania. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Szacowanie aprowizowanej przepływności i kosztów w trybie podstawowym
Aby szybko oszacować obciążenie za pomocą trybu podstawowego, przejdź do [programu planerów pojemności](https://cosmos.azure.com/capacitycalculator/). Wprowadź następujące parametry na podstawie obciążenia: 

|**Dane wejściowe**  |**Opis**  |
|---------|---------|
|Liczba regionów|Usługa Azure Cosmos DB jest dostępna we wszystkich regionach platformy Azure. Wybierz liczbę regionów wymaganych dla obciążenia. Możesz skojarzyć dowolną liczbę regionów z kontem usługi Cosmos. Zobacz [dystrybucję globalną](distribute-data-globally.md) w usłudze Azure Cosmos DB, aby uzyskać więcej informacji.|
|Zapisy w wielu regionach|Jeśli [włączysz zapisy wieloregionowe,](distribute-data-globally.md#key-benefits-of-global-distribution)aplikacja może odczytywać i zapisywać w dowolnym regionie platformy Azure. Jeśli wyłączysz zapisy wieloregionowe, aplikacja może zapisywać dane w jednym regionie. <br/><br/> Włącz zapisy wieloregionowe, jeśli oczekujesz, że obciążenie jest aktywne i aktywne, które wymaga zapisu o małym opóźnieniu w różnych regionach. Na przykład obciążenia IOT, który zapisuje dane do bazy danych w dużych ilościach w różnych regionach. <br/><br/> Zapisy wieloregionowe gwarantują dostępność odczytu i zapisu w 99,999%. Zapisy w wielu regionach wymagają większej przepływności w porównaniu do regionów pojedynczego zapisu. Aby dowiedzieć się więcej, zobacz, [jak jednostek SU różnią się dla regionów pojedynczego i wielu zapisu](optimize-cost-regions.md) artykułu.|
|Łączna liczba przechowywanych danych (na region)|Łączna szacunkowa ilość danych przechowywanych w GB w jednym regionie.|
|Rozmiar elementu|Szacowany rozmiar elementu danych (np. dokumentu), od 1 KB do 2 MB. |
|Odczyty/s na region|Liczba odczytów oczekiwanych na sekundę. |
|Zapisy/s na region|Liczba zapisów oczekiwanych na sekundę. |

Po wypełnieniu wymaganych szczegółów wybierz opcję **Oblicz**. Karta **Szacowanie kosztów** zawiera całkowity koszt magazynowania i aprowizowaną przepływność. Można rozwinąć **pokaż szczegóły** łącza w tej karcie, aby uzyskać podział przepływności wymagane dla odczytu i zapisu żądań. Za każdym razem, gdy zmieniasz wartość dowolnego pola, wybierz pozycję **Oblicz,** aby ponownie obliczyć szacowany koszt. 

![Tryb podstawowy planowania pojemności](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Szacowanie aprowizowanej przepływności i kosztów w trybie zaawansowanym

Tryb zaawansowany pozwala na zapewnienie większej liczby ustawień, które mają wpływ na oszacowanie RU/s. Aby użyć tej opcji, przejdź do [planisty pojemności](https://cosmos.azure.com/capacitycalculator/) i zaloguj się do narzędzia za pomocą konta używanego na platformie Azure. Opcja logowania jest dostępna w prawym rogu. 

Po zalogowaniu się można wyświetlić dodatkowe pola w porównaniu z polami w trybie podstawowym. Wprowadź dodatkowe parametry na podstawie obciążenia. 

|**Dane wejściowe**  |**Opis**  |
|---------|---------|
|interfejs API|Usługa Azure Cosmos DB to usługa wielomodelowa i usługa z wieloma interfejsami API. W przypadku nowych obciążeń wybierz interfejs API JĘZYKA SQL (Core). |
|Liczba regionów|Usługa Azure Cosmos DB jest dostępna we wszystkich regionach platformy Azure. Wybierz liczbę regionów wymaganych dla obciążenia. Możesz skojarzyć dowolną liczbę regionów z kontem usługi Cosmos. Zobacz [dystrybucję globalną](distribute-data-globally.md) w usłudze Azure Cosmos DB, aby uzyskać więcej informacji.|
|Zapisy w wielu regionach|Jeśli [włączysz zapisy wieloregionowe,](distribute-data-globally.md#key-benefits-of-global-distribution)aplikacja może odczytywać i zapisywać w dowolnym regionie platformy Azure. Jeśli wyłączysz zapisy wieloregionowe, aplikacja może zapisywać dane w jednym regionie. <br/><br/> Włącz zapisy wieloregionowe, jeśli oczekujesz, że obciążenie jest aktywne i aktywne, które wymaga zapisu o małym opóźnieniu w różnych regionach. Na przykład obciążenia IOT, który zapisuje dane do bazy danych w dużych ilościach w różnych regionach. <br/><br/> Zapisy wieloregionowe gwarantują dostępność odczytu i zapisu w 99,999%. Zapisy w wielu regionach wymagają większej przepływności w porównaniu do regionów pojedynczego zapisu. Aby dowiedzieć się więcej, zobacz, [jak jednostek SU różnią się dla regionów pojedynczego i wielu zapisu](optimize-cost-regions.md) artykułu.|
|Domyślna spójność|Usługa Azure Cosmos DB obsługuje 5 poziomów spójności, aby umożliwić deweloperom zrównoważenie kompromisu między kompromisami spójności, dostępności i opóźnienia. Aby dowiedzieć się więcej, zobacz artykuł [poziomy spójności.](consistency-levels.md) <br/><br/> Domyślnie usługa Azure Cosmos DB używa spójności sesji, co gwarantuje możliwość odczytywania własnych zapisów w sesji. <br/><br/> Wybranie silnego lub ograniczonego nieaktualności będzie wymagało podwojenia wymaganego formatu RU/s dla odczytów w porównaniu z sesją, spójnego prefiksu i spójności ostatecznej. Silna spójność z zapisów wieloregionowych nie jest obsługiwana i automatycznie domyślnie zapisuje w jednym regionie z silną spójnością. |
|Zasady indeksowania|Domyślnie usługa Azure Cosmos DB [indeksuje wszystkie właściwości](index-policy.md) we wszystkich elementach dla elastycznych i wydajnych zapytań (mapuje do **zasad indeksowania automatycznego).** <br/><br/> Jeśli wybierzesz **opcję wyłączenia,** żadna z właściwości nie zostanie zindeksowana. Skutkuje to najniższą opłatą RU za zapisy. Wybierz **poza** zasady, jeśli oczekujesz, że tylko [odczyty punktowe](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (wyszukiwania wartości klucza) i/lub zapisy i nie kwerendy. <br/><br/> Niestandardowe zasady indeksowania umożliwia uwzględnienie lub wykluczenie określonych właściwości z indeksu dla niższej przepływności zapisu i magazynu. Aby dowiedzieć się więcej, zobacz [zasady indeksowania](index-overview.md) i [przykładowe zasady indeksowania](how-to-manage-indexing-policy.md#indexing-policy-examples) artykuły.|
|Łączna liczba przechowywanych danych (na region)|Łączna szacunkowa ilość danych przechowywanych w GB w jednym regionie.|
|Tryb obciążenia|Wybierz **opcję Stały,** jeśli wolumin obciążenia jest stały. <br/><br/> Wybierz **opcję Zmienna,** jeśli wolumin obciążenia zmienia się wraz z czasem.  Na przykład w określonym dniu lub miesiącu. <br/><br/> Następujące ustawienia są dostępne, jeśli wybierzesz opcję zmiennego obciążenia:<ul><li>Procent czasu w szczytowym momencie: procent czasu w miesiącu, w którym obciążenie wymaga szczytowej (najwyższej) przepływności. <br/><br/> Na przykład, jeśli masz obciążenie, które ma wysoką aktywność podczas 9am - 6pm godzin pracy w dni powszednie, a następnie procent czasu w szczycie jest: 45 godzin w szczycie / 730 godzin / miesiąc = ~ 6%.<br/><br/></li><li>Odczyty/s na region w szczytowym momencie — liczba odczytów oczekiwanych na sekundę w szczytowym momencie.</li><li>Zapisy/s na region w szczytowym momencie — liczba zapisów oczekiwanych na sekundę w szczytowym momencie.</li><li>Odczyty/s na region poza szczytem — liczba odczytów oczekiwanych na sekundę poza szczytem.</li><li>Zapisy/s na region poza szczytem — liczba zapisów oczekiwanych na sekundę poza szczytem.</li></ul>W interwałach szczytowych i poza szczytowych można zoptymalizować koszt, [programowo skalując odpowiednio aprowizowaną przepływność](set-throughput.md#update-throughput-on-a-database-or-a-container) w górę i w dół.|
|Rozmiar elementu|Rozmiar elementu danych (np. dokumentu), od 1 KB do 2 MB. <br/><br/>Można również **przesłać przykładowy dokument (JSON)** dla dokładniejszego oszacowania.<br/><br/>Jeśli obciążenie ma wiele typów elementów (z inną zawartością JSON) w tym samym kontenerze, można przekazać wiele dokumentów JSON i uzyskać oszacowanie. Użyj przycisku **Dodaj nowy element,** aby dodać wiele przykładowych dokumentów JSON.|

Można również użyć przycisku **Zapisz oszacowanie,** aby pobrać plik CSV zawierający bieżące oszacowanie. 

![Zaawansowany tryb planowania pojemności](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Ceny wyświetlane w planowaniu pojemności usługi Azure Cosmos DB są szacunkami opartymi na publicznych cenach przepływności i magazynu. Wszystkie ceny są wyświetlane w dolarach amerykańskich. Zapoznaj się ze [stroną cennika usługi Azure Cosmos DB,](https://azure.microsoft.com/pricing/details/cosmos-db/) aby wyświetlić wszystkie stawki według regionów.  

## <a name="estimating-throughput-for-queries"></a>Szacowanie przepływności dla zapytań

Kalkulator pojemności usługi Azure Cosmos zakłada odczyty punktowe (odczyt pojedynczego elementu, np. Aby oszacować przepływność potrzebną dla kwerend, uruchom kwerendę na reprezentatywnym zestawie danych w kontenerze usługi Cosmos i [uzyskaj opłatę RU](find-request-unit-charge.md). Pomnóż opłatę RU przez liczbę zapytań, które można przewidzieć do uruchomienia na sekundę, aby uzyskać całkowitą wymaganą wartość RU/s. 

Na przykład jeśli obciążenie wymaga ``SELECT * FROM c WHERE c.id = 'Alice'`` kwerendy, która jest uruchamiana 100 razy na sekundę, a opłata RU kwerendy wynosi 10 ru, trzeba będzie 100 kwerenda / s * 10 RU / kwerenda = 1000 RU/s w sumie do obsługi tych żądań. Dodaj te ru/s do ru/s wymagane dla wszelkich odczytów lub zapisów dzieje się w obciążeniu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [modelu cenowym usługi Azure Cosmos DB.](how-pricing-works.md)
* Utwórz nowe [konto, bazę danych i kontener usługi Cosmos](create-cosmosdb-resources-portal.md).
* Dowiedz się, jak [zoptymalizować aprowizowaną przepustowość](optimize-cost-throughput.md).
* Dowiedz się, jak [zoptymalizować koszty za pomocą pojemności zarezerwowanej.](cosmos-db-reserved-capacity.md)

