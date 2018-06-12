---
title: Łączenie z serwerem SQL lub bazą danych Azure SQL — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Dostęp i zarządzaniu baz danych lokalnie lub w chmurze automatyzując przepływy pracy z usługą Azure Logic Apps
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
ms.openlocfilehash: dccb91c782408a5fed5c3ef1b68f9918823ce402
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296292"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Łączenie z serwerem SQL lub bazy danych Azure SQL z usługi Azure Logic Apps

W tym artykule przedstawiono, jak można pobrać danych w bazie danych SQL z wewnątrz aplikacji logiki z łącznikiem programu SQL Server. W ten sposób można zautomatyzować zadania, procesów i przepływy pracy, które zarządzanie przez tworzenie aplikacji logiki danych SQL i zasobów. Łącznik działa zarówno [programu SQL Server na lokalnych](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) i [bazy danych SQL Azure w chmurze](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Można utworzyć aplikacji logiki, które można uruchamiać, gdy wyzwalane przez zdarzenia w bazie danych SQL lub w innych systemach, takich jak usługi Dynamics CRM Online. Aplikacje logiki można również pobrać, wstawianie i usuwanie danych oraz wykonywanie zapytań SQL i zapisanych procedur. Na przykład można utworzyć aplikację logiki, który automatycznie sprawdza, czy dla nowych rekordów w Dynamics CRM Online, dodaje elementy do bazy danych SQL dla nowych rekordów, a następnie wysyła wiadomości e-mail dla alertów.

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/sql/" target="blank">odwołanie łącznika programu SQL Server</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikację logiki, gdy potrzebny jest dostęp do bazy danych SQL. Aby uruchomić aplikację logiki z wyzwalaczem SQL, należy [aplikacji logiki puste](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* [Bazy danych Azure SQL](../sql-database/sql-database-get-started-portal.md) lub [bazy danych programu SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Tabele musi mieć dane, tak aby aplikację logiki może zwrócić wyniki podczas wywoływania operacji. W przypadku utworzenia bazy danych SQL Azure, można użyć przykładowej bazy danych, które są dołączone. 

* Nazwa serwera SQL, nazwa bazy danych, nazwę użytkownika i hasło. Potrzebujesz poświadczeń, dzięki czemu można autoryzować logiki, aby uzyskać dostęp do serwera SQL. 

  * Bazy danych SQL Azure te szczegółowe informacje można znaleźć w parametrach połączenia lub w portalu Azure w obszarze właściwości bazy danych SQL:

    "Serwer = tcp: <*yourServerName*>. database.windows.net,1433;Initial katalogu = <*yourDatabaseName*>; Utrzymuj informacje o zabezpieczeniach = False; Nazwa użytkownika = <*nazwa_użytkownika*>; Hasło = <*yourPassword*>; MultipleActiveResultSets = False; Szyfrowanie = True; TrustServerCertificate = False; Limit czasu połączenia = 30; "

  * Dla programu SQL Server te informacje można znaleźć w ciągu połączenia: 

    "Serwer = <*yourServerAddress*>; Baza danych = <*yourDatabaseName*>; Nazwa użytkownika = <*nazwa_użytkownika*>; Hasło = <*yourPassword*>; "

* Zanim będzie można połączyć aplikacji logiki do systemów lokalnych, takich jak SQL Server, należy najpierw [Skonfiguruj bramę danych lokalnych](../logic-apps/logic-apps-gateway-install.md). W ten sposób można wybrać bramy podczas tworzenia aplikacji logiki połączenia SQL.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Dodaj wyzwalacza SQL

W aplikacjach logiki platformy Azure, każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), które odbywa się uruchamiany, gdy określonego zdarzenia lub gdy zostanie spełniony określony warunek. Zawsze uruchamiany wyzwalacza aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania aplikacji przepływu pracy.

1. W portalu Azure lub programu Visual Studio tworzenie aplikacji logiki puste, który otwiera projektanta aplikacji logiki. W przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "sql server" jako filtr. Na liście wyzwalaczy zaznacz wyzwalaczy SQL, które chcesz. 

   Na przykład wybierz wyzwalacz: **SQL Server — po utworzeniu elementu**

   ![Wybierz opcję "SQL Server — po utworzeniu elementu" wyzwalacza](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Jeśli zostanie wyświetlony monit o szczegóły połączenia [utworzyć połączenia SQL. teraz](#create-connection). 
   Lub, jeśli połączenie już istnieje, wybierz **nazwy tabeli** interesujące z listy.

   ![Wybierz tabelę](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Ustaw **interwał** i **częstotliwość** właściwości, które określają, jak często sprawdza aplikację logiki w tabeli.

   W tym przykładzie sprawdza tylko wybranej tabeli nic. 
   Aby wykonać ciekawsze, dodać czynności służących do wykonywania zadań, które mają. 
   
   Na przykład aby wyświetlić nowy element w tabeli, możesz może dodać inne akcje, takie jak utworzyć plik, który ma pola z tabeli i następnie wysyłania alertów e-mail. 
   Aby dowiedzieć się o innych akcji dla tego łącznika lub innych łączników, zobacz [łączniki Logic Apps](../connectors/apis-list.md).

5. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**. 

   Ten krok umożliwia i automatycznie publikuje aplikację logiki na platformie Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Dodaj akcję SQL

W aplikacjach logiki platformy Azure [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy następujący wyzwalacz inną akcję. W tym przykładzie aplikacji logiki rozpoczyna się od [wyzwalacza cyklu](../connectors/connectors-native-recurrence.md)i wywołuje akcję, która pobiera wiersz z bazy danych SQL.

1. W portalu Azure lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W przykładzie użyto portalu Azure.

2. W Projektancie aplikacji logiki w ramach wyzwalacza lub akcji, wybierz **nowy krok** > **Dodaj akcję**.

   ![Wybierz pozycję "Nowy krok", "Dodaj akcję"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus (**+**) zostanie wyświetlony, a następnie wybierz **Dodaj akcję**.

2. W polu wyszukiwania wprowadź "sql server" jako filtr. Wybierz dowolną akcję SQL, która ma z listy akcji. 

   Na przykład wybierz tę akcję, która pobiera jeden rekord: **SQL Server — wiersz Get**

   ![Wpisz "sql server", wybierz "SQL Server — wiersz Get"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Jeśli zostanie wyświetlony monit o szczegóły połączenia [utworzyć połączenia SQL. teraz](#create-connection). 
   Lub, jeśli istnieje połączenie, wybierz **nazwy tabeli**, a następnie wprowadź **identyfikator wiersza** dla rekordu, który ma.

   ![Wprowadź nazwę tabeli i identyfikator wiersza:](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   W tym przykładzie zwraca tylko jeden wiersz z tabeli, nic. 
   Aby wyświetlić dane w tym wierszu, możesz dodać inne akcje, które Utwórz plik zawierający pola z wiersza do przeglądu nowsze i przechowywać plik w konta magazynu w chmurze. Aby poznać inne akcje w ten łącznik lub inne łączniki, zobacz temat [łączniki Logic Apps](../connectors/apis-list.md).

4. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Dane procesu zbiorcze

Podczas pracy z zestawów wyników tak duża, że łącznik nie zwraca wszystkie wyniki w tym samym czasie, lub lepszą kontrolę nad rozmiaru i struktury dla Twojego zestawów wyników, można użyć *podział na strony*, który pomaga zarządzać tymi wyniki w postaci mniejszych zestawów. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Utwórz procedurę składowaną

Podczas pobierania lub wstawianie wiele wierszy, aplikację logiki można wykonać iterację tych elementów przy użyciu [ *do pętli* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) w ramach tych [limity](../logic-apps/logic-apps-limits-and-config.md). Jednak czasami ma aplikacji logiki do pracy z zestawów rekordów tak duży, takich jak tysiące lub miliony wierszy, które mają być zminimalizować koszty dla połączeń z bazą danych. 

Zamiast tego można utworzyć <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *procedury składowanej* </a> że wystąpienia SQL, korzysta z **wybierz - ORDER BY** instrukcji, aby zorganizować wyniki w sposób. To rozwiązanie zapewnia większą kontrolę nad rozmiaru i struktury wyników. Aplikację logiki wywołuje procedurę składowaną przy użyciu łącznika programu SQL Server **wykonać procedurę składowaną** akcji. 

Szczegółowe rozwiązania zobacz następujące artykuły:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL podział na strony do transferu danych zbiorczego z usługą Logic Apps</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Wybierz — klauzula ORDER BY</a>

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać informacje techniczne dotyczące wyzwalaczy, akcje i limity tego łącznika, zobacz [szczegóły odwołanie łącznika](/connectors/sql/). 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączniki Logic Apps](../connectors/apis-list.md)

