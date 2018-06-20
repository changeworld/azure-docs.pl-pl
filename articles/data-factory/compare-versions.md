---
title: Porównanie wersji 1 i 2 usługi Azure Data Factory | Microsoft Docs
description: Ten artykuł zawiera porównanie usług Azure Data Factory V1 i Azure Data Factory V2.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: 7d7fcaded1f088961fb139b1fba6b1944b383435
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616515"
---
# <a name="compare-azure-data-factory-v1-and-v2"></a>Porównanie wersji 1 i 2 usługi Azure Data Factory
W tym artykule porównano wersję 2 z wersją 1 usługi Azure Data Factory. Aby zapoznać się z opisem wersji 1, zobacz [Wprowadzenie do usługi Azure Data Factory](v1/data-factory-introduction.md). Aby zapoznać się z opisem wersji 2, zobacz [Wprowadzenie do usługi Data Factory (V2 — wersja zapoznawcza)](introduction.md).

## <a name="feature-comparison"></a>Porównanie funkcji
W poniższej tabeli porównano funkcje wersji 1 i wersji 2. 

| Cecha | Wersja 1 | Wersja 2 | 
| ------- | --------- | --------- | 
| Zestawy danych | Nazwany widok danych odwołujący się do danych, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów blob platformy Azure określa kontener obiektów blob i folder w usłudze Azure Blob Storage, z których działanie ma odczytywać dane.<br/><br/>**Dostępność** definiuje model tworzenia wycinków okien przetwarzania dla zestawu danych (na przykład co godzinę, codziennie itd.). | Zestawy danych są takie same w wersji 2. Nie trzeba jednak definiować harmonogramów **dostępności** dla zestawów danych. Można zdefiniować zasób wyzwalający, który może planować potoki z paradygmatu harmonogramu zegarowego. Aby uzyskać więcej informacji, zobacz [Triggers](concepts-pipeline-execution-triggers.md#triggers) (Wyzwalacze) i [Datasets](concepts-datasets-linked-services.md) (Zestawy danych). | 
| Połączone usługi | Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji dla usługi Data Factory, które są niezbędne do nawiązywania połączeń z zasobami zewnętrznymi. | Połączone usługi są takie same, jak w usłudze Data Factory V1, ale z nową właściwością **connectVia** do korzystania ze środowiska obliczeniowego Integration Runtime usługi Data Factory V2. Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md) i [Linked service properties for Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) (Właściwości usługi połączonej dla usługi Azure Blob Storage). |
| Potoki | Fabryka danych może obejmować jeden lub wiele potoków. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Do planowania i uruchamiania potoków są używane parametry startTime, endTime i isPaused. | Potoki są grupami działań wykonywanych na danych. Jednak planowanie działań w potoku zostało rozdzielone na nowe zasoby wyzwalające. Potoki w usłudze Data Factory V2 można traktować raczej jako „jednostki przepływu pracy”, które są planowane oddzielnie za pośrednictwem wyzwalaczy. <br/><br/>Potoki nie mają „okien” czasu wykonywania w usłudze Data Factory V2. Koncepcje startTime, endTime i isPaused z usługi Data Factory V1 nie są już dostępne w usłudze Data Factory V2. Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md) oraz [Potoki i działania](concepts-pipelines-activities.md). |
| Działania | Działania definiują akcje do wykonania na danych w potoku. Obsługiwane są działania przenoszenia danych (działanie kopiowania) i przekształcania danych (takie jak Hive, Pig i MapReduce). | W usłudze Data Factory V2 działania są nadal akcjami zdefiniowanymi w potoku. W wersji 2 wprowadzono nowe [działania przepływu sterowania](concepts-pipelines-activities.md#control-activities). Te działania są używane w przepływie sterowania (zapętlanie i rozgałęzianie). Działania przenoszenia i przekształcania danych, które były obsługiwane w wersji 1, są obsługiwane w wersji 2. Możesz zdefiniować działania przekształcania bez używania zestawów danych w wersji 2. |
| Hybrydowe przenoszenie danych i wysyłanie działania | Teraz nazywana środowiskiem Integration Runtime, [brama zarządzania danymi](v1/data-factory-data-management-gateway.md) obsługiwała przenoszenie danych między środowiskiem lokalnym i chmurą.| Brama zarządzania danymi jest teraz nazywana środowiskiem Integration Runtime (Self-hosted). Zapewnia te same funkcje, co w wersji 1. <br/><br/> Infrastruktura Azure-SSIS Integration Runtime w wersji 2 obsługuje również wdrażanie i uruchamianie pakietów usług SQL Server Integration Services (SSIS) w chmurze. Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).|
| Parametry | Nie dotyczy | Parametry to pary klucz-wartość ustawień konfiguracji tylko do odczytu, które są zdefiniowane w potokach. Argumenty dla parametrów można przekazywać podczas ręcznego uruchamiania potoku. Jeśli używasz wyzwalacza harmonogramu, wyzwalacz też może przekazywać wartości parametrów. Działania w ramach potoku wykorzystują wartości parametrów.  |
| Wyrażenia | Usługa Data Factory V1 pozwala używać funkcji i zmiennych systemowych w zapytaniach wyboru danych i właściwościach działania/zestawu danych. | W usłudze Data Factory V2 można używać wyrażeń w dowolnym miejscu w wartości ciągu JSON. Aby uzyskać więcej informacji, zobacz [Expressions and Functions in V2](control-flow-expression-language-functions.md) (Wyrażenia i funkcje w wersji 2).|
| Uruchomienia potoków | Nie dotyczy | Pojedyncze wystąpienie wykonania potoku. Załóżmy na przykład istnienie potoku, który jest wykonywany o 8:00, 9:00 i 10:00. W takim przypadku występują trzy osobne uruchomienia potoku. Każde uruchomienie potoku ma unikatowy identyfikator uruchomienia potoku. Każdy identyfikator uruchomienia potoku jest identyfikatorem GUID, który w sposób unikatowy definiuje to konkretne uruchomienie potoku. Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów, które są definiowane w potokach. |
| Uruchomienia działania | Nie dotyczy | Wystąpienie wykonania działania w potoku. | 
| Uruchomienia wyzwalacza | Nie dotyczy | Wystąpienie wykonania wyzwalacza. Aby uzyskać więcej informacji, zobacz [Triggers](concepts-pipeline-execution-triggers.md) (Wyzwalacze). |
| Planowanie | Planowanie zależy od godzin rozpoczęcia/zakończenia potoku i dostępności zestawu danych. | Wyzwalacz harmonogramu lub wykonywanie przez zewnętrzny harmonogram. Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md). |

Następujące sekcje zawierają więcej informacji na temat funkcji wersji 2. 

## <a name="control-flow"></a>Przepływ sterowania  
W celu zapewnienia obsługi różnych przepływów i wzorców integracji w nowoczesnym magazynie danych usługa Data Factory V2 wprowadziła nowy, elastyczny model potoku danych, który nie jest już powiązany z danymi szeregów czasowych. Udostępniono kilka typowych przepływów, które wcześniej nie były możliwe. Zostały one opisane w poniższych sekcjach.   

### <a name="chaining-activities"></a>Tworzenie łańcuchów działań
W wersji 1 trzeba było skonfigurować dane wyjściowe działania jako dane wejściowe kolejnego działania, aby połączyć je w łańcuch. W wersji 2 możesz łączyć działania w sekwencję w ramach potoku. Możesz użyć właściwości **dependsOn** w definicji działania, aby połączyć ją z działaniem nadrzędnym. Więcej informacji i przykład możesz znaleźć w artykułach [Potoki i działania](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) oraz [Rozgałęzianie działań i tworzenie łańcuchów działań](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Rozgałęzianie działań
W wersji 2 można rozgałęziać działania w potoku. Działanie [If-condition](control-flow-if-condition-activity.md) pełni taką samą rolę, co instrukcja `if` w językach programowania. Powoduje ono obliczenie zestawu działań, gdy warunek zostanie obliczony na wartość `true`, oraz innego zestawu działań, gdy warunek zostanie obliczony na wartość `false`. Aby poznać przykłady rozgałęziania działań, zobacz samouczek [Rozgałęzianie działań i tworzenie łańcuchów działań](tutorial-control-flow.md).

### <a name="parameters"></a>Parametry 
Parametry można definiować na poziomie potoku, a argumenty przekazywać w trakcie wywoływania potoku na żądanie lub przy użyciu wyzwalacza. Działania mogą wykorzystywać argumenty przekazywane do potoku. Aby uzyskać więcej informacji, zobacz [Pipelines and triggers](concepts-pipeline-execution-triggers.md) (Potoki i wyzwalacze). 

### <a name="custom-state-passing"></a>Przekazywanie stanów niestandardowych
Dane wyjściowe działania, w tym jego stan, mogą być wykorzystywane przez kolejne działania w potoku. Na przykład w definicji JSON działania można uzyskać dostęp do danych wyjściowych poprzedniego działania przy użyciu następującej składni: `@activity('NameofPreviousActivity').output.value`. Przy użyciu tej funkcji można tworzyć przepływy pracy, w których wartości mogą być przekazywane przez działania.

### <a name="looping-containers"></a>Tworzenie pętli kontenerów
[Działanie ForEach](control-flow-for-each-activity.md) definiuje powtarzający się przepływ sterowania w potoku. To działanie iteruje po kolekcji i uruchamia określone działania w pętli. Implementacja pętli tego działania przypomina strukturę pętli Foreach w językach programowania. 

Działanie [Until](control-flow-until-activity.md) udostępnia te same funkcje, co struktura pętli do-until w językach programowania. Służy do uruchamiania zestawu działań w pętli do momentu, gdy warunek skojarzony z działaniem zostanie obliczony na wartość `true`. W usłudze Data Factory można określić wartość limitu czasu działania Until.  

### <a name="trigger-based-flows"></a>Przepływy na podstawie wyzwalaczy
Potoki mogą być wywoływane na żądanie lub zgodnie z zegarem. Artykuł dotyczący [potoków i wyzwalaczy](concepts-pipeline-execution-triggers.md) zawiera szczegółowe informacje o wyzwalaczach. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Wywoływanie potoku z poziomu innego potoku
[Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md) umożliwia potokowi usługi Data Factory wywoływanie innego potoku.

### <a name="delta-flows"></a>Przepływy delta
Przypadek użycia klucza we wzorcach ETL to „ładowania delta”, w których ładowane są tylko te dane, które zmieniły się od czasu ostatniej iteracji potoku. Nowe funkcje w wersji 2, takie jak [działanie wyszukiwania](control-flow-lookup-activity.md), elastyczne harmonogramy i przepływ sterowania w naturalny sposób umożliwiają ten przypadek użycia. Aby uzyskać samouczek z instrukcjami krok po kroku, zobacz [Samouczek: kopia przyrostowa](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Inne działania przepływu sterowania
Poniżej przedstawiono kilka kolejnych działań przepływu sterowania, które są obsługiwane przez usługę Data Factory V2. 

Działanie sterowania | Opis
---------------- | -----------
[Działanie ForEach](control-flow-for-each-activity.md) | Definiuje powtarzający się przepływ sterowania w potoku. To działanie służy do wykonywania iteracji po kolekcji i uruchamia określone działania w pętli. Implementacja pętli tego działania przypomina strukturę pętli Foreach w językach programowania.
[Działanie Web](control-flow-web-activity.md) | Wywołuje niestandardowy punkt końcowy REST z potoku usługi Data Factory. Można przekazywać zestawy danych i połączone usługi do zużycia i dostępu przez działanie. 
[Działanie Lookup](control-flow-lookup-activity.md) | Odczytuje lub wyszukuje wartość nazwy rekordu lub tabeli z dowolnego źródła zewnętrznego. Do tych danych wyjściowych mogą także odwoływać się kolejne działania. 
[Działanie GetMetadata](control-flow-get-metadata-activity.md) | Pobiera metadane dowolnych danych z usługi Azure Data Factory. 
[Działanie Wait](control-flow-wait-activity.md) | Wstrzymuje potok na określony przedział czasu.

## <a name="deploy-ssis-packages-to-azure"></a>Wdrażanie pakietów usług SSIS na platformie Azure 
Użyj usług Azure-SSIS, jeśli chcesz przenieść obciążenia usług SSIS do chmury, utworzyć fabrykę danych w wersji 2 i zainicjować środowisko Azure-SSIS Integration Runtime.

Azure-SSIS Integration Runtime to w pełni zarządzany klaster maszyn wirtualnych platformy Azure (węzłów), których przeznaczeniem jest uruchamianie pakietów SSIS w chmurze. Po zainicjowaniu środowiska Azure-SSIS Integration Runtime możesz użyć tych samych narzędzi, których używano do wdrażania pakietów SSIS w lokalnym środowisku SSIS. 

Na przykład możesz użyć programu SQL Server Data Tools lub SQL Server Management Studio do wdrożenia pakietów usług SSIS w tym środowisku uruchomieniowym na platformie Azure. Instrukcje krok po kroku znajdują się w samouczku [Wdrażanie pakietów usług SQL Server Integration Services na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="flexible-scheduling"></a>Elastyczne harmonogramy
W usłudze Data Factory V2 nie trzeba definiować harmonogramów dostępności zestawu danych. Można zdefiniować zasób wyzwalający, który może planować potoki z paradygmatu harmonogramu zegarowego. Parametry można też przekazać do potoków z wyzwalacza dla modelu elastycznego harmonogramu i wykonywania. 

Potoki nie mają „okien” czasu wykonywania w usłudze Data Factory V2. Koncepcje startTime, endTime i isPaused z usługi Data Factory V1 nie istnieją w usłudze Data Factory V2. Aby uzyskać więcej informacji o sposobie tworzenia, a następnie planowania potoku w usłudze Data Factory V2, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Obsługa większej liczby magazynów danych
Wersja 2 obsługuje kopiowanie danych do i z większej liczby magazynów danych niż wersja 1. Listę obsługiwanych magazynów danych zawierają następujące artykuły:

- [V1 — obsługiwane magazyny danych](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [V2 — obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Obsługa klastra platformy Spark na żądanie
Wersja 2 obsługuje tworzenie klastra platformy Spark na żądanie w usłudze Azure HDInsight. Aby utworzyć klaster platformy Spark na żądanie, określ typ klastra Spark w definicji usługi połączonej HDInsight na żądanie. Następnie możesz skonfigurować działanie platformy Spark w potoku, aby używać tej usługi połączonej. 

W czasie wykonywania, gdy jest wykonywane działanie, usługa Data Factory automatycznie tworzy klaster platformy Spark. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Działanie platformy Spark w wersji 2](transform-data-using-spark.md)
- [Połączona usługa Azure HDInsight na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Działania niestandardowe
W wersji 1 kod działania DotNet (niestandardowy) jest wdrażany przez tworzenie projektu biblioteki klas .NET w klasie, która implementuje metodę Execute interfejsu IDotNetActivity. Dlatego kod niestandardowy należy napisać w języku .NET Framework 4.5.2 i uruchomić w węzłach puli usługi Azure Batch opartych na systemie Windows. 

W działaniu niestandardowym w wersji 2 nie trzeba implementować interfejsu .NET. Można uruchamiać bezpośrednio polecenia i skrypty oraz własny kod niestandardowy skompilowany jako wykonywalny. 

Aby uzyskać więcej informacji, zobacz [Difference between custom activity in V1 and V2](transform-data-using-dotnet-custom-activity.md#compare-v2-v1) (Różnica między działaniami niestandardowymi w wersjach V1 i V2).

## <a name="sdks"></a>Zestawy SDK
 Usługa Data Factory V2 udostępnia bogatszy zestaw środowisk SDK, których można używać do tworzenia i monitorowania potoków oraz zarządzania nimi.

- **.NET SDK**: zestaw .NET SDK został zaktualizowany do wersji 2.

- **PowerShell**: polecenia cmdlet programu PowerShell zostały zaktualizowane do wersji 2. Polecenia cmdlet wersji 2 zawierają w nazwie ciąg **DataFactoryV2**, na przykład: Get-AzureRmDataFactoryV2. 

- **Python SDK**: ten zestaw SDK jest nowym składnikiem w wersji 2.

- **REST API**: interfejs API REST został zaktualizowany w wersji 2. 

Zestawy SDK, które zostały zaktualizowane w wersji 2, nie są zgodne wstecz z klientami w wersji 1. 

## <a name="authoring-experience"></a>Środowisko tworzenia

| &nbsp; | Wersja 2 | Wersja 1 |
| ------ | -- | -- | 
| Azure Portal | [Tak](quickstart-create-data-factory-portal.md) | [Tak](data-factory-build-your-first-pipeline-using-editor.md) |
| Azure PowerShell | [Tak](quickstart-create-data-factory-powershell.md) | [Tak](data-factory-build-your-first-pipeline-using-powershell.md) |
| Zestaw SDK .NET | [Tak](quickstart-create-data-factory-dot-net.md) | [Tak](data-factory-build-your-first-pipeline-using-vs.md) |
| Interfejs API REST | [Tak](quickstart-create-data-factory-rest-api.md) | [Tak](data-factory-build-your-first-pipeline-using-rest-api.md) |
| Zestaw SDK dla języka Python | [Tak](quickstart-create-data-factory-python.md) | Nie |
| Szablon usługi Resource Manager | [Tak](quickstart-create-data-factory-resource-manager-template.md) | [Tak](data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Role i uprawnienia

Roli współautora usługi Data Factory w wersji 1 nie można użyć do tworzenia zasobów usługi Data Factory w wersji 2 i zarządzania nimi. Aby uzyskać więcej informacji, zobacz [Współautor Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

## <a name="monitoring-experience"></a>Środowisko monitorowania
W wersji 2 można także monitorować fabryki danych za pomocą usługi [Azure Monitor](monitor-using-azure-monitor.md). Nowe polecenia cmdlet programu PowerShell obsługują monitorowanie [środowisk Integration Runtime](monitor-integration-runtime.md). Zarówno wersja 1, jak i wersja 2, obsługują monitorowanie wizualne za pośrednictwem aplikacji monitorowania, którą można uruchamiać z witryny Azure Portal.


## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak utworzyć fabrykę danych, wykonując instrukcje krok po kroku zamieszczone w przewodnikach Szybki start dotyczących [programu PowerShell](quickstart-create-data-factory-powershell.md), [platformy .NET](quickstart-create-data-factory-dot-net.md), [języka Python](quickstart-create-data-factory-python.md) i [interfejsu API REST](quickstart-create-data-factory-rest-api.md). 
