---
title: 'Usługi Azure Data Factory: Często zadawane pytania | Dokumentacja firmy Microsoft'
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
ms.openlocfilehash: be0cdeed81c66e1a848b44d2429c1c67bce9b4f3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024097"
---
# <a name="azure-data-factory-faq"></a>Fabrykę danych platformy Azure — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory? 
Data Factory to usługa w pełni zarządzane, oparte na chmurze, integracji danych, która automatyzuje operacje przenoszenia i przekształcania danych. Podobnie jak fabryki, sprzęt, przekształcić surowców w gotowe towary usługi Azure Data Factory organizuje istniejące usługi, które są zbierane są oryginalne dane są następnie przekształcane w gotowe do użycia informacje. 

Za pomocą usługi Azure Data Factory, można utworzyć opartych na danych przepływy pracy służące do przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych. Może przetwarzać i przekształcać dane za pomocą obliczeń usług, takich jak Azure HDInsight, Azure Data Lake Analytics i SQL Server Integration Services (SSIS) środowiska integration runtime. 

Usługa Data Factory można wykonać przetwarzanie danych w usłudze w chmurze oparte na platformie Azure lub we własnym środowisku Self-Hosted obliczeniowych, takich jak SSIS, programu SQL Server lub Oracle. Po utworzeniu potoku, który wykonuje akcję, które są potrzebne, można zaplanować go do wykonywania okresowo (na przykład co godzinę, codziennie lub co tydzień), tworzenia okna harmonogramu lub wyzwalacz potoku z wystąpienia zdarzenia. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Przepływów sterowania i skali 
Do obsługi różnych przepływów i wzorców integracji w nowoczesnym magazynie danych, Data Factory umożliwia modelowanie potoku danych elastyczne, obejmującą przepływ Pełna kontrola programowania paradygmatów, w tym wykonywanie warunkowe rozgałęzianie w potokach danych, a jawne przekazywanie parametrów wewnątrz i pomiędzy tymi przepływami. Przepływ sterowania obejmuje także przekształcać dane za pośrednictwem wysyłanie działania do wykonania zewnętrznego aparatów i możliwości przepływu danych, w tym przenoszenie danych na dużą skalę za pomocą działania kopiowania.

Data Factory oferuje utworzyć dowolny styl przepływu, który jest wymagany przez integrację danych i mogą być wysyłane na żądanie lub wielokrotnie zgodnie z harmonogramem. Przedstawiono kilka typowych przepływów, które ten model umożliwia:   

- Przepływów sterowania:
    - Łańcuch działań w Sekwencje w ramach potoku.
    - Gałąź działania w potoku.
    - Parametry
        - Definiowanie parametrów na poziomie potoku, a następnie przekazać argumenty, podczas gdy wywołujesz potoku na żądanie lub przy użyciu wyzwalacza.
        - Działania mogą wykorzystywać argumenty przekazywane do potoku.
    - Przekazywanie stanów niestandardowych
        - Dane wyjściowe działania, w tym jego stan, mogą być wykorzystywane przez kolejne działania w potoku.
    - Tworzenie pętli kontenerów
        - Dla każdego 
- Przepływy na podstawie wyzwalaczy:
    - Potoki mogą być wywoływane na żądanie lub według czasu zgodnie z zegarem.
- Przepływy delta:
    - Korzystanie z parametrów i zdefiniować swoje górnego dla kopii delta podczas przenoszenia tabel wymiarów i referencyjnych z magazynu relacyjnego, lokalnie lub w chmurze, aby załadować dane do usługi Data lake. 

Aby uzyskać więcej informacji, zobacz [Samouczek: Kontrole przepływu](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Przekształcanie danych w dużej skali z potokami bezpłatne kodu
Nowe środowisko oparte na przeglądarce narzędzi zapewnia niekorzystające z kodu potoku tworzenia i wdrażania za pomocą nowoczesnych, interaktywnych środowiska opartego na sieci web.

Dla deweloperów danych wizualnych i inżynierom danych interfejs użytkownika sieci Web usługi ADF jest środowisko niekorzystające z kodu projektu, który będzie używany do tworzenia potoków. Jego jest w pełni zintegrowana z Git usługi Visual Studio Online i zapewnia integracji na potrzeby ciągłej integracji/ciągłego Dostarczania i iteracyjne projektowanie za pomocą opcji debugowania.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Zaawansowane obejmujące wiele zestawów SDK platformy dla zaawansowanych użytkowników
Jeśli jesteś użytkownikiem zaawansowanym i szukasz interfejsu programowego, ADF w wersji 2 oferuje bogaty zestaw SDK, które mogą być wykorzystywane do tworzenia, zarządzania, monitorowania potoków przy użyciu ulubionego środowiska IDE
1.  Zestaw SDK dla języka Python
2.  Interfejs wiersza polecenia programu PowerShell
3.  Użytkownicy zestawu SDK języka C# można również korzystać z udokumentowanych interfejsów API REST do interfejsu z ADF w wersji 2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Debugowanie za pomocą narzędzi wizualnych i programowanie iteracyjne
Narzędzia visual Data Factory (ADF) platformy Azure umożliwiają wykonywanie debugowanie i programowanie iteracyjne. Można tworzyć potoki i przetestować przebiegów przy użyciu funkcji debugowania na kanwie potoku bez konieczności pisania nawet jednego wiersza kodu. Można wyświetlić wyniki przebiegów testowych w oknie danych wyjściowych w kanwie potoku. Po pomyślnym przebiegu testowego, można dodać więcej działań do potoku i Kontynuuj debugowanie w sposób iteracyjny. Możesz również anulować przebiegów testowych, gdy są one w toku. Nie należy opublikować zmiany Usługa data factory, przed kliknięciem przycisku debugowania. Jest to przydatne w scenariuszach, gdzie chcesz aby upewnić się, że nowa praca dodatki lub zmiany zgodnie z oczekiwaniami, przed uaktualnieniem przepływy pracy fabryki danych w dev, test lub produktu środowisk. 

### <a name="deploy-ssis-packages-to-azure"></a>Wdrażanie pakietów usług SSIS na platformie Azure 
Jeśli chcesz przenieść obciążenia SSIS, możesz utworzyć fabrykę danych i aprowizowanie środowiska Azure-SSIS integration runtime. Środowisko Azure-SSIS integration runtime jest w pełni zarządzany klaster maszyn wirtualnych platformy Azure (węzłów), których przeznaczeniem uruchamianie pakietów SSIS w chmurze. Aby uzyskać instrukcje krok po kroku, zobacz [wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) samouczka. 
 
### <a name="sdks"></a>Zestawy SDK
Jeśli jesteś użytkownikiem zaawansowanym i szukasz interfejsu programowego, ADF zawiera bogaty zestaw SDK, które umożliwia tworzenie, zarządzania i monitorowania potoków przy użyciu ulubionego środowiska IDE. Obsługa języków obejmuje .NET, PowerShell, Python i REST.

### <a name="monitoring"></a>Monitorowanie
Można monitorować fabryk danych przy użyciu programu PowerShell, zestawu SDK lub wizualne narzędzia monitorowania w interfejsie użytkownika przeglądarki. Można monitorować i zarządzać na żądanie, na podstawie wyzwalacza i zegar oparte na niestandardowych przepływów w sposób wydajny i skuteczne. Anuluj istniejących zadań, zobacz awarie na pierwszy rzut oka przejść do szczegółów, aby uzyskać szczegółowe komunikaty o błędach i debugować problemy z jedną taflę szkła bez kontekstu przełączanie lub przechodząc do i z powrotem między ekranami. 

### <a name="new-features-for-ssis-in-adf"></a>Nowe funkcje dla usług SSIS w usłudze ADF
Od czasu początkowej wersji publicznej wersji zapoznawczej w 2017 usługi Data Factory dodano następujące funkcje usług SSIS:

-   Obsługa trzy więcej konfiguracje/wariantów z bazy danych (bazy danych SQL Azure) do katalogu usług SSIS hosta projektów/pakietów (SSISDB):
-   Usługa Azure SQL DB za pomocą punktów końcowych usługi sieci wirtualnej
-   Wystąpienie zarządzane (MI)
-   Pula elastyczna
-   Obsługa usługi Azure Resource Manager Virtual Network (VNet) na podstawie klasycznej sieci wirtualnej, które staną się przestarzałe w przyszłości — dzięki temu można wstawić/join Twojego środowiska Azure-SSIS Integration Runtime (IR) do sieci wirtualnej, która jest skonfigurowany dla usługi Azure SQL DB przy użyciu usługi sieci wirtualnej punkty końcowe/MI / w środowisku lokalnym dostępu do danych, zobacz: https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Obsługa uwierzytelniania usługi Azure Active Directory (AAD) na podstawie uwierzytelniania SQL, aby nawiązać połączenie bazy SSISDB — dzięki temu możesz użyć uwierzytelniania usługi AAD za pomocą usługi ADF tożsamości zarządzanej dla zasobów platformy Azure
-   Obsługa użycie własnej licencji programu SQL Server w środowisku lokalnym, aby uzyskać znaczne oszczędności z poziomu opcji korzyść użycia hybrydowego platformy Azure (AHB)
-   Obsługa Enterprise Edition z Azure-SSIS IR, która umożliwia korzystanie z funkcji Zaawansowane/premium, niestandardowego Instalatora w celu zainstalowania dodatkowych składników/rozszerzeń i 3 ekosystem firm, zobacz: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Lepsza integracja usług SSIS w usłudze ADF, który umożliwia wywoływanie/wyzwalacza najwyższej jakości działania wykonywania pakietów SSIS w potokach ADF i planować je za pomocą programu SSMS, zobacz: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Co to jest środowisko integration runtime?
Środowisko IR to infrastruktura obliczeniowa, która jest używany przez usługi Azure Data Factory w celu zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przenoszenie danych**: W przypadku przenoszenia danych środowisko IR przenosi dane ze źródłowego do docelowego magazynu danych i oferuje obsługę wbudowanych łączników, konwersji formatów, mapowania kolumn oraz wydajnego i skalowalnego transferu danych.
- **Wysyłanie działania**: W przypadku przekształcania środowisko IR umożliwia natywne wykonywanie pakietów usług SSIS.
- **Wykonywanie pakietów usług SSIS**: Natywnie uruchamia pakiety usług SSIS w środowisku zarządzanym obliczeniowych platformy Azure. Ponadto środowisko IR obsługuje wysyłanie i monitorowanie działań przekształcania uruchamianych w różnych usługach obliczeniowych, takich jak Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server i nie tylko.

Można wdrożyć co najmniej jedno wystąpienie środowiska IR na potrzeby przenoszenia i przekształcania danych. Środowisko Integration runtime można uruchomić w sieci publicznej platformy Azure lub w sieci prywatnej (lokalnej, sieci wirtualnej platformy Azure lub usług Amazon Web Services wirtualnej chmury prywatnej [VPC]). 

Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Co to jest limit liczby środowisk integration Runtime?
Istnieje nie stałego limitu liczby wystąpień środowiska uruchomieniowego integracji, jaką może mieć w usłudze data factory. Istnieje jednak limit liczby rdzeni maszyn wirtualnych, które środowiska integration runtime można użyć na subskrypcję na wykonanie pakietu SSIS. Aby uzyskać więcej informacji, zobacz [limity usługi Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Co to są koncepcje najwyższego poziomu usługi Azure Data Factory?
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Usługa Azure Data Factory zawiera czterech kluczowych składników, które współpracują ze sobą jako platformę, na którym można utworzyć, oparte na danych przepływy pracy, za pomocą kroki w celu przenoszenia i przekształcania danych.

### <a name="pipelines"></a>Potoki
Fabryka danych może obejmować jeden lub wiele potoków. Potok to logiczna grupa działań, które wykonują pracę. które umożliwiają wykonanie zadania. Na przykład potok może zawierać grupę działań, które pozyskiwać dane z obiektu blob platformy Azure, a następnie uruchomić zapytanie programu Hive w klastrze usługi HDInsight w celu podzielenia danych. Korzyścią jest to, że umożliwia potokowi zarządzania działaniami jako zestawem zamiast konieczności zarządzania każde działanie oddzielnie. Możesz łączyć ze sobą działania w potoku w celu ich sekwencyjny lub działać je niezależnie, równolegle.

### <a name="activity"></a>Działanie
Działania reprezentują krok przetwarzania w potoku. Na przykład, można użyć *kopiowania* działanie w celu kopiowania danych z jednego magazynu danych do innego magazynu danych. Podobnie można użyć działania programu Hive, które uruchomi zapytanie programu Hive w klastrze usługi Azure HDInsight, aby przekształcić lub przeanalizować dane. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="datasets"></a>Zestawy danych
Zestawy danych reprezentują struktury w magazynach danych. Struktury te po prostu wskazują na dane, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Mechanizm działa w ten sposób: połączona usługa zawiera definicję połączenia ze źródłem danych i zestaw danych reprezentuje strukturę danych. Na przykład połączona usługa Azure Storage określa parametry połączenia, aby nawiązać połączenie z kontem usługi Azure Storage. I zestaw danych obiektów Blob platformy Azure Określa kontener obiektów blob oraz folder, który zawiera dane.

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

### <a name="control-flows"></a>Przepływów sterowania
Przepływów sterowania organizowanie działań potoku, które obejmują tworzenie łańcuchów działań w sekwencję, rozgałęzienia, parametry są zdefiniowane na poziomie potoku oraz argumenty przekazywane podczas wywołania potoku na żądanie lub przy użyciu wyzwalacza. Przepływów sterowania także stanów niestandardowych przekazywanie i tworzenie pętli kontenerów (to znaczy Iteratory for-each).


Aby uzyskać więcej informacji o pojęciach związanych z usługą Data Factory, zobacz następujące artykuły:

- [Zestaw danych i połączone usługi](concepts-datasets-linked-services.md)
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
Wyzwalacz harmonogramu lub zarówno wyzwalacz okna czasu służy do planowania potoku. Wyzwalacz korzysta z harmonogramem zegarowym kalendarza i służą do planowania potoków okresowo lub za pomocą powtarzającego się wzorców, na podstawie kalendarza (na przykład co tydzień w poniedziałek o godzinie 18: 00 i czwartki o godzinie 21: 00). Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Parametry można przekazać do uruchomienia potoku?
Tak, parametry są najwyższej jakości, najwyższego poziomu pojęcie w usłudze ADF. Można zdefiniować parametry na poziomie potoku i przekazać argumenty, podczas wykonywania potoku uruchamianej na żądanie lub przy użyciu wyzwalacza.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Można zdefiniować wartości domyślne dla parametrów potoku? 
Tak. Wartości domyślne dla parametrów można zdefiniować w potokach. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Działania w potoku, mogą wykorzystywać argumenty, które są przekazywane do uruchomienia potoku? 
Tak. Każde działanie w potoku może zużywać wartość parametru, która jest przekazywane do potoku i uruchomić z `@parameter` konstruowania. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Właściwości danych wyjściowych działania mogą być używane w innym działaniu? 
Tak. Wyjście działania mogą być używane w kolejnych działań z `@activity` konstruowania.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak bezpiecznie obsługiwać wartości null w danych wyjściowych działania? 
Możesz użyć `@coalesce` konstruowania w wyrażeniach, które można bezpiecznie obsłużyć wartości null. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać instrukcje krok po kroku utworzysz fabrykę danych zobacz następujące samouczki:

- [Szybki start: Tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md)
- [Samouczek: Kopiowanie danych w chmurze](tutorial-copy-data-dot-net.md)
