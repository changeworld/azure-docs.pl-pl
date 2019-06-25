---
title: Połączyć się z serwerem z programem IBM MQ — Azure Logic Apps
description: Wysyłanie i pobieranie wiadomości z platformy Azure lub w środowisku lokalnym serwerem IBM MQ i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273127"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Połącz z serwerem IBM MQ z usługi Azure Logic Apps

Łącznik IBM MQ wysyła i pobiera wiadomości przechowywanych na serwerze z programem IBM MQ w środowisku lokalnym lub na platformie Azure. Ten łącznik obejmuje klienta MQ firmy Microsoft, który komunikuje się ze zdalnym serwerem IBM MQ w sieci TCP/IP. Ten artykuł zawiera wprowadzenie — przewodnik do korzystania z łącznika MQ. Można Rozpocznij od przejrzenia pojedynczej wiadomości w kolejce, a następnie spróbuj innych działań.

Łącznik IBM MQ te akcje obejmują, ale zapewnia żadnych wyzwalaczy:

- Przejdź jedną wiadomość bez usuwania komunikatu z serwera z programem IBM MQ
- Przeglądaj partię komunikatów bez usuwania komunikatów z serwera z programem IBM MQ
- Pojedynczy komunikat i Usuń komunikat z serwera z programem IBM MQ
- Odbieranie partię komunikatów i usuwanie wiadomości z serwera z programem IBM MQ
- Wysyłać jedną wiadomość do serwera z programem IBM MQ

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli używasz lokalnego serwera MQ [zainstalować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) na serwerze w sieci. Serwerze, na którym jest zainstalowany lokalnej bramy danych również musi mieć .NET Framework 4.6 zainstalowany łącznik MQ do pracy. Możesz również utworzyć zasób na platformie Azure dla lokalnej bramy danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia bramy danych](../logic-apps/logic-apps-gateway-connection.md).

  Jednak jeśli serwer MQ jest publicznie dostępne lub dostępne w ramach platformy Azure, nie trzeba używać bramy danych.

* Oficjalnie obsługiwane wersje programu IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* Aplikacja logiki, które chcesz dodać akcję MQ. Ta aplikacja logiki musi używać tej samej lokalizacji co połączenie bramy danych lokalnych i powinien być już wyzwalacz, który uruchamia przepływ pracy. 

  Łącznik MQ nie ma żadnych wyzwalaczy, dlatego należy dodać wyzwalacza najpierw do aplikacji logiki. Na przykład można użyć wyzwalacza. Jeśli dopiero zaczynasz pracę z usługi logic apps, skorzystaj z tej [szybkiego startu, aby utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Przeglądaj pojedynczej wiadomości

1. W aplikacji logiki, w obszarze wyzwalacza lub innej akcji, wybierz **nowy krok**. 

1. W polu wyszukiwania wpisz "mq" i wybierz następującą akcję: **Przeglądanie komunikatów**

   ![Przeglądanie komunikatów](media/connectors-create-api-mq/Browse_message.png)

1. Jeśli nie masz istniejącego połączenia MQ, należy utworzyć połączenia:  

   1. W akcji, wybierz **Połącz za pośrednictwem lokalnej bramy danych**.
   
   1. Wprowadź właściwości serwera MQ.  

      Aby uzyskać **serwera**, wprowadź nazwę serwera MQ lub wprowadź adres IP, w którym następuje dwukropek i numer portu.
    
   1. Otwórz **bramy** listę, która pokazuje wszelkie uprzednio skonfigurowane połączenia bramy. Wybierz bramy.
    
   1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 
   
      Połączenie będzie wyglądać następująco:

      ![Właściwości połączenia](media/connectors-create-api-mq/Connection_Properties.png)

1. Skonfiguruj właściwości akcji:

   * **kolejka**: Określ kolejki, która różni się od połączenia.

   * **Identyfikator komunikatu**, **CorrelationId**, **GroupId**i inne właściwości: Przeglądaj w poszukiwaniu komunikatów, na podstawie różnych właściwości komunikatu MQ

   * **IncludeInfo**: Określ **True** zawierać komunikat dodatkowe informacje w danych wyjściowych. Alternatywnie można wskazać **False** wykluczającą komunikat dodatkowe informacje w danych wyjściowych.

   * **Limit czasu**: Wprowadź wartość w celu określenia, jak długo czekać na dotrze do pustej kolejki wiadomości. Jeśli nic nie zostanie wprowadzony, są pobierane do pierwszej wiadomości w kolejce i ma nie czas oczekiwania na komunikat wyświetlany.

     ![Przeglądaj właściwości komunikatu](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Zapisz** zmiany, a następnie **Uruchom** aplikacji logiki.

   ![Zapisz i uruchom](media/connectors-create-api-mq/Save_Run.png)

   Po zakończeniu uruchom kroki opisane w wyniku uruchomienia są wyświetlane i można przejrzeć dane wyjściowe.

1. Aby poznać szczegóły dla każdego kroku, wybierz zielony znacznik wyboru. Aby przejrzeć więcej informacji na temat danych wyjściowych, wybierz **Pokaż nieprzetworzone dane wyjściowe**.

   ![Przeglądaj dane wyjściowe komunikatu](media/connectors-create-api-mq/Browse_message_output.png)  

   Poniżej przedstawiono niektóre przykładowe nieprzetworzone wyniki:

   ![Przeglądaj nieprzetworzone dane wyjściowe komunikatu](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Jeśli ustawisz **IncludeInfo** na wartość true, następujące dane wyjściowe są wyświetlane:

   ![Przeglądaj komunikat obejmować informacje](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Przeglądaj wiele wiadomości

**Przeglądanie komunikatów** czynność obejmuje **BatchSize** opcję, aby określić, ile komunikatów ma zostać zwrócony z kolejki.  Jeśli **BatchSize** zawiera żadnego wpisu zwracane są wszystkie komunikaty. Zwrócone dane wyjściowe są tablicą wiadomości.

1. Po dodaniu **Przeglądanie komunikatów** akcji, pierwszy uprzednio skonfigurowane połączenie jest domyślnie zaznaczone. Aby utworzyć nowe połączenie, wybierz **Zmień połączenie**. Lub wybierz inne połączenie.

1. Po uruchomieniu aplikacja logiki zostanie zakończone, poniżej przedstawiono niektóre przykładowe dane wyjściowe **Przeglądanie komunikatów** akcji:

   ![Przeglądaj dane wyjściowe komunikaty](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Pojedynczy komunikat

**Odbieranie wiadomości** akcja ma tych samych wejściach i dane wyjściowe jako **przeglądania wiadomości** akcji. Korzystając z **odbieranie wiadomości**, komunikat jest usuwany z kolejki.

## <a name="receive-multiple-messages"></a>Odbierać wiele wiadomości

**Odbierać komunikaty** akcja ma tych samych wejściach i dane wyjściowe jako **Przeglądanie komunikatów** akcji. Korzystając z **odbierać komunikaty**, komunikaty są usuwane z kolejki.

Jeśli brak komunikatów w kolejce w trakcie przeglądania lub receive, ten krok kończy się niepowodzeniem z tych danych wyjściowych:  

![Błąd wiadomości nie MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Wyślij wiadomość

Po dodaniu **wysyłać** akcji, pierwszy uprzednio skonfigurowane połączenie jest domyślnie zaznaczone. Aby utworzyć nowe połączenie, wybierz **Zmień połączenie**. Lub wybierz inne połączenie.

1. Wybierz typ prawidłowy komunikat: **Datagram**, **odpowiedzi**, lub **żądania**  

   ![Wyślij komunikat właściwości](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Poniżej przedstawiono niektóre przykładowe dane wyjściowe po zakończeniu aplikacja logiki **Wyślij wiadomość** akcji:

   ![Wysłać dane wyjściowe Msg](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące działań i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/mq/).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
