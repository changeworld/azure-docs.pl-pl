---
title: Inspekcji w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Więcej informacji na temat inspekcji i sposobu konfigurowania inspekcji w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: rortloff
ms.reviewer: jrj
ms.openlocfilehash: 7bce59114dcfef7f9faa7f112c62dbb88044f2ab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Inspekcja w magazynie danych Azure SQL

Więcej informacji na temat inspekcji i sposobu konfigurowania inspekcji w usłudze Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>Co to jest inspekcji?
Inspekcja SQL Data Warehouse pozwala do rekordu dziennika zdarzeń w bazie danych inspekcji na koncie magazynu Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń. Inspekcja SQL Data Warehouse integruje się również z usługi Microsoft Power BI dla raportów i analiz.

Narzędzia inspekcji włączyć i ułatwienia przestrzeganie standardów zgodności, ale nie gwarantuje się zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy tego zgodność ze standardami pomocy technicznej, zobacz <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centrum zaufania Azure</a>.

## <a id="subheading-1"></a>Podstawowe informacje o inspekcji
Inspekcja bazy danych SQL Data Warehouse umożliwia:

* **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań przeprowadzać inspekcję bazy danych.
* **Raport** na działanie bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczęcie pracy z aktywności i raportowanie zdarzeń.
* **Analizowanie** raportów. Można znaleźć podejrzane zdarzenia, nietypowe działania i trendów.

Można skonfigurować inspekcji w ramach następujących kategorii zdarzeń:

**Zwykły SQL** i **sparametryzowana SQL** dla którego dzienników inspekcji zbierane są sklasyfikowane jako  

* **Dostęp do danych**
* **Zmiany schematu (DDL)**
* **Zmiany danych (DML)**
* **Konta, role i uprawnienia (DCL)**
* **Procedura składowana**, **logowania** i **zarządzania transakcji**.

Dla każdej kategorii zdarzenia inspekcji z **Powodzenie** i **błąd** operacje nie zostały skonfigurowane osobno.

Aby uzyskać więcej informacji dotyczących działań i zdarzeń inspekcji, zobacz <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">odwołanie do formatu dziennika inspekcji (Pobieranie pliku doc)</a>.

Dzienniki inspekcji są przechowywane na koncie magazynu Azure. Można zdefiniować okres przechowywania dziennika inspekcji.

Można zdefiniować zasady inspekcji dla określonej bazy danych lub jako domyślne zasady serwera. Domyślne zasady inspekcji serwera ma zastosowanie do wszystkich baz danych na serwerze, które nie mają określonej bazy danych inspekcji zasad zdefiniowane.

Przed przystąpieniem do ustawiania inspekcji inspekcji wyboru, jeśli używasz ["Klientów niższych poziomów."](sql-data-warehouse-auditing-downlevel-clients.md)

## <a id="subheading-2"></a>Inspekcja bazy danych
1. Uruchom <a href="https://portal.azure.com" target="_blank">portalu Azure</a>.
2. Przejdź do **ustawienia** dla inspekcji ma usługi SQL Data Warehouse. Wybierz **Inspekcja i wykrywanie zagrożeń**.
   
    ![][1]
3. Należy również włączyć inspekcję, klikając **ON** przycisku.
   
    ![][3]
4. W panelu inspekcji konfiguracji wybierz **szczegóły MAGAZYNU** aby otworzyć panel magazynu dzienników inspekcji. Wybierz konto magazynu Azure dla dzienników i okresu przechowywania. 
>[!TIP]
>Użyj tego samego konta magazynu dla wszystkich baz danych inspekcji na maksymalne wykorzystanie szablonów wstępnie skonfigurowane raporty.
   
    ![][4]
5. Kliknij przycisk **OK** przycisk, aby zapisać konfigurację szczegóły magazynu.
6. W obszarze **rejestrowanie przez zdarzenie**, kliknij przycisk **Powodzenie** i **błąd** do rejestrowania wszystkich zdarzeń lub wybierz kategorie poszczególnych zdarzeń.
7. W przypadku konfigurowania inspekcji bazy danych, może być konieczne zmienić parametry połączenia klientowi upewnij się, że poprawnie przechwycone dane inspekcji. Sprawdź [zmodyfikować FDQN serwera w parametrach połączenia](sql-data-warehouse-auditing-downlevel-clients.md) tematu dla połączeń klientów niższych poziomów.
8. Kliknij przycisk **OK**.

## <a id="subheading-3"></a>Analizowanie dzienników inspekcji i raportów
Dzienniki inspekcji są agregowane w zbiorze magazynu tabel z **SQLDBAuditLogs** prefiks na koncie magazynu Azure wybrana w Instalatorze. Można wyświetlić przy użyciu narzędzia, takie jak pliki dziennika <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Eksploratora usługi Storage Azure</a>.

Szablon raportu dotyczącego wstępnie skonfigurowane pulpit nawigacyjny jest dostępny jako <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">arkusz kalkulacyjny programu Excel do pobrania</a> ułatwiają szybkie analizowanie danych dziennika. Aby użyć szablonu w dziennikach inspekcji, należy Excel 2013 lub nowszy i dodatku Power Query, który można pobrać <a href="http://www.microsoft.com/download/details.aspx?id=39379">tutaj</a>.

Szablon ma fikcyjnej przykładowe dane w nim, a dodatku Power Query można skonfigurować do zaimportowania dziennik inspekcji bezpośrednio z kontem magazynu platformy Azure.

## <a id="subheading-4"></a>Ponowne generowanie klucza magazynu
W środowisku produkcyjnym najprawdopodobniej będzie okresowo Odśwież kluczy magazynu. Podczas odświeżania kluczy, należy zapisać zasady. Proces przebiega w następujący sposób:

1. W przypadku inspekcji panelu konfiguracji, które opisano w poprzedniej instalacji inspekcji sekcji, zmień **klucz dostępu do magazynu** z *głównej* do *dodatkowej* i  **ZAPISZ**.

   ![][4]
2. Przejdź do panelu konfiguracji magazynu i **ponownie wygenerować** *podstawowy klucz dostępu*.
3. Wróć do panelu Konfiguracja inspekcji 
4. Przełącz **klucz dostępu do magazynu** z *dodatkowej* do *głównej* i naciśnij klawisz **ZAPISAĆ**.
4. Wróć do magazynu interfejsu użytkownika i **ponownie wygenerować** *pomocniczy klucz dostępu* (jako przygotowania do następnej klucze odświeżania w tle.

## <a id="subheading-5"></a>Automatyzacja (programu PowerShell/REST API)
Można również skonfigurować inspekcji w usłudze Azure SQL Data Warehouse przy użyciu następujących narzędzi automatyzacji:

* **Polecenia cmdlet programu PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Obsługa klientów niższego poziomu inspekcji i dynamicznego maskowania danych
Inspekcja współpracuje z klientami SQL, które obsługują przekierowanie TDS.

Każdy klient, który implementuje TDS 7.4 również powinien obsługiwać przekierowania. Wyjątki od tej reguły obejmują JDBC 4.0, w którym funkcji przekierowania nie jest w pełni obsługiwane i Tedious dla środowiska Node.JS, w których przekierowania nie została zaimplementowana.

Dla "Klientów niższych poziomów" obsługujących TDS wersji 7.3 oraz poniżej, zmodyfikuj nazwa FQDN serwera w ciągu połączenia w następujący sposób:

- Oryginalna nazwa FQDN serwera w ciągu połączenia: <*nazwy serwera*>. database.windows.net
- Nazwa FQDN serwera zmodyfikowane w ciągu połączenia: <*nazwy serwera*> .database. **bezpieczne**. windows.net

Zawiera listę częściowej "Klienci z obniżonym poziomem":

* .NET 4.0 i poniżej,
* ODBC 10.0 i poniżej.
* JDBC (podczas JDBC obsługuje TDS 7.4, funkcji przekierowania TDS nie jest całkowicie obsługiwana)
* Niewygodny (dla środowiska Node.JS)

**Uwaga:** poprzedni serwer modyfikacji FDQN może być przydatne także stosowania zasad inspekcji programu SQL Server poziom bez potrzebę konfiguracji kroku w każdej bazie danych (ograniczenie tymczasowe).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


