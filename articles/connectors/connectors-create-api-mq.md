---
title: Połączyć się z serwerem MQ - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłanie i pobieranie wiadomości z platformy Azure lub lokalnego MQ serwera i usługi Azure Logic Apps
author: valthom
manager: cfowler
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 0877d0e9394d1a54eae5482001bd53da0e1a83b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609452"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Połącz się z serwerem IBM MQ z aplikacji logiki przy użyciu łącznika programu MQ 

Microsoft Connector dla programu MQ wysyła i pobiera wiadomości przechowywanych w MQ serwera lokalnego lub na platformie Azure. Ten łącznik obejmuje klienta MQ firmy Microsoft, który komunikuje się ze zdalnym serwerem IBM MQ w sieci TCP/IP. Ten dokument jest starter przewodnik dotyczący korzystania z łącznika MQ. Zalecamy rozpocząć od przeglądanie pojedynczej wiadomości w kolejce, a następnie spróbuj innych działań.    

Łącznik MQ obejmuje następujące czynności. Nie ma żadnych wyzwalaczy.

-   Przeglądaj pojedynczą wiadomość bez usuwania komunikatu z serwera IBM MQ
-   Przeglądaj komunikaty zbiorczo, bez usuwania komunikatów z serwera IBM MQ
-   Pojedynczy komunikat o błędzie i Usuń wiadomości z serwera IBM MQ
-   Odbieranie partię komunikatów i usuwanie wiadomości z serwera IBM MQ
-   Wyślij wiadomość jednym serwerem IBM MQ 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli za pomocą lokalnego serwera MQ [instalowanie bramy danych lokalnych](../logic-apps/logic-apps-gateway-install.md) na serwerze w sieci. Jeśli serwer MQ jest publicznie dostępna, albo dostępna w systemie Azure, brama danych jest nie używane lub wymagane.

    > [!NOTE]
    > Na serwerze, na którym jest zainstalowana brama danych lokalnego również musi być .net Framework 4.6 zainstalowany łącznik MQ do działania.

* Tworzenie zasobu platformy Azure do bramy danych lokalnych - [Konfigurowanie połączenia bramy danych](../logic-apps/logic-apps-gateway-connection.md).

* Oficjalnie obsługiwane wersje programu IBM WebSphere MQ:
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**. 
2. Wprowadź **nazwa**, takich jak MQTestApp, **subskrypcji**, **grupy zasobów**, i **lokalizacji** (Użyj lokalizacji, w którym jest skonfigurowane połączenie bramy danych lokalnych). Wybierz **Przypnij do pulpitu nawigacyjnego**i wybierz **Utwórz**.  
![Tworzenie aplikacji logiki](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Dodaj wyzwalacz

> [!NOTE]
> Łącznik programu MQ nie ma żadnych wyzwalaczy. Tak, aby uruchomić aplikację logiki, takich jak jest użycie innego wyzwalacza **cyklu** wyzwalacza. 

1. **Projektanta aplikacji logiki** zostanie otwarta, wybierz opcję **cyklu** na liście typowych wyzwalaczy.
2. Wybierz **Edytuj** w ramach wyzwalacza cyklu. 
3. Ustaw **częstotliwość** do **dzień**i ustaw **interwał** do **7**. 

## <a name="browse-a-single-message"></a>Przeglądaj w pojedynczym komunikacie
1. Wybierz **+ nowy krok**i wybierz **Dodaj akcję**.
2. W polu wyszukiwania wpisz `mq`, a następnie wybierz **MQ - przeglądania wiadomości**.  
![Przeglądaj wiadomości](media/connectors-create-api-mq/Browse_message.png)

3. Jeśli nie ma istniejącego połączenia MQ, następnie należy utworzyć połączenie:  

    1. Wybierz **Połącz za pośrednictwem bramy danych lokalnych**, a następnie wprowadź właściwości serwera MQ.  
    Aby uzyskać **serwera**, wprowadź nazwę serwera MQ lub wprowadź adres IP, a następnie dwukropek i numer portu. 
    2. **Bramy** lista rozwijana zawiera listę istniejących połączeń bramy, które zostały skonfigurowane. Wybierz bramę.
    3. Po zakończeniu wybierz pozycję **Utwórz**. Połączenie jest podobny do następującego:   
    ![Właściwości połączenia](media/connectors-create-api-mq/Connection_Properties.png)

4. We właściwościach akcji można:  

    * Użyj **kolejki** właściwość, aby uzyskać dostęp do nazwy kolejki innego niż zdefiniowana w połączeniu
    * Użyj **MessageId**, **CorrelationId**, **GroupId**i inne właściwości, aby wyszukać komunikat na podstawie różnych właściwości komunikatu MQ
    * Ustaw **IncludeInfo** do **True** zawierać komunikat dodatkowe informacje w danych wyjściowych. Lub ustaw ją na **False** nie zawierać komunikat dodatkowe informacje w danych wyjściowych.
    * Wprowadź **limitu czasu** wartość, aby określić czas oczekiwania na wiadomość w pustej kolejce. Jeśli nic nie zostanie wprowadzona, są pobierane do pierwszej wiadomości w kolejce i nie ma żadnych czas oczekiwania na wiadomość pojawią się.  
    ![Przeglądaj właściwości wiadomości](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Zapisz** zmiany, a następnie **Uruchom** aplikację logiki:  
![Zapisz i uruchom](media/connectors-create-api-mq/Save_Run.png)

6. Po kilku sekundach przedstawiono kroki uruchomienia, a można przeglądać dane wyjściowe. Wybierz zielony znacznik wyboru, aby wyświetlić szczegóły każdego kroku. Wybierz **Zobacz nieprzetworzone dane wyjściowe** Aby wyświetlić dodatkowe informacje w danych wyjściowych.  
![Przeglądaj dane wyjściowe komunikatu](media/connectors-create-api-mq/Browse_message_output.png)  

    Nieprzetworzone dane wyjściowe:  
    ![Przeglądaj nieprzetworzone dane wyjściowe komunikatu](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Gdy **IncludeInfo** opcja jest ustawiona na wartość true, jest wyświetlany następujące dane wyjściowe:  
![Przeglądaj wiadomości zawierają informacje o](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Przeglądaj wiele komunikatów
**Przeglądaj wiadomości** akcja zawiera **BatchSize** opcję, aby określić, ile komunikatów powinny być zwracane z kolejki.  Jeśli **BatchSize** nie ma wpisu, zwracane są wszystkie komunikaty. Dane wyjściowe jest tablicą wiadomości.

1. Podczas dodawania **Przeglądaj wiadomości** akcji, pierwszego połączenia, który jest skonfigurowany jest domyślnie zaznaczona. Wybierz **zmienić połączenie** Aby utworzyć nowe połączenie, lub wybierz inne połączenie.

2. Dane wyjściowe przeglądania wiadomości pokazuje:  
![Przeglądaj wiadomości w danych wyjściowych](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Pojedynczy komunikat o błędzie
**Odbieranie wiadomości** akcji ma tego samego dane wejściowe i wyjściowe jako **przeglądania wiadomości** akcji. Korzystając z **odbieranie wiadomości**, komunikat jest usuwany z kolejki.

## <a name="receive-multiple-messages"></a>Wiele komunikaty
**Komunikaty** akcji ma tego samego dane wejściowe i wyjściowe jako **Przeglądaj wiadomości** akcji. Korzystając z **komunikaty**, komunikaty są usuwane z kolejki.

Jeśli występują żadne komunikaty w kolejce, podczas przeglądania lub receive, tego kroku nie powiedzie się z następujących danych wyjściowych:  
![Błąd wiadomości nie MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Wysyłanie komunikatu
1. Podczas dodawania **wysyła komunikat** akcji, pierwszego połączenia, który jest skonfigurowany jest domyślnie zaznaczona. Wybierz **zmienić połączenie** Aby utworzyć nowe połączenie, lub wybierz inne połączenie. Poprawne **typów komunikatów** są **Datagram**, **odpowiedzi**, lub **żądania**.  
![Wyślij Msg właściwości](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Dane wyjściowe wysyła komunikat wygląda następująco:  
![Wyślij dane wyjściowe Msg](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/mq/).

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Eksploruj dostępnych łączników w aplikacjach logiki w naszym [listy interfejsów API](apis-list.md).
