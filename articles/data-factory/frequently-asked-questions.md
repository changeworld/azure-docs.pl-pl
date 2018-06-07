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
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: b7f654fb3b24954bb1c9222a4e97dc4b6844411c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619035"
---
# <a name="azure-data-factory-faq"></a>Fabryki danych Azure — często zadawane pytania
Ten artykuł dotyczy wersji 2 usługi fabryka danych Azure. Zawiera on odpowiedzi na często zadawane pytania dotyczące usługi fabryka danych.  

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [— często zadawane pytania dla fabryki danych w wersji 1](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory? 
Fabryka danych jest usługą pełni zarządzana, oparte na chmurze, integracja danych, która automatyzuje przepływu i przekształcania danych. Podobnie jak fabryka, która uruchamia urządzeń do przekształcania materiałów gotowych fabryki danych Azure organizuje istniejących usług, które zbierać dane i przetransformować je w informacji gotowe do użycia. 

Przy użyciu fabryki danych Azure, można utworzyć danymi przepływy pracy w celu przenoszenia danych między lokalnymi i w chmurze magazynów danych. Może przetwarzać i przekształcanie danych za pomocą obliczeniowe usług, takich jak środowiska uruchomieniowego integracji Azure HDInsight, Azure Data Lake Analytics i SQL Server Integration Services (SSIS). 

Usługi w chmurze systemem Azure albo we własnym środowisku siebie obliczeń, takich jak SSIS, SQL Server lub Oracle przy użyciu fabryki danych można wykonać przetwarzania danych. Po utworzeniu potok, który wykonuje czynność, którą należy można zaplanować jego wyzwalacza potoku z wystąpienia zdarzeń lub uruchom okresowo (na przykład co godzinę, codziennie lub co tydzień). Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>Czym się różni wersja 2?
Usługa Azure Data Factory w wersji 2 opiera się na oryginalnej usłudze przenoszenia i przekształcania danych Azure Data Factory, obsługując jednocześnie większy zestaw scenariuszy integracji danych w chmurze. Fabryka danych Azure w wersji 2 oferuje następujące możliwości:

- Przepływami sterowania i skali
- Wdrażanie i uruchamianie pakietów SSIS na platformie Azure

Po wydaniu wersji 1 możemy rozpoznany, że klienci muszą projektowania złożonych, scenariusze integracji danych hybrydowych, które wymagają zarówno przenoszenia danych i przetwarzania w chmurze, lokalne i na maszynach wirtualnych w chmurze (VM). Te wymagania Przełącz trzeba przenieść i przetwarzania danych wewnątrz zabezpieczonych środowisk sieci wirtualnych i skalowania w poziomie z mocy obliczeniowej na żądanie.

Jak potoki danych stają się coraz bardziej istotną częścią strategii analizy biznesowej, zaobserwowano, że te działania wymaga elastyczne planowanie do obsługi obciążeń danych pierwotnych i wykonaniami wyzwolenia zdarzenia. Miarę wzrostu złożoności, więc zbyt jest wymaganie usługi do obsługi wspólnej wzorcami przepływu pracy, obejmujących rozgałęzianie, pętli i przetwarzania warunkowego.

Wersja 2 umożliwia migrowanie istniejących pakietów SSIS do chmury. Ta akcja wind i przenosi SSIS jako usługa Azure, która jest zarządzana w fabryce danych, która korzysta z nowych funkcji środowiska uruchomieniowego integracji. Przez Obracająca się środowiska uruchomieniowego integracji usług SSIS w wersji 2, można wykonać, zarządzania, monitorowania i tworzenia pakietów SSIS w chmurze.

### <a name="control-flows-and-scale"></a>Przepływami sterowania i skali 
Do obsługi integracji różnych przepływów i wzorce w magazynie danych nowoczesnych, fabryki danych jest włączony model procesu nowych, elastycznych danych, który jest już powiązane dane szeregów czasowych. W tej wersji można modelu warunków, gałąź w przepływ kontroli w potoku danych i jawnie przekazania parametrów w obrębie przepływy oraz.

Masz teraz swobody modelu każdy styl przepływu, który wymaga integracji danych i mogą być wysyłane na żądanie lub wielokrotnie zgodnie z harmonogramem. Kilka typowych przepływów dostępnych obecnie, które wcześniej nie były możliwe, to:   

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

### <a name="deploy-ssis-packages-to-azure"></a>Wdrażanie pakietów usług SSIS na platformie Azure 
Jeśli chcesz przenieść obciążeń SSIS, można utworzyć fabryki danych w wersji 2 i udostępnić środowiska uruchomieniowego integracji usług SSIS Azure. Środowisko wykonawcze integracji usług SSIS Azure jest pełni zarządzany klaster maszyn wirtualnych platformy Azure (węzłów), które są przeznaczone wyłącznie do uruchamiania pakietów SSIS w chmurze. Aby uzyskać instrukcje, zobacz [pakietów SSIS wdrażanie na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) samouczka. 
 

### <a name="sdks"></a>Zestawy SDK
Jeśli jesteś użytkownikiem zaawansowanym i wyszukiwanie interfejs programistyczny, wersja 2 zawiera bogaty zestaw SDK, które służy do tworzenia, zarządzania i monitorowania potoki przy użyciu Twoje ulubione IDE.

- **.NET SDK**: zestaw .NET SDK został zaktualizowany do wersji 2. 
- **PowerShell**: polecenia cmdlet programu PowerShell zostały zaktualizowane do wersji 2. Polecenia cmdlet w wersji 2 mają w nazwie *DataFactoryV2*. Na przykład *Get-AzureRmDataFactoryV2*. 
- **Python SDK**: ten zestaw SDK jest nowym składnikiem w wersji 2.
- **REST API**: interfejs API REST został zaktualizowany w wersji 2.  

Zestawy SDK, które zostały zaktualizowane w wersji 2, nie są zgodne wstecz z klientami w wersji 1. 

### <a name="monitoring"></a>Monitorowanie
Obecnie wersja 2 obsługuje monitorowanie fabryk danych przy użyciu samych zestawów SDK. Portal nie obsługuje jeszcze monitorowania fabryk danych w wersji 2. 

## <a name="what-is-integration-runtime"></a>Co to jest środowiska uruchomieniowego integracji?
Integracja środowiska uruchomieniowego jest używany przez fabryki danych Azure zapewniają następujące możliwości integracji danych w różnych środowiskach sieci infrastruktury obliczeniowej:

- **Przenoszenie danych**: przenoszenie danych między magazyny danych w publicznych sklepach sieci i danych w sieci prywatnej (lokalnej lub virtual private network). Zapewnia obsługę wbudowanych łączników, konwersji formatów i mapowania kolumn oraz wydajne i skalowalne przenoszenie danych.
- **Wysłanie działania**: monitor i wysyłania działania przekształcania, które są uruchomione na różnych obliczeniowe usług, takich jak Azure HDInsight, usługi Azure Machine Learning, baza danych SQL Azure i SQL Server.
- **Wykonanie pakietów SSIS**: natywnie wykonuje pakietów SSIS w środowisku zarządzanym obliczeń platformy Azure.

Jeden lub wiele wystąpień środowiska uruchomieniowego integracji można wdrożyć jako wymagane do przeniesienia i przekształcania danych. Integracja środowiska uruchomieniowego można uruchomić w sieci publicznej platformy Azure lub w sieci prywatnej (lokalnej, sieci wirtualnej platformy Azure lub usług Amazon Web Services wirtualnego chmury prywatnej [VPC]). 

Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Co to jest limit liczby środowisk uruchomieniowych integracji?
Nie ma żadnego twardych limitu liczby wystąpień środowiska uruchomieniowego integracji, które mogą mieć w fabryce danych. Istnieje jednak limit liczby rdzeni maszyny Wirtualnej, które środowiska uruchomieniowego integracji można użyć dla subskrypcji wykonywanie pakietów SSIS. Aby uzyskać więcej informacji, zobacz [ogranicza fabryki danych](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>Kiedy należy używać w wersji 2, a nie wersja 1? 
Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, należy uruchomić bezpośrednio w wersji 2. Jeśli korzystasz już z wersji 1, ponownie z fabryki danych w wersji 2.

> [!WARNING]
> Fabryka danych w wersji 2 jest w wersji zapoznawczej i nie jest ogólnie dostępności (GA). W związku z tym podlega on te same zobowiązania Umowa dotycząca poziomu (SLA) usługa Azure w wersji 1 fabryki danych, który znajduje się w po 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Co to są pojęcia najwyższego poziomu w wersji 2?
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Fabryka danych Azure zawiera cztery najważniejsze składniki, które współpracują ze sobą jako platformy, na którym można utworzyć, opartych na danych z przepływów pracy czynności, aby przenieść i przekształcanie danych.

### <a name="pipelines"></a>Potoki
Fabryka danych może obejmować jeden lub wiele potoków. Potok to logiczne grupowanie działań do wykonania jednostkę pracy. które umożliwiają wykonanie zadania. Na przykład potoku może zawierać grupy działań, które pozyskiwania danych z obiektów blob platformy Azure, a następnie uruchom zapytania programu Hive w klastrze usługi HDInsight do partycjonowania danych. Korzyścią jest to, że można użyć potoku do zarządzania działania zgodnie z ustaleniami nie trzeba zarządzać każde działanie oddzielnie. Użytkownik może łańcuch działań w potoku do ich działania sekwencyjnie, lub nie działać je niezależnie, równolegle.

### <a name="activity"></a>Działanie
Działania reprezentują krok przetwarzania w potoku. Na przykład można użyć *kopiowania* działanie, aby skopiować dane z jednym magazynie danych do innego magazynu danych. Podobnie można użyć działania Hive, uruchamianego zapytań programu Hive w klastrze Azure HDInsight do transformacji lub analizowania danych. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="data-sets"></a>Zestawy danych
Zestawy danych reprezentują struktury danych w ramach magazynów danych, które po prostu wskaż lub odwołują się dane, które ma być używany w działaniach jako danych wejściowych lub wyjściowych. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Należy traktować go w ten sposób: połączonej usługi definiuje połączenie ze źródłem danych i struktury danych reprezentuje zestaw danych. Na przykład połączoną usługą magazynu Azure określa parametry połączenia, aby nawiązać połączenie z kontem magazynu platformy Azure. I zestawu danych obiektów Blob platformy Azure określa folder, który zawiera dane i kontener obiektów blob.

Połączone usługi ma dwa cele w fabryce danych:

- Do reprezentowania *magazynu danych* obejmuje, ale nie jest ograniczona do lokalnego wystąpienia programu SQL Server, wystąpienie bazy danych Oracle, udziału plików lub konta magazynu obiektów Blob platformy Azure. Aby uzyskać listę obsługiwanych magazynów w temacie [działanie kopiowania w fabryce danych Azure](copy-activity-overview.md).
- Reprezentowanie *zasobu obliczeniowego*, który może hostować wykonywanie działania. Na przykład działanie HDInsight Hive jest uruchomione w klastrze usługi HDInsight Hadoop. Listę działań transformacji i środowisk obliczeniowych obsługiwane, zobacz [Przekształć dane w fabryce danych Azure](transform-data.md).

### <a name="triggers"></a>Wyzwalacze
Wyzwalacze reprezentują jednostki przetwarzania, która określa, kiedy zostało rozpoczęte wykonywania potoku. Istnieją różne typy wyzwalaczy dla różnych typów zdarzeń. Podgląd obsługujemy wyzwalacz harmonogramu wall zegara. 


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

## <a name="what-regions-support-azure-data-factory-version-2"></a>Jakie regionów pomocy technicznej usługi fabryka danych Azure w wersji 2?
Obecnie można utworzyć fabryki danych w wersji 2 w regionach wschodnie stany USA, wschodnie stany USA 2 i Europa. Jednak fabryki danych umożliwia integrację środowiska uruchomieniowego w innym regionie przenoszenie danych między magazyny danych, działań wysyłania usługi obliczeniowe lub wysyłania pakietów SSIS. Aby uzyskać więcej informacji, zobacz [lokalizacje fabryki danych](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak mogę bądź na bieżąco z informacjami o fabryki danych?
Aby uzyskać najbardziej aktualne informacje o usłudze fabryka danych Azure przejdź do następujących lokacji:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Strona główna dokumentacji](/azure/data-factory)
- [Strona główna produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Nowości techniczne 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>Można w wersji 1 i uruchom równolegle potoki w wersji 2?
Nie. W wersji 2 i wersji 1 fabryki danych nie może zawierać jednostek (na przykład połączone usługi, zestawy danych lub potoki) innych wersji.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>Nadal należy do definiowania zestawów danych w wersji 2?
Zestaw danych nie jest już wymagane jednostki dla większości działań. Jest wymagany dla kopii, uczenia maszynowego, wyszukiwania, weryfikacji i niestandardowych działań, które wykorzystują schematu i inne informacje metadanych zestawu danych dla transformacji. Pozostałe działania nie wymagają już zestawy danych.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>Czy można łańcucha dwa działania bez zestawu danych w wersji 2?
Tak. Powiązane z działaniami w wersji 2, bez konieczności zestawów danych. Działania są powiązane za pomocą **dependsOn** właściwości w definicji JSON z potoku. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Są w wersji 1 działania obsługiwane w wersji 2? 
Tak, wszystkie działania w wersji 1 są obsługiwane w wersji 2.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Jak można zaplanować potoku w wersji 2 
Wyzwalacz harmonogramu służy do planowania potoku w wersji 2. Wyzwalacz wykorzystuje harmonogram kalendarza wall zegara i służy do planowania potoki okresowo lub za pomocą kalendarza na podstawie powtarzającego się wzorce (na przykład, co tydzień w poniedziałek godzinie 6 i czwartki o 21: 00). Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Dla potoku, uruchom w wersji 2 można przekazywać parametrów?
Parametry są tak, pojęcie najwyższej jakości, najwyższego poziomu w wersji 2. Można zdefiniować parametrów na poziomie potoku i przekazywać argumenty, jak wykonać potoku uruchamiane na żądanie lub przy użyciu wyzwalacza.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Czy można zdefiniować wartości domyślne parametrów potoku? 
Tak. Wartości domyślne dla parametrów można zdefiniować w potokach. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Działania w potoku można korzystać z argumentów, które są przekazywane do procesu, uruchom? 
Tak. Każde działanie w potoku, jaką może wykorzystać wartość parametru, które zostały przekazane do potoku Uruchom z `@parameter` utworzenia. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Właściwości danych wyjściowych działania mogą być używane w innym działaniu? 
Tak. Dane wyjściowe działania mogą być używane w następnych działań z `@activity` utworzenia.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak bezpiecznie Obsługa wartości zerowych w danych wyjściowych działania? 
Można użyć `@coalesce` skonstruować w wyrażeniach można bezpiecznie obsłużyć wartości null. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>Czy można użyć ponawiania i limit czasu na poziomie działania w wersji 2?
Tak. Dotyczące wykonywania działań w wersji 2, tak jak w wersji 1, można skonfigurować na poziomie działania ponawiania i limit czasu. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać instrukcje krok po kroku można utworzyć fabryki danych w wersji 2 zobacz następujące samouczki:

- [Szybki Start: Tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md)
- [Samouczek: Kopiowanie danych w chmurze](tutorial-copy-data-dot-net.md)

