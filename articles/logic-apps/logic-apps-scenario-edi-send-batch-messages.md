---
title: Batch przetwarzania komunikatów EDI jako grupy lub kolekcji — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłanie komunikatów EDI wsadowo w usłudze logic apps
keywords: Przetwarzanie wsadowe partii, kodowanie zbiorcze
author: divswa
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: fb15688968cb29039fc669ed6b8685ba64df9e81
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432137"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Wyślij X12 komunikatów w partii do partnerów handlowych

W przypadkach firmami (B2B) partnerów często wymieniać wiadomości w grupach lub partii. Do wysyłania wiadomości w grupach lub partii do partnerów handlowych, można utworzyć partię przy wielu elementów, a następnie użyj X12 akcji usługi batch do przetwarzania tych elementów jako zadania wsadowego.


Partie dla X12 wiadomości, takich jak inne komunikaty, korzysta z wyzwalaczem partii i akcji. Również dla X12, partii przechodzi przez X12 Koduj kroku przed przejściem do partnera lub dowolnego miejsca docelowego. Aby uzyskać więcej informacji na temat akcji i wyzwalaczy usługi batch, zobacz [komunikaty przetwarzania wsadowego](logic-apps-batch-process-send-receive-messages.md).

W tym temacie pokazano, jak może przetwarzać X12 wiadomości jako zadaniu wsadowym, wykonując następujące czynności:
* [Utwórz aplikację logiki, która odbiera elementy i tworzy partię](#receiver). Ta aplikacja logiki "odbiornik" wykonuje następujące działania:
 
   * Określa kryteria nazwa i wersja usługi batch, aby spełnić przed zwolnieniem elementy jako zadania wsadowego.

   * Przetwarza lub koduje elementów w usłudze batch przy użyciu określonego X12 umowy lub partnera tożsamości.

* [Utwórz aplikację logiki, która wysyła elementów do przetworzenia wsadowego](#sender). Ta aplikacja logiki "sender" Określa, gdzie wysyłać elementów dla przetwarzania wsadowego, które muszą być istniejącą aplikację logiki odbiorcy.


## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przykładu, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). W przeciwnym razie możesz [skorzystać z subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/purchase-options/).

* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure

* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) zdefiniowanego na koncie integracji. Upewnij się, że każdy partner używa X12 (standardowy kod operatora alfa) kwalifikator we właściwościach partnera jako tożsamość firmy.

* [X12 umowy](logic-apps-enterprise-integration-x12.md) jest już zdefiniowany w ramach konta integracji

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Utwórz logikę aplikacji, która odbiera X12 komunikatów i tworzy partię

Przed może wysyłać komunikaty do usługi batch, należy najpierw utworzyć aplikację logiki "odbiornik" za pomocą **partii** wyzwalacza. W ten sposób można wybrać tę aplikację logiki odbiorcy podczas tworzenia aplikacji logiki nadawcy. Odbiornik, należy określić nazwę instancji, kryteria, X12 zwalniania umowy i inne ustawienia. 


1. W [witryny Azure portal](https://portal.azure.com), tworzenie aplikacji logiki o tej nazwie: "BatchX12Messages".

1. W Projektancie aplikacji logiki, Dodaj **partii** wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wprowadź "batch" jako filtr. Wybierz następujący wyzwalacz: **usługi Batch — komunikaty przetwarzania wsadowego**

   ![Dodawanie wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

1. Podaj nazwę dla usługi batch i określić kryteria zwalniania partii, na przykład:

   * **Nazwa partii**: Nazwa używana do identyfikowania usługi batch, czyli "TestBatch" w tym przykładzie.

   * **Kryteria zwalniania**: kryteriów publikowania partii, które mogą być oparte na liczba komunikatów i/lub harmonogramu.
   
     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Liczba komunikatów**: liczba komunikatów do przechowywania jako zadania wsadowego przed zwolnieniem do przetworzenia, czyli "5" w tym przykładzie.

     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Harmonogram**: Planowanie wydania usługi batch do przetwarzania, czyli "co 10 minut" w tym przykładzie.

     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


1. Dodaj kolejną akcję, która koduje zgrupowane lub partii komunikatów i tworzy X12 partii komunikatów. 

   a. Wybierz **+ nowy krok** > **Dodaj akcję**.

   b. W polu wyszukiwania wpisz "X 12 usługi batch" jako filtr, a następnie wybierz akcję dla **X12 — kodowanie zbiorcze**. X12, takie jak kodowanie connector znajduje się wielu wariantów partii kodowanie akcji. Można wybrać jedną z nich.

   ![Wybierz X12 kodowanie zbiorcze akcji](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
1. Ustaw właściwości dla akcji, który właśnie został dodany.

   * W **nazwa X12 umowy** wybierz umowę z listy rozwijanej. Jeśli lista jest pusta, upewnij się, że połączenie nie zostało utworzone na koncie integracji.

   * W **BatchName** wybierz opcję **Nazwa partii** pola z listy zawartości dynamicznej.
   
   * W **PartitionName** wybierz opcję **nazwa partycji** pola z listy zawartości dynamicznej.

   * W **elementów** wybierz opcję **partii elementów** z listy zawartości dynamicznej.

   ![Szczegóły akcji Koduj usługi Batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

1. Do celów testowych, dodawanie akcji HTTP do wysyłania wsadowej komunikat, który ma [Bin żądania usługi](https://requestbin.fullcontact.com/). 

   1. Wprowadź "HTTP" jako filtr w polu wyszukiwania. Wybierz tę akcję: **HTTP - HTTP**
    
      ![Wybieranie akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   1. Z **metoda** listy wybierz **WPIS**. Aby uzyskać **Uri** , wygenerować identyfikator URI dla pojemnika Twoje żądanie i wpisz tego identyfikatora URI. W **treści** pola, gdy zostanie otwarta lista dynamiczna, wybierz **treści** pola pod **kodowanie zbiorcze według nazwy umowy** sekcji. Jeśli nie widzisz **treści**, wybierz **Zobacz więcej** obok **kodowanie zbiorcze według nazwy umowy**.

      ![Podaj szczegóły akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

1.  Teraz, gdy utworzono aplikację logiki odbiorcy, Zapisz aplikację logiki.

    ![Zapisywanie aplikacji logiki](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Partycja obowiązuje limit 5000 wiadomości lub 80 MB. Spełnieniu tych warunków usługi batch mogą zostać zwolnione, nawet wtedy, gdy zdefiniowana przez użytkownika warunek nie jest spełniony.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Tworzenie aplikacji logiki, która wysyła X12 komunikatów do partii

Teraz należy utworzyć co najmniej jedną aplikację logiki, które wysyłają elementów do przetworzenia wsadowego zdefiniowane przez aplikację logiki odbiorcy. Dla nadawcy należy określić aplikację logiki odbiornik i nazwę instancji, zawartość komunikatu i inne ustawienia. Opcjonalnie możesz podać unikatowego klucza partycji do dzielenia partii na podzestawy do zbierania elementów za pomocą tego klucza.

Nadawca aplikacje logiki muszą wiedzieć, dokąd wysyłać elementów, gdy aplikacje logiki odbiorcy nie muszą nic wiedzieć o nadawcy.


1. Tworzenie innej aplikacji logiki o tej nazwie: "X12MessageSender". Dodaj ten wyzwalacz aplikacji logiki: **żądanie / odpowiedź — żądanie** 
   
   ![Dodawanie wyzwalacza żądania](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

1. Dodawanie nowego kroku do wysyłania komunikatów do partii.

   1. Wybierz **+ nowy krok** > **Dodaj akcję**.

   1. W polu wyszukiwania wprowadź "batch" jako filtr. 

1. Wybierz tę akcję: **wysyłać komunikaty do usługi batch — wybierz przepływ pracy usługi Logic Apps z wyzwalaczem partii**

   ![Wybierz pozycję "Wyślij komunikaty do przetwarzania zbiorczego"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

1. Teraz wybierz aplikację logiki "BatchX12Messages", która została wcześniej utworzona, który teraz jest wyświetlany jako akcję.

   ![Wybieranie aplikacji logiki "odbiornik usługi batch"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > Lista zawiera również inne aplikacje logiki, które może mieć wyzwalaczy usługi batch.

1. Ustaw właściwości usługi batch.

   * **Nazwa partii**: Nazwa usługi batch, zdefiniowane przez aplikację logiki odbiorcy, który jest "TestBatch" w tym przykładzie i sprawdzania poprawności w czasie wykonywania.

     > [!IMPORTANT]
     > Upewnij się, że nie zmieniaj nazwy usługi batch, która musi pasować do nazwy usługi batch, który jest określony przez aplikację logiki odbiorcy.
     > Zmienianie nazwy usługi batch powoduje, że nadawca aplikację logiki, aby zakończyć się niepowodzeniem.

   * **Zawartość komunikatu**: zawartość komunikatu, który chcesz wysłać do usługi batch
   
   ![Ustawianie właściwości usługi batch](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

1. Zapisz aplikację logiki. Twoja aplikacja logiki nadawcy powinna wyglądać następująco:

   ![Zapisz aplikację logiki nadawcy](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązania do przetwarzania wsadowego, post X12 wiadomości do aplikacji logiki nadawcy [Postman](https://www.getpostman.com/postman) lub podobnego narzędzia. Wkrótce, należy rozpocząć pobieranie X12 wiadomości jako partii pięć elementów lub co 10 minut w pojemniku Twoje żądanie — wszystko w tym samym kluczem partycji.

## <a name="next-steps"></a>Kolejne kroki

* [Przetwarzanie komunikatów jako partie](logic-apps-batch-process-send-receive-messages.md) 
* [Tworzenie aplikacji bezserwerowej w programie Visual Studio za pomocą usługi Azure Logic Apps i Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Obsługa wyjątków i rejestrowania błędów dla usługi logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
