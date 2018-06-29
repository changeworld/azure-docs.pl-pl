---
title: 'Fabryka danych Azure: Często zadawane pytania | Dokumentacja firmy Microsoft'
description: Odpowiedzi na często zadawane pytania dotyczące usługi fabryka danych Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045953"
---
# <a name="azure-data-factory-faq"></a>Fabryki danych Azure — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi fabryka danych Azure.  

## <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory? 
Fabryka danych jest usługą pełni zarządzana, oparte na chmurze, integracja danych, która automatyzuje przepływu i przekształcania danych. Podobnie jak fabryka, która uruchamia urządzeń do przekształcania materiałów gotowych fabryki danych Azure organizuje istniejących usług, które zbierać dane i przetransformować je w informacji gotowe do użycia. 

Przy użyciu fabryki danych Azure, można utworzyć danymi przepływy pracy w celu przenoszenia danych między lokalnymi i w chmurze magazynów danych. Może przetwarzać i przekształcanie danych za pomocą obliczeniowe usług, takich jak środowiska uruchomieniowego integracji Azure HDInsight, Azure Data Lake Analytics i SQL Server Integration Services (SSIS). 

Usługi w chmurze systemem Azure albo we własnym środowisku siebie obliczeń, takich jak SSIS, SQL Server lub Oracle przy użyciu fabryki danych można wykonać przetwarzania danych. Po utworzeniu potok, który wykonuje akcję, które są potrzebne, można zaplanować do uruchomienia okresowo (na przykład co godzinę, codziennie lub co tydzień), tworzenia okna harmonogramu lub wyzwalacza potoku z wystąpienia zdarzenia. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Przepływami sterowania i skali 
Do obsługi integracji różnych przepływów i wzorce w magazynie danych nowoczesnych, fabryki danych umożliwia modelowanie potoku elastyczne danych, zawierającą Pełna kontrola przepływu programowania wzorcami tym wykonanie warunkowe gałęzie w potokach danych i jawnie przekazania parametrów w obrębie przepływy oraz. Przepływ sterowania obejmuje również Przekształcanie danych za pośrednictwem działania wysyłania do wykonania zewnętrznego aparaty i możliwości przepływu danych, w tym przenoszenie danych na dużą skalę, za pomocą działania kopiowania.

Fabryka danych udostępnia swobody modelu każdy styl przepływu, który wymaga integracji danych i mogą być wysyłane na żądanie lub wielokrotnie zgodnie z harmonogramem. Ten model umożliwia kilka typowych przepływów są:   

- Przepływami sterowania:
    - Łańcuch działań w sekwencji w potoku.
    - Gałąź działania w potoku.
    - Parametry
        - Zdefiniuj parametry na poziomie potoku i przekazywać argumenty podczas wywołania potoku na żądanie lub wyzwalacza.
        - Działania mogą wykorzystywać argumenty przekazywane do potoku.
    - Przekazywanie stanów niestandardowych
        - Dane wyjściowe działania, w tym jego stan, mogą być wykorzystywane przez kolejne działania w potoku.
    - Tworzenie pętli kontenerów
        - Dla każdego 
- Na podstawie wyzwalania przepływów:
    - Potoki mogą być wyzwalane na żądanie lub według czasu zegara tablicy.
- Przepływy zmian:
    - Parametry i zdefiniuj z górnego różnicowych kopii podczas przenoszenia wymiaru lub odwołanie do tabel z relacyjnego magazynu lokalnie lub w chmurze, aby załadować dane do jeziora. 

Aby uzyskać więcej informacji, zobacz [samouczek: sterowanie przepływem](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Przekształcanie danych na dużą skalę korzystających z potoków wolnego kodu
Nowe środowisko narzędzi przeglądarki zapewnia potoku kodu bez tworzenia i wdrażania przy użyciu nowoczesnych, interaktywnych opartych na sieci web środowiska.

W przypadku danych wizualnych projektantów i inżynierów danych interfejsu użytkownika sieci Web ADF jest środowisku projektowania niekorzystające z kodu, który będzie używany do tworzenia potoki. Jest w pełni zintegrowana z programu Visual Studio Git Online, a zapewnia integrację dla elementu konfiguracji/CD i iteracyjnych programowanie za pomocą opcji debugowania.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Sformatowany cross platform SDK dla użytkowników zaawansowanych
Jeśli jesteś użytkownikiem zaawansowanym i wyszukiwanie interfejs programistyczny, ADF V2 oferuje bogaty zestaw SDK, które można wykorzystać do tworzenia, zarządzania, monitorowania potoki przy użyciu Twoje ulubione IDE
1.  Zestaw SDK dla języka Python
2.  Interfejs wiersza polecenia programu PowerShell
3.  C# SDK użytkowników można też skorzystać udokumentowane interfejsów API REST do interfejsu z fabryki danych AZURE w wersji 2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Iteracyjne projektowanie i debugowanie przy użyciu narzędzia visual
Azure Data Factory (ADF) narzędzia visual umożliwiają wykonywanie iteracyjne projektowanie i debugowanie. Można tworzyć z potoków i przetestować działa przy użyciu funkcji w debugowania na kanwie potoku bez pisania pojedynczy wiersz kodu. W oknie dane wyjściowe z procesu roboczego można wyświetlić wyniki z testów. Po pomyślnym testu, można dodać więcej działań do potoku sieci i Kontynuuj debugowanie w sposób iteracji. Można również anulować Twojej uruchomień testów, gdy są w toku. Nie należy opublikować zmiany usługi fabryka danych przed kliknięciem przycisku debugowania. Jest to przydatne w scenariuszach, w którym ma zostać upewnij się, że nowe pracy dodatków lub zmian zgodnie z oczekiwaniami przed zaktualizowaniem przepływy pracy fabryki danych w deweloperów, testów lub środowiska produkcyjnego. 

### <a name="deploy-ssis-packages-to-azure"></a>Wdrażanie pakietów usług SSIS na platformie Azure 
Jeśli chcesz przenieść obciążeń SSIS, można utworzyć fabryki danych i udostępnić środowiska uruchomieniowego integracji usług SSIS Azure. Środowisko wykonawcze integracji usług SSIS Azure jest pełni zarządzany klaster maszyn wirtualnych platformy Azure (węzłów), które są przeznaczone wyłącznie do uruchamiania pakietów SSIS w chmurze. Aby uzyskać instrukcje, zobacz [pakietów SSIS wdrażanie na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) samouczka. 
 
### <a name="sdks"></a>Zestawy SDK
Jeśli jesteś użytkownikiem zaawansowanym i wyszukiwanie interfejs programistyczny, ADF zapewnia bogaty zestaw SDK, które służy do tworzenia, zarządzania i monitorowania potoki przy użyciu Twoje ulubione IDE. Obsługa języka obejmuje .NET, programu PowerShell, Python i REST.

### <a name="monitoring"></a>Monitorowanie
Można monitorować z fabryki danych za pomocą programu PowerShell, zestawu SDK lub Visual narzędzia monitorowania w interfejsie użytkownika przeglądarki. Można monitorować i zarządzać nimi na żądanie, wyzwalania na podstawie i zegara zmiennych przepływów niestandardowych w sposób wydajny i efektywny. Aby uzyskać szczegółowe komunikaty o błędach i debugowania problemów z jednego okienka szkła bez kontekstu przełączanie lub i z powrotem nawigowanie między ekranami Anuluj istniejących zadań, zobacz awarie jeden rzut oka, przejść do szczegółów. 

### <a name="new-features-for-ssis-in-adf"></a>Nowe funkcje usług SSIS w ADF
Od początkowej wersji publicznej wersji zapoznawczej w 2017 fabryki danych została dodana do usług SSIS, następujące funkcje:

-   Obsługuje trzy więcej konfiguracje/wariantów z Azure bazy danych (bazy danych SQL) do katalogu usług SSIS hosta projektów/pakietów (SSISDB):
-   Bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej
-   Zarządzane wystąpienia (MI)
-   Pula elastyczna
-   Obsługa Menedżera zasobów sieci wirtualnej platformy Azure (VNet) u góry klasycznych sieci wirtualnej, które zostaną wycofane w przyszłości — dzięki temu można wstrzyknąć/sprzężenia sieci Azure SSIS integracji środowiska uruchomieniowego (IR) do sieci wirtualnej, skonfigurowanego dla bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej/MI / lokalnego dostępu do danych, zobacz: https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Obsługa uwierzytelniania usługi Azure Active Directory (AAD) u góry uwierzytelniania SQL, aby nawiązać połączenie z bazy danych SSISDB — to pozwala używać uwierzytelniania w usłudze AAD z ADF zarządzane usługi tożsamości (MSI)
-   Obsługa dostarczają własnej licencji programu SQL Server lokalnego, aby uzyskać znaczne oszczędności z opcji Azure korzyści hybrydowych (AHB)
-   Obsługa Enterprise Edition z Azure SSIS IR umożliwiające korzystanie z funkcji Zaawansowane/premium, instalacja niestandardowa, aby zainstalować dodatkowe składniki/rozszerzenia i 3 ekosystem firm, zobacz: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Lepsza integracja usług SSIS w ADF umożliwiający wywołać/wyzwalacza najwyższej jakości wykonanie pakietu usług SSIS działań w potokach ADF i zaplanować ich za pomocą narzędzia SSMS, zobacz: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Co to jest środowiska uruchomieniowego integracji?
Integracja środowiska uruchomieniowego jest używany przez fabryki danych Azure zapewniają następujące możliwości integracji danych w różnych środowiskach sieci infrastruktury obliczeniowej:

- **Przenoszenie danych**: dla przepływu danych środowiska uruchomieniowego integracji przenosi dane między magazynami danych źródłowych i docelowych, zapewniając obsługę dla łączników, Konwersja formatu, mapowania kolumn i wydajności i transfer danych skalowalnych.
- **Wysłanie działania**: dla transformacji, integrację środowiska uruchomieniowego oferować natywnie wykonanie pakietów SSIS.
- **Wykonanie pakietów SSIS**: natywnie wykonuje pakietów SSIS w środowisku zarządzanym obliczeń platformy Azure. Ponadto środowisko IR obsługuje wysyłanie i monitorowanie działań przekształcania uruchamianych w różnych usługach obliczeniowych, takich jak Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server i nie tylko.

Jeden lub wiele wystąpień środowiska uruchomieniowego integracji można wdrożyć jako wymagane do przeniesienia i przekształcania danych. Integracja środowiska uruchomieniowego można uruchomić w sieci publicznej platformy Azure lub w sieci prywatnej (lokalnej, sieci wirtualnej platformy Azure lub usług Amazon Web Services wirtualnego chmury prywatnej [VPC]). 

Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Co to jest limit liczby środowisk uruchomieniowych integracji?
Nie ma żadnego twardych limitu liczby wystąpień środowiska uruchomieniowego integracji, które mogą mieć w fabryce danych. Istnieje jednak limit liczby rdzeni maszyny Wirtualnej, które środowiska uruchomieniowego integracji można użyć dla subskrypcji wykonywanie pakietów SSIS. Aby uzyskać więcej informacji, zobacz [ogranicza fabryki danych](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Co to są najwyższego poziomu pojęcia związane z fabryki danych Azure?
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Fabryka danych Azure zawiera cztery najważniejsze składniki, które współpracują ze sobą jako platformy, na którym można utworzyć, opartych na danych z przepływów pracy czynności, aby przenieść i przekształcanie danych.

### <a name="pipelines"></a>Potoki
Fabryka danych może obejmować jeden lub wiele potoków. Potok to logiczne grupowanie działań do wykonania jednostkę pracy. które umożliwiają wykonanie zadania. Na przykład potoku może zawierać grupy działań, które pozyskiwania danych z obiektów blob platformy Azure, a następnie uruchom zapytania programu Hive w klastrze usługi HDInsight do partycjonowania danych. Korzyścią jest to, że można użyć potoku do zarządzania działania zgodnie z ustaleniami nie trzeba zarządzać każde działanie oddzielnie. Użytkownik może łańcuch działań w potoku do ich działania sekwencyjnie, lub nie działać je niezależnie, równolegle.

### <a name="activity"></a>Działanie
Działania reprezentują krok przetwarzania w potoku. Na przykład można użyć *kopiowania* działanie, aby skopiować dane z jednym magazynie danych do innego magazynu danych. Podobnie można użyć działania Hive, uruchamianego zapytań programu Hive w klastrze Azure HDInsight do transformacji lub analizowania danych. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="datasets"></a>Zestawy danych
Zestawy danych reprezentują struktury w magazynach danych. Struktury te po prostu wskazują na dane, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Należy traktować go w ten sposób: połączonej usługi definiuje połączenie ze źródłem danych i struktury danych reprezentuje zestaw danych. Na przykład połączoną usługą magazynu Azure określa parametry połączenia, aby nawiązać połączenie z kontem magazynu platformy Azure. I zestawu danych obiektów Blob platformy Azure określa folder, który zawiera dane i kontener obiektów blob.

Połączone usługi ma dwa cele w fabryce danych:

- Do reprezentowania *magazynu danych* obejmuje, ale nie jest ograniczona do lokalnego wystąpienia programu SQL Server, wystąpienie bazy danych Oracle, udziału plików lub konta magazynu obiektów Blob platformy Azure. Aby uzyskać listę obsługiwanych magazynów w temacie [działanie kopiowania w fabryce danych Azure](copy-activity-overview.md).
- Reprezentowanie *zasobu obliczeniowego*, który może hostować wykonywanie działania. Na przykład działanie HDInsight Hive jest uruchomione w klastrze usługi HDInsight Hadoop. Listę działań transformacji i środowisk obliczeniowych obsługiwane, zobacz [Przekształć dane w fabryce danych Azure](transform-data.md).

### <a name="triggers"></a>Wyzwalacze
Wyzwalacze reprezentują jednostki przetwarzania, która określa, kiedy zostało rozpoczęte wykonywania potoku. Istnieją różne typy wyzwalaczy dla różnych typów zdarzeń. 

### <a name="pipeline-runs"></a>Uruchomienia potoków
Uruchom potoku jest wystąpienie wykonywania potoku. Zazwyczaj wystąpienia potoku, uruchamiając przekazywanie argumentów do parametrów, które są zdefiniowane w potoku. Można przekazać argumenty ręcznie lub w ramach definicji wyzwalacza.

### <a name="parameters"></a>Parametry
Parametry są pary klucz wartość w trybie tylko do odczytu konfiguracji. Zdefiniuj parametry w potoku i przekazać argumenty zdefiniowanych parametrów podczas wykonywania z kontekstu wykonywania. Kontekst wykonywania jest tworzony przez wyzwalacz lub potok, który można wykonywać ręcznie. Działania w ramach potoku wykorzystują wartości parametrów.

Zestaw danych jest jednoznacznie parametr i jednostką, której można użyć ponownie lub referencyjnego. Działania może odwoływać się zestawów danych i może używać właściwości, które są zdefiniowane w definicji zestawu danych.

Połączona usługa jest również jednoznacznie parametr, który zawiera informacje o połączeniu do magazynu danych lub środowiska obliczeniowego. Istnieje również jednostką, której można użyć ponownie lub referencyjnego.

### <a name="control-flows"></a>Przepływu sterowania
Przepływami sterowania organizowania potoku działań, które obejmują tworzenie łańcuchów działań w sekwencji, rozgałęzianie, parametrów zdefiniowanych przez użytkownika na poziomie potoku i argumentów, które przekazujesz podczas wywołania potoku na żądanie lub wyzwalacza. Przepływami sterowania także stan niestandardowe przekazywanie i zapętlenia kontenery (Iteratory dla każdego).


Aby uzyskać więcej informacji o pojęciach związanych z usługą Data Factory, zobacz następujące artykuły:

- [Zestaw danych i połączone usługi](concepts-datasets-linked-services.md)
- [Potoki i działania](concepts-pipelines-activities.md)
- [Integration Runtime (Produkt Integration Runtime)](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Co to jest model cen dla fabryki danych?
Aby uzyskać szczegóły cennika usługi fabryka danych Azure, zobacz [szczegóły cennika usługi fabryka danych](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak zapewnić aktualne informacje o fabryki danych?
Aby uzyskać najbardziej aktualne informacje o usłudze fabryka danych Azure przejdź do następujących lokacji:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Strona główna dokumentacji](/azure/data-factory)
- [Strona główna produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Nowości techniczne 

### <a name="how-can-i-schedule-a-pipeline"></a>Jak można zaplanować potoku? 
Można zaplanować potoku można użyć wyzwalacza harmonogramu lub wyzwalacza okno czasu. Wyzwalacz wykorzystuje harmonogram kalendarza wall zegara i służy do planowania potoki okresowo lub za pomocą kalendarza na podstawie powtarzającego się wzorce (na przykład, co tydzień w poniedziałek godzinie 6 i czwartki o 21: 00). Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Aby uruchomić potoku można przekazywać parametrów?
Parametry są tak, pojęcie najwyższej jakości, najwyższego poziomu w ADF. Można zdefiniować parametrów na poziomie potoku i przekazywać argumenty, jak wykonać potoku uruchamiane na żądanie lub przy użyciu wyzwalacza.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Czy można zdefiniować wartości domyślne parametrów potoku? 
Tak. Wartości domyślne dla parametrów można zdefiniować w potokach. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Działania w potoku można korzystać z argumentów, które są przekazywane do procesu, uruchom? 
Tak. Każde działanie w potoku, jaką może wykorzystać wartość parametru, które zostały przekazane do potoku Uruchom z `@parameter` utworzenia. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Właściwości danych wyjściowych działania mogą być używane w innym działaniu? 
Tak. Dane wyjściowe działania mogą być używane w następnych działań z `@activity` utworzenia.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak bezpiecznie Obsługa wartości zerowych w danych wyjściowych działania? 
Można użyć `@coalesce` skonstruować w wyrażeniach można bezpiecznie obsłużyć wartości null. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać instrukcje krok po kroku można utworzyć fabryki danych zobacz następujące samouczki:

- [Szybki Start: Tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md)
- [Samouczek: Kopiowanie danych w chmurze](tutorial-copy-data-dot-net.md)
