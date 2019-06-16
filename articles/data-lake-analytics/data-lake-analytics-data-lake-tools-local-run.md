---
title: Skrypty uruchomienia usługi Azure Data Lake U-SQL na komputerze lokalnym
description: Dowiedz się, jak uruchamiać zadania U-SQL na komputerze lokalnym za pomocą usługi Azure Data Lake Tools for Visual Studio.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62113939"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Uruchamianie skryptów U-SQL na komputerze lokalnym

Podczas tworzenia skryptów U-SQL można zapisać czas i pieniądze, uruchamiając skrypty lokalnie. Usługa Azure Data Lake Tools for Visual Studio obsługuje uruchamianie skryptów U-SQL na maszynie lokalnej. 

## <a name="basic-concepts-for-local-runs"></a>Podstawowe pojęcia dla przebiegów lokalnych

Na poniższym wykresie przedstawiono składniki lokalne uruchomienie i sposób mapowania tych składników w chmurze, uruchom.

|Składnik|Uruchamianie lokalne|Uruchom chmury|
|---------|---------|---------|
|Magazyn|Folder główny danych lokalnych|Konto domyślne usługi Azure Data Lake Store|
|Wystąpienia obliczeniowe|Lokalne aparatu wykonywania języka U-SQL|Usługa Azure Data Lake Analytics|
|Środowisko uruchomieniowe|Katalog roboczy na komputerze lokalnym|Usługa Azure Data Lake Analytics klastra|

W kolejnych sekcjach zawierają więcej informacji o składnikach uruchomienia lokalnego.

### <a name="local-data-root-folders"></a>Dane lokalne folderów głównych

Folder główny danych lokalnych jest **magazynu lokalnego** lokalnych obliczenia konta. Dowolny folder w lokalnym systemie plików na komputerze lokalnym, może być folderem głównym danych lokalnych. Jest taka sama jak domyślnego konta usługi Azure Data Lake Store w ramach konta usługi Data Lake Analytics. Przełączanie do folderu głównego różnych danych jest podobnie jak przełączanie na inną domyślne konto magazynu. 

Folder główny danych jest używany w następujący sposób:
- Store metadanych. Przykładami są bazy danych, tabel, zwracających tabelę i zestawów.
- Wyszukaj ścieżki wejściowe i wyjściowe, które są zdefiniowane jako ścieżki względne w skryptów U-SQL. Za pomocą ścieżek względnych, łatwiej jest wdrażanie skryptów U-SQL na platformie Azure.

### <a name="u-sql-local-run-engines"></a>Uruchamiania aparatów lokalnego języka U-SQL

U-SQL jest lokalnego uruchomienia aparatu **konto obliczeniowe lokalnego** dla zadań U-SQL. Użytkownicy mogą uruchamiać zadania U-SQL lokalnie za pomocą usługi Azure Data Lake Tools for Visual Studio. Przebiegów lokalnych są również obsługiwane za pośrednictwem interfejsów wiersza polecenia i programowania zestawu SDK usługi Azure Data Lake U-SQL. Dowiedz się więcej o [zestawu SDK usługi Azure Data Lake U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Katalogi robocze

Po uruchomieniu skryptu U-SQL, folder katalogu roboczego pozwala na potrzeby pamięci podręcznej wyników kompilacji, uruchamiania dzienników i wykonywania innych funkcji. W usłudze Azure Data Lake Tools for Visual Studio katalog roboczy jest katalog roboczy projekt U-SQL. Znajduje się w obszarze `<U-SQL project root path>/bin/debug>`. Katalog roboczy jest czyszczona za każdym razem, gdy uruchomienie nowego przebiegu, zostanie wywołany.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokalnych przebiegów w programie Microsoft Visual Studio

Usługa Azure Data Lake Tools for Visual Studio mają wbudowany aparat uruchomienia lokalnego. Narzędzia powierzchni silnika jako konto lokalne obliczeń. Aby uruchomić skrypt U-SQL lokalnie, wybierz **maszyny lokalnej** lub **lokalnego projektu** konta w menu rozwijanym marginesu edytora skryptu. Następnie wybierz pozycję **przesyłania**.

![Prześlij skrypt U-SQL, do konta lokalnego](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokalny jest uruchamiany z konta komputera lokalnego do

A **maszyny lokalnej** konto jest udostępniony lokalne obliczenia konta za pomocą danych lokalnych z jednego folderu głównego, jako konto magazynu lokalnego. Domyślnie folder główny danych znajduje się w **C:\Users\<username > \AppData\Local\USQLDataRoot**. Jest także można skonfigurować za pośrednictwem **narzędzia** > **usługi Data Lake** > **opcje i ustawienia**.

![Skonfiguruj folder główny danych lokalnych](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Projekt U-SQL jest wymagany do uruchamiania lokalnego. Katalog roboczy projekt U-SQL jest używany dla języka U-SQL wykonywania pracy katalogu lokalnego. Wyniki kompilacji, uruchom dzienniki i inne pliki związane z wykonywania zadania są generowane i przechowywane w folderze katalog roboczy podczas uruchamiania lokalnego. Za każdym razem, gdy uruchomisz skrypt, wszystkie pliki w katalogu roboczym są czyszczone i ponownie wygenerowany.

## <a name="local-runs-with-a-local-project-account"></a>Przebiegów lokalnych za pomocą konta lokalnego projektu

A **lokalnego projektu** konto jest lokalnym odizolowanym od projektu obliczenia konta dla każdego projektu z folderu głównego izolowane danych lokalnych. Każdy aktywny projekt U-SQL, które otwiera w Eksploratorze rozwiązań w programie Visual Studio ma odpowiadające mu `(Local-project: <project name>)` konta. Konta są wyświetlane w Eksploratorze serwera w programie Visual Studio i marginesu edytora skryptów języka U-SQL.  

**Lokalnego projektu** konto zapewnia czyste i izolowane środowisko projektowe. A **maszyny lokalnej** konto ma folder główny udostępnionych danych lokalnych, która przechowuje dane metadane i dane wejściowe i wyjściowe dla wszystkich zadań lokalnych. Ale **lokalnego projektu** konto służy do tworzenia folderu głównego tymczasowe danych lokalnych w katalogu roboczym projektu U-SQL za każdym razem, gdy jest uruchamiany skrypt U-SQL. Ten folder główny danych tymczasowych jest czyszczona po ponownej kompilacji lub uruchom ponownie się dzieje. 

Projekt U-SQL zarządza izolowanym lokalne środowisko uruchomieniowe poprzez odwołanie do projektu i właściwości. Źródła danych wejściowych dla skryptów U-SQL można skonfigurować zarówno projekt, jak i środowiska, w której istnieje odwołanie.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Zarządzanie źródłem danych wejściowych dla konta lokalnego projektu 

Projekt U-SQL tworzy folder główny danych lokalnych i konfiguruje dane **lokalnego projektu** konta. Folder główny danych tymczasowych jest oczyszczony i ponownie utworzyć w obszarze Katalog roboczy projektu U-SQL, za każdym razem, gdy się stanie, ponownej kompilacji i uruchamiania lokalnego. Wszystkich źródeł danych, które są konfigurowane przez projekt U-SQL są kopiowane do tego folderu głównego tymczasowe dane lokalne, przed uruchomieniem zadań lokalnych. 

Można skonfigurować w folderze głównym źródeł danych. Kliknij prawym przyciskiem myszy **projekt U-SQL** > **właściwość** > **testowego źródła danych**. Po uruchomieniu skryptu U-SQL **lokalnego projektu** konta, wszystkie pliki i podfoldery w **testowego źródła danych** folderu są kopiowane do folderu głównego tymczasowe danych lokalnych. Dołączane są pliki w podfolderach. Po uruchomieniu zadań lokalnych wyjściowe można także znaleźć w folderze głównym tymczasowe danych lokalnych w katalogu roboczym projektu. Te dane wyjściowe jest usuwana i oczyszczone, podczas projektu pobiera odbudować i oczyszczone. 

![Konfigurowanie projektu testowego źródła danych](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Zarządzanie środowiskiem bazy danych dla **lokalnego projektu** konta 

Jeśli U-SQL zapytanie używa lub zapytań przy użyciu obiektów bazy danych U-SQL, musisz wprowadzić środowisk bazy danych gotowe lokalnie, aby uruchomić skrypt U-SQL lokalnie. Aby uzyskać **lokalnego projektu** konta, bazy danych U-SQL, zależności, można zarządzać przy użyciu odwołań do projektu U-SQL. Można dodać odwołania do projektu bazy danych U-SQL projektu U-SQL. Przed uruchomieniem skryptów U-SQL na **lokalnego projektu** konta wszystkich przywoływanych baz danych są wdrażane do głównego folderu tymczasowego danych lokalnych. I dla każdego uruchomienia folder główny danych tymczasowych jest czyszczona jako świeżego środowiska izolowanego.

Ten artykuł pokrewne:
* Dowiedz się, jak zarządzać definicje bazy danych U-SQL i odwołania w [projektów bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Różnica między **maszyny lokalnej** i **lokalnego projektu** kont

A **maszyny lokalnej** konta symuluje konto usługi Azure Data Lake Analytics na komputerach lokalnych użytkowników. Współdzieli taki sam sposób, przy użyciu konta usługi Azure Data Lake Analytics. A **lokalnego projektu** konto zapewnia przyjazny dla użytkownika lokalnego środowiska deweloperskiego. To środowisko ułatwiają wdrażanie odwołania do bazy danych i dane wejściowe, zanim lokalnie uruchamiać skrypty. A **maszyny lokalnej** konto zapewnia współużytkowanym środowisku stałe, który jest możliwy za pośrednictwem wszystkich projektów. A **lokalnego projektu** konto zapewnia środowisko izolowane rozwoju dla każdego projektu. Jest on odświeżany dla każdego uruchomienia. A **lokalnego projektu** konto oferuje szybsze środowisko programistyczne, stosując szybko nowe zmiany.

Więcej różnice między **maszyny lokalnej** i **lokalnego projektu** konta są wyświetlane w poniższej tabeli:

|Kąt różnicy|Komputer lokalny|Local-project|
|----------------|---------------|---------------|
|Dostęp lokalny|Są dostępne dla wszystkich projektów.|Odpowiedniego projektu mogą uzyskać dostęp do tego konta.|
|Folder główny danych lokalnych|Stałe folderu lokalnego. Skonfigurowane za pomocą **narzędzia** > **usługi Data Lake** > **opcje i ustawienia**.|Folder tymczasowy utworzony dla każdego przebiegu lokalnego w ramach projektu U-SQL, katalog roboczy. Folder pobiera czyszczone po ponownej kompilacji lub się stanie, uruchom ponownie.|
|Dane wejściowe dla skryptu U-SQL|Ścieżka względna w folderze głównym trwałych danych lokalnych.|Ustaw za pomocą **właściwość projektu U-SQL** > **testowego źródła danych**. Wszystkie pliki i podfoldery są kopiowane do folderu głównego danych tymczasowych przed przebieg lokalny.|
|Dane wyjściowe skryptu U-SQL|Ścieżka względna w folderze głównym trwałych danych lokalnych.|Dane wyjściowe do folderu głównego dane tymczasowe. Wyniki są czyszczone po ponownej kompilacji lub uruchom ponownie się dzieje.|
|Wdrażanie bazy danych|Przywoływane bazy danych nie są wdrażane automatycznie przy uruchamianiu **maszyny lokalnej** konta. Jest to ten sam sposób, przesyłania z kontem usługi Azure Data Lake Analytics.|Przywoływane bazy danych są wdrażane na **lokalnego projektu** konta automatycznie przed przebieg lokalny. Wszystkie bazy danych środowiska są czyszczone i ponownego wdrożenia po ponownej kompilacji lub się stanie, uruchom ponownie.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Uruchom przy użyciu zestawu SDK U-SQL lokalnie

Można uruchomić skrypty U-SQL lokalnie w programie Visual Studio, a także uruchamianie skryptów U-SQL lokalnie z interfejsem wiersza polecenia i programowania przy użyciu zestawu SDK usługi Azure Data Lake U-SQL. Za pomocą tych interfejsów można zautomatyzować uruchomienia lokalnego języka U-SQL i testy.

Dowiedz się więcej o [zestawu SDK usługi Azure Data Lake U-SQL](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Kolejne kroki

- [Jak skonfigurować potok CI/CD dla usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
