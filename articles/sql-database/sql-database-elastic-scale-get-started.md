---
title: Wprowadzenie do narzędzi elastycznej bazy danych
description: Podstawowe wyjaśnienie funkcji narzędzia elastycznej bazy danych usługi Azure SQL Database, w tym łatwe do uruchomienia przykładowa aplikacja.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 4577f611287a27366b1168e07565861c16fe0e38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987026"
---
# <a name="get-started-with-elastic-database-tools"></a>Wprowadzenie do narzędzi elastycznej bazy danych

W tym dokumencie przedstawiono środowisko deweloperskie dla [biblioteki klienta elastycznej bazy danych,](sql-database-elastic-database-client-library.md) ułatwiając uruchamianie przykładowej aplikacji. Przykładowa aplikacja tworzy prostą aplikację podzieloną na fragmenty i eksploruje kluczowe możliwości funkcji narzędzi elastycznej bazy danych usługi Azure SQL Database. Koncentruje się na przypadkach użycia do [zarządzania mapami niezależnego fragmentu,](sql-database-elastic-scale-shard-map-management.md) [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md)i [wykonywania zapytań z wieloma fragmentami.](sql-database-elastic-scale-multishard-querying.md) Biblioteka klienta jest dostępna dla platformy .NET oraz języka Java. 

## <a name="elastic-database-tools-for-java"></a>Narzędzia elastycznej bazy danych dla oprogramowania Java

### <a name="prerequisites"></a>Wymagania wstępne

* Zestaw Java Developer Kit (JDK), wersja 1.8 lub nowsza
* [Maven](https://maven.apache.org/download.cgi)
* Serwer bazy danych SQL na platformie Azure lub lokalne wystąpienie programu SQL Server

### <a name="download-and-run-the-sample-app"></a>Pobieranie i uruchamianie przykładowej aplikacji

Aby utworzyć pliki JAR i rozpocząć pracę z przykładowym projektem, wykonaj następujące czynności: 
1. Sklonuj [repozytorium GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) zawierające bibliotekę klienta wraz z przykładową aplikacją. 

2. Edytuj plik _./sample/src/main/resources/resource.properties,_ aby ustawić następujące ustawienia:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Aby utworzyć przykładowy projekt, w katalogu _./sample_ uruchom następujące polecenie:

    ```
    mvn install
    ```
    
4. Aby uruchomić przykładowy projekt, w katalogu _./sample_ uruchom następujące polecenie: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Aby dowiedzieć się więcej o możliwościach biblioteki klienta, poeksperymentuj z różnymi opcjami. Zapraszam do zapoznania się z kodem, aby dowiedzieć się więcej o implementacji przykładowej aplikacji.

    ![Progress-java][5]
    
Gratulacje! Pomyślnie skulisz i uruchomisz pierwszą aplikację podzieloną na fragmenty przy użyciu narzędzi elastycznej bazy danych w bazie danych SQL azure. Użyj programu Visual Studio lub SQL Server Management Studio, aby połączyć się z bazą danych SQL i szybko przyjrzeć się fragmentom utworzonym w przykładzie. Można zauważyć nowe przykładowe bazy danych niezależnego fragmentu i bazy danych menedżera map niezależnego fragmentu, który został utworzony przez przykład. 

Aby dodać bibliotekę klienta do własnego projektu Maven, dodaj następującą zależność w pliku POM:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Narzędzia elastycznej bazy danych dla platformy .NET

### <a name="prerequisites"></a>Wymagania wstępne

* Visual Studio 2012 lub nowsze z c#. Pobierz darmową wersję w [programie Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 lub nowsza. Aby uzyskać najnowszą wersję, zobacz [Instalowanie nuget](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Pobieranie i uruchamianie przykładowej aplikacji

Aby zainstalować bibliotekę, przejdź do [witryny Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Biblioteka jest zainstalowana z przykładową aplikacją opisaną w poniższej sekcji.

Aby pobrać i uruchomić próbkę, wykonaj następujące czynności: 

1. Pobierz [przykład narzędzia elastycznej bazy danych dla usługi Azure SQL — wprowadzenie](https://github.com/Azure/elastic-db-tools). Rozpakuj próbkę do wybranego miejsca.

2. Aby utworzyć projekt, otwórz rozwiązanie *ElasticScaleStarter.sln* z katalogu *C#.*

3. W rozwiązaniu dla przykładowego projektu otwórz plik *app.config.* Następnie postępuj zgodnie z instrukcjami w pliku, aby dodać nazwę serwera usługi Azure SQL Database i informacje logowania (nazwa użytkownika i hasło).

4. Skompiluj i uruchom aplikację. Po wyświetleniu monitu włącz program Visual Studio, aby przywrócić pakiety NuGet rozwiązania. Ta akcja pobiera najnowszą wersję biblioteki klienta elastycznej bazy danych z NuGet.

5. Aby dowiedzieć się więcej o możliwościach biblioteki klienta, poeksperymentuj z różnymi opcjami. Należy zwrócić uwagę na kroki, które aplikacja wykonuje w danych wyjściowych konsoli i nie krępuj się eksplorować kod za kulisami.
   
    ![Postęp][4]

Gratulacje! Pomyślnie sbudowano i uruchomisz pierwszą aplikację podzieloną na fragmenty przy użyciu narzędzi elastycznej bazy danych w bazie danych SQL. Użyj programu Visual Studio lub SQL Server Management Studio, aby połączyć się z bazą danych SQL i szybko przyjrzeć się fragmentom utworzonym w przykładzie. Można zauważyć nowe przykładowe bazy danych niezależnego fragmentu i bazy danych menedżera map niezależnego fragmentu, który został utworzony przez przykład.

> [!IMPORTANT]
> Zaleca się, aby zawsze używać najnowszej wersji programu Management Studio, aby pozostać zsynchronizowanym z aktualizacjami platformy Azure i bazy danych SQL. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Kluczowe fragmenty przykładu kodu

* **Zarządzanie fragmentami i mapami fragmentów:** Kod ilustruje sposób pracy z fragmentami, zakresami i mapowaniami w pliku *ShardManagementUtils.cs.* Aby uzyskać więcej informacji, zobacz [Skalowanie w poziomie baz danych za pomocą menedżera map niezależnego fragmentu](https://go.microsoft.com/?linkid=9862595).  

* **Routing zależny od danych:** Routing transakcji do prawego fragmentu jest wyświetlany w pliku *DataDependentRoutingSample.cs.* Aby uzyskać więcej informacji, zobacz [Routing zależny od danych](https://go.microsoft.com/?linkid=9862596). 

* **Wykonywanie zapytań o wiele fragmentów:** Wykonywanie zapytań między fragmentami jest zilustrowane w pliku *MultiShardQuerySample.cs.* Aby uzyskać więcej informacji, zobacz [Kwerendy wielodyskowego](https://go.microsoft.com/?linkid=9862597).

* **Dodawanie pustych fragmentów:** Iteracyjne dodawanie nowych pustych fragmentów jest wykonywane przez kod w pliku *CreateShardSample.cs.* Aby uzyskać więcej informacji, zobacz [Skalowanie w poziomie baz danych za pomocą menedżera map niezależnego fragmentu](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Inne operacje elastycznej skali

* **Dzielenie istniejącego fragmentu:** Możliwość dzielenia fragmentów jest dostarczana przez narzędzie scalania podziału. Aby uzyskać więcej informacji, zobacz [Przenoszenie danych między skalowane w poziomie baz danych w chmurze](sql-database-elastic-scale-overview-split-and-merge.md).

* **Scalanie istniejących fragmentów:** Scalanie fragmentów są również wykonywane przy użyciu narzędzia scalania podziału. Aby uzyskać więcej informacji, zobacz [Przenoszenie danych między skalowane w poziomie baz danych w chmurze](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Koszty

Biblioteka narzędzi elastycznej bazy danych jest bezpłatna. Korzystając z narzędzi elastycznej bazy danych, nie ponosisz żadnych dodatkowych opłat przekraczających koszt użycia platformy Azure. 

Na przykład przykładowa aplikacja tworzy nowe bazy danych. Koszt tej funkcji zależy od wersji bazy danych SQL, którą wybierzesz i użycia platformy Azure aplikacji.

Aby uzyskać informacje o cenach, zobacz [szczegóły cen bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat narzędzi elastycznej bazy danych, zobacz następujące artykuły:

* Przykłady kodu: 
  * Narzędzia elastycznej bazy danych ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Narzędzia elastycznej bazy danych dla usługi Azure SQL — integracja z platformą entity framework](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elastyczność niezależnego fragmentu w centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Ogłoszenie o skali elastycznej](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Kanał 9: [Przegląd skali elastycznej wideo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum dyskusyjne: [Forum usługi Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Aby zmierzyć wydajność: [liczniki wydajności menedżera map niezależnego fragmentu](sql-database-elastic-database-client-library.md)

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

