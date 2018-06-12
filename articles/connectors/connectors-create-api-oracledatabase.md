---
title: Nawiązać połączenia z bazą danych Oracle — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Wstaw rekordy i zarządzać nimi z interfejsów API REST bazy danych Oracle i usługi Azure Logic Apps
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
ms.openlocfilehash: 8e83a246c815a01b417f7658535906c396bf5996
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296023"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Rozpoczynanie pracy z bazą danych Oracle łącznika

Za pomocą łącznika bazą danych Oracle, możesz utworzyć organizacyjnej przepływów pracy korzystających z danych w istniejącej bazie danych. Ten łącznik można nawiązać połączenia z lokalną bazą danych Oracle lub maszyny wirtualnej platformy Azure z bazą danych Oracle zainstalowane. Ten łącznik można:

* Tworzenie przepływu pracy przez dodanie nowego klienta do bazy danych klientów lub aktualizowania zamówienia w bazie danych zamówienia.
* Użyj akcji, aby pobrać wiersz danych, wstawienia nowego wiersza, a nawet usuwać. Na przykład gdy zostaje utworzony rekord w Dynamics CRM Online (wyzwalacz), następnie wstawienia wiersza w bazie danych programu Oracle (działanie). 

W tym artykule przedstawiono sposób korzystania z łącznika danych Oracle w aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Obsługiwane wersje programu Oracle: 
    * Oracle 9 lub nowszy
    * Oprogramowania klienta Oracle 8.1.7 lub nowszej

* Instalowanie bramy danych lokalnych. [Połącz się z lokalnymi danymi z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md) zawiera listę czynności. Brama jest wymagany do nawiązania połączenia z lokalną bazą danych Oracle lub zainstalowany na maszynie Wirtualnej platformy Azure z bazy danych programu Oracle. 

    > [!NOTE]
    > Brama lokalna danych działa jako mostka i udostępnia bezpiecznego transferu danych między lokalnymi danych (który nie jest w chmurze) a aplikacje logiki. Tę samą bramę można z wielu usług i wiele źródeł danych. Tak może wystarczy raz zainstaluj bramę.

* Zainstaluj klienta Oracle na komputerze, na którym zainstalowano bramę danych na lokalnym. Należy zainstalować dostawcę danych Oracle 64-bitowego dla platformy .NET z bazy danych Oracle:  

  [64-bitowych ODAC 12c w wersji 4 (12.1.0.2.4) dla systemu Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Jeśli nie zainstalowano klienta programu Oracle, wystąpi błąd podczas próby utworzenia lub używania połączenia. Zobacz typowe błędy w tym artykule.


## <a name="add-the-connector"></a>Dodawanie łącznika

> [!IMPORTANT]
> Ten łącznik nie ma żadnych wyzwalaczy. Ma ona tylko akcje. Dlatego podczas tworzenia aplikacji logiki, dodać innego trigger, aby uruchomić aplikację logiki, takich jak **harmonogram - cyklu**, lub **żądania / odpowiedzi - odpowiedzi**. 

1. W [portalu Azure](https://portal.azure.com), tworzenie aplikacji logiki puste.

2. Na początku aplikację logiki, wybierz **żądanie / odpowiedź — żądanie** wyzwalacz: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Wybierz pozycję **Zapisz**. Po zapisaniu, adres URL żądania jest generowana automatycznie. 

4. Wybierz **nowy krok**i wybierz **Dodaj akcję**. Wpisz `oracle` Aby wyświetlić dostępne akcje: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Jest to również najszybszym sposobem na wyświetlenie wyzwalacze i Akcje dostępne dla wszystkich łączników. Wpisz część nazwy łącznika, takich jak `oracle`. Projektant wyświetla wszelkie wyzwalacze i działaniami. 

5. Wybierz jedną z akcji, takich jak **bazą danych Oracle — wiersz Get**. Wybierz **Połącz za pośrednictwem bramy danych lokalnych**. Wprowadź nazwę serwera Oracle, metody uwierzytelniania, nazwę użytkownika, hasło, a następnie wybierz bramy:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po nawiązaniu połączenia, wybierz tabelę z listy, a następnie wprowadź identyfikator wiersza do tabeli. Musisz wiedzieć identyfikator tabeli. Jeśli nie znasz, skontaktuj się z administratorem bazy danych Oracle i pobrać dane wyjściowe z `select * from yourTableName`. Dzięki temu można zidentyfikować informacje, które musisz wykonać procedurę.

    W poniższym przykładzie danych zadania zostały zwrócone z bazy danych zasobów ludzkich: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. W następnym kroku można użyć dowolnej z innych łączników do tworzenia przepływu pracy. Jeśli chcesz przetestować pobieranie danych z bazy danych Oracle, następnie wyślij do siebie wiadomość e-mail z danych Oracle przy użyciu jednej z łączników wysyłania wiadomości e-mail, takie usługi Office 365 lub Gmail. Umożliwia tworzenie dynamicznych tokenów z tabeli programu Oracle `Subject` i `Body` Twojego adresu e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Zapisz** aplikacji logiki, a następnie wybierz **Uruchom**. Zamknij projektanta i przyjrzyj się Historia uruchomień stanu. Jeśli nie powiedzie się, zaznacz wiersz wiadomości nie powiodło się. Otwiera projektanta i pokazuje użytkownik, który krok nie powiodło się, a także zawiera informacje o błędzie. Zakończy się pomyślnie, powinien otrzymywać wiadomość e-mail z informacjami o dodane.


### <a name="workflow-ideas"></a>Koncepcje przepływu pracy

* Chcesz monitorować hasztagiem #oracle i umieścić tweetów w bazie danych, dzięki czemu może być zbadać i używane w innych aplikacjach. W aplikacji logiki, Dodaj `Twitter - When a new tweet is posted` wyzwalania, a następnie wprowadź **#oracle** hasztagiem. Następnie należy dodać `Oracle Database - Insert row` akcji i wybierz tabelę:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Komunikaty są wysyłane do kolejki usługi Service Bus. Chcesz uzyskać te komunikaty i umieść je w bazie danych. W aplikacji logiki, Dodaj `Service Bus - when a message is received in a queue` wyzwalania, a następnie wybierz kolejki. Następnie należy dodać `Oracle Database - Insert row` akcji i wybierz tabelę:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Typowe błędy

#### <a name="error-cannot-reach-the-gateway"></a>**Błąd**: nie można nawiązać połączenia z bramą

**Przyczyna**: Brama danych lokalnych nie jest w stanie nawiązać połączenia z chmurą. 

**Środki zaradcze**: Upewnij się, że brama jest uruchomiona na lokalnym komputerze, na którym został zainstalowany i czy można go połączyć z Internetem.  Firma Microsoft zaleca, nie zainstalowano bramę na komputerze, który może zostać wyłączone lub uśpienia. Można również uruchomić ponownie usługę bramy danych lokalnych (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Błąd**: używany dostawca jest przestarzały: "element System.Data.OracleClient wymaga Oracle oprogramowania klienta w wersji version 8.1.7 lub nowszej.". Zobacz [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) Aby zainstalować oficjalnego dostawcę.

**Przyczyna**: zestaw SDK nie jest zainstalowany na komputerze, na którym jest uruchomiona brama lokalna danych klienta Oracle.  

**Rozdzielczość**: Pobierz i zainstaluj zestaw SDK klienta Oracle na tym samym komputerze co brama danych lokalnych.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Błąd**: Tabela "[Nazwa_tabeli]" nie definiuje żadnych kolumn klucza

**Przyczyna**: tabela nie ma żadnego klucza podstawowego.  

**Rozdzielczość**: baza danych Oracle łącznika wymaga użycia tabelę z kolumną klucza podstawowego.

#### <a name="currently-not-supported"></a>Obecnie nieobsługiwane

* Widoki i procedury składowane 
* Tabeli za pomocą kluczy złożonych
* Zagnieżdżone typy obiektów w tabelach
 
## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/oracle/). 

## <a name="get-some-help"></a>Uzyskaj pomoc

[Forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) jest doskonałym miejscem, aby zadać pytania, odpowiedzi na pytania i zobacz, co robią użytkownicy innych Logic Apps. 

Można zwiększyć Logic Apps i łącznikami głosu i przesyłanie pomysłów na [ http://aka.ms/logicapps-wish ](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)i przejrzyj dostępne łączniki w aplikacjach logiki w [listy interfejsów API](apis-list.md).
