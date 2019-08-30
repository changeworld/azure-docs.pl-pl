---
title: 'Azure Data Factory: Często zadawane pytania | Microsoft Docs'
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: c4836d519556e5a031f81279fef4891ba8d47c05
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141570"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory? 
Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze, która automatyzuje przenoszenie i transformację danych. Podobnie jak w przypadku fabryki, która uruchamia sprzęt w celu przekształcenia surowców na gotowe towary, Azure Data Factory organizować istniejące usługi, które zbierają dane pierwotne i przekształcają je w gotowe do użycia informacje. 

Za pomocą Azure Data Factory można tworzyć oparte na danych przepływy pracy do przenoszenia danych między lokalnymi i magazynami danych w chmurze. Możesz przetwarzać i przekształcać dane przy użyciu usług obliczeniowych, takich jak Azure HDInsight, Azure Data Lake Analytics i SQL Server Integration Services (SSIS) Integration Runtime. 

Dzięki Data Factory można wykonać przetwarzanie danych w usłudze w chmurze opartej na platformie Azure lub we własnym własnym środowisku obliczeniowym, takim jak SSIS, SQL Server lub Oracle. Po utworzeniu potoku, który wykonuje wymaganą akcję, można zaplanować okresowe uruchamianie (co godzinę, codziennie lub co tydzień, na przykład), planowanie okna czasu lub wyzwolić potok z wystąpienia zdarzenia. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Sterowanie przepływami i skalowaniem 
Aby zapewnić obsługę różnorodnych przepływów integracji i wzorców w nowoczesnych magazynach danych, Data Factory umożliwia elastyczne modelowanie potoków danych. Pociąga to za sobą model programowania przepływu pełnej kontroli, który obejmuje wykonywanie warunkowe, rozgałęzianie potoków danych oraz możliwość jawnego przekazywania parametrów w ramach i między tymi przepływami. Przepływ sterowania obejmuje również Przekształcanie danych za pomocą wysyłania działań do zewnętrznych aparatów wykonywania i możliwości przepływu danych, w tym przenoszenia danych na dużą skalę za pomocą działania kopiowania.

Data Factory zapewnia swobodny model dowolnego stylu przepływu, który jest wymagany do integracji danych i który może być wysyłany na żądanie lub wielokrotnie zgodnie z harmonogramem. Oto kilka typowych przepływów obsługiwanych przez ten model:   

- Przepływy sterowania:
    - Działania mogą być łańcucha połączone w sekwencję w potoku.
    - Działania można rozgałęziać w ramach potoku.
    - Parametry:
        - Parametry można definiować na poziomie potoku, a argumenty mogą być przesyłane podczas wywoływania potoku na żądanie lub przy użyciu wyzwalacza.
        - Działania mogą wykorzystywać argumenty przekazywane do potoku.
    - Przekazywanie stanu niestandardowego:
        - Dane wyjściowe działania, w tym stan, mogą być wykorzystywane przez kolejne działania w potoku.
    - Kontenery zapętlenia:
        - Działanie foreach przejdzie do iteracji w określonej kolekcji działań w pętli. 
- Przepływy oparte na wyzwalaczach:
    - Potoki mogą być wyzwalane na żądanie lub według czasu zegarka.
- Przepływy różnicowe:
    - Parametry mogą służyć do definiowania znacznika limitu górnego dla kopiowania różnicowego podczas przesuwania tabel wymiarów lub odwołań z magazynu relacyjnego, lokalnego lub w chmurze, w celu załadowania danych do usługi Lake. 

Aby uzyskać więcej informacji, zobacz [Samouczek: Przepływy](tutorial-control-flow.md)sterowania.

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Przekształcanie danych na dużą skalę przy użyciu potoków bez kodu
Nowe środowisko narzędziowe oparte na przeglądarce zapewnia tworzenie i wdrażanie potoków bez obsługi kodu przy użyciu nowoczesnego, interaktywnego środowiska opartego na sieci Web.

W przypadku deweloperów danych wizualnych i inżynierów danych Data Factory internetowy interfejs użytkownika jest środowiskiem projektowym bez kodu, który będzie używany do tworzenia potoków. Jest ona w pełni zintegrowana z usługą Visual Studio Online git i zapewnia integrację dla ciągłej integracji/ciągłego wdrażania z opcjami debugowania.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Rozbudowane zestawy SDK dla wielu platform dla zaawansowanych użytkowników
Data Factory v2 oferuje bogaty zestaw zestawów SDK, których można używać do tworzenia i monitorowania potoków oraz zarządzania nimi przy użyciu ulubionego środowiska IDE, w tym:
* Zestaw SDK dla języka Python
* Interfejs wiersza polecenia programu PowerShell
* Zestaw SDK języka C#

Użytkownicy mogą również używać udokumentowanych interfejsów API REST do interfejsów z Data Factory v2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iteracyjne programowanie i debugowanie za pomocą narzędzi wizualnych
Azure Data Factory narzędzia wizualne umożliwiają programowanie iteracyjne i debugowanie. Możesz tworzyć potoki i uruchamiać testy przy użyciu możliwości **debugowania** na kanwie potoku bez konieczności pisania pojedynczego wiersza kodu. Wyniki przebiegów testowych można wyświetlić w oknie **danych wyjściowych** kanwy potoku. Po pomyślnym uruchomieniu testu można dodać więcej działań do potoku i kontynuować debugowanie w sposób iteracyjny. Możesz również anulować przebiegi testowe po ich zakończeniu. 

Nie jest wymagane publikowanie zmian w usłudze Data Factory przed wybraniem opcji **Debuguj**. Jest to przydatne w scenariuszach, w których chcesz mieć pewność, że nowe dodatki lub zmiany będą działały zgodnie z oczekiwaniami przed aktualizacją przepływów pracy fabryki danych w środowiskach programistycznych, testowych lub produkcyjnych. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Możliwość wdrażania pakietów usług SSIS na platformie Azure 
Jeśli chcesz przenieść obciążenia usług SSIS, możesz utworzyć Data Factory i zainicjować obsługę środowiska Azure-SSIS Integration Runtime. Azure-SSIS Integration Runtime to w pełni zarządzany klaster maszyn wirtualnych platformy Azure (węzłów), które są przeznaczone do uruchamiania pakietów SSIS w chmurze. Instrukcje krok po kroku znajdują się w samouczku [wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) . 
 
### <a name="sdks"></a>Zestawy SDK
Jeśli jesteś użytkownikiem zaawansowanym i szukasz interfejsu programowego, Data Factory udostępnia bogaty zestaw zestawów SDK, których można używać do tworzenia i monitorowania potoków oraz zarządzania nimi przy użyciu ulubionego środowiska IDE. Obsługa języków obejmuje .NET, PowerShell, Python i REST.

### <a name="monitoring"></a>Monitorowanie
Fabryki danych można monitorować za pomocą programu PowerShell, zestawu SDK lub narzędzi do monitorowania wizualnego w interfejsie użytkownika przeglądarki. Można monitorować i zarządzać przepływami niestandardowymi na żądanie, opartymi na wyzwalaczach oraz w skuteczny i efektywny sposób. Anuluj istniejące zadania, zobacz niepowodzenia w skrócie, przechodzenie do szczegółów w celu uzyskania szczegółowych komunikatów o błędach i Debugowanie problemów, wszystko to w jednym z okien szklanych bez przełączania kontekstu lub przechodzenia między ekranami. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nowe funkcje usług SSIS w Data Factory
Począwszy od początkowej publicznej wersji zapoznawczej w 2017, Data Factory dodano następujące funkcje dla usług SSIS:

-   Obsługa trzech więcej konfiguracji/wariantów Azure SQL Database do hostowania bazy danych SSIS (SSISDB) projektów/pakietów:
-   SQL Database z punktami końcowymi usługi sieci wirtualnej
-   Wystąpienie zarządzane
-   Pula elastyczna
-   Obsługa Azure Resource Manager sieci wirtualnej na podstawie klasycznej sieci wirtualnej, która ma zostać przestarzała w przyszłości, która pozwala na wstrzyknięcie lub dołączenie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej skonfigurowanej dla SQL Database za pomocą usługi sieci wirtualnej punkty końcowe/dostęp do danych lokalnych. Aby uzyskać więcej informacji, zobacz również dołączanie [środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).
-   Obsługa uwierzytelniania Azure Active Directory (Azure AD) i uwierzytelniania SQL w celu łączenia się z SSISDB, co pozwala na uwierzytelnianie usługi Azure AD za pomocą tożsamości zarządzanej Data Factory dla zasobów platformy Azure
-   Obsługa tworzenia własnej lokalnej licencji SQL Server w celu zdobycia znacznego oszczędności kosztów z poziomu opcji Korzyść użycia hybrydowego platformy Azure
-   Obsługa wersji Enterprise środowiska Azure-SSIS Integration Runtime, która umożliwia korzystanie z funkcji Advanced/Premium, niestandardowego interfejsu instalacji służącego do instalowania dodatkowych składników/rozszerzeń i ekosystemu partnerów. Aby uzyskać więcej informacji, zobacz również [Enterprise Edition, Konfiguracja niestandardowa i rozszerzalność innych firm dla usług SSIS w podajniku ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Głębia Integracja usług SSIS w Data Factory, która umożliwia wywoływanie i wyzwalanie działań pakietu SSIS pierwszej klasy w potokach Data Factory i planowanie ich za pośrednictwem programu SSMS. Aby uzyskać więcej informacji, zobacz również modernizowanie [i zwiększanie przepływów pracy ETL/ELT za pomocą działań SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Co to jest środowisko Integration Runtime?
Środowisko Integration Runtime to infrastruktura obliczeniowa, która Azure Data Factory używa do zapewniania następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przenoszenie danych**: W przypadku przenoszenia danych środowisko Integration Runtime przenosi dane między źródłowym i docelowym magazynem danych, a jednocześnie zapewnia obsługę wbudowanych łączników, konwersji formatów, mapowania kolumn oraz wydajnego i skalowalnego transferu danych.
- **Działania wysyłania**: W przypadku transformacji środowisko Integration Runtime zapewnia możliwość natywnego wykonywania pakietów usług SSIS.
- **Wykonaj pakiety SSIS**: Środowisko Integration Runtime natywnie wykonuje pakiety usług SSIS w zarządzanym środowisku obliczeniowym platformy Azure. Środowisko Integration runtime obsługuje również wysyłanie i monitorowanie działań przekształcania działających w różnych usługach obliczeniowych, takich jak Azure HDInsight, Azure Machine Learning, SQL Database i SQL Server.

Do przenoszenia i przekształcania danych można wdrożyć jedno lub wiele wystąpień środowiska Integration Runtime. Środowisko Integration Runtime może działać w sieci publicznej platformy Azure lub w sieci prywatnej (lokalnej, na platformie Azure Virtual Network lub w Amazon Web Services wirtualnej chmurze prywatnej [VPC]). 

Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Jaki jest limit liczby środowisk Integration Runtime?
Brak sztywnego limitu liczby wystąpień środowiska Integration Runtime, które mogą znajdować się w fabryce danych. Istnieje jednak limit liczby rdzeni maszyn wirtualnych, które mogą być używane przez środowisko Integration Runtime na subskrypcję na potrzeby wykonywania pakietów SSIS. Aby uzyskać więcej informacji, zobacz [limity Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Jakie są koncepcje najwyższego poziomu Azure Data Factory?
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Azure Data Factory zawiera cztery kluczowe składniki, które współpracują ze sobą jako platforma, na której można tworzyć przepływy pracy oparte na danych z etapami przenoszenia i przekształcania danych.

### <a name="pipelines"></a>Potoki
Fabryka danych może obejmować jeden lub wiele potoków. Potok jest logicznym grupą działań do wykonania jednostką pracy. które umożliwiają wykonanie zadania. Na przykład potok może zawierać grupę działań, które pobierają dane z obiektu blob platformy Azure, a następnie uruchamiają zapytanie programu Hive w klastrze usługi HDInsight w celu partycjonowania danych. Korzyść polega na tym, że można użyć potoku do zarządzania działaniami jako zestawem, zamiast konieczności oddzielnego zarządzania poszczególnymi działaniami. Można połączyć działania w potoku w celu ich sekwencyjnego działania lub można obsługiwać je niezależnie, równolegle.

### <a name="activities"></a>Działania
Działania reprezentują krok przetwarzania w potoku. Można na przykład użyć działania kopiowania do skopiowania danych z jednego magazynu danych do innego. Podobnie można użyć działania programu Hive, które uruchamia zapytanie programu Hive w klastrze usługi Azure HDInsight, aby przekształcić lub przeanalizować dane. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="datasets"></a>Zestawy danych
Zestawy danych reprezentują struktury w magazynach danych. Struktury te po prostu wskazują na dane, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. W ten sposób należy wziąć pod uwagę: Połączona usługa definiuje połączenie ze źródłem danych, a zestaw danych reprezentuje jego strukturę. Na przykład połączona usługa Azure Storage określa parametry połączenia w celu nawiązania połączenia z kontem usługi Azure Storage. A zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob i folder, który zawiera dane.

Połączone usługi mają dwa cele w Data Factory:

- Do reprezentowania *magazynu danych* , który zawiera, ale nie jest ograniczony do, lokalnego wystąpienia SQL Server, wystąpienia bazy danych Oracle, udziału plików lub konta usługi Azure Blob Storage. Listę obsługiwanych magazynów danych można znaleźć w temacie [copy Activity in Azure Data Factory](copy-activity-overview.md).
- Reprezentowanie *zasobu obliczeniowego*, który może hostować wykonywanie działania. Na przykład działanie programu Hive w usłudze HDInsight działa w klastrze usługi HDInsight Hadoop. Listę działań przekształcania i obsługiwanych środowisk obliczeniowych można znaleźć [w temacie Przekształcanie danych w Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Wyzwalacze
Wyzwalacze reprezentują jednostki przetwarzania, które określają, kiedy zostanie rozpoczęte wykonywanie potoku. Istnieją różne typy wyzwalaczy dla różnych typów zdarzeń. 

### <a name="pipeline-runs"></a>Uruchomienia potoków
Uruchomienie potoku to wystąpienie wykonania potoku. Zazwyczaj tworzy się wystąpienie uruchomienia potoku przez przekazanie argumentów do parametrów, które są zdefiniowane w potoku. Argumenty można przekazać ręcznie lub w ramach definicji wyzwalacza.

### <a name="parameters"></a>Parametry
Parametry to pary klucz-wartość w konfiguracji tylko do odczytu. Parametry są definiowane w potoku, a argumenty dla zdefiniowanych parametrów są przekazywane z kontekstu uruchomienia. Kontekst uruchomienia jest tworzony przez wyzwalacz lub z potoku, który jest wykonywany ręcznie. Działania w ramach potoku wykorzystują wartości parametrów.

Zestaw danych jest silnie określonym parametrem i jednostką, której można użyć ponownie lub do której się odwołuje. Działanie może odwoływać się do zestawów danych i może zużywać właściwości, które są zdefiniowane w definicji DataSet.

Połączona usługa jest również jednoznacznie określonym parametrem zawierającym informacje o połączeniu z magazynem danych lub środowiskiem obliczeniowym. Jest to również jednostka, której można użyć ponownie lub odwoływać się do niej.

### <a name="control-flows"></a>Przepływy sterowania
Przepływy sterowania organizują działania potokowe, które obejmują działania związane z łańcuchami w sekwencji, rozgałęziania, parametry zdefiniowane na poziomie potoku, a także argumenty, które są przekazywane podczas wywoływania potoku na żądanie lub przy użyciu wyzwalacza. Przepływy sterowania obejmują również niestandardowe przekazanie stanu i kontenery zapętlenia (czyli Iteratory foreach).


Aby uzyskać więcej informacji o pojęciach związanych z usługą Data Factory, zobacz następujące artykuły:

- [Dataset and linked services (Zestaw danych i połączone usługi)](concepts-datasets-linked-services.md)
- [Potoki i działania](concepts-pipelines-activities.md)
- [Integration Runtime (Produkt Integration Runtime)](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Jaki jest model cen dla Data Factory?
Aby uzyskać szczegółowe informacje o cenach Azure Data Factory, zobacz [Data Factory szczegóły cennika](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak mogę uzyskać aktualne informacje o Data Factory?
Aby uzyskać najbardziej aktualne informacje dotyczące Azure Data Factory, przejdź do następujących lokacji:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Strona główna dokumentacji](/azure/data-factory)
- [Strona główna produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Głębokie szczegółowee techniczne 

### <a name="how-can-i-schedule-a-pipeline"></a>Jak zaplanować potok? 
Można użyć wyzwalacza harmonogramu lub wyzwalacza przedziału czasu, aby zaplanować potok. Wyzwalacz korzysta z harmonogramu kalendarza zegarowego, który umożliwia okresowe planowanie potoków lub w wzorcach cyklicznych opartych na kalendarzach (na przykład w poniedziałek o godzinie 6:00 PM i czwartki o godzinie 9:00 PM). Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Czy mogę przekazać parametry do uruchomienia potoku?
Tak, parametry są pierwszą klasą koncepcji najwyższego poziomu w Data Factory. Parametry można definiować na poziomie potoku i przekazywać argumenty podczas wykonywania potoku na żądanie lub przy użyciu wyzwalacza.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Czy mogę definiować wartości domyślne dla parametrów potoku? 
Tak. Można zdefiniować wartości domyślne parametrów w potokach. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Czy działanie w potoku zużywa argumenty, które są przekazane do uruchomienia potoku? 
Tak. Każde działanie w potoku może zużywać wartość parametru, która jest przesyłana do potoku i uruchamiana z `@parameter` konstrukcja. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Czy właściwość wyjściowa działania może być używana w innym działaniu? 
Tak. Dane wyjściowe działania mogą być używane w kolejnym działaniu z `@activity` konstruowaniem.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak mogę bezpiecznie obsłużyć wartości null w danych wyjściowych działania? 
Można użyć `@coalesce` konstrukcji w wyrażeniach, aby bezpiecznie obsługiwać wartości null. 

## <a name="mapping-data-flows"></a>Mapowanie przepływów danych

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Która wersja Data Factory używana do tworzenia przepływów danych?
Użyj wersji Data Factory v2, aby utworzyć przepływy danych.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Jestem poprzednim klientem prywatnej wersji zapoznawczej, który używał przepływów danych, a w przypadku przepływów danych została użyta wersja zapoznawcza Data Factory v2.
Ta wersja jest obecnie przestarzała. Użyj Data Factory v2 dla przepływów danych.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Co się zmieniło od prywatnej wersji zapoznawczej do ograniczonej publicznej wersji zapoznawczej w odniesieniu do przepływów danych?
Nie będzie już konieczne korzystanie z własnych klastrów Azure Databricks. Data Factory będzie zarządzać tworzeniem i rozbiciem klastra. Zestawy danych BLOB i zestawy danych Azure Data Lake Storage Gen2 są rozdzielone na tekst rozdzielony i zestawy danych Apache Parquet. Można nadal używać Data Lake Storage Gen2 i magazynu obiektów BLOB do przechowywania tych plików. Użyj odpowiedniej połączonej usługi dla tych aparatów pamięci masowej.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Czy mogę migrować własne fabryki w wersji zapoznawczej do Data Factory v2?

Tak. [Postępuj zgodnie z instrukcjami](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Potrzebuję pomocy przy rozwiązywaniu problemów z moją logiką przepływu danych. Jakie informacje muszę udostępnić, aby uzyskać pomoc?

Gdy firma Microsoft zapewnia pomoc lub Rozwiązywanie problemów z przepływami danych, należy podać plan kodu DSL. W tym celu wykonaj następujące kroki:

1. W Projektancie przepływu danych wybierz pozycję **kod** w prawym górnym rogu. Spowoduje to wyświetlenie edytowalnego kodu JSON dla przepływu danych.
2. W widoku Kod wybierz pozycję **Plan** w prawym górnym rogu. Ten przełącznik będzie przełączać się z formatu JSON do sformatowanego planu skryptu DSL tylko do odczytu.
3. Skopiuj i wklej ten skrypt lub Zapisz go w pliku tekstowym.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Jak mogę dostęp do danych przy użyciu innych typów zestawów danych 80 w Data Factory?

Funkcja Mapowanie przepływu danych umożliwia obecnie Azure SQL Database, Azure SQL Data Warehouse, rozdzielane pliki tekstowe z usługi Azure Blob Storage lub Azure Data Lake Storage Gen2 oraz pliki Parquet z magazynu obiektów blob lub Data Lake Storage Gen2 natywne dla źródła i ujścia. 

Działanie kopiowania służy do przemieszczania danych z dowolnego innego łącznika, a następnie wykonywania działania przepływu danych w celu przekształcenia danych po ich przygotowaniu. Na przykład potok będzie najpierw kopiowany do magazynu obiektów blob, a następnie działanie przepływu danych będzie używać zestawu danych w źródle do przekształcenia tych danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia fabryki danych, zobacz następujące samouczki:

- [Szybki start: Tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md)
- [Samouczek: Kopiowanie danych w chmurze](tutorial-copy-data-dot-net.md)
