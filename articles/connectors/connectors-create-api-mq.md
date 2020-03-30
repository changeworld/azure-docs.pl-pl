---
title: Łączenie się z serwerem IBM MQ
description: Wysyłanie i pobieranie wiadomości za pomocą platformy Azure lub lokalnego serwera IBM MQ i aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650951"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Łączenie się z serwerem IBM MQ z usługi Azure Logic Apps

Łącznik IBM MQ wysyła i pobiera wiadomości przechowywane na serwerze IBM MQ lokalnie lub na platformie Azure. Ten łącznik zawiera klienta MQ firmy Microsoft, który komunikuje się ze zdalnym serwerem IBM MQ w sieci TCP/IP. W tym artykule przedstawiono przewodnik startowy dotyczący używania złącza MQ. Możesz rozpocząć od przeglądania pojedynczej wiadomości w kolejce, a następnie wypróbować inne akcje.

Złącze IBM MQ zawiera następujące działania, ale nie zapewnia wyzwalaczy:

- Przeglądanie pojedynczej wiadomości bez usuwania wiadomości z serwera IBM MQ
- Przeglądanie partii wiadomości bez usuwania wiadomości z serwera IBM MQ
- Odbieranie pojedynczej wiadomości i usuwanie wiadomości z serwera IBM MQ
- Odbieranie partii wiadomości i usuwanie wiadomości z serwera IBM MQ
- Wysyłanie pojedynczej wiadomości do serwera IBM MQ

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli używasz lokalnego serwera MQ, [zainstaluj lokalną bramę danych na](../logic-apps/logic-apps-gateway-install.md) serwerze w sieci. Serwer, na którym zainstalowana jest lokalna brama danych, musi również mieć zainstalowany program .NET Framework 4.6, aby łącznik MQ działał. Należy również utworzyć zasób na platformie Azure dla lokalnej bramy danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia bramy danych](../logic-apps/logic-apps-gateway-connection.md).

  Jeśli jednak serwer MQ jest publicznie dostępny lub dostępny na platformie Azure, nie trzeba używać bramy danych.

* Oficjalnie obsługiwane wersje IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* Aplikacja logiki, w której chcesz dodać akcję MQ. Ta aplikacja logiki musi używać tej samej lokalizacji co lokalne połączenie bramy danych i musi już mieć wyzwalacz, który uruchamia przepływ pracy. 

  Łącznik MQ nie ma żadnych wyzwalaczy, więc należy najpierw dodać wyzwalacz do aplikacji logiki. Na przykład można użyć wyzwalacza cyklu. Jeśli jesteś nowy w aplikacjach logiki, spróbuj tego [przewodnika Szybki start, aby utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Przeglądanie pojedynczej wiadomości

1. W aplikacji logiki w obszarze wyzwalacza lub innej akcji wybierz pozycję **Nowy krok**. 

1. W polu wyszukiwania wpisz "mq" i wybierz tę akcję: **Przeglądaj wiadomość**

   ![Przeglądaj wiadomość](media/connectors-create-api-mq/Browse_message.png)

1. Jeśli nie masz istniejącego połączenia MQ, utwórz połączenie:  

   1. W akcji wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**.
   
   1. Wprowadź właściwości serwera MQ.  

      W przypadku **programu Server**można wprowadzić nazwę serwera MQ lub wprowadzić adres IP, po którym następuje dwukropek i numer portu.
    
   1. Otwórz listę **bramy,** na której są wyświetlane wszystkie wcześniej skonfigurowane połączenia bramy. Wybierz bramę.
    
   1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 
   
      Twoje połączenie wygląda następująco w tym przykładzie:

      ![Właściwości połączenia](media/connectors-create-api-mq/Connection_Properties.png)

1. Konfigurowanie właściwości akcji:

   * **Kolejka**: Określ kolejkę, która różni się od połączenia.

   * **MessageId**, **CorrelationId**, **GroupId**i inne właściwości: Przeglądaj wiadomość na podstawie różnych właściwości wiadomości MQ

   * **IncludeInfo**: Określ **wartość True,** aby uwzględnić dodatkowe informacje o wiadomościach w danych wyjściowych. Lub określ **False,** aby nie dołączać dodatkowych informacji o wiadomościach w danych wyjściowych.

   * **Limit czasu:** Wprowadź wartość, aby określić, jak długo czekać na wiadomość, aby dotrzeć do pustej kolejki. Jeśli nic nie zostanie wprowadzone, pierwsza wiadomość w kolejce jest pobierana i nie ma czasu spędzonego na oczekiwanie na wyświetlenie wiadomości.

     ![Przeglądaj właściwości wiadomości](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Zapisz** zmiany, a następnie **uruchom** aplikację logiki.

   ![Zapisz i uruchom](media/connectors-create-api-mq/Save_Run.png)

   Po zakończeniu biegu są wyświetlane kroki z przebiegu i można przejrzeć dane wyjściowe.

1. Aby przejrzeć szczegóły każdego kroku, wybierz zielony znacznik wyboru. Aby przejrzeć więcej informacji o danych wyjściowych, wybierz opcję **Pokaż surowe dane wyjściowe**.

   ![Przeglądanie danych wyjściowych wiadomości](media/connectors-create-api-mq/Browse_message_output.png)  

   Oto kilka przykładowych surowych danych wyjściowych:

   ![Przeglądanie nieprzetworzonego wydruku wiadomości](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Jeśli zostanie **ustawiona wartość TrueInfo,** wyświetlane są następujące dane wyjściowe:

   ![Przeglądaj wiadomość zawiera informacje](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Przeglądanie wielu wiadomości

Akcja **Przeglądaj wiadomości** zawiera **batchSize** opcja, aby wskazać, ile wiadomości powinny być zwracane z kolejki.  Jeśli **BatchSize** nie ma wpisu, wszystkie wiadomości są zwracane. Zwrócone dane wyjściowe to tablica komunikatów.

1. Po dodaniu akcji **Przeglądaj wiadomości** najpierw skonfigurowane wcześniej połączenie jest wybierane domyślnie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Możesz też wybrać inne połączenie.

1. Po zakończeniu uruchamiania aplikacji logiki, oto kilka przykładowych danych wyjściowych z **akcji Przeglądaj wiadomości:**

   ![Przeglądanie danych wyjściowych wiadomości](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Odbieranie pojedynczej wiadomości

Akcja **Odbieraj komunikat** ma takie same dane wejściowe i wyjściowe jak akcja **Przeglądaj wiadomość.** Podczas korzystania z **receive wiadomości,** wiadomość jest usuwana z kolejki.

## <a name="receive-multiple-messages"></a>Odbieranie wielu wiadomości

Akcja **Odbieraj wiadomości** ma takie same dane wejściowe i wyjściowe jak akcja **Przeglądaj wiadomości.** Podczas korzystania **z receive wiadomości,** wiadomości są usuwane z kolejki.

Jeśli w kolejce nie ma żadnych komunikatów podczas przeglądania lub odbierania, krok kończy się niepowodzeniem z tym wyjściem:  

![MQ Brak błędu komunikatu](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Wyślij wiadomość

Po dodaniu akcji **Wyślij wiadomości,** pierwsze wcześniej skonfigurowane połączenie jest zaznaczone domyślnie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Możesz też wybrać inne połączenie.

1. Wybierz prawidłowy typ wiadomości: **Datagram,** **Odpowiedz**lub **Poproś**  

   ![Wyślij rekwizyty Msg](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Po zakończeniu działania aplikacji logiki, oto kilka przykładowych danych wyjściowych z akcji **Wyślij wiadomość:**

   ![Wyślij wyjście MSG](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/mq/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
