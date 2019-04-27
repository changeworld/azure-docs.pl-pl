---
title: Połączyć się z serwerem MQ — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłanie i pobieranie wiadomości dzięki platformie Azure lub na lokalnym serwerze MQ i Azure Logic Apps
author: valrobb
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e6ae5cb0afd75a1e87fe4d4d0cf307abab5a02a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688831"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Połącz z serwerem IBM MQ z aplikacji logiki przy użyciu łącznika MQ

Microsoft Connector dla programu MQ wysyła i pobiera wiadomości przechowywanych w MQ serwera lokalnego lub na platformie Azure. Ten łącznik obejmuje klienta MQ firmy Microsoft, który komunikuje się ze zdalnym serwerem IBM MQ w sieci TCP/IP. Ten dokument jest wprowadzenie — przewodnik do korzystania z łącznika MQ. Zaleca się, że możesz rozpocząć przeglądanie pojedynczej wiadomości w kolejce, a następnie wypróbować inne akcje.

Łącznik MQ obejmuje następujące czynności. Nie ma żadnych wyzwalaczy.

- Przejdź jedną wiadomość bez usuwania komunikatu z serwerem IBM MQ
- Przeglądaj partię komunikatów bez usuwania komunikatów z serwerem IBM MQ
- Pojedynczy komunikat i usunięcia jej z serwerem IBM MQ
- Odbieranie partię komunikatów i usuwanie wiadomości z serwerem IBM MQ
- Wyślij wiadomość jednym serwerem IBM MQ

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli za pomocą lokalnego serwera MQ [zainstalować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) na serwerze w sieci. Jeśli serwer MQ jest publicznie dostępne lub dostępne w ramach platformy Azure, brama data gateway jest nie używane lub wymagane.

    > [!NOTE]
    > Serwerze, na którym jest zainstalowany program On-Premises Data Gateway musi mieć również zainstalowane dla łącznika MQ do funkcji programu .NET Framework 4.6.

* Tworzenie zasobu platformy Azure dla lokalnej bramy danych — [Konfigurowanie połączenia bramy danych](../logic-apps/logic-apps-gateway-connection.md).

* Oficjalnie obsługiwane wersje programu IBM WebSphere MQ:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. W **Azure start tablicy**, wybierz opcję **+** (znak plus), **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak MQTestApp, **subskrypcji**, **grupy zasobów**, i **lokalizacji** (Użyj lokalizacji gdzie lokalne Połączenie bramy danych jest skonfigurowane). Wybierz **Przypnij do pulpitu nawigacyjnego**i wybierz **Utwórz**.  
![Tworzenie aplikacji logiki](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Dodaj wyzwalacz

> [!NOTE]
> Łącznik MQ nie ma żadnych wyzwalaczy. Tak, użyj innego wyzwalacza, aby uruchomić aplikację logiki, takich jak **cyklu** wyzwalacza.

1. **Projektant aplikacji logiki** otworzy, wybierz opcję **cyklu** na liście typowe wyzwalacze.
2. Wybierz **Edytuj** w ramach wyzwalacza.
3. Ustaw **częstotliwość** do **dzień**i ustaw **interwał** do **7**.

## <a name="browse-a-single-message"></a>Przeglądaj pojedynczej wiadomości
1. Wybierz **+ nowy krok**i wybierz **Dodaj akcję**.
2. W polu wyszukiwania wpisz `mq`, a następnie wybierz pozycję **MQ — Przeglądanie komunikatów**.  
![Przeglądanie komunikatów](media/connectors-create-api-mq/Browse_message.png)

3. Jeśli nie ma istniejące połączenie z programem MQ, następnie utwórz połączenie:  

    1. Wybierz **Połącz za pośrednictwem lokalnej bramy danych**, a następnie wprowadź właściwości serwera MQ.  
    Aby uzyskać **serwera**, wprowadź nazwę serwera MQ lub wprowadź adres IP, w którym następuje dwukropek i numer portu.
    2. **Bramy** liście rozwijanej są wyświetlane wszelkie istniejące połączenia bramy, które zostały skonfigurowane. Wybierz bramy.
    3. Po zakończeniu wybierz pozycję **Utwórz**. Połączenie wygląda podobnie do następującego:  
    ![Właściwości połączenia](media/connectors-create-api-mq/Connection_Properties.png)

4. We właściwościach akcji można wykonywać następujące czynności:  

    * Użyj **kolejki** właściwość, aby uzyskać dostęp do nazwy kolejki inną niż zdefiniowana w połączeniu
    * Użyj **MessageId**, **CorrelationId**, **GroupId**i inne właściwości, aby przeglądać w poszukiwaniu komunikatów, na podstawie różnych właściwości komunikatu MQ
    * Ustaw **IncludeInfo** do **True** zawierać komunikat dodatkowe informacje w danych wyjściowych. Lub ustaw go na **False** wykluczającą komunikat dodatkowe informacje w danych wyjściowych.
    * Wprowadź **limitu czasu** wartość do określenia, jak długo czekać na dotrze do pustej kolejki wiadomości. Jeśli nic nie zostanie wprowadzony, są pobierane do pierwszej wiadomości w kolejce i ma nie czas oczekiwania na komunikat wyświetlany.  
    ![Przeglądaj właściwości komunikatu](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Zapisz** zmiany, a następnie **Uruchom** aplikację logiki:  
![Zapisz i uruchom](media/connectors-create-api-mq/Save_Run.png)

6. Po kilku sekundach kroki uruchomienia są wyświetlane i można przyjrzeć się dane wyjściowe. Zaznacz zielony znacznik wyboru, aby wyświetlić szczegóły każdego kroku. Wybierz **Zobacz nieprzetworzone dane wyjściowe** dodatkowe szczegółowe informacje w danych wyjściowych.  
![Przeglądaj dane wyjściowe komunikatu](media/connectors-create-api-mq/Browse_message_output.png)  

    Nieprzetworzone dane wyjściowe:  
    ![Przeglądaj nieprzetworzone dane wyjściowe komunikatu](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Gdy **IncludeInfo** opcja jest ustawiona na wartość true, zostaną wyświetlone następujące dane wyjściowe:  
![Przeglądaj komunikat obejmować informacje](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Przeglądaj wiele wiadomości
**Przeglądanie komunikatów** czynność obejmuje **BatchSize** opcję, aby określić, ile komunikatów ma zostać zwrócony z kolejki.  Jeśli **BatchSize** zawiera żadnego wpisu zwracane są wszystkie komunikaty. Zwrócone dane wyjściowe są tablicą wiadomości.

1. Podczas dodawania **Przeglądanie komunikatów** akcji, pierwszego połączenia, który jest skonfigurowany jest domyślnie zaznaczone. Wybierz **Zmień połączenie** Utwórz nowe połączenie lub wybierz inne połączenie.

2. Dane wyjściowe Przeglądanie komunikatów pokazuje:  
![Przeglądaj dane wyjściowe komunikaty](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Pojedynczy komunikat o błędzie
**Odbieranie wiadomości** akcja ma tych samych wejściach i dane wyjściowe jako **przeglądania wiadomości** akcji. Korzystając z **odbieranie wiadomości**, komunikat jest usuwany z kolejki.

## <a name="receive-multiple-messages"></a>Odbierać wiele wiadomości
**Odbierać komunikaty** akcja ma tych samych wejściach i dane wyjściowe jako **Przeglądanie komunikatów** akcji. Korzystając z **odbierać komunikaty**, komunikaty są usuwane z kolejki.

Jeśli brak komunikatów w kolejce w trakcie przeglądania lub receive, ten krok kończy się niepowodzeniem z następujące dane wyjściowe:  
![Błąd wiadomości nie MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Wysyłanie komunikatu
1. Podczas dodawania **Wyślij wiadomość** akcji, pierwszego połączenia, który jest skonfigurowany jest domyślnie zaznaczone. Wybierz **Zmień połączenie** Utwórz nowe połączenie lub wybierz inne połączenie. Prawidłowe **typy komunikatów** są **Datagram**, **odpowiedzi**, lub **żądania**.  
![Wyślij komunikat właściwości](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Dane wyjściowe Wyślij wiadomość wygląda podobnie do poniższego:  
![Wysłać dane wyjściowe Msg](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/mq/).

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zapoznaj się z innych dostępnych łączników w usłudze Logic Apps w naszym [listy interfejsów API](apis-list.md).
