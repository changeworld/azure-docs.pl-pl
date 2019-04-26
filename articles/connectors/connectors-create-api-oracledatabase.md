---
title: Nawiązać połączenie z bazą danych Oracle — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wstaw i zarządzanie nimi przy użyciu interfejsów API REST usługi bazy danych Oracle Database i Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 06f65aef203b4f0d765f21b9d17b90081de85c94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453617"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Rozpoczynanie pracy z łącznikiem usługi bazy danych Oracle

Za pomocą łącznika bazy danych Oracle Database, możesz utworzyć organizacji przepływów pracy korzystających z danych w istniejącej bazy danych. Ten łącznik może nawiązać połączenia bazy danych Oracle w środowisku lokalnym lub maszynie wirtualnej platformy Azure z bazą danych Oracle zainstalowane. Za pomocą tego łącznika możesz wykonywać następujące czynności:

* Tworzenie przepływu pracy przez dodanie nowego klienta do bazy danych klientów, lub zaktualizowanie zamówienia w bazie danych zamówień.
* Umożliwia akcji Pobierz wiersz danych, wstawić nowy wiersz, a nawet usuwać. Na przykład gdy rekord zostanie utworzony w usłudze Dynamics CRM Online (wyzwalacz), następnie Wstaw wiersz w bazie danych Oracle (akcję). 

W tym artykule przedstawiono sposób korzystania z łącznika bazy danych Oracle Database w aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Obsługiwane wersje programu Oracle: 
    * Oracle 9 lub nowszy
    * Oprogramowanie klienckie Oracle 8.1.7 lub nowszy

* Zainstaluj lokalną bramę danych. [Łączenie z danymi w środowisku lokalnym z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md) zawiera listę czynności. Brama jest wymagany do nawiązania połączenia z bazą Oracle w środowisku lokalnym lub Maszynie wirtualnej platformy Azure z bazą danych Oracle zainstalowany. 

    > [!NOTE]
    > Lokalna brama danych działa jak most i zapewnia bezpieczny transfer danych między danymi lokalnymi (danymi, które nie znajduje się w chmurze) i aplikacji usługi logic apps. Tej samej bramy może służyć za pomocą wielu usług i wielu źródeł danych. Tak może wystarczy raz zainstalować bramę.

* Instalowanie klienta Oracle na maszynie zainstalowano lokalnej bramy danych. Pamiętaj zainstalować 64-bitowego dostawcę danych programu Oracle dla platformy .NET od firmy Oracle:  

  [64-bitowy program ODAC 12c w wersji 4 (12.1.0.2.4) dla Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Jeśli klient Oracle nie jest zainstalowany, wystąpi błąd podczas próby utworzenia lub używania połączenia. Zobacz typowych błędów, w tym artykule.


## <a name="add-the-connector"></a>Dodaj łącznik

> [!IMPORTANT]
> Ten łącznik nie ma żadnych wyzwalaczy. Ma on tylko akcje. Dlatego podczas tworzenia aplikacji logiki dodanie kolejnego wyzwalacza, aby uruchomić aplikację logiki, takich jak **harmonogram — cyklicznie**, lub **żądanie / odpowiedź — odpowiedź**. 

1. W [witryny Azure portal](https://portal.azure.com), tworzenie pustej aplikacji logiki.

2. Podczas uruchamiania aplikacji logiki wybierz pozycję **żądanie / odpowiedź — żądanie** wyzwalacza: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Wybierz pozycję **Zapisz**. Po zapisaniu adresu URL żądania jest generowany automatycznie. 

4. Wybierz **nowy krok**i wybierz **Dodaj akcję**. Wpisz `oracle` Aby wyświetlić dostępne akcje: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Jest to również najszybszy sposób, aby zobaczyć, wyzwalacze i Akcje dostępne dla wszystkich łącznikach. Wpisz część nazwy łącznika, takich jak `oracle`. Projektant wyświetla wszelkie wyzwalacze i akcje. 

5. Wybierz jedną z akcji, takich jak **bazy danych Oracle — Pobierz wiersz**. Wybierz **Połącz za pośrednictwem lokalnej bramy danych**. Wprowadź nazwę serwera bazy danych Oracle, metody uwierzytelniania, nazwa użytkownika, hasło i wybierz bramę:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po nawiązaniu połączenia wybierz tabelę z listy, a następnie wprowadź identyfikator wiersza do tabeli. Musisz wiedzieć, identyfikator tabeli. Jeśli nie znasz, skontaktuj się z administratorem bazy danych Oracle i pobierać dane wyjściowe z `select * from yourTableName`. Zapewnia informacje użytkownika, których potrzebujesz, aby kontynuować.

    W poniższym przykładzie dane zadania zostały zwrócone z bazy danych zarządzania zasobami ludzkimi: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. W następnym kroku można użyć dowolnego z łączników programu do utworzenia przepływu pracy. Jeśli chcesz przetestować pobieranie danych z bazy danych Oracle, następnie wyślij do siebie wiadomość e-mail przy użyciu danych Oracle przy użyciu jednej z łączników wysyłania wiadomości e-mail, takie usługi Office 365 lub Gmail. Umożliwia tworzenie dynamicznych tokenów z tabeli bazy danych Oracle `Subject` i `Body` wiadomości e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Zapisz** aplikację logiki, a następnie wybierz **Uruchom**. Zamknij projektanta i spójrz na historii przebiegów stanu. Jeśli nie powiedzie się, wybierz wiersz dotyczący wiadomości nie powiodło się. Zostanie otwarty projektant i pokazuje, które krok nie powiodło się, a także zawiera informacje o błędzie. Jeśli się powiedzie, powinien otrzymać wiadomość e-mail z informacjami, którą dodałeś.


### <a name="workflow-ideas"></a>Pomysły przepływu pracy

* Chcesz monitorować hasztag #oracle i umieścić tweety w bazie danych, dzięki czemu można można tworzyć zapytania i używane w innych aplikacjach. W aplikacji logiki Dodaj `Twitter - When a new tweet is posted` wyzwalacza, a następnie wprowadź **#oracle** hasztagiem. Następnie należy dodać `Oracle Database - Insert row` akcji i Wybieranie tabeli:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Komunikaty są wysyłane do kolejki usługi Service Bus. Chcesz uzyskać te komunikaty i umieść je w bazie danych. W aplikacji logiki Dodaj `Service Bus - when a message is received in a queue` wyzwalacza, a następnie wybierz kolejkę. Następnie należy dodać `Oracle Database - Insert row` akcji i Wybieranie tabeli:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Typowe błędy

#### <a name="error-cannot-reach-the-gateway"></a>**Błąd**: Nie można uzyskać dostępu do bramy

**Przyczyna**: Lokalna brama danych nie jest w stanie połączyć się z chmury. 

**Środki zaradcze**: Upewnij się, że brama jest uruchomiona na maszynie lokalnej, na którym został zainstalowany, a może nawiązać połączenie z Internetem.  Firma Microsoft zaleca nie instalowania bramy na komputerze, który może zostać wyłączony lub uśpiony. Można również uruchomić ponownie usługę bramy danych (PBIEgwService) w środowisku lokalnym.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Błąd**: Używany dostawca jest przestarzały: "Element System.Data.OracleClient wymaga oprogramowania Oracle w wersji oprogramowania 8.1.7 lub nowszej.". Zobacz [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) zainstalować oficjalnego dostawcę.

**Przyczyna**: Nie zainstalowano zestawu SDK klienta Oracle na maszynie, na którym jest uruchomiona brama danych lokalnych.  

**Rozwiązanie**: Pobierz i zainstaluj zestaw SDK klienta Oracle na tym samym komputerze co lokalna brama danych.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Błąd**: Tabela "[Tablename]" nie definiuje żadnych kolumn kluczy

**Przyczyna**: Tabela nie ma żadnego klucza podstawowego.  

**Rozwiązanie**: Łącznik bazy danych programu Oracle wymaga służyć tabelę z kolumną kluczy podstawowych.

#### <a name="currently-not-supported"></a>Obecnie nieobsługiwane

* Widoki 
* Każda tabela za pomocą kluczy złożonych
* Zagnieżdżone typy obiektów w tabelach
 
## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/oracle/). 

## <a name="get-some-help"></a>Uzyskaj pomoc

[Forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) jest doskonałym miejscem, aby zadawać pytania, odpowiadać na pytania i zobacz, co robią inni użytkownicy Logic Apps. 

Możesz pomóc zwiększyć Logic Apps i łączniki do głosowania i przesyłanie pomysłów na [ https://aka.ms/logicapps-wish ](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)i Poznaj dostępnych łączników w usłudze Logic Apps w [listy interfejsów API](apis-list.md).
