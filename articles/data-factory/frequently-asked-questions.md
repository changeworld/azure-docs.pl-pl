---
title: 'Azure Data Factory: Często zadawane pytania | Dokumentacja firmy Microsoft'
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61345758"
---
# <a name="azure-data-factory-faq"></a>Fabrykę danych platformy Azure — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory? 
Data Factory to usługa w pełni zarządzane, oparte na chmurze, integracji danych, która automatyzuje operacje przenoszenia i przekształcania danych. Podobnie jak fabryki, sprzęt, przekształcić surowców w gotowe towary usługi Azure Data Factory organizuje istniejące usługi, które są zbierane są oryginalne dane są następnie przekształcane w gotowe do użycia informacje. 

Za pomocą usługi Azure Data Factory, można utworzyć opartych na danych przepływy pracy służące do przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych. Może przetwarzać i przekształcać dane za pomocą obliczeń usług, takich jak Azure HDInsight, Azure Data Lake Analytics i SQL Server Integration Services (SSIS) środowiska integration runtime. 

Usługa Data Factory można wykonać przetwarzanie danych w usłudze w chmurze oparte na platformie Azure lub we własnym środowisku Self-Hosted obliczeniowych, takich jak SSIS, programu SQL Server lub Oracle. Po utworzeniu potoku, który wykonuje akcję, które są potrzebne, można zaplanować jego okresowe uruchamianie (co godzinę, codziennie lub co tydzień, na przykład), przedział czasu planowania lub wyzwalanie potoku z wystąpienia zdarzenia. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Przepływów sterowania i skali 
Do obsługi różnych przepływów i wzorców integracji w nowoczesnym magazynie danych, Data Factory umożliwia elastyczne danych potoku modelowania. Pociąga za sobą pełnej kontroli przepływu paradygmatów, które obejmują wykonanie warunkowe programowania, rozgałęzianie w potoków danych oraz możliwość jawne przekazywanie parametrów wewnątrz i pomiędzy tymi przepływami. Przepływ sterowania obejmuje także przekształcać dane za pośrednictwem wysyłanie działania do wykonania zewnętrznego aparatów i możliwości przepływu danych, w tym przenoszenie danych na dużą skalę za pomocą działania kopiowania.

Data Factory oferuje utworzyć dowolny styl przepływu, który jest wymagany przez integrację danych i mogą być wysyłane na żądanie lub wielokrotnie zgodnie z harmonogramem. Przedstawiono kilka typowych przepływów, które ten model umożliwia:   

- Przepływów sterowania:
    - Działania można łączyć w łańcuch w kolejności, w ramach potoku.
    - Działania mogą rozgałęzione w ramach potoku.
    - Parametry:
        - Parametry można definiować na poziomie potoku, a argumenty przekazywać w trakcie wywołania potoku na żądanie lub przy użyciu wyzwalacza.
        - Działania mogą wykorzystywać argumenty przekazywane do potoku.
    - Przekazywanie stanów niestandardowych:
        - Dane wyjściowe działania, w tym jego stan, mogą być używane przez kolejne działania w potoku.
    - Tworzenie pętli kontenerów:
        - Działanie foreach, będzie ona przechodzić przez określony zbiór działań w pętli. 
- Przepływy na podstawie wyzwalaczy:
    - Potoki mogą być wywoływane na żądanie lub według czasu zgodnie z zegarem.
- Przepływy delta:
    - Parametry może służyć do definiowania usługi górnego dla kopii delta podczas przenoszenia tabel wymiarów i referencyjnych z magazynu relacyjnego, lokalnie lub w chmurze, aby załadować dane do usługi Data lake. 

Aby uzyskać więcej informacji, zobacz [Samouczek: Kontrole przepływu](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dane przekształcone na dużą skalę z potokami niekorzystające z kodu
Nowe środowisko oparte na przeglądarce narzędzi zapewnia niekorzystające z kodu potoku tworzenia i wdrażania za pomocą nowoczesnych, interaktywnych środowiska opartego na sieci web.

Dla deweloperów danych wizualnych i inżynierom danych internetowy interfejs użytkownika usługi Data Factory jest środowisko niekorzystające z kodu projektu, który będzie używany do tworzenia potoków. Jego jest w pełni zintegrowana z Git usługi Visual Studio Online i zapewnia integracji na potrzeby ciągłej integracji/ciągłego Dostarczania i iteracyjne projektowanie za pomocą opcji debugowania.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Rozbudowane dla wielu platform SDK dla zaawansowanych użytkowników
Data Factory w wersji 2 oferuje bogaty pakiet zestawów SDK, które może służyć do tworzenia, zarządzania i monitorowania potoków przy użyciu ulubionego środowiska IDE, w tym:
* Zestaw SDK dla języka Python
* Interfejs wiersza polecenia programu PowerShell
* Zestaw SDK języka C#

Użytkownicy umożliwia również udokumentowanych interfejsów API REST interfejs o usłudze Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Debugowanie przy użyciu narzędzia graficzne i programowanie iteracyjne
Usługa Azure Data Factory narzędzia graficzne, Włącz debugowanie i programowanie iteracyjne. Można tworzyć potoki i uruchomień testów przy użyciu **debugowania** możliwości na kanwie potoku, bez konieczności pisania nawet jednego wiersza kodu. Można wyświetlić wyniki przebiegów testowych w **dane wyjściowe** okna kanwie potoku. Po pomyślnym przebiegu testowego można dodać więcej działań do potoku i Kontynuuj debugowanie w sposób iteracyjny. Możesz również anulować przebiegów testowych, po umieszczeniu ich w toku. 

Nie musisz opublikować zmiany Usługa data factory, przed wybraniem **debugowania**. Jest to przydatne w scenariuszach, w którym chcesz upewnić się, że nowe dodatki lub zmiany będą działać zgodnie z oczekiwaniami, przed uaktualnieniem przepływy pracy fabryki danych w środowiskach programowania, testowym czy produkcyjnym. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Możliwość wdrażania pakietów usług SSIS na platformie Azure 
Jeśli chcesz przenieść obciążenia SSIS, możesz utworzyć fabrykę danych i aprowizowanie środowiska Azure-SSIS integration runtime. Środowisko Azure-SSIS integration runtime jest w pełni zarządzany klaster maszyn wirtualnych platformy Azure (węzłów), których przeznaczeniem uruchamianie pakietów SSIS w chmurze. Aby uzyskać instrukcje krok po kroku, zobacz [wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) samouczka. 
 
### <a name="sdks"></a>Zestawy SDK
Jeśli jesteś użytkownikiem zaawansowanym i szukasz interfejsu programowego, Data Factory oferuje bogaty zestaw SDK, które umożliwia tworzenie, zarządzania i monitorowania potoków przy użyciu ulubionego środowiska IDE. Obsługa języków obejmuje .NET, PowerShell, Python i REST.

### <a name="monitoring"></a>Monitorowanie
Można monitorować fabryk danych przy użyciu programu PowerShell, zestawu SDK lub wizualne narzędzia monitorowania w interfejsie użytkownika przeglądarki. Można monitorować i zarządzać na żądanie, na podstawie wyzwalaczy i opartych na zegarze niestandardowych przepływów w sposób i efektywne. Anuluj istniejących zadań, zobacz awarie na pierwszy rzut oka przejść do szczegółów, aby uzyskać szczegółowe komunikaty o błędach i debugowanie problemów, z jedną taflę szkła bez kontekstu przełączanie lub przechodząc do i z powrotem między ekranami. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nowe funkcje dla usług SSIS w usłudze Data Factory
Od czasu początkowej publicznej wersji zapoznawczej wersji w 2017 r., Data Factory dodano następujące funkcje usług SSIS:

-   Obsługa trzy więcej wariantów/konfiguracji usługi Azure SQL Database na bazę danych usług SSIS (SSISDB) projektów i pakietów:
-   SQL Database za pomocą punktów końcowych usługi sieci wirtualnej
-   Wystąpienie zarządzane
-   Pula elastyczna
-   Obsługa siecią wirtualną usługi Azure Resource Manager, na podstawie klasyczną siecią wirtualną staną się przestarzałe w przyszłości, która pozwala na wsuwania/scalania środowiska Azure-SSIS integration runtime do sieci wirtualnej skonfigurowano usługi SQL Database przy użyciu usługi sieci wirtualnej dostęp do punktów końcowych/indeksu MI/danych lokalnych. Aby uzyskać więcej informacji, zobacz też [dołączyć środowisko Azure-SSIS integration runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).
-   Obsługa uwierzytelniania usługi Azure Active Directory (Azure AD) i uwierzytelnianie SQL, aby nawiązać połączenie bazy danych SSISDB, dzięki czemu uwierzytelniania usługi Azure AD za pomocą tożsamości zarządzanych usługi fabryka danych dla zasobów platformy Azure
-   Obsługa użycie własnej licencji programu SQL Server w środowisku lokalnym, aby uzyskać znaczne oszczędności z poziomu opcji korzyść użycia hybrydowego platformy Azure
-   Wsparcie dla wersji Enterprise programu środowiska Azure-SSIS integration runtime, umożliwiająca korzystanie z funkcji Zaawansowane/premium, interfejs instalacji niestandardowej, aby zainstalować dodatkowe składniki/rozszerzenia i ekosystem partnerów. Aby uzyskać więcej informacji, zobacz też [Enterprise Edition, ustawienia niestandardowe i 3 rozszerzeń innych firm dla usług SSIS w usłudze ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Lepsza integracja usług SSIS w usłudze Data Factory, która umożliwia wywoływanie/wyzwalacza najwyższej jakości działania wykonywania pakietów SSIS w potoków usługi Data Factory i zaplanować ich za pomocą programu SSMS. Aby uzyskać więcej informacji, zobacz też [Modernize i rozszerzanie przepływów pracy usługi ETL/ELT, za pomocą działania SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Co to jest środowisko integration runtime?
Środowisko IR to infrastruktura obliczeniowa, korzystającą z usługi Azure Data Factory w celu zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przenoszenie danych**: W przypadku przenoszenia danych środowisko IR przenosi dane między magazynami danych źródłowych i docelowych, oferuje obsługę wbudowanych łączników, konwersji formatów, mapowania kolumn oraz wydajnego i skalowalnego transferu danych.
- **Wysyłanie działania**: W przypadku przekształcania środowisko integration runtime zapewnia możliwość natywne wykonywanie pakietów usług SSIS.
- **Wykonywanie pakietów usług SSIS**: Środowisko integration runtime natywnie wykonuje pakietów usług SSIS w środowisku zarządzanym obliczeniowych platformy Azure. Środowisko integration runtime obsługuje również wysyłanie i monitorowanie działań przekształcania uruchamianych w różnych usługach obliczeniowych, takich jak Azure HDInsight, Azure Machine Learning, SQL Database i programu SQL Server.

Można wdrożyć co najmniej jedno wystąpienie środowiska IR na potrzeby przenoszenia i przekształcania danych. Środowisko integration runtime można uruchomić w sieci publicznej platformy Azure lub w sieci prywatnej (lokalnej, sieci wirtualnej platformy Azure lub usług Amazon Web Services wirtualnej chmury prywatnej [VPC]). 

Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Co to jest limit liczby środowisk integration Runtime?
Istnieje nie stałego limitu liczby wystąpień środowiska uruchomieniowego integracji, jaką może mieć w usłudze data factory. Istnieje jednak limit liczby rdzeni maszyn wirtualnych, które środowiska integration runtime można użyć na subskrypcję na wykonanie pakietu SSIS. Aby uzyskać więcej informacji, zobacz [limity usługi Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Co to są koncepcje najwyższego poziomu usługi Azure Data Factory?
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Usługa Azure Data Factory zawiera czterech kluczowych składników, które współpracują ze sobą jako platformę, na którym można utworzyć, oparte na danych przepływy pracy, za pomocą kroki w celu przenoszenia i przekształcania danych.

### <a name="pipelines"></a>Potoki
Fabryka danych może obejmować jeden lub wiele potoków. Potok to logiczna grupa działań, które wykonują pracę. które umożliwiają wykonanie zadania. Na przykład potok może zawierać grupę działań, które pozyskiwać dane z obiektu blob platformy Azure, a następnie uruchomić zapytanie programu Hive w klastrze usługi HDInsight w celu podzielenia danych. Korzyścią jest to, że umożliwia potokowi zarządzania działaniami jako zestawem zamiast konieczności zarządzania każde działanie oddzielnie. Możesz łączyć ze sobą działania w potoku w celu ich sekwencyjny lub działać je niezależnie, równolegle.

### <a name="activities"></a>Działania
Działania reprezentują krok przetwarzania w potoku. Na przykład służy działanie kopiowania może służyć do kopiowania danych z jednego magazynu danych do innego magazynu danych. Podobnie można użyć działania programu Hive, które uruchomi zapytanie programu Hive w klastrze usługi Azure HDInsight, aby przekształcić lub przeanalizować dane. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="datasets"></a>Zestawy danych
Zestawy danych reprezentują struktury w magazynach danych. Struktury te po prostu wskazują na dane, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Mechanizm działa w ten sposób: Połączona usługa zawiera definicję połączenia ze źródłem danych, a zestaw danych reprezentuje strukturę danych. Na przykład połączona usługa Azure Storage określa parametry połączenia, aby nawiązać połączenie z kontem usługi Azure Storage. A zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob oraz folder, który zawiera dane.

Połączone usługi mają dwa cele w usłudze Data Factory:

- Do reprezentowania *magazynu danych* obejmują, ale nie jest ograniczona do wystąpienia programu SQL Server w środowisku lokalnym, wystąpienie bazy danych Oracle, udziału plików lub konta usługi Azure Blob storage. Aby uzyskać listę obsługiwanych magazynów danych, zobacz [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md).
- Reprezentowanie *zasobu obliczeniowego*, który może hostować wykonywanie działania. Na przykład działanie HDInsight Hive jest uruchamiany w klastrze usługi HDInsight Hadoop. Aby uzyskać listę działań przekształcania i obsługiwanych obliczeń środowisk, zobacz [Przekształcanie danych w usłudze Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Wyzwalacze
Wyzwalacze reprezentują jednostki przetwarzania, które określają, kiedy rozpoczęła wykonywanie potoku. Istnieją różne typy wyzwalaczy dla różnych typów zdarzeń. 

### <a name="pipeline-runs"></a>Uruchomienia potoków
Uruchomienia potoku to wystąpienie wykonania potoku. Zazwyczaj tworzenia wystąpienia potoku przez przekazanie argumentów do parametrów, które są definiowane w potoku. Argumenty można przekazać ręcznie lub w ramach definicji wyzwalacza.

### <a name="parameters"></a>Parametry
Parametry to pary klucz wartość w konfiguracji tylko do odczytu. Definiowanie parametrów w potoku i przekazać argumenty dla zdefiniowanych parametrów w trakcie wykonania z kontekstu uruchomienia. Kontekst wykonywania jest tworzony przez wyzwalacz lub potok, który można wykonać ręcznie. Działania w ramach potoku wykorzystują wartości parametrów.

Zestaw danych to silnie typizowany parametr oraz jednostki, do której można ponownie użyć lub odwołania. Działanie może odwoływać się zestawów danych i może zużyć właściwości, które są zdefiniowane w definicji zestawu danych.

Połączona usługa to również silnie typizowany parametr zawierający informacje o połączeniu z magazynu danych lub środowiskiem obliczeniowym. Jest również jednostki, do której można ponownie użyć lub odwołania.

### <a name="control-flows"></a>Przepływy sterowania
Przepływów sterowania organizowanie działań potoku, które obejmują tworzenie łańcuchów działań w sekwencję, rozgałęzienia, parametry są zdefiniowane na poziomie potoku oraz argumenty przekazywane podczas wywołania potoku na żądanie lub przy użyciu wyzwalacza. Przepływów sterowania obejmują również przekazywanie stanów niestandardowych oraz tworzenie pętli kontenerów (to znaczy Iteratory foreach).


Aby uzyskać więcej informacji o pojęciach związanych z usługą Data Factory, zobacz następujące artykuły:

- [Dataset and linked services (Zestaw danych i połączone usługi)](concepts-datasets-linked-services.md)
- [Potoki i działania](concepts-pipelines-activities.md)
- [Integration Runtime (Produkt Integration Runtime)](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Co to jest model cenowy dla usługi Data Factory?
Aby uzyskać szczegóły cennika usługi Azure Data Factory, zobacz [szczegóły cennika usługi fabryka danych](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak zapewnić aktualne informacje na temat usługi Data Factory?
Aby uzyskać najbardziej aktualne informacje o usłudze Azure Data Factory przejdź do następujących lokacji:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Strona główna dokumentacji](/azure/data-factory)
- [Strona główna produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Techniczne 

### <a name="how-can-i-schedule-a-pipeline"></a>Jak można zaplanować potok? 
Wyzwalacz harmonogramu lub zarówno wyzwalacz okna czasu służy do planowania potoku. Wyzwalacz będzie używać zgodnie z zegarem harmonogram kalendarza, który może planować potoki okresowo lub we wzorcach powtarzającego się kalendarz oparty na przykład na (poniedziałek o godzinie 18:00:00) i czwartki o godzinie 21:00:00. Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Parametry można przekazać do uruchomienia potoku?
Tak, parametry są najwyższej jakości, najwyższego poziomu pojęcie w usłudze Data Factory. Można zdefiniować parametry na poziomie potoku i przekazać argumenty, podczas wykonywania potoku uruchamianej na żądanie lub przy użyciu wyzwalacza.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Można zdefiniować wartości domyślne dla parametrów potoku? 
Tak. Wartości domyślne dla parametrów można zdefiniować w potokach. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Działania w potoku, mogą wykorzystywać argumenty, które są przekazywane do uruchomienia potoku? 
Tak. Każde działanie w potoku może zużywać wartość parametru, która jest przekazywane do potoku i uruchomić z `@parameter` konstruowania. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Właściwości danych wyjściowych działania mogą być używane w innym działaniu? 
Tak. Wyjście działania mogą być używane w kolejnych działań z `@activity` konstruowania.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak bezpiecznie obsługiwać wartości null w danych wyjściowych działania? 
Możesz użyć `@coalesce` konstruowania w wyrażeniach, które można bezpiecznie obsłużyć wartości null. 

## <a name="mapping-data-flows"></a>Mapowanie przepływu danych

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Która wersja usługi fabryka danych należy używać do tworzenia przepływów danych?
Wersja Data Factory V2 umożliwia tworzenie przepływów danych.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Byłem poprzedniej klientów prywatnej wersji zapoznawczej, która korzystała z przepływu danych, a po użyciu przepływy danych w wersji zapoznawczej w usłudze Data Factory V2.
Ta wersja jest już nieaktualny. Data Factory V2 na użytek przepływów danych.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Co to została zmieniona z prywatnej wersji zapoznawczej ograniczonej publicznej wersji zapoznawczej w zakresie przepływów danych?
Należy już do klastrów usługi Azure Databricks. Data Factory zarządzać tworzenia klastra i zakończenia. Zestawy danych obiektów blob i zestawy danych usługi Azure Data Lake Storage Gen2 są podzielone na tekst rozdzielany i Apache Parquet zestawów danych. Można nadal używać Data Lake Storage Gen2 i Blob storage do przechowywania tych plików. Użyj odpowiedniego połączonej usługi dla tych aparaty magazynu.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Aby Data Factory V2 można migrować fabryki Moje prywatnej wersji zapoznawczej?

Tak. [Postępuj zgodnie z instrukcjami](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Potrzebuję pomocy w rozwiązywaniu problemów Moje logika przepływu danych. Jakie informacje należy zapewnić, aby uzyskać pomoc?

Gdy firma Microsoft zapewnia pomoc i rozwiązywanie problemów przy użyciu przepływów danych, podaj plan kodu DSL. W tym celu wykonaj następujące kroki:

1. W Projektancie przepływu danych wybierz **kodu** w prawym górnym rogu. Spowoduje to wyświetlenie można edytować kod JSON dla przepływu danych.
2. Widok kodu, zaznacz **Planowanie** w prawym górnym rogu. Ten przełącznik zostanie przełączona z formatu JSON do tylko do odczytu sformatowane DSL skryptu planu.
3. Skopiuj i wklej ten skrypt lub zapisać go w pliku tekstowym.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Jak dostęp do danych za pomocą 80 innych typów zestawu danych w usłudze Data Factory?

Funkcja mapowanie przepływu danych umożliwia obecnie usługi Azure SQL Database, Azure SQL Data Warehouse, rozdzielane pliki tekstowe z usługi Azure Blob storage lub Azure Data Lake Storage Gen2 i Parquet plików z magazynu obiektów Blob lub Data Lake Storage Gen2 natywnie źródła i ujścia. 

Użyj działania Copy do przygotowania danych pochodzących z dowolnych innych łączników, a następnie wykonywania działań przepływu danych do przekształcania danych, po to zostały przygotowane. Na przykład potok zostanie najpierw skopiować do magazynu obiektów Blob, a następnie przepływ danych działanie użyje zestawu danych w źródle do przekształcania danych.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać instrukcje krok po kroku utworzysz fabrykę danych zobacz następujące samouczki:

- [Szybki start: Tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md)
- [Samouczek: Kopiowanie danych w chmurze](tutorial-copy-data-dot-net.md)
