---
title: Połącz z serwerem IBM MQ
description: Wysyłanie i pobieranie wiadomości za pomocą serwera platformy Azure lub lokalnego programu IBM MQ oraz Azure Logic Apps
services: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ef9e91b526055ece58ce283572deb98cff951653
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789575"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Nawiązywanie połączenia z serwerem IBM MQ z poziomu usługi Azure Logic Apps

Łącznik IBM MQ wysyła i pobiera komunikaty przechowywane na serwerze IBM MQ lokalnie lub na platformie Azure. Ten łącznik zawiera klienta Microsoft MQ, który komunikuje się ze zdalnym serwerem IBM MQ w sieci TCP/IP. Ten artykuł zawiera Przewodnik początkowy dotyczący używania łącznika MQ. Możesz rozpocząć od przejrzenia pojedynczej wiadomości w kolejce, a następnie wypróbowania innych akcji.

Łącznik IBM MQ zawiera te akcje, ale nie zapewnia żadnych wyzwalaczy:

- Przeglądaj pojedynczy komunikat bez usuwania komunikatu z serwera IBM MQ
- Przeglądanie partii komunikatów bez usuwania komunikatów z serwera IBM MQ
- Odbierz pojedynczy komunikat i Usuń komunikat z serwera IBM MQ
- Odbieranie partii komunikatów i usuwanie komunikatów z serwera IBM MQ
- Wyślij pojedynczy komunikat do serwera IBM MQ

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli używasz lokalnego serwera MQ, [Zainstaluj lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) na serwerze w sieci. Na serwerze, na którym jest zainstalowana lokalna Brama danych, musi być także zainstalowana .NET Framework 4,6 dla łącznika MQ. Należy również utworzyć zasób na platformie Azure dla lokalnej bramy danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia z bramą danych](../logic-apps/logic-apps-gateway-connection.md).

  Jeśli jednak serwer MQ jest publicznie dostępny lub dostępny na platformie Azure, nie trzeba używać bramy Data Gateway.

* Oficjalnie obsługiwane wersje oprogramowania IBM WebSphere MQ:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0

* Aplikacja logiki, do której ma zostać dodana akcja MQ. Ta aplikacja logiki musi używać tej samej lokalizacji co lokalne połączenie bramy danych i musi już mieć wyzwalacz, który uruchamia przepływ pracy. 

  Łącznik MQ nie ma żadnych wyzwalaczy, więc musisz najpierw dodać wyzwalacz do aplikacji logiki. Można na przykład użyć wyzwalacza cykl. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, Wypróbuj ten [Przewodnik Szybki Start, aby utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Przeglądaj pojedynczy komunikat

1. W aplikacji logiki pod wyzwalaczem lub inną akcją wybierz pozycję **nowy krok**. 

1. W polu wyszukiwania wpisz ciąg "MQ" i wybierz tę akcję: **Przeglądaj wiadomość**

   ![Przeglądaj wiadomość](media/connectors-create-api-mq/Browse_message.png)

1. Jeśli nie masz istniejącego połączenia MQ, Utwórz połączenie:  

   1. W akcji wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**.
   
   1. Wprowadź właściwości serwera MQ.  

      W przypadku **serwera**wpisz nazwę serwera MQ lub wprowadź adres IP, po którym następuje dwukropek i numer portu.
    
   1. Otwórz listę **bram** , która zawiera wszystkie skonfigurowane wcześniej połączenia bramy. Wybierz bramę.
    
   1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 
   
      Twoje połączenie wygląda podobnie do tego przykładu:

      ![Właściwości połączenia](media/connectors-create-api-mq/Connection_Properties.png)

1. Skonfiguruj właściwości akcji:

   * **Kolejka**: Określ kolejkę, która różni się od połączenia.

   * **MessageID**, **identyfikatorem** **grupy**i innymi właściwościami: Przeglądaj w poszukiwaniu wiadomości na podstawie różnych właściwości komunikatu MQ

   * **IncludeInfo**: Określ **wartość true** , aby uwzględnić dodatkowe informacje o komunikatach w danych wyjściowych. Lub Określ **wartość false** , aby nie zawierać dodatkowych informacji o komunikatach w danych wyjściowych.

   * **Limit czasu**: wprowadź wartość, aby określić, jak długo czekać na nadejście wiadomości w pustej kolejce. Jeśli nic nie zostanie wprowadzone, zostanie pobrany pierwszy komunikat w kolejce i nie ma czasu oczekiwania na wyświetlenie komunikatu.

     ![Przeglądaj właściwości wiadomości](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Zapisz** zmiany, a następnie **Uruchom** aplikację logiki.

   ![Zapisz i uruchom](media/connectors-create-api-mq/Save_Run.png)

   Po zakończeniu przebiegu są wyświetlane kroki z przebiegu i można przejrzeć dane wyjściowe.

1. Aby przejrzeć szczegóły dla każdego kroku, zaznacz zielony znacznik wyboru. Aby przejrzeć więcej informacji na temat danych wyjściowych, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   ![Przeglądaj dane wyjściowe wiadomości](media/connectors-create-api-mq/Browse_message_output.png)  

   Oto przykładowe nieprzetworzone dane wyjściowe:

   ![Przeglądaj nieprzetworzone dane wyjściowe wiadomości](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Jeśli ustawisz wartość **IncludeInfo** na true, wyświetlane są następujące dane wyjściowe:

   ![Przeglądanie informacji dotyczących wiadomości](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Przeglądaj wiele komunikatów

Akcja **Przeglądaj wiadomości** zawiera opcję **BatchSize** , aby wskazać, ile komunikatów należy zwrócić z kolejki.  Jeśli **BatchSize** nie ma wpisu, zwracane są wszystkie komunikaty. Zwrócone dane wyjściowe to tablica komunikatów.

1. Po dodaniu akcji **Przeglądaj wiadomości** domyślnie wybrane jest pierwsze skonfigurowane połączenie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Lub wybierz inne połączenie.

1. Po zakończeniu przebiegu aplikacji logiki poniżej przedstawiono przykładowe dane wyjściowe akcji **Przeglądaj komunikaty** :

   ![Przeglądaj komunikaty wyjściowe](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Odbierz pojedynczy komunikat

Akcja **Odbierz wiadomość** zawiera te same dane wejściowe i wyjściowe co Akcja **Przeglądaj wiadomość** . Gdy jest używany **komunikat Receive**, komunikat jest usuwany z kolejki.

## <a name="receive-multiple-messages"></a>Odbieranie wielu komunikatów

Akcja **Odbierz komunikaty** ma te same dane wejściowe i wyjściowe co Akcja **Przeglądaj wiadomości** . W przypadku korzystania z **komunikatów Receive**komunikaty są usuwane z kolejki.

Jeśli w kolejce nie ma żadnych komunikatów podczas przeglądania lub odbierania, ten krok zakończy się niepowodzeniem z tymi danymi wyjściowymi:  

![MQ, błąd braku komunikatu](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Wyślij wiadomość

Po dodaniu akcji **Wyślij wiadomości** domyślnie wybrane jest pierwsze skonfigurowane połączenie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Lub wybierz inne połączenie.

1. Wybierz prawidłowy typ komunikatu: **datagram**, **odpowiedź**lub **żądanie**  

   ![Wysyłanie komunikatów props](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Gdy aplikacja logiki zakończy działanie, Oto przykładowe dane wyjściowe akcji **Wyślij wiadomość** :

   ![Wysyłanie danych wyjściowych komunikatów](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat działań i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/mq/)łącznika.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
