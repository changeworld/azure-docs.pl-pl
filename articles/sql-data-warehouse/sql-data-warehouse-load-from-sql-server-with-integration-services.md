---
title: "Ładowanie danych z programu SQL Server do magazynu danych SQL Azure (SSIS) | Dokumentacja firmy Microsoft"
description: "Pokazuje, jak utworzyć pakiet SQL Server Integration Services (SSIS) do przenoszenia danych z różnych źródeł danych do usługi SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Ładowanie danych z programu SQL Server do magazynu danych SQL Azure (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Utwórz pakiet SQL Server Integration Services (SSIS) ładowanie danych z programu SQL Server do usługi Azure SQL Data Warehouse. Można opcjonalnie restrukturyzacji, przekształcanie i czyszczenia danych, ponieważ przechodzi ona przez przepływ danych usług SSIS.

W tym samouczku zostaną wykonane następujące czynności:

* Utwórz nowy projekt usług integracji w programie Visual Studio.
* Łączenie ze źródłami danych, w tym programu SQL Server (jako źródło) i usługi SQL Data Warehouse (jako miejsce docelowe).
* Projekt SSIS pakietu, który ładuje dane ze źródła do miejsca docelowego.
* Uruchom pakiet SSIS do ładowania danych.

Ten samouczek używa programu SQL Server jako źródła danych. SQL Server może działać lokalnie lub w maszynie wirtualnej platformy Azure.

## <a name="basic-concepts"></a>Podstawowe pojęcia
Pakiet jest jednostka pracy w SSIS. Pakiety powiązane są pogrupowane w projektach. W programie Visual Studio z programu SQL Server Data Tools jest tworzenie projektów i pakietów projektów. Proces projektowania jest procesem visual, w którym możesz przeciągnij i upuść składniki z przybornika do powierzchni projektu, podłącz je i ustawiania ich właściwości. Po zakończeniu pakietu, można opcjonalnie wdrożyć go do programu SQL Server kompleksowego zarządzania, monitorowania i zabezpieczeń.

## <a name="options-for-loading-data-with-ssis"></a>Opcje dotyczące ładowania danych z usług SSIS
SQL Server Integration Services (SSIS) to elastyczne zestaw narzędzi, która zapewnia różne opcje połączenie i ładowania danych do usługi SQL Data Warehouse.

1. Docelowy NET ADO używana do łączenia usługi SQL Data Warehouse. W tym samouczku używa docelowego NET ADO ponieważ składa się z najmniejszą liczbą opcji konfiguracji.
2. OLE DB docelowym używana do łączenia usługi SQL Data Warehouse. Ta opcja może zapewnić nieco lepszą wydajność niż docelowy NET ADO.
3. Aby przemieścić danych w magazynie obiektów Blob Azure, użyj zadań przekazania obiektu Blob Azure. Następnie użyć zadania SSIS wykonaj instrukcję SQL, aby uruchomić skrypt programu Polybase, który ładuje dane do usługi SQL Data Warehouse. Ta opcja zapewnia najlepszą wydajność trzy opcje wymienione w tym miejscu. Aby uzyskać zadanie przekazania obiektu Blob Azure, Pobierz [Microsoft SQL Server 2016 integracji usług Feature Pack dla systemu Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Aby dowiedzieć się więcej na temat programu Polybase, zobacz [Przewodnik po programie PolyBase][PolyBase Guide].

## <a name="before-you-start"></a>Przed rozpoczęciem
Do wykonania kroków opisanych w tym samouczku potrzebne są:

1. **SQL Server Integration Services (SSIS)**. SSIS jest składnikiem programu SQL Server i wymaga wersji próbnej lub licencjonowanej wersji programu SQL Server. Aby pobrać wersję ewaluacyjną programu SQL Server 2016 w wersji zapoznawczej, zobacz [programu SQL Server ocen][SQL Server Evaluations].
2. Program **Visual Studio**. Aby uzyskać bezpłatne Visual Studio Community Edition, zobacz [Visual Studio Community][Visual Studio Community].
3. **SQL Server Data Tools dla programu Visual Studio (SSDT)**. Aby uzyskać SQL Server Data Tools dla programu Visual Studio, zobacz [Pobierz programu SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Przykładowe dane**. Ten samouczek używa przykładowe dane przechowywane w programie SQL Server w przykładowej bazie danych AdventureWorks jako źródło danych do załadowania do usługi SQL Data Warehouse. Aby pobrać przykładową bazę danych AdventureWorks, zobacz [AdventureWorks 2014 przykładowe bazy danych][AdventureWorks 2014 Sample Databases].
5. **Baza danych SQL Data Warehouse i uprawnienia**. W tym samouczku łączy się z wystąpieniem usługi SQL Data Warehouse i ładuje dane do niego. Musisz mieć uprawnienia do tworzenia tabeli i ładowanie danych.
6. **Reguły zapory**. Należy utworzyć regułę zapory na SQL Data Warehouse przy użyciu adresu IP komputera lokalnego, przed może przekazywać dane do usługi SQL Data Warehouse.

## <a name="step-1-create-a-new-integration-services-project"></a>Krok 1: Utwórz nowy projekt usług Integration Services
1. Uruchom program Visual Studio.
2. Na **pliku** menu, wybierz opcję **nowy | Projekt**.
3. Przejdź do **zainstalowane | Szablony | Analiza biznesowa | Usługi integracji** typy projektów.
4. Wybierz **projektu usług integracji**. Podaj wartości dla **nazwa** i **lokalizacji**, a następnie wybierz **OK**.

Visual Studio otwierania i tworzenia nowego projektu Integration Services (SSIS). Następnie Visual Studio otworzy projektanta dla jednego nowego pakietu SSIS (Package.dtsx) w projekcie. Zostanie wyświetlony ekran następujących obszarów:

* Po lewej stronie **przybornika** składników usług SSIS.
* W środku powierzchnię projektu z wieloma kartami. Używane zwykle co najmniej **przepływ sterowania** i **przepływu danych** karty.
* Po prawej stronie **Eksploratora rozwiązań** i **właściwości** okienka.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Krok 2: Tworzenie przepływu danych podstawowych
1. Przeciągnij zadania przepływu danych z przybornika do środka powierzchni projektu (na **przepływ sterowania** kartę).
   
    ![][02]
2. Kliknij dwukrotnie zadania przepływu danych, aby przełączyć na kartę przepływu danych.
3. Na liście innych źródeł w przyborniku przeciągnij źródła danych ADO.NET na powierzchnię projektu. Z kartą źródła wciąż zaznaczony, zmień jego nazwę, aby **źródła SQL Server** w **właściwości** okienka.
4. Na liście inne miejsca docelowe w przyborniku przeciągnij docelowego ADO.NET na powierzchnię projektu w źródle danych ADO.NET. Z kartą docelowego nadal zaznaczone, zmień jego nazwę, aby **docelowego SQL DW** w **właściwości** okienka.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Krok 3: Konfigurowanie karty źródła
1. Kliknij dwukrotnie kartę źródła, aby otworzyć **Edytor źródła ADO.NET**.
   
    ![][03]
2. Na **Menedżera połączeń** karty **Edytor źródła ADO.NET**, kliknij przycisk **nowy** znajdujący się obok **Menedżera połączeń ADO.NET** listy, aby otworzyć **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe i utworzyć ustawienia połączenia dla bazy danych programu SQL Server, z którego ten samouczek ładuje dane.
   
    ![][04]
3. W **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe, kliknij przycisk **nowy** przycisk, aby otworzyć **Menedżera połączeń** okno dialogowe i utworzyć nowe połączenie danych.
   
    ![][05]
4. W **Menedżera połączeń** okno dialogowe, wykonaj następujące czynności.
   
   1. Aby uzyskać **dostawcy**, wybierz dostawcę danych SqlClient.
   2. Aby uzyskać **nazwy serwera**, wprowadź nazwę serwera SQL.
   3. W **Zaloguj się do serwera** wybierz lub wprowadź informacje dotyczące uwierzytelniania.
   4. W **połączenie z bazą danych** Wybierz przykładową bazę danych AdventureWorks.
   5. Kliknij przycisk **połączenie testowe**.
      
       ![][06]
   6. W oknie dialogowym wyświetla wyniki testu połączenia, kliknij przycisk **OK** aby powrócić do **Menedżera połączeń** okno dialogowe.
   7. W **Menedżera połączeń** okno dialogowe, kliknij przycisk **OK** aby powrócić do **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe.
5. W **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe, kliknij przycisk **OK** aby powrócić do **Edytor źródła ADO.NET**.
6. W **Edytor źródła ADO.NET**w **nazwę tabeli lub widoku** listy, wybierz **Sales.SalesOrderDetail** tabeli.
   
    ![][07]
7. Kliknij przycisk **Podgląd** aby zobaczyć pierwszych 200 wierszy danych w tabeli źródłowej w **Podgląd wyników zapytania** okno dialogowe.
   
    ![][08]
8. W **Podgląd wyników zapytania** okno dialogowe, kliknij przycisk **Zamknij** aby powrócić do **Edytor źródła ADO.NET**.
9. W **Edytor źródła ADO.NET**, kliknij przycisk **OK** do zakończenia konfigurowania źródła danych.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Krok 4: Połącz kartę źródła adapter miejsca docelowego
1. Wybierz kartę źródła na powierzchni projektu.
2. Wybierz niebieską strzałką, rozszerzający z karty źródła i przeciągnij go do docelowego edytora, dopóki nie zostanie zablokowany na miejscu.
   
    ![][10]
   
    W typowych pakietów SSIS umożliwia wiele innych składników z przybornika SSIS Between źródłową i docelową restrukturyzacji, transformacji i czyszczenia danych, ponieważ przechodzi ona przez przepływ danych usług SSIS. Aby zachować w tym przykładzie jest tak proste, jak to możliwe, możemy ustanawiane jest połączenie źródło bezpośrednio do miejsca docelowego.

## <a name="step-5-configure-the-destination-adapter"></a>Krok 5: Konfigurowanie adapter miejsca docelowego
1. Kliknij dwukrotnie kartę docelowego, aby otworzyć **ADO.NET docelowego edytora**.
   
    ![][11]
2. Na **Menedżera połączeń** karty **ADO.NET docelowego edytora**, kliknij przycisk **nowy** znajdujący się obok **Menedżera połączeń** listy, aby otworzyć **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe i utworzyć ustawienia połączenia dla bazy danych Azure SQL Data Warehouse, w którym w tym samouczku ładuje dane.
3. W **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe, kliknij przycisk **nowy** przycisk, aby otworzyć **Menedżera połączeń** okno dialogowe i utworzyć nowe połączenie danych.
4. W **Menedżera połączeń** okno dialogowe, wykonaj następujące czynności.
   1. Aby uzyskać **dostawcy**, wybierz dostawcę danych SqlClient.
   2. Aby uzyskać **nazwy serwera**, wprowadź nazwę usługi SQL Data Warehouse.
   3. W **Zaloguj się do serwera** zaznacz **uwierzytelniania programu SQL Server używana** , a następnie wprowadź informacje dotyczące uwierzytelniania.
   4. W **połączenie z bazą danych** wybierz istniejącą bazę danych usługi SQL Data Warehouse.
   5. Kliknij przycisk **połączenie testowe**.
   6. W oknie dialogowym wyświetla wyniki testu połączenia, kliknij przycisk **OK** aby powrócić do **Menedżera połączeń** okno dialogowe.
   7. W **Menedżera połączeń** okno dialogowe, kliknij przycisk **OK** aby powrócić do **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe.
5. W **Konfigurowanie Menedżera połączeń ADO.NET** okno dialogowe, kliknij przycisk **OK** aby powrócić do **ADO.NET docelowego edytora**.
6. W **ADO.NET docelowego edytora**, kliknij przycisk **nowy** obok **za pomocą tabeli lub widoku** listy, aby otworzyć **Create Table** okno dialogowe, aby utworzyć nową tabelę miejsce docelowe z listy kolumn, który odpowiada tabeli źródłowej.
   
    ![][12a]
7. W **Create Table** okno dialogowe, wykonaj następujące czynności.
   
   1. Zmień nazwę tabeli docelowej do **szczegóły zamówienia sprzedaży**.
   2. Usuń **rowguid** kolumny. **Uniqueidentifier** — typ danych nie jest obsługiwana w usłudze SQL Data Warehouse.
   3. Zmień typ danych **LineTotal** kolumny **pieniędzy**. **Dziesiętną** — typ danych nie jest obsługiwana w usłudze SQL Data Warehouse. Informacje o obsługiwane typy danych, zobacz [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Kliknij przycisk **OK** do utworzenia tabeli, a następnie wróć do **ADO.NET docelowego edytora**.
8. W **ADO.NET docelowego edytora**, wybierz pozycję **mapowania** kartę, aby zobaczyć, jak kolumny w źródle są mapowane na kolumny w lokalizacji docelowej.
   
    ![][13]
9. Kliknij przycisk **OK** do zakończenia konfigurowania źródła danych.

## <a name="step-6-run-the-package-to-load-the-data"></a>Krok 6: Uruchamianie pakietu do ładowania danych
Uruchom pakiet, klikając **Start** przycisk na pasku narzędzi lub wybierając jedną z **Uruchom** opcje na **debugowania** menu.

Pakiet po rozpoczęciu do uruchomienia, zostanie wyświetlony żółty koła Obracająca, aby wskazać, działania, a także liczbę wierszy do tej pory przetworzone.

![][14]

Gdy pakiet zakończył działanie, zostanie wyświetlony zielony znaczniki wyboru w celu wskazania powodzenia, jak również całkowita liczba wierszy danych załadowanych od źródła do miejsca docelowego.

![][15]

Gratulacje! SQL Server Integration Services została pomyślnie użyta, ładowanie danych do usługi Azure SQL Data Warehouse.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat przepływu danych usług SSIS. Zacznij tutaj: [przepływu danych][Data Flow].
* Dowiedz się, jak debugowania i rozwiązywania problemów z uprawnienie pakiety w środowisku projektowym. Zacznij tutaj: [Rozwiązywanie problemów z narzędzia do tworzenia pakietu][Troubleshooting Tools for Package Development].
* Dowiedz się, jak wdrożyć swoje projekty SSIS i pakietów, na serwer usług Integration Services lub do innej lokalizacji magazynu. Zacznij tutaj: [projekty wdrożeń i pakietów][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
