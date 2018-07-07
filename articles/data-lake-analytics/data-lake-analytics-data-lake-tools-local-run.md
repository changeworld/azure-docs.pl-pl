---
title: Skrypt uruchomienia usługi Azure Data Lake U-SQL na maszynie lokalnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchamiać zadania U-SQL na komputerze lokalnym za pomocą usługi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888970"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>Uruchom skrypt U-SQL na komputerze lokalnym

Podczas tworzenia skryptu U-SQL, są często do uruchomienia skryptu U-SQL lokalnie, jak zapisuje kosztów i czasu. Usługa Azure Data Lake Tools for Visual Studio obsługuje uruchamianie skryptów U-SQL na maszynie lokalnej. 

## <a name="basic-concepts-for-local-run"></a>Podstawowe zagadnienia dotyczące uruchamiania lokalnego

Poniżej wykresu przedstawiono składniki lokalne uruchomienie i sposób mapowania tych składników w chmurze, uruchom.

|Składnik|Lokalne uruchomienie|Uruchom w chmurze|
|---------|---------|---------|
|Magazyn|Lokalny folder główny danych|Konto domyślne usługi Azure Data Lake Store|
|Wystąpienia obliczeniowe|Lokalne aparatu wykonywania języka U-SQL|Usługa Azure Data Lake Analytics|
|Środowisko wykonawcze|Katalog roboczy na komputerze lokalnym|Usługa Azure Data Lake Analytics klastra|

Objaśnienie więcej lokalny przebieg składników:

### <a name="local-data-root-folder"></a>Lokalny folder główny danych

Lokalny folder główny danych jest "Magazyn lokalny" dla konta lokalnego obliczeń. Dowolny folder w lokalnym systemie plików na komputerze lokalnym, może być lokalny folder główny danych. Jest równa domyślnego konta usługi Azure Data Lake Store w ramach konta usługi Data Lake Analytics. Przełączanie na inny folder główny danych jest podobnie jak przełączanie na inną domyślne konto magazynu. 

Folder główny danych służy do:
- Store metadane, takie jak bazy danych, tabel, zwracających tabelę i zestawów.
- Wyszukaj ścieżki wejściowe i wyjściowe, które są zdefiniowane jako ścieżki względne w skrypcie U-SQL. Używanie ścieżek względnych ułatwia wdrażanie skryptów U-SQL na platformie Azure.

### <a name="u-sql-local-run-engine"></a>Lokalne aparatu wykonywania języka U-SQL

Lokalne aparatu wykonywania języka U-SQL jest "konto obliczeniowe lokalnego" dla zadania U-SQL. Użytkownicy mogą uruchamiać zadania U-SQL lokalnie za pomocą usługi Azure Data Lake Tools for Visual Studio. Wykonanie lokalne jest również obsługiwany za pośrednictwem interfejsów wiersza polecenia i programowania zestawu SDK usługi Azure Data Lake U-SQL. [Dowiedz się więcej na temat zestawu SDK usługi Azure Data Lake U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directory"></a>Katalog roboczy

W przypadku uruchomienia skryptu U-SQL, folder katalogu roboczego jest wymagany przez buforowanie wyników kompilacji, dzienniki wykonywania i tak dalej. W usłudze Azure Data Lake Tools for Visual Studio, katalog roboczy jest katalog roboczy projekt U-SQL (zwykle znajdujący się w `<U-SQL project root path>/bin/debug>`). Katalog roboczy zostaną wyczyszczone, za każdym razem, gdy nowy uruchamianie wyzwalane.

## <a name="local-run-in-visual-studio"></a>Lokalne uruchamianie w programie Visual Studio

Usługa Azure Data Lake Tools for Visual Studio ma wbudowane lokalnego uruchomienia aparatu i wydobywa informacje dotyczące jej jako lokalne konto obliczeniowe. Aby uruchomić skrypt U-SQL lokalnie, wybierz opcję (Local-machine) lub konta (lokalny projekt) w marginesu edytora skryptu listy rozwijanej, a następnie kliknij przycisk **przesyłania**.

![Narzędzia Data Lake Tools for Visual Studio Prześlij skrypt do konta lokalnego](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>Lokalne uruchamianie przy użyciu konta (Local-machine)

Konta (local-machine) jest kontem udostępnionego obliczeń lokalnego z pojedynczy folder lokalny katalog głównych danych jako konto magazynu lokalnego. Folder główny danych jest domyślnie znajdujący się w "C:\Users\<username > \AppData\Local\USQLDataRoot", jest również można konfigurować za pośrednictwem **Narzędzia > Data Lake > Opcje i ustawienia**.

![Narzędzia Data Lake Tools for Visual Studio skonfigurować katalog główny danych lokalnych](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Projekt U-SQL jest wymagany dla przebiegu lokalnego. Katalog roboczy projekt U-SQL jest używany dla katalogu roboczego lokalnych wykonań U-SQL. Wyniki kompilacji, dzienniki wykonywania i inne pliki powiązane z wykonywaniem zadań są generowane i przechowywane w folderze katalog roboczy podczas uruchamiania lokalnego. Należy pamiętać, że za każdym razem, gdy uruchomisz skrypt, wszystkie te pliki w katalogu roboczym zostanie wyczyszczone i ponownie wygenerowany.

## <a name="local-run-with-local-project-account"></a>Lokalne uruchamianie przy użyciu konta (lokalny projekt)

Konto (lokalny projekt) jest konto obliczeniowe lokalnym odizolowanym od projektu dla każdego projektu przy użyciu izolowanego lokalny folder główny danych. Każdego aktywnego projektu U-SQL, otwierając w oknie Eksploratora rozwiązań ma odpowiadające mu `(Local-project: <project name>)` konto jest wyświetlane w Eksploratorze serwera i języka U-SQL marginesu edytora skryptów. 

Konto (lokalny projekt) zapewnia czyste i izolowane środowisko projektowe dla deweloperów. W odróżnieniu od konta (Local-machine), który ma udostępnionego folderu głównego dane lokalne przechowywanie danych wejściowych/wyjściowych dla wszystkich zadań lokalnych i metadanych (lokalny projekt) konto służy do tworzenia lokalnego katalogu głównego danych folderu tymczasowego w katalogu roboczym projektu U-SQL każdym razem, gdy skrypt U-SQL pobiera Uruchom. Ten tymczasowy folder główny danych pobiera czyszczone po ponownej kompilacji lub uruchom ponownie. 

Projekt U-SQL zapewnia dobre środowisko do zarządzania tym izolowane lokalnego Uruchom środowisko poprzez odwołanie do projektu i właściwości. Można jednocześnie skonfigurować źródła danych wejściowych dla skryptów U-SQL w projekcie, a także środowiska, w której istnieje odwołanie.

### <a name="manage-input-data-source-for-local-project-account"></a>Zarządzanie źródłem danych wejściowych dla konta (lokalny projekt)

Projekt U-SQL zajmuje się tworzenie folderu głównego dane lokalne i dane ustawienie Konto (lokalny projekt). Tymczasowy folder główny danych jest oczyszczony i ponownie utworzyć w obszarze Katalog roboczy projektu U-SQL, za każdym razem, gdy się stanie, ponownej kompilacji i uruchamiania lokalnego. Wszystkie źródła danych skonfigurowane przez projekt U-SQL są kopiowane do ten tymczasowy lokalny folder główny danych przed wykonaniem zadań lokalnych. 

Można skonfigurować źródła danych za pomocą polecenia w folderze głównym **kliknij prawym przyciskiem myszy projekt U-SQL > Właściwości > testowe źródło danych**. Podczas uruchamiania skryptu U-SQL na konto (lokalny projekt), wszystkie pliki i podfoldery (w tym pliki w podfolderach) **testowego źródła danych** folderu są kopiowane do folderu tymczasowego katalogu głównego danych lokalnych. Po ukończeniu wykonywania zadania lokalnego wyjściowe można także znaleźć w obszarze folderu tymczasowego katalogu głównego danych lokalnych w katalogu roboczym projektu. Należy pamiętać, czy wszystkie te dane wyjściowe zostanie usunięty i oczyszczone, podczas projektu pobiera odbudować i oczyszczone. 

![Narzędzia Data Lake Tools for Visual Studio skonfiguruj projektu testowego źródła danych](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>Zarządzanie środowiskiem bazy danych dla konta (lokalny projekt) 

Jeśli U-SQL zapytanie używa lub zapytań przy użyciu obiektów bazy danych U-SQL, należy w środowiskach bazy danych gotowe lokalnie przed uruchomienie tego skryptu U-SQL lokalnie. Dla konta (lokalny projekt) odwołania do projektu U-SQL może zarządzać zależności bazy danych U-SQL. Można dodać odwołania do projektu bazy danych U-SQL projektu U-SQL. Przed uruchomieniem skryptów U-SQL na koncie (lokalny projekt), wszystkie przywoływane bazy danych są wdrażane do folderu tymczasowego katalogu głównego danych lokalnych. I dla każdego uruchomienia tymczasowy folder główny danych jest czyszczona jako świeżego środowiska izolowanego.

Powiązane artykuły:
* [Dowiedz się, jak zarządzać definicją bazy danych U-SQL za pomocą projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [Dowiedz się, jak zarządzać odwołanie do bazy danych U-SQL projektu U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>Różnica między (Local-machine) i konto (lokalny projekt)

Konta (local-machine) ma na celu symulowania konta usługi Azure Data Lake Analytics na maszynie lokalnej użytkowników. Współdzieli takie samo środowisko przy użyciu konta usługi Azure Data Lake Analytics. (Lokalny projekt) mają na celu dostarczenie przyjazny dla użytkownika lokalne Środowisko deweloperskie, które ułatwiają wdrażanie odwołania do bazy danych i dane wejściowe przed uruchomieniem skryptu lokalnie. Konta (local-machine) zapewnia stałe środowisku współdzielonym, który jest możliwy za pośrednictwem wszystkich projektów. Konta (lokalny projekt) udostępnia środowisko programowania izolowane dla każdego projektu i jest on odświeżany dla każdego uruchomienia. Na podstawie powyżej, konto (lokalny projekt) oferuje szybsze środowisko programistyczne, stosując szybko nowe zmiany.

Można znaleźć więcej różnica między (Local-machine) i konto (lokalny projekt) na wykresie w następujący sposób:

|Kąt różnicy|(Local-machine)|(Lokalny projekt)|
|----------------|---------------|---------------|
|Dostęp lokalny|Są dostępne dla wszystkich projektów|Dostęp do tego konta odpowiedniego projektu|
|Folder główny danych lokalnych|Stałe folderu lokalnego. Skonfigurowane za pomocą **Narzędzia > Data Lake > Opcje i ustawienia**|Folder tymczasowy utworzony dla każdego przebiegu lokalnego w ramach projektu U-SQL, katalog roboczy. Folder pobiera czyszczone po ponownej kompilacji lub uruchom ponownie|
|Dane wejściowe dla skryptu U-SQL|Ścieżkę względną w folderze stałe lokalnego katalogu głównego danych|Ustaw za pomocą **właściwość projektu U-SQL > testowe źródło danych**. Wszystkie pliki i podfoldery są kopiowane do tymczasowego folderu głównego danych przed wykonanie lokalne|
|Dane wyjściowe skryptu U-SQL|Ścieżkę względną w folderze stałe lokalnego katalogu głównego danych|Zwrócone do tymczasowego folderu głównego danych. Wyniki są czyszczone po ponownej kompilacji lub uruchom ponownie.|
|Wdrażanie bazy danych|Przywoływane bazy danych nie są wdrażane automatycznie, gdy działających w odniesieniu do konta (Local-machine). Wartość taka sama przesyłania do konta usługi Azure Data Lake Analytics.|Przywoływane bazy danych są wdrażane na koncie (lokalny projekt), automatycznie przed wykonaniem lokalnego. Wszystkie bazy danych środowiska są czyszczone i ponownego wdrożenia po ponownej kompilacji lub uruchom ponownie.|

## <a name="local-run-with-u-sql-sdk"></a>Lokalne uruchamianie za pomocą zestawu SDK U-SQL

Oprócz uruchamiania skryptów U-SQL lokalnie w programie Visual Studio, umożliwia także zestawu SDK usługi Azure Data Lake U-SQL do uruchamiania skryptów U-SQL lokalnie z interfejsem wiersza polecenia i programowania. Za pomocą tych interfejsów można zautomatyzować lokalne uruchomienie języka U-SQL i testowania.

[Dowiedz się więcej na temat zestawu SDK usługi Azure Data Lake U-SQL](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Następne kroki

- [Zestaw SDK usługi Azure Data Lake w języku U-SQL](data-lake-analytics-u-sql-sdk.md)
- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Użyj projektu bazy danych U-SQL do tworzenia bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
