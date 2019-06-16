---
title: Wprowadzenie do narzędzi elastycznej bazy danych — Azure | Dokumentacja firmy Microsoft
description: Podstawowe informacje dotyczące funkcji narzędzia elastycznych baz danych Azure SQL Database, w tym łatwy do uruchomienia przykładowych aplikacji.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7c2e3b2bea7ddef3dfbf69f85b499d9a60158689
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585018"
---
# <a name="get-started-with-elastic-database-tools"></a>Wprowadzenie do narzędzi elastycznej bazy danych

Ten dokument stanowi wprowadzenie do środowiska dewelopera w systemie [Biblioteka kliencka elastic database](sql-database-elastic-database-client-library.md) , ułatwiając uruchamianie przykładowej aplikacji. Przykładowa aplikacja tworzy prostą podzieloną aplikację i analizuje kluczowe możliwości funkcji elastyczne narzędzia graficzne bazy danych Azure SQL Database. Koncentruje się ona na przypadki użycia [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md), [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md), i [zapytań z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md). Biblioteka klienta jest dostępna dla platformy .NET, a także języka Java. 

## <a name="elastic-database-tools-for-java"></a>Narzędzia elastycznych baz danych dla języka Java

### <a name="prerequisites"></a>Wymagania wstępne

* Java Developer Kit (JDK), wersja 1.8 lub nowszego
* [Maven](https://maven.apache.org/download.cgi)
* Serwer bazy danych SQL na platformie Azure lub lokalnego wystąpienia programu SQL Server

### <a name="download-and-run-the-sample-app"></a>Pobieranie i uruchamianie przykładowej aplikacji

Aby utworzyć pliki JAR i rozpocząć pracę z projektem przykładowym, wykonaj następujące czynności: 
1. Klonuj [repozytorium GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) zawierający biblioteki klienckiej, wraz z przykładowej aplikacji. 

2. Edytuj _./sample/src/main/resources/resource.properties_ plik, aby określić następujące ustawienia:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Do utworzenia przykładowego projektu, w _. / sample_ katalogu, uruchom następujące polecenie:

    ```
    mvn install
    ```
    
4. Aby uruchomić z przykładowym projektem w _. / sample_ katalogu, uruchom następujące polecenie: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Aby dowiedzieć się więcej na temat możliwości biblioteki klienta, Poeksperymentuj z różnymi opcjami. Możesz eksplorować kod, aby dowiedzieć się więcej o implementacji aplikacji przykładowej.

    ![Progress-java][5]
    
Gratulacje! Pomyślnie skompilowane i uruchamianie pierwszej aplikacji podzielonej na fragmenty za pomocą narzędzia elastycznych baz danych w usłudze Azure SQL Database. Użyj programu Visual Studio lub SQL Server Management Studio do połączenia z bazą danych SQL i przyjrzyj się szybkie fragmentów, utworzone w przykładzie. Można zauważyć nowe przykładowe fragmentu bazy danych i fragmentu bazy danych Menedżera mapowań utworzony plik. 

Aby dodać biblioteki klienckiej Projekt narzędzia Maven, dodaj następującą zależność w pliku POM:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Narzędzia elastycznych baz danych dla platformy .NET

### <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio 2012 lub nowszym za pomocą języka C#. Pobierz bezpłatną wersję na [pobieranie Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 lub nowszego. Aby uzyskać najnowszą wersję, zobacz [Instalowanie systemu NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Pobieranie i uruchamianie przykładowej aplikacji

Aby zainstalować bibliotekę, przejdź do [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Biblioteka jest instalowany z przykładowej aplikacji, opisanej w poniższej sekcji.

Aby pobrać i uruchomić przykład, wykonaj następujące kroki: 

1. Pobierz [narzędzia elastyczne bazy danych SQL Azure — wprowadzenie do próbki](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) z witryny MSDN. Rozpakuj plik do lokalizacji, z wybraniu.

2. Aby utworzyć projekt, należy otworzyć *ElasticScaleStarterKit.sln* rozwiązania z *C#* katalogu.

3. W rozwiązaniu dla przykładowego projektu otwórz *app.config* pliku. Następnie postępuj zgodnie z instrukcjami w pliku, aby dodać nazwę serwera usługi Azure SQL Database i Twoje informacje logowania (nazwy użytkownika i hasło).

4. Skompiluj i uruchom aplikację. Po wyświetleniu monitu, Włącz Przywracanie pakietów NuGet rozwiązania programu Visual Studio. Ta akcja spowoduje pobranie najnowszej wersji biblioteki klienckiej elastycznej bazy danych z pakietów NuGet.

5. Aby dowiedzieć się więcej na temat możliwości biblioteki klienta, Poeksperymentuj z różnymi opcjami. Należy pamiętać, kroki, które aplikacja zajmuje się w konsoli danych wyjściowych i możesz eksplorować kod w tle.
   
    ![Postęp][4]

Gratulacje! Pomyślnie skompilowane i uruchamianie pierwszej aplikacji podzielonej na fragmenty za pomocą narzędzia elastycznych baz danych w bazie danych SQL. Użyj programu Visual Studio lub SQL Server Management Studio do połączenia z bazą danych SQL i przyjrzyj się szybkie fragmentów, utworzone w przykładzie. Można zauważyć nowe przykładowe fragmentu bazy danych i fragmentu bazy danych Menedżera mapowań utworzony plik.

> [!IMPORTANT]
> Firma Microsoft zaleca, zawsze używaj najnowszej wersji programu Management Studio, aby to zapewnić synchronizację z aktualizacjami platformy Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Kluczowych przykładowy kod

* **Zarządzanie fragmentami i fragmentów mapowania**: Kod ilustruje sposób pracy z fragmentów, zakresy i mapowania w *ShardManagementUtils.cs* pliku. Aby uzyskać więcej informacji, zobacz [skalowania w poziomie bazy danych z Menedżera map fragmentów](https://go.microsoft.com/?linkid=9862595).  

* **Routing zależny od danych**: Routing transakcji do prawego fragmentu jest wyświetlany w *DataDependentRoutingSample.cs* pliku. Aby uzyskać więcej informacji, zobacz [routingu zależnego od danych](https://go.microsoft.com/?linkid=9862596). 

* **Wykonywanie zapytań za pośrednictwem wielu fragmentów**: Wykonywanie zapytań między fragmentami zilustrowano *MultiShardQuerySample.cs* pliku. Aby uzyskać więcej informacji, zobacz [zapytań z wieloma fragmentami](https://go.microsoft.com/?linkid=9862597).

* **Dodawanie pustego fragmentów**: Iteracyjne Dodawanie nowych fragmentów pusty odbywa się przez kod w *CreateShardSample.cs* pliku. Aby uzyskać więcej informacji, zobacz [skalowania w poziomie bazy danych z Menedżera map fragmentów](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Inne operacje elastycznego skalowania

* **Podział istniejących fragmentu**: Możliwość podziału fragmentów są udostępniane przez narzędzie do dzielenia i scalania. Aby uzyskać więcej informacji, zobacz [przenoszenie danych między bazami danych w chmurze skalowanych w poziomie](sql-database-elastic-scale-overview-split-and-merge.md).

* **Scalanie istniejącego fragmentów**: Scalenia fragmentów są również wykonywane przy użyciu narzędzia do dzielenia i scalania. Aby uzyskać więcej informacji, zobacz [przenoszenie danych między bazami danych w chmurze skalowanych w poziomie](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Koszty

Biblioteka narzędzi elastycznej bazy danych jest bezpłatna. Korzystając z narzędzi elastycznych baz danych, ponosisz żadnych dodatkowych opłat za poza koszty użycia platformy Azure. 

Na przykład przykładowa aplikacja tworzy nowe bazy danych. Koszt tej funkcji zależy od wybranej wersji bazy danych SQL Database i Azure użycia aplikacji.

Aby uzyskać informacje o cenach, zobacz [szczegóły cennika usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat narzędzia elastycznych baz danych zobacz następujące artykuły:

* Przykłady kodu: 
  * Narzędzia elastycznych baz danych ([.NET](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Narzędzia elastycznych baz danych dla usług SQL Azure - Entity Framework integracji](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Skrypty dotyczące elastyczności w Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Elastyczne skalowanie anonsu](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [Elastyczne skalowanie — omówienie wideo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum dyskusyjne: [Forum usługi Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Aby zmierzyć wydajność: [Liczniki wydajności dla menedżera map fragmentów](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

