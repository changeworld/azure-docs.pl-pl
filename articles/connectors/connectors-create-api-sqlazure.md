---
title: Łączenie się z programu SQL Server lub usługi Azure SQL Database — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Uzyskiwanie dostępu i zarządzania bazami danych SQL, lokalnie lub w chmurze dzięki automatyzacji przepływów pracy z usługą Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 998fcba50636cd92b14bdbe1633c2548e84a6bfc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696417"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Nawiązywanie połączenia programu SQL Server lub usługi Azure SQL Database z usługi Azure Logic Apps

Ten artykuł pokazuje, jak można pobrać danych w usłudze SQL database z wewnątrz aplikacji logiki za pomocą łącznika programu SQL Server. W ten sposób można zautomatyzować zadania, procesów i przepływów pracy, które Zarządzanie danych SQL i zasobów przez tworzenie aplikacji logiki. Łącznik działa zarówno [programu SQL Server w środowisku lokalnym](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) i [usługi Azure SQL Database w chmurze](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Można tworzyć aplikacje logiki, które są uruchamiane po wyzwoleniu przez zdarzenia w usłudze SQL database lub w innych systemach, takich jak Dynamics CRM Online. Aplikacje logiki można również uzyskać, wstawianie i usuwanie danych oraz wykonywanie zapytań SQL i procedur składowanych. Można na przykład, utworzyć aplikację logiki, która automatycznie sprawdza, czy dla nowych rekordów w usłudze Dynamics CRM Online, dodaje elementy do bazy danych SQL dla żadnych nowych rekordów, a następnie wysyła wiadomości e-mail dla alertów.

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/sql/" target="blank">dokumentacja łączników programu SQL Server</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja logiki gdzie należy uzyskać dostęp do usługi SQL database. Aby uruchomić aplikację logiki z wyzwalaczem SQL, musisz mieć [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* [Bazy danych Azure SQL](../sql-database/sql-database-get-started-portal.md) lub [bazy danych programu SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Tabele musi mieć danych tak, aby Twoja aplikacja logiki może zwrócić wyniki podczas wywoływania operacji. Jeśli tworzysz usługi Azure SQL Database, można użyć przykładowych baz danych, które są zawarte. 

* Nazwę serwera SQL, nazwę bazy danych, nazwę użytkownika i hasło. Należy korzystać z tych poświadczeń, aby autoryzować logika dostępu do usługi serwera SQL. 

  * Usługi Azure SQL Database możesz znaleźć te informacje w parametrach połączenia lub w witrynie Azure portal w obszarze właściwości bazy danych SQL:

    "Server = tcp: <*yourServerName*>. database.windows.net,1433;Initial Catalog = <*yourDatabaseName*>; Utrwal informacje zabezpieczające = False;. Nazwa użytkownika = <*nazwa_użytkownika*>; Hasło = <*yourPassword*>; MultipleActiveResultSets = False;. Szyfruj = True; TrustServerCertificate = False;. Connection Timeout = 30; "

  * Dla programu SQL Server można znaleźć te informacje w parametrach połączenia: 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* Zanim będzie można połączyć aplikacji logiki do systemów lokalnych, takich jak SQL Server, należy najpierw [skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). W ten sposób można wybrać bramy podczas tworzenia połączenia SQL dla aplikacji logiki.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Dodaj wyzwalacz SQL

W usłudze Azure Logic Apps, każda aplikacja logiki musi rozpoczynać się [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest aktywowany wystąpienia, gdy zajdzie określone zdarzenie lub po spełnieniu określonego warunku. Każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania przepływu pracy Twojej aplikacji.

1. W witrynie Azure portal lub programu Visual Studio należy utworzyć pustej aplikacji logiki, który zostanie otwarty projektant aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "sql server" jako filtr. Z listy wyzwalaczy wybierz wyzwalacz SQL, który chcesz. 

   W tym przykładzie wybierz następujący wyzwalacz: **SQL Server — po utworzeniu elementu**

   ![Wybierz pozycję "SQL Server — po utworzeniu elementu" wyzwalacza](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenia SQL. teraz](#create-connection). 
   Lub, jeśli istnieje już połączenie, wybierz opcję **nazwy tabeli** , ma na liście.

   ![Wybieranie tabeli](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Ustaw **interwał** i **częstotliwość** właściwości, które określają, jak często aplikacja logiki sprawdza tabelę.

   W tym przykładzie sprawdza tylko wybranej tabeli, nic innego. 
   Aby wykonać ciekawsze, Dodaj akcje, które wykonują zadania, które chcesz. 
   
   Na przykład aby wyświetlić nowy element w tabeli, możesz może dodać inne akcje, takie jak utworzyć plik, który zawiera pola z tabeli i następnie wysyłania alertów e-mail. 
   Aby dowiedzieć się o innych akcji dla tego łącznika lub innych łączników, zobacz [łączników Logic Apps](../connectors/apis-list.md).

5. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**. 

   Ten krok umożliwia i automatycznie publikuje aplikację logiki na platformie Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Dodawanie akcji SQL

W usłudze Azure Logic Apps [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest to krok w przepływie pracy, występującego wyzwalacza lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacz cykliczny](../connectors/connectors-native-recurrence.md)i wywołuje akcję, która pobiera wiersz z bazy danych SQL.

1. W witrynie Azure portal lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W Projektancie aplikacji logiki w obszarze wyzwalacza lub akcji, wybierz **nowy krok** > **Dodaj akcję**.

   ![Wybierz pozycję "Nowy krok", "Dodaj akcję"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

2. W polu wyszukiwania wprowadź "sql server" jako filtr. Z listy akcji wybierz dowolną akcję SQL, która ma. 

   W tym przykładzie wybierz tej akcji, które pobiera pojedynczy rekord: **SQL Server — Pobierz wiersz**

   ![Wprowadź "sql server", wybierz pozycję "SQL Server — Pobierz wiersz"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenia SQL. teraz](#create-connection). 
   Lub, jeśli istnieje połączenie, wybierz **nazwy tabeli**, a następnie wprowadź **identyfikator wiersza** dla rekordu, który ma.

   ![Wprowadź nazwę tabeli, a identyfikator wiersza](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   W tym przykładzie zwraca tylko jeden wiersz z tabeli, nic innego. 
   Aby wyświetlić dane w tym wierszu, możesz dodać inne akcje, które Utwórz plik z polami z wierszy do późniejszego przejrzenia i ten plik jest przechowywany na koncie magazynu w chmurze. Aby poznać inne akcje, które ten łącznik lub innych łączników, zobacz temat [łączników Logic Apps](../connectors/apis-list.md).

4. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Dojście do zbiorczego danych

Czasami trzeba pracować tak duża, że łącznik nie zwraca wszystkie wyniki, w tym samym czasie lub mają lepszą kontrolę nad rozmiarem i struktury dla zestawów wyników zestawów wyników. Oto kilka sposobów obsługi takich dużych zestawów wyników:

* Aby ułatwić zarządzanie wyniki w postaci mniejszych zestawów, należy włączyć funkcję *dzielenia na strony*. Aby uzyskać więcej informacji, zobacz [uzyskać dane zbiorcze, rekordy i elementy za pomocą dzielenia na strony](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Utwórz procedurę składowaną, który organizuje wyniki w żądany sposób.

  Podczas pobierania lub wstawianie wiele wierszy, aplikacja logiki może wykonać iterację te wiersze przy użyciu [ *pętlą until* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) w ramach tych [limity](../logic-apps/logic-apps-limits-and-config.md). 
  Jednak jeśli Twoja aplikacja logiki ma do pracy z zestawami rekordów tak dużych, na przykład, tysięcy lub milionów wierszy, chcesz zminimalizować koszty wynikające z wywołania do bazy danych.

  Aby uporządkować wyniki w taki sposób, który chcesz, możesz utworzyć [ *procedury składowanej* ](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , w ramach wystąpienia SQL, korzysta z **wybierz - ORDER BY** instrukcji. 
  To rozwiązanie zapewnia większą kontrolę nad rozmiarem i struktury wyników. 
  Twoja aplikacja logiki wywołuje procedury składowanej przy użyciu łącznika programu SQL Server **wykonaj procedurę składowaną** akcji.

  Aby uzyskać szczegółowe informacje z rozwiązania zobacz następujące artykuły:

  * [Stronicowanie SQL do zbiorczego transferu danych za pomocą aplikacji logiki](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [Należy wybrać - ORDER BY — klauzula](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Aby uzyskać informacje techniczne o wyzwalaczach, akcje i limity tego łącznika, zobacz [szczegóły łącznika](/connectors/sql/). 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)

