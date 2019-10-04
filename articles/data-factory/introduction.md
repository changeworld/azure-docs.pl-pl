---
title: Wprowadzenie do usługi Azure Data Factory | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure Data Factory, czyli usłudze integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 7bc03e80fc49756d19677edbef6bd8d372849732
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937255"
---
# <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory?

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-introduction.md)
> * [Bieżąca wersja](introduction.md)

W świecie danych big data dane nieprzetworzone i niezorganizowane często są przechowywane w relacyjnych, nierelacyjnych i innych systemach magazynowania. Jednak same nieprzetworzone dane nie mają prawidłowego kontekstu ani znaczenia umożliwiającego zapewnienie istotnych informacji analitykom, specjalistom z zakresu danych i osobom podejmującym decyzje biznesowe. 

Dane big data wymagają usługi, która umożliwia organizację i operacjonalizację procesów mających na celu przekształcenie tych ogromnych zbiorów nieprzetworzonych danych w praktyczną analizę biznesową. Azure Data Factory to zarządzana usługa w chmurze, stworzona z myślą o tych kompleksowych, hybrydowych projektach typu wyodrębnianie-transformacja-ładowanie (ETL), wyodrębnianie-ładowanie-transformacja (ELT) i integracji danych.

Na przykład wyobraź sobie producenta gier, który zbiera petabajty dzienników gier tworzonych przez gry w chmurze. Firma chce analizować te dzienniki w celu uzyskania szczegółowych informacji dotyczących preferencji klientów, danych demograficznych oraz zachowania użytkowników podczas korzystania z gier. Dzięki temu firma będzie mogła identyfikować możliwości sprzedaży droższych produktów i sprzedaży wiązanej, opracowywać nowe atrakcyjne funkcje pozytywnie wpływające na rozwój biznesowy oraz oferować klientom udoskonalone środowisko gier.

Do analizy tych dzienników firma musi użyć danych referencyjnych, np. informacji o kliencie, grze i kampanii marketingowej, które znajdują się w lokalnym magazynie danych. Firma chce korzystać z tych danych z lokalnego magazynu danych, łącząc je z dodatkowymi danymi dzienników znajdującymi się w magazynie danych w chmurze. 

Aby wyodrębnić szczegółowe informacje, chce przetworzyć połączone dane przy użyciu klastra Spark w chmurze (Azure HDInsight), a następnie opublikować przekształcone dane w magazynie danych w chmurze, takim jak Azure SQL Data Warehouse, co ułatwi tworzenie raportów na ich podstawie. Firma zamierza zautomatyzować przepływ pracy, a także monitorować go i zarządzać nim w cyklu dziennym. Poza tym chce wykonywać przepływ pracy, gdy pliki znajdą się w kontenerze usługi Blob Storage.

Azure Data Factory to platforma, która umożliwia pracę z takimi scenariuszami danych. Jest to *oparta na chmurze usługa integracji danych, za pomocą której można tworzyć oparte na danych przepływy pracy w chmurze służące do organizowania i automatyzowania przenoszenia i przekształcania danych*. Za pomocą usługi Azure Data Factory można tworzyć oparte na danych przepływy pracy (nazywane potokami) i ustalać ich harmonogram. Te przepływy mogą pozyskiwać dane z różnych magazynów danych, przetwarzać i przekształcać je za pomocą usług obliczeniowych, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics i Azure Machine Learning. 

Ponadto umożliwiają publikowanie danych wyjściowych w magazynach danych, np. Azure SQL Data Warehouse, w celu użycia przez aplikacje analizy biznesowej. Ostatecznie dzięki usłudze Azure Data Factory nieprzetworzone dane mogą być organizowane w praktyczne magazyny i usługi data lake ułatwiające podejmowanie właściwych decyzji biznesowych.

![Widok najwyższego poziomu usługi Data Factory](media/introduction/big-picture.png)

## <a name="how-does-it-work"></a>Jak to działa?
Potoki (oparte na danych przepływy pracy) w usłudze Azure Data Factory zwykle wykonują następujące cztery kroki:

![Cztery kroki przepływu pracy opartego na danych](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Łączenie i zbieranie

Przedsiębiorstwa dysponują danymi różnych typów, znajdującymi się w różnych magazynach lokalnych i w chmurze, ze strukturą pełną i częściową, przychodzącymi w różnych interwałach i w różnym tempie. 

Pierwszy krok tworzenia systemu uzyskiwania informacji polega na połączeniu wszystkich wymaganych źródeł danych i systemów przetwarzania, takich jak usługi SaaS, bazy danych, udziały plików oraz internetowe usługi FTP. Następnym krokiem jest przeniesienie danych w miarę potrzeb do centralnej lokalizacji w celu ich dalszego przetwarzania.

Firmy, które nie korzystają z usługi Data Factory, muszą tworzyć niestandardowe składniki umożliwiające przepływ danych lub projektować własne usługi, aby zintegrować źródła danych i systemy przetwarzania. Takie podejście jest kosztowne, a integracja i utrzymanie systemów stwarza trudności. Ponadto rozwiązanie to rzadko zapewnia mechanizmy kontrolne oraz funkcje monitorowania i wysyłania alertów na poziomie korporacyjnym. Możliwości te są natomiast dostępne w przypadku w pełni zarządzanej usługi.

Usługa Data Factory udostępnia [działanie kopiowania](copy-activity-overview.md) w potoku danych, które pozwala przenosić dane z lokalnych magazynów danych i źródeł danych w chmurze do centralnego magazynu danych w chmurze w celu przeprowadzenia kolejnych etapów analizy. Można na przykład zebrać dane w Azure Data Lake Storage i przekształcić je później za pomocą usługi obliczeniowej Azure Data Lake Analytics. Można też pobrać dane z usługi Azure Blob Storage, aby przekształcić je później przy użyciu klastra usługi Azure HDInsight na platformie Hadoop.

### <a name="transform-and-enrich"></a>Przekształcanie i wzbogacanie
Po umieszczeniu danych w centralnym magazynie danych w chmurze zebrane dane należy przetworzyć lub przekształcić za pomocą usług obliczeniowych, takich jak HDInsight Hadoop, Spark, Data Lake Analytics i Machine Learning. Przekształcone dane powinny być generowane niezawodnie i zgodnie z możliwym do utrzymania i kontrolowania harmonogramem, aby zapewnić środowiskom produkcyjnym zaufane dane.

### <a name="publish"></a>Publikuj
Po przekształceniu danych pierwotnych do postaci gotowej dla działań biznesowych należy załadować je do magazynu Azure Data Warehouse, bazy danych Azure SQL Database, bazy danych Azure CosmosDB lub dowolnego aparatu analizy, z którego mogą korzystać użytkownicy biznesowi przy użyciu swoich narzędzi do analizy biznesowej.

### <a name="monitor"></a>Monitorowanie
Po pomyślnym utworzeniu i wdrożeniu potoku integracji danych, zapewniającego wartość biznesową danym przetworzonym, należy monitorować zaplanowane działania i potoki pod kątem współczynników powodzenia i niepowodzenia. Usługa Azure Data Factory dysponuje wbudowaną obsługą monitorowania potoków przy użyciu narzędzi Azure Monitor, API, PowerShell, dzienników usługi Azure Monitor i paneli kondycji w witrynie Azure Portal.

## <a name="top-level-concepts"></a>Koncepcje najwyższego poziomu
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Usługa Azure Data Factory składa się z czterech głównych składników. Ich współdziałanie pozwala udostępnić platformę umożliwiającą tworzenie opartych na danych przepływów pracy wraz z etapami służącymi do przenoszenia i przekształcania danych.

### <a name="pipeline"></a>Potok
Fabryka danych może obejmować jeden lub wiele potoków. Potoki to logiczne grupy działań, które wspólnie wykonują pracę. które umożliwiają wykonanie zadania. Na przykład potok może zawierać grupę działań, które pozwalają pozyskać dane z obiektu blob platformy Azure, a następnie uruchomić zapytanie programu Hive w klastrze usługi HDInsight w celu podzielenia danych. 

Zaletą korzystania z potoku jest możliwość zarządzania zestawem działań, a nie pojedynczymi zadaniami. Działania w potoku można ze sobą połączyć w sposób sekwencyjny, mogą też działać niezależnie i równolegle.

### <a name="activity"></a>Działanie
Działania reprezentują krok przetwarzania w potoku. Można na przykład użyć działania kopiowania w celu skopiowania danych z jednego magazynu danych do drugiego. Podobnie można użyć działania programu Hive, które uruchomi zapytanie programu Hive w klastrze usługi Azure HDInsight, aby przekształcić lub przeanalizować dane. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="datasets"></a>Zestawy danych
Zestawy danych reprezentują struktury w magazynach danych. Struktury te po prostu wskazują na dane, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Mechanizm ten działa następująco: połączona usługa zawiera definicję połączenia ze źródłem danych, a zestaw danych reprezentuje strukturę danych. Na przykład połączona usługa Azure Storage określa parametry połączenia, które umożliwiają połączenie z kontem usługi Azure Storage. Natomiast zestaw danych usługi Azure Blob określa kontener obiektów blob oraz folder, który zawiera dane.

Połączone usługi w usłudze Fabryka danych służą do dwóch celów:

- Reprezentowanie **magazynu danych**, w tym między innymi bazy danych na lokalnym serwerze SQL Server, bazy danych Oracle, udziału plików lub konta usługi Azure Blob Storage. Listę obsługiwanych magazynów danych zamieszczono w artykule na temat [działania kopiowania](copy-activity-overview.md).

- Reprezentowanie **zasobu obliczeniowego**, który może hostować wykonywanie działania. Na przykład działanie HDInsightHive jest wykonywane w klastrze HDInsight na platformie Hadoop. Listę działań przekształcania i obsługiwanych środowisk obliczeniowych zamieszczono w artykule dotyczącym [przekształcania danych](transform-data.md).

### <a name="triggers"></a>Wyzwalacze
Wyzwalacze reprezentują jednostki przetwarzania, które określają, kiedy należy rozpocząć wykonanie potoku. Istnieją różne typy wyzwalaczy dla różnych typów zdarzeń.

### <a name="pipeline-runs"></a>Uruchomienia potoków
Uruchomienie potoku to wystąpienie wykonania potoku. Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów zdefiniowanych w potokach. Argumenty można przekazać ręcznie lub w ramach definicji wyzwalacza.

### <a name="parameters"></a>Parametry
Parametry to pary klucz-wartość w konfiguracji tylko do odczytu.  Parametry są definiowane w potoku. Argumenty dla zdefiniowanych parametrów są przekazywane w trakcie wykonania z kontekstu uruchomienia utworzonego przez wyzwalacz lub potok wykonany ręcznie. Działania w ramach potoku wykorzystują wartości parametrów.

Zestaw danych to silnie typizowany parametr oraz obiekt wielokrotnego użytku, do którego można się odwoływać. Działanie może odwoływać się do zestawów danych oraz wykorzystywać właściwości określone w definicji zestawu danych.

Połączona usługa to również silnie typizowany parametr zawierający informacje o połączeniu z magazynem danych lub środowiskiem obliczeniowym. Również jest to obiekt wielokrotnego użytku, do którego można się odwoływać.

### <a name="control-flow"></a>Przepływ sterowania
Przepływ sterowania to inaczej organizacja działań potoku, która obejmuje działania połączone w sekwencję, rozgałęzienia, parametry zdefiniowane na poziomie potoku oraz argumenty przekazywane podczas wywoływania potoku na żądanie lub przy użyciu wyzwalacza. Zawiera również kontenery przekazywania stanów niestandardowych i pętli, czyli iteratory For-each.


Aby uzyskać więcej informacji o pojęciach związanych z usługą Data Factory, zobacz następujące artykuły:

- [Dataset and linked services (Zestaw danych i połączone usługi)](concepts-datasets-linked-services.md)
- [Potoki i działania](concepts-pipelines-activities.md)
- [Integration Runtime (Produkt Integration Runtime)](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Obsługiwane regiony

Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Jednak fabryka danych może mieć dostęp do magazynów danych i usług obliczeniowych w innych regionach świadczenia usługi Azure, aby przenosić dane między magazynami danych lub przetwarzać dane przy użyciu usług obliczeniowych.

Sama usługa Azure Data Factory nie przechowuje żadnych danych. Usługa umożliwia tworzenie opartych na danych przepływów pracy do aranżowania przenoszenia danych między obsługiwanymi magazynami danych oraz przetwarzania danych przy użyciu usług obliczeniowych w innych regionach lub w środowisku lokalnym. Umożliwia także monitorowanie przepływów pracy i zarządzanie nimi przy użyciu zarówno mechanizmów programowych, jak i interfejsu użytkownika.

Chociaż usługa Data Factory jest dostępna tylko w wybranych regionach, to usługa zapewniająca przenoszenie danych w usłudze Data Factory jest dostępna globalnie w kilku regionach. Jeśli magazyn danych znajduje się za zaporą, dane są przenoszone przez własne środowisko Integration Runtime zainstalowane w środowisku lokalnym.

Załóżmy na przykład, że środowiska obliczeniowe, takie jak klaster usługi Azure HDInsight i usługa Azure Machine Learning, są uruchamiane z regionu Europa Zachodnia. Można utworzyć i zastosować wystąpienie usługi Azure Data Factory w regionie Wschodnie stany USA lub Wschodnie stany USA 2 oraz użyć go do planowania zadań wykonywanych w środowiskach obliczeniowych w regionie Europa Zachodnia. Wyzwolenie zadania w środowisku obliczeniowym zajmuje usłudze Data Factory kilka milisekund, ale czas uruchomienia zadania w środowisku obliczeniowym nie zmienia się.

## <a name="accessibility"></a>Ułatwienia dostępu

Dostępne jest środowisko użytkownika usługi Data Factory w witrynie Azure Portal.

## <a name="compare-with-version-1"></a>Porównanie z wersją 1
Aby uzyskać listę różnic między wersjami 1 i bieżącą usługi Data Factory, zobacz [Porównanie z wersją 1](compare-versions.md). 

## <a name="next-steps"></a>Następne kroki
Wprowadzenie do tworzenia potoku usługi Data Factory przy użyciu jednego z następujących narzędzi/zestawów SDK: 

- [Interfejs użytkownika usługi Data Factory w witrynie Azure Portal](quickstart-create-data-factory-portal.md)
- [Narzędzie do kopiowania danych w witrynie Azure Portal](quickstart-create-data-factory-copy-data-tool.md)
- [Program PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Szablon usługi Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md)
 
