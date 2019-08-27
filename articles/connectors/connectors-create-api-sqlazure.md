---
title: Nawiązywanie połączenia z usługą SQL Server lub Azure SQL Database-Azure Logic Apps | Microsoft Docs
description: Jak uzyskać dostęp do baz danych SQL i zarządzać nimi lokalnie lub w chmurze przez Automatyzowanie przepływów pracy za pomocą Azure Logic Apps
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
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050758"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Połącz się z SQL Server lub Azure SQL Database z Azure Logic Apps

W tym artykule pokazano, jak uzyskać dostęp do danych w bazie danych SQL z poziomu aplikacji logiki za pomocą łącznika SQL Server. Dzięki temu można zautomatyzować zadania, procesy i przepływy pracy służące do zarządzania danymi i zasobami SQL za pomocą tworzenia aplikacji logiki. Łącznik działa zarówno dla [SQL Server lokalnych](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) , jak i dla [Azure SQL Database w chmurze](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Możesz tworzyć aplikacje logiki, które są uruchamiane, gdy wyzwalane przez zdarzenia w bazie danych SQL lub w innych systemach, takich jak Dynamics CRM Online. Aplikacje logiki mogą również pobierać, wstawiać i usuwać dane wraz z wykonywaniem zapytań SQL i procedur składowanych. Można na przykład utworzyć aplikację logiki, która automatycznie sprawdza dostępność nowych rekordów w usłudze Dynamics CRM Online, dodaje do bazy danych SQL elementy dla nowych rekordów, a następnie wysyła alerty e-mail.

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Jeśli jesteś nowym usługą Logic Apps, zapoznaj [się](../logic-apps/logic-apps-overview.md) z tematem [Azure Logic Apps i szybki start: Utwórz swoją pierwszą aplikację](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiki. Informacje techniczne dotyczące konkretnego łącznika znajdują się w [dokumentacji dotyczącej łącznika SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja logiki, do której jest potrzebny dostęp do bazy danych SQL. Aby uruchomić aplikację logiki przy użyciu wyzwalacza SQL, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Baza danych [SQL Azure](../sql-database/sql-database-get-started-portal.md) lub [baza danych SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Tabele muszą zawierać dane, aby aplikacja logiki mogła zwracać wyniki podczas wywoływania operacji. W przypadku tworzenia Azure SQL Database można użyć przykładowych baz danych, które są dołączone. 

* Nazwa programu SQL Server, nazwa bazy danych, nazwa użytkownika i hasło. Te poświadczenia są potrzebne, aby można było autoryzować logikę w celu uzyskania dostępu do programu SQL Server. 

  * Aby uzyskać Azure SQL Database, można znaleźć te szczegóły w parametrach połączenia lub Azure Portal w obszarze właściwości SQL Database:

    "Serwer = TCP: <*yourServerName*>. Database. Windows. NET, 1433; Initial Catalog = <*yourDatabaseName*>; Utrwalaj informacje zabezpieczające = FAŁSZ; Identyfikator użytkownika = <*yourUserName*>; Password = <*yourPassword*>; MultipleActiveResultSets = FAŁSZ; Szyfrowanie = true; TrustServerCertificate = FAŁSZ; Limit czasu połączenia = 30; "

  * W przypadku SQL Server te szczegóły można znaleźć w parametrach połączenia: 

    "Server = <*yourServerAddress*>;D atabase = <*yourDatabaseName*>; Identyfikator użytkownika = <*yourUserName*>; Password = <*yourPassword*>; "

* Aby można było połączyć Aplikacje logiki z systemami lokalnymi, takimi jak SQL Server, należy [skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). Dzięki temu można wybrać bramę podczas tworzenia połączenia SQL dla aplikacji logiki.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Dodaj wyzwalacz SQL

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

1. W Azure Portal lub Visual Studio Utwórz pustą aplikację logiki, która otwiera Logic Apps projektanta. Ten przykład używa Azure Portal.

2. W polu wyszukiwania wprowadź wartość "SQL Server" jako filtr. Z listy Wyzwalacze wybierz wyzwalacz SQL, którego chcesz użyć. 

   Na potrzeby tego przykładu wybierz ten wyzwalacz: **SQL Server — po utworzeniu elementu**

   ![Wybierz wyzwalacz "SQL Server — po utworzeniu elementu"](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie SQL](#create-connection). 
   Lub jeśli połączenie już istnieje, wybierz z listy **nazwę tabeli** .

   ![Wybierz tabelę](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Ustaw właściwości **interwału** i **częstotliwości** , które określają, jak często aplikacja logiki ma sprawdzać tabelę.

   W tym przykładzie jest sprawdzana tylko wybrana tabela, nic innego. 
   Aby zrobić coś bardziej interesującego, Dodaj akcje, które wykonują żądane zadania. 
   
   Na przykład, aby wyświetlić nowy element w tabeli, możesz dodać inne akcje, takie jak tworzenie pliku z polami z tabeli, a następnie wysyłanie alertów e-mail. 
   Aby dowiedzieć się więcej o innych akcjach dla tego łącznika lub innych łączników, zobacz [Logic Apps łączników](../connectors/apis-list.md).

5. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

   Ten krok powoduje automatyczne włączenie i opublikowanie aplikacji logiki na żywo na platformie Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Dodaj akcję SQL

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza [cykl](../connectors/connectors-native-recurrence.md)i wywołuje akcję, która pobiera wiersz z bazy danych SQL.

1. W Azure Portal lub w programie Visual Studio Otwórz aplikację logiki w programie Logic Apps Designer. Ten przykład używa Azure Portal.

2. W Projektancie aplikacji logiki w obszarze wyzwalacza lub akcji wybierz pozycję **nowy krok** > **Dodaj akcję**.

   ![Wybierz pozycję "nowy krok", "Dodaj akcję"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

2. W polu wyszukiwania wprowadź wartość "SQL Server" jako filtr. Z listy Akcje wybierz dowolną akcję SQL. 

   Na potrzeby tego przykładu wybierz tę akcję, która pobiera pojedynczy rekord: **SQL Server — Pobierz wiersz**

   ![Wprowadź wartość "SQL Server", wybierz pozycję "SQL Server-Get Row"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie SQL](#create-connection). 
   Lub, jeśli połączenie istnieje, wybierz **nazwę tabeli**i wprowadź **Identyfikator wiersza** dla żądanego rekordu.

   ![Wprowadź nazwę tabeli i Identyfikator wiersza](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Ten przykład zwraca tylko jeden wiersz z wybranej tabeli, nic nie jest inne. 
   Aby wyświetlić dane w tym wierszu, możesz dodać inne akcje, które tworzą plik z polami z wiersza na potrzeby późniejszego przeglądu i przechowują ten plik na koncie magazynu w chmurze. Aby dowiedzieć się więcej o innych akcjach w tym łączniku lub innych łącznikach, zobacz [Logic Apps łączników](../connectors/apis-list.md).

4. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Obsługa danych zbiorczych

Czasami może być konieczne współdziałanie z zestawami wyników tak duże, że łącznik nie zwróci wszystkich wyników w tym samym czasie lub chcesz mieć lepszą kontrolę nad rozmiarem i strukturą zestawów wyników. Oto kilka sposobów obsługi takich dużych zestawów wyników:

* Aby ułatwić zarządzanie wynikami w postaci mniejszych zestawów, Włącz *podział na strony*. Aby uzyskać więcej informacji, zobacz [pobieranie danych zbiorczych, rekordów i elementów za pomocą dzielenia na strony](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Utwórz procedurę przechowywaną, która organizuje wyniki w odpowiedni sposób.

  Podczas pobierania lub wstawiania wielu wierszy aplikacja logiki może wykonać iterację tych wierszy przy użyciu [*pętli until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) w ramach tych [limitów](../logic-apps/logic-apps-limits-and-config.md). 
  Jeśli jednak aplikacja logiki musi współpracować z zestawami rekordów tak duże, na przykład tysięcy lub milionów wierszy, można zminimalizować koszty związane z wywołaniami bazy danych.

  Aby zorganizować wyniki w odpowiedni sposób, można utworzyć [*procedurę składowaną*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , która jest uruchamiana w wystąpieniu SQL, i używa instrukcji **SELECT-order by** . 
  To rozwiązanie zapewnia większą kontrolę nad rozmiarem i strukturą wyników. 
  Aplikacja logiki wywołuje procedurę składowaną za pomocą akcji **procedury składowanej wykonaj** SQL Server łącznika.

  Aby uzyskać szczegółowe informacje o rozwiązaniu, zobacz następujące artykuły:

  * [Podział na strony SQL na potrzeby transferu danych zbiorczych za pomocą Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY — klauzula](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać informacje techniczne na temat wyzwalaczy, akcji i limitów tego łącznika, zobacz [szczegóły](/connectors/sql/)dotyczące łącznika. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)

