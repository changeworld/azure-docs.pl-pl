---
title: Łączenie się z bazą danych SQL Server lub Azure SQL
description: Automatyzuj zadania dla baz danych SQL lokalnie lub w chmurze przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789203"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatyzacja przepływów pracy dla programu SQL Server lub usługi Azure SQL Database przy użyciu aplikacji logiki Azure

W tym artykule pokazano, jak można uzyskać dostęp do danych w bazie danych SQL z wewnątrz aplikacji logiki z łącznikiem programu SQL Server. W ten sposób można zautomatyzować zadania, procesy lub przepływy pracy, które zarządzają danymi i zasobami SQL, tworząc aplikacje logiki. Łącznik programu SQL Server działa zarówno [dla lokalnego programu SQL Server,](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) jak i dla opartej na [chmurze bazy danych SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)azure.

Można tworzyć aplikacje logiki, które są uruchamiane po wyzwoleniu przez zdarzenia w bazie danych SQL lub w innych systemach, takich jak Dynamics CRM Online. Aplikacje logiki można również uzyskać, wstawić i usunąć dane wraz z uruchamianiem zapytań SQL i procedur przechowywanych. Na przykład można utworzyć aplikację logiki, która automatycznie sprawdza nowe rekordy w dynamics CRM Online, dodaje elementy do bazy danych SQL dla nowych rekordów, a następnie wysyła alerty e-mail dotyczące dodanych elementów.

Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Informacje techniczne, ograniczenia i znane problemy dotyczące łącznika dotyczą informacji technicznych, ograniczeń i znanych problemów, zobacz [stronę odwołania do łącznika programu SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Baza danych programu SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) lub baza danych SQL usługi [Azure](../sql-database/sql-database-get-started-portal.md)

  Tabele muszą mieć dane, dzięki czemu aplikacja logiki może zwracać wyniki podczas wywoływania operacji. Jeśli tworzysz usługę Azure SQL Database, można użyć przykładowych baz danych, które są uwzględniane.

* Nazwa serwera SQL, nazwa bazy danych, nazwa użytkownika i hasło. Te poświadczenia są potrzebne, aby autoryzować logikę w celu uzyskania dostępu do serwera SQL.

  * W przypadku programu SQL Server te szczegóły można znaleźć w ciągu połączenia:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * W przypadku usługi Azure SQL Database te szczegóły można znaleźć w ciągu połączenia lub w portalu Azure w obszarze właściwości bazy danych SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* [Lokalna brama danych](../logic-apps/logic-apps-gateway-install.md) zainstalowana na komputerze lokalnym i [zasób bramy danych platformy Azure utworzony w witrynie Azure portal](../logic-apps/logic-apps-gateway-connection.md) dla tych scenariuszy:

  * Aplikacje logiki nie działają w [środowisku usługi integracji (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * Aplikacje logiki *działają* w środowisku usługi integracji, ale musisz użyć uwierzytelniania systemu Windows dla połączenia programu SQL Server. W tym scenariuszu należy użyć wersji łącznika programu SQL Server innej niż ISE wraz z bramą danych, ponieważ wersja środowiska ISE nie obsługuje uwierzytelniania systemu Windows.

* Aplikacja logiki, w której potrzebny jest dostęp do bazy danych SQL. Aby uruchomić aplikację logiki z wyzwalaczem SQL, potrzebujesz [pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Dodawanie wyzwalacza SQL

W usłudze Azure Logic Apps każda aplikacja logiki musi zaczynać się od [wyzwalacza,](../logic-apps/logic-apps-overview.md#logic-app-concepts)który uruchamia się, gdy występuje określone zdarzenie lub gdy zostanie spełniony określony warunek. Za każdym razem, gdy wyzwalany jest wyzwalacz, aparat aplikacji logiki tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji logiki.

1. W witrynie Azure portal lub visual studio utwórz pustą aplikację logiki, która otwiera Projektanta aplikacji logiki. W tym przykładzie użyto witryny Azure portal.

1. Na projektancie w polu wyszukiwania wprowadź "sql server" jako filtr. Z listy wyzwalaczy wybierz odpowiedni wyzwalacz SQL.

   W tym przykładzie użyto **wyzwalacza Kiedy element jest tworzony.**

   ![Wybierz wyzwalacz "Po utworzeniu elementu"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Jeśli zostanie wyświetlony monit o utworzenie połączenia, [utwórz połączenie SQL teraz](#create-connection). Jeśli połączenie istnieje, wybierz **nazwę tabeli**.

   ![Wybierz odpowiednią tabelę](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Ustaw **interwał** i **częstotliwość** właściwości, które określają, jak często aplikacja logiki sprawdza tabeli.

   Ten wyzwalacz zwraca tylko jeden wiersz z wybranej tabeli, nic więcej. Aby wykonać inne zadania, dodaj inne akcje, które wykonują żądane zadania. Na przykład, aby wyświetlić dane w tym wierszu, można dodać inne akcje, które tworzą plik, który zawiera pola z zwróconego wiersza, a następnie wysłać alerty e-mail. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego [łącznika, zobacz stronę referencyjną łącznika](https://docs.microsoft.com/connectors/sql/).

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok automatycznie włącza i publikuje aplikację logiki na żywo na platformie Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Dodawanie akcji SQL

W usłudze Azure Logic Apps [akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy, który następuje wyzwalacz lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacza cyklu](../connectors/connectors-native-recurrence.md)i wywołuje akcję, która pobiera wiersz z bazy danych SQL.

1. W witrynie Azure portal lub visual studio otwórz aplikację logiki w Projektancie aplikacji logiki. W tym przykładzie użyto witryny Azure portal.

1. W obszarze wyzwalacza lub akcji, w której chcesz dodać akcję SQL, wybierz pozycję **Nowy krok**.

   ![Dodawanie nowego kroku do aplikacji logiki](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wpisz "sql server" jako filtr. Z listy akcji wybierz odpowiednią akcję SQL.

   W tym przykładzie użyto **get wiersz** akcji, która pobiera pojedynczy rekord.

   ![Znajdź i wybierz akcję SQL "Pobierz wiersz"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Ta akcja zwraca tylko jeden wiersz z wybranej tabeli, nic więcej. Aby wyświetlić dane w tym wierszu, można dodać inne akcje, które tworzą plik, który zawiera pola z zwróconego wiersza i przechowywać ten plik na koncie magazynu w chmurze. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego [łącznika, zobacz stronę referencyjną łącznika](https://docs.microsoft.com/connectors/sql/).

1. Jeśli zostanie wyświetlony monit o utworzenie połączenia, [utwórz połączenie SQL teraz](#create-connection). Jeśli połączenie istnieje, wybierz **nazwę tabeli**i wprowadź **identyfikator wiersza** dla odpowiedniego rekordu.

   ![Wprowadź nazwę tabeli i identyfikator wiersza](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok automatycznie włącza i publikuje aplikację logiki na żywo na platformie Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Obsługa danych zbiorczych

Czasami trzeba pracować z zestawami wyników tak duże, że łącznik nie zwraca wszystkie wyniki w tym samym czasie lub chcesz lepszą kontrolę nad rozmiarem i strukturą dla zestawów wyników. Oto kilka sposobów obsługi tak dużych zestawów wyników:

* Aby ułatwić zarządzanie wynikami jako mniejszymi zestawami, włącz podział na *strony*. Aby uzyskać więcej informacji, zobacz [Wprowadzanie zbiorczych danych, rekordów i elementów przy użyciu funkcji podziałania na strony .](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)

* Utwórz procedurę składowaną, która organizuje wyniki w odpowiedni sposób.

  Podczas uzyskiwania lub wstawiania wielu wierszy aplikacja logiki może iterować przez te wiersze przy użyciu [*pętli do do*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) tych [limitów](../logic-apps/logic-apps-limits-and-config.md). Jednak gdy aplikacja logiki musi pracować z zestawami rekordów tak duże, na przykład tysiące lub miliony wierszy, które chcesz zminimalizować koszty wynikające z wywołań do bazy danych.

  Aby uporządkować wyniki w odpowiedni sposób, można utworzyć [*procedurę składowaną,*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) która jest uruchamiana w wystąpieniu SQL i używa instrukcji **SELECT - ORDER BY.** To rozwiązanie daje większą kontrolę nad rozmiarem i strukturą wyników. Aplikacja logiki wywołuje procedurę składowaną przy użyciu akcji **wykonaj procedurę składowaną** łącznika programu SQL Server.

  Aby uzyskać więcej informacji na temat rozwiązania, zobacz następujące artykuły:

  * [Udostępnianie danych SQL do zbiorczego przesyłania danych za pomocą aplikacji logiki](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY Clause](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Aby uzyskać informacje techniczne dotyczące wyzwalaczy, akcji i limitów tego [łącznika, zobacz stronę referencyjną łącznika](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji Azure Logic Apps](../connectors/apis-list.md)
