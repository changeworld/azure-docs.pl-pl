---
title: Łączenie się z bazą danych Oracle
description: Wstawianie rekordów i zarządzanie nimi za pomocą interfejsów API ORACLE Database REST i aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789430"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Wprowadzenie do łącznika bazy danych Oracle

Korzystając z łącznika bazy danych Oracle Database, tworzysz organizacyjne przepływy pracy, które wykorzystują dane w istniejącej bazie danych. Ten łącznik może łączyć się z lokalną bazą danych Oracle Database lub maszyną wirtualną platformy Azure z zainstalowaną bazą danych Oracle Database. Za pomocą tego złącza można:

* Tworzenie przepływu pracy przez dodanie nowego klienta do bazy danych klientów lub aktualizowanie zamówienia w bazie danych zamówień.
* Użyj akcji, aby uzyskać wiersz danych, wstawić nowy wiersz, a nawet usunąć. Na przykład, gdy rekord jest tworzony w Dynamics CRM Online (wyzwalacz), a następnie wstawić wiersz w bazie danych Oracle Database (akcja). 

W tym artykule pokazano, jak używać łącznika bazy danych Oracle w aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Obsługiwane wersje oprogramowania Oracle: 
    * Oracle 9 lub nowszy
    * Oprogramowania klienta Oracle 8.1.7 lub nowsze

* Zainstaluj lokalną bramę danych. [Połącz się z danymi lokalnymi z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md) zawiera listę kroków. Brama jest wymagana do łączenia się z lokalną bazą danych Oracle Database lub maszyną wirtualną platformy Azure z zainstalowaną usługą Oracle DB. 

    > [!NOTE]
    > Lokalna brama danych działa jako mostek i zapewnia bezpieczny transfer danych między danymi lokalnymi (danymi, które nie znajdują się w chmurze) a aplikacjami logiki. Tej samej bramy można używać z wieloma usługami i wieloma źródłami danych.Tak więc może być konieczne tylko raz zainstalować bramę.

* Zainstaluj klienta Oracle na komputerze, na którym zainstalowano lokalną bramę danych.Pamiętaj, aby zainstalować 64-bitowy dostawca danych Oracle dla platformy .NET firmy Oracle:  

  [64-bitowa wersja programu ODAC 12c w wersji 4 (12.1.0.2.4) dla systemu Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Jeśli klient Oracle nie jest zainstalowany, podczas próby utworzenia lub użycia połączenia występuje błąd. Zobacz typowe błędy w tym artykule.


## <a name="add-the-connector"></a>Dodawanie łącznika

> [!IMPORTANT]
> To złącze nie ma żadnych wyzwalaczy. Ma tylko działania. Tak więc podczas tworzenia aplikacji logiki, dodać inny wyzwalacz, aby uruchomić aplikację logiki, takich jak **Harmonogram - Cykl**lub Żądanie / Odpowiedź - **Odpowiedź**. 

1. W [witrynie Azure portal](https://portal.azure.com)utwórz pustą aplikację logiki.

2. Na początku aplikacji logiki wybierz żądanie **/ odpowiedź - żądanie** wyzwalacza: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Wybierz **pozycję Zapisz**. Podczas zapisywania adres URL żądania jest generowany automatycznie. 

4. Wybierz pozycję **Nowy krok**, a następnie pozycję **Dodaj akcję**. `oracle` Wpisz, aby wyświetlić dostępne akcje: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Jest to również najszybszy sposób, aby zobaczyć wyzwalacze i akcje dostępne dla dowolnego łącznika. Wpisz część nazwy łącznika, `oracle`na przykład . Projektant wyświetla listę wszystkich wyzwalaczy i wszelkich akcji. 

5. Wybierz jedną z akcji, na przykład **Oracle Database - Get row**. Wybierz **pozycję Połącz za pośrednictwem lokalnej bramy danych**. Wprowadź nazwę serwera Oracle, metodę uwierzytelniania, nazwę użytkownika, hasło i wybierz bramę:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po nawiązaniu połączenia wybierz tabelę z listy i wprowadź identyfikator wiersza do tabeli. Musisz znać identyfikator do tabeli. Jeśli nie wiesz, skontaktuj się z administratorem bazy danych `select * from yourTableName`Oracle DB i uzyskaj dane wyjściowe. Daje to identyfikowalne informacje, które należy wykonać.

    W poniższym przykładzie dane zadania są zwracane z bazy danych zasobów ludzkich: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. W tym następnym kroku można użyć dowolnego z innych łączników do tworzenia przepływu pracy. Jeśli chcesz przetestować uzyskiwanie danych z Oracle, wyślij sobie wiadomość e-mail z danymi Oracle przy użyciu jednego z łączników wysyłania wiadomości e-mail, takiego jak Office 365 lub Gmail. Użyj tokenów dynamicznych z tabeli `Subject` `Body` Oracle, aby zbudować i wiadomości e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Zapisz** aplikację logiki, a następnie wybierz pozycję **Uruchom**. Zamknij projektanta i spójrz na historię przebiegów dla stanu. Jeśli to się nie powiedzie, wybierz wiersz wiadomości nie powiodło się. Projektant otwiera i pokazuje, który krok nie powiódł się, a także pokazuje informacje o błędzie. Jeśli to się powiedzie, powinieneś otrzymać wiadomość e-mail z dodanymi informacjami.


### <a name="workflow-ideas"></a>Pomysły na przepływ pracy

* Chcesz monitorować #oracle hashtag i umieścić tweety w bazie danych, aby mogły być wyszukiwane i używane w innych aplikacjach. W aplikacji logiki `Twitter - When a new tweet is posted` dodaj wyzwalacz i wprowadź **hashtag #oracle.** Następnie dodaj `Oracle Database - Insert row` akcję i wybierz tabelę:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Wiadomości są wysyłane do kolejki usługi Service Bus. Chcesz uzyskać te wiadomości i umieścić je w bazie danych. W aplikacji logiki `Service Bus - when a message is received in a queue` dodaj wyzwalacz i wybierz kolejkę. Następnie dodaj `Oracle Database - Insert row` akcję i wybierz tabelę:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Typowe błędy

#### <a name="error-cannot-reach-the-gateway"></a>**Błąd:** Nie można dotrzeć do bramy

**Przyczyna:** Lokalna brama danych nie może połączyć się z chmurą. 

**Środki zaradcze:** Upewnij się, że brama jest uruchomiona na komputerze lokalnym, na którym została zainstalowana, i że może łączyć się z Internetem.Nie zaleca się instalowania bramy na komputerze, który może być wyłączony lub uśpiony.Można również ponownie uruchomić lokalną usługę bramy danych (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Błąd:** Używany dostawca jest przestarzały: "System.Data.OracleClient wymaga oprogramowania klienta Oracle w wersji 8.1.7 lub nowszej.". Zobacz, [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) aby zainstalować oficjalnego dostawcę.

**Przyczyna:** Zestaw SDK klienta Oracle nie jest zainstalowany na komputerze, na którym działa lokalna brama danych.  

**Rozwiązanie:** Pobierz i zainstaluj zestaw SDK klienta Oracle na tym samym komputerze co lokalna brama danych.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Błąd**: Tabela "[Nazwa tabeli]" nie definiuje żadnych kolumn kluczowych

**Przyczyna:** Tabela nie ma żadnego klucza podstawowego.  

**Rozdzielczość:** Łącznik bazy danych Oracle wymaga użycia tabeli z kolumną klucza podstawowego.

#### <a name="currently-not-supported"></a>Obecnie nie jest obsługiwana

* Widoki 
* Dowolna tabela z kluczami kompozytowymi
* Zagnieżdżone typy obiektów w tabelach
 
## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger, a także zobacz wszelkie limity w [szczegółach łącznika](/connectors/oracle/). 

## <a name="get-some-help"></a>Uzyskaj pomoc

[Forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) to doskonałe miejsce do zadawania pytań, odpowiadania na pytania i zobacz, co robią inni użytkownicy aplikacji logiki. 

Możesz ulepszyć aplikacje logiki i łączniki, głosując i przesyłając swoje pomysły w pliku [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Następne kroki
[Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)i eksploruj dostępne łączniki na liście Aplikacji logiki na [liście interfejsów API](apis-list.md).
