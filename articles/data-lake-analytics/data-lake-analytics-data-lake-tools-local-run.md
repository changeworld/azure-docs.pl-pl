---
title: Uruchamianie skryptów U-SQL usługi Azure Data Lake na komputerze lokalnym
description: Dowiedz się, jak używać narzędzi usługi Azure Data Lake Tools for Visual Studio do uruchamiania zadań U-SQL na komputerze lokalnym.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62113939"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Uruchamianie skryptów U-SQL na komputerze lokalnym

Podczas tworzenia skryptów U-SQL, można zaoszczędzić czas i wydatki, uruchamiając skrypty lokalnie. Narzędzia usługi Azure Data Lake Tools for Visual Studio obsługują uruchamianie skryptów U-SQL na komputerze lokalnym. 

## <a name="basic-concepts-for-local-runs"></a>Podstawowe koncepcje dla przebiegów lokalnych

Na poniższym wykresie przedstawiono składniki uruchamiania lokalnego i sposób działania tych składników do działania w chmurze.

|Składnik|Uruchamianie lokalne|Przebieg w chmurze|
|---------|---------|---------|
|Magazyn|Lokalny folder główny danych|Domyślne konto usługi Azure Data Lake Store|
|Wystąpienia obliczeniowe|Lokalny silnik uruchamiania U-SQL|Usługa Usługi Azure Data Lake Analytics|
|Uruchom środowisko|Katalog roboczy na komputerze lokalnym|Klaster usługi Azure Data Lake Analytics|

Sekcje, które należy wykonać zawierają więcej informacji na temat składników uruchamiania lokalnego.

### <a name="local-data-root-folders"></a>Foldery główne danych lokalnych

Lokalny folder główny danych jest **magazynem lokalnym** dla lokalnego konta obliczeniowego. Każdy folder w lokalnym systemie plików na komputerze lokalnym może być lokalnym folderem głównym danych. Jest taka sama jak domyślne konto usługi Azure Data Lake Store konta usługi Data Lake Analytics. Przełączenie do innego folderu głównego danych jest jak przełączenie się na inne domyślne konto sklepu. 

Folder główny danych jest używany w następujący sposób:
- Przechowywanie metadanych. Przykładami są bazy danych, tabele, funkcje wyceniane w tabeli i zestawy.
- Wyszukuj ścieżki wejściowe i wyjściowe, które są zdefiniowane jako ścieżki względne w skryptach U-SQL. Za pomocą ścieżek względnych, łatwiej jest wdrożyć skrypty U-SQL na platformie Azure.

### <a name="u-sql-local-run-engines"></a>Lokalne aparaty uruchamiania U-SQL

Aparat uruchamiania lokalnego U-SQL jest **lokalnym kontem obliczeniowym** dla zadań U-SQL. Użytkownicy mogą uruchamiać zadania U-SQL lokalnie za pośrednictwem narzędzia Usługi Azure Data Lake Tools dla programu Visual Studio. Przebiegi lokalne są również obsługiwane za pośrednictwem wiersza polecenia Azure Data Lake U-SQL SDK i interfejsów programowania. Dowiedz się więcej o [sdk Azure Data Lake U-SQL.](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)

### <a name="working-directories"></a>Katalogi robocze

Po uruchomieniu skryptu U-SQL, działający folder katalogu jest potrzebny do buforowania wyników kompilacji, uruchamiania dzienników i wykonywania innych funkcji. W usłudze Azure Data Lake Tools for Visual Studio katalog roboczy jest katalogiem roboczym projektu U-SQL. Znajduje się pod `<U-SQL project root path>/bin/debug>`. Katalog roboczy jest czyszczony za każdym razem, gdy zostanie wyzwolone nowe uruchomienie.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokalne przebiegi w programie Microsoft Visual Studio

Narzędzia usługi Azure Data Lake tools dla programu Visual Studio mają wbudowany aparat uruchamiania lokalnego. Narzędzia powierzchni aparatu jako lokalnego konta obliczeniowego. Aby uruchomić skrypt U-SQL lokalnie, wybierz konto **Local-machine** lub **Local-project** w menu rozwijanym marginesu edytora skryptu. Następnie wybierz pozycję **Prześlij**.

![Przesyłanie skryptu U-SQL do konta lokalnego](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokalne przebiegi z kontem komputera lokalnego

Konto **komputera lokalnego** to udostępnione lokalne konto obliczeniowe z jednym lokalnym folderem głównym danych jako kontem magazynu lokalnego. Domyślnie folder główny danych znajduje się w folderze **C:\Users\<nazwa użytkownika>\AppData\Local\USQLDataRoot**. Można go również konfigurować za pomocą**opcji i ustawień** **aplikacji Tools** > **Data Lake** > .

![Konfigurowanie lokalnego folderu głównego danych](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Projekt U-SQL jest wymagany dla uruchomienia lokalnego. Katalog roboczy projektu U-SQL jest używany dla lokalnego katalogu roboczego uruchamiania U-SQL. Wyniki kompilacji, dzienniki uruchamiania i inne pliki związane z uruchamianiem zadań są generowane i przechowywane w folderze katalogu roboczego podczas uruchamiania lokalnego. Za każdym razem, gdy skrypt jest ponownie odtwarzany, wszystkie pliki w katalogu roboczym są czyszczone i regenerowane.

## <a name="local-runs-with-a-local-project-account"></a>Przebiegi lokalne z kontem projektu lokalnego

Konto **projektu lokalnego** jest kontem obliczeniowym lokalnym izolowanym i w ramach projektu dla każdego projektu z izolowanym folderem głównym danych lokalnych. Każdy aktywny projekt U-SQL, który otwiera się `(Local-project: <project name>)` w Eksploratorze rozwiązań w programie Visual Studio ma odpowiednie konto. Konta są wymienione zarówno w Eksploratorze serwera w programie Visual Studio, jak i w marginesie edytora skryptów U-SQL.  

Konto **projektu lokalnego** zapewnia czyste i izolowane środowisko programistyczne. Konto **komputera lokalnego** ma udostępniony lokalny folder główny danych, który przechowuje metadane oraz dane wejściowe i wyjściowe dla wszystkich zadań lokalnych. Ale konto **projektu lokalnego** tworzy tymczasowy folder główny danych lokalnych w katalogu roboczym projektu U-SQL za każdym razem, gdy skrypt U-SQL jest uruchamiany. Ten tymczasowy folder główny danych jest czyszczony po przebudowie lub ponownym uruchomieniu. 

Projekt U-SQL zarządza izolowanym środowiskiem uruchamiania lokalnego za pomocą odwołania do projektu i właściwości. Można skonfigurować źródła danych wejściowych dla skryptów U-SQL zarówno w projekcie, jak i w środowiskach bazy danych, do których istnieje odwołanie.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Zarządzanie źródłem danych wejściowych dla konta projektu lokalnego 

Projekt U-SQL tworzy lokalny folder główny danych i konfiguruje dane dla konta **projektu lokalnego.** Tymczasowy folder główny danych jest czyszczony i odtwarzany w katalogu roboczym projektu U-SQL za każdym razem, gdy nastąpi przebudowa i uruchomienie lokalne. Wszystkie źródła danych skonfigurowane przez projekt U-SQL są kopiowane do tego tymczasowego folderu głównego danych lokalnych przed uruchomieniu zadania lokalnego. 

Można skonfigurować folder główny źródeł danych. Kliknij prawym przyciskiem myszy**źródło danych testu**właściwości**projektu** >  >  **U-SQL**. Po uruchomieniu skryptu U-SQL na koncie **projektu lokalnego** wszystkie pliki i podfoldery w folderze **Test Data Source** są kopiowane do tymczasowego folderu głównego danych lokalnych. Pliki w podfolderach są uwzględniane. Po uruchomieniu zadania lokalnego wyniki wyjściowe można również znaleźć w tymczasowym folderze głównym danych lokalnych w katalogu roboczym projektu. Wszystkie te dane wyjściowe są usuwane i czyszczone po przebudowy projektu i czyszczone. 

![Konfigurowanie źródła danych testowych projektu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Zarządzanie środowiskiem bazy danych, do którego istnieje odwołanie, dla konta **projektu lokalnego** 

Jeśli kwerenda U-SQL używa lub kwerendy z obiektów bazy danych U-SQL, należy przygotować środowiska bazy danych lokalnie przed uruchomieniem skryptu U-SQL lokalnie. W przypadku konta **projektu lokalnego** zależności bazy danych U-SQL mogą być zarządzane przez odwołania do projektu U-SQL. Można dodać odwołania do projektu bazy danych U-SQL do projektu U-SQL. Przed uruchomieniem skryptów U-SQL na koncie **projektu lokalnego** wszystkie bazy danych, do których istnieją odwołania, są wdrażane w tymczasowym folderze głównym danych lokalnych. I dla każdego uruchomienia folder główny danych tymczasowych jest czyszczony jako świeże środowisko izolowane.

Zobacz ten powiązany artykuł:
* Dowiedz się, jak zarządzać definicjami i odwołaniami do baz danych [U-SQL w projektach bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Różnica między **kontami lokalnego komputera** i **projektu lokalnego**

Konto **komputera lokalnego** symuluje konto usługi Azure Data Lake Analytics na komputerach lokalnych użytkowników. Współużytkuje to samo środowisko z kontem usługi Azure Data Lake Analytics. Konto **projektu lokalnego** zapewnia przyjazne dla użytkownika lokalne środowisko programistyczne. To środowisko pomaga użytkownikom wdrażać odwołania do bazy danych i dane wejściowe przed uruchomieniem skryptów lokalnie. Konto **komputera lokalnego** udostępnia udostępnione stałe środowisko, do które można uzyskać dostęp za pośrednictwem wszystkich projektów. Konto **projektu lokalnego** zapewnia izolowane środowisko programistyczne dla każdego projektu. Jest odświeżany dla każdego uruchomienia. Konto **projektu lokalnego** oferuje szybsze środowisko programistyczne, szybko stosując nowe zmiany.

Więcej różnic między kontami **projektu lokalnego** i **lokalnego** przedstawiono w poniższej tabeli:

|Kąt różnicy|Maszyna lokalna|Projekt lokalny|
|----------------|---------------|---------------|
|Dostęp lokalny|Dostęp do wszystkich projektów jest dostępny.|Tylko odpowiedni projekt może uzyskać dostęp do tego konta.|
|Lokalny folder główny danych|Stały folder lokalny. Skonfigurowano za pomocą opcji**i ustawień** **aplikacji Tools** > **Data Lake** > .|Folder tymczasowy utworzony dla każdego uruchomienia lokalnego w katalogu roboczym projektu U-SQL. Folder zostanie wyczyszczony, gdy nastąpi przebudowa lub ponowne uruchomienie.|
|Dane wejściowe dla skryptu U-SQL|Ścieżka względna w folderze głównym stałych danych lokalnych.|Ustaw za pomocą >  **właściwości projektu U-SQL**Źródło danych**testowych**. Wszystkie pliki i podfoldery są kopiowane do folderu głównego danych tymczasowych przed uruchomieniem lokalnym.|
|Dane wyjściowe dla skryptu U-SQL|Ścieżka względna w folderze głównym stałych danych lokalnych.|Dane wyjściowe do folderu głównego danych tymczasowych. Wyniki są czyszczone po przebudowy lub ponownego uruchomienia.|
|Wdrożenie bazy danych, do którego istnieje odwołanie|Bazy danych, do których istnieje odwołanie, nie są wdrażane automatycznie podczas uruchamiania na **koncie komputera lokalnego.** To samo dotyczy przesyłania do konta usługi Azure Data Lake Analytics.|Bazy danych, do których istnieją odwołania, są wdrażane na koncie **projektu lokalnego** automatycznie przed uruchomieniem lokalnym. Wszystkie środowiska bazy danych są czyszczone i ponownie wcielone w przypadku przebudowy lub ponownego uruchomienia.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Uruchomienie lokalne przy za pomocą sdk U-SQL

Skrypty U-SQL można uruchamiać lokalnie w programie Visual Studio, a także używać narzędzia Azure Data Lake U-SQL SDK do lokalnego uruchamiania skryptów U-SQL za pomocą interfejsów wiersza polecenia i programowania. Za pośrednictwem tych interfejsów można zautomatyzować u-SQL lokalnych uruchomień i testów.

Dowiedz się więcej o [sdk Azure Data Lake U-SQL.](data-lake-analytics-u-sql-sdk.md)

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
