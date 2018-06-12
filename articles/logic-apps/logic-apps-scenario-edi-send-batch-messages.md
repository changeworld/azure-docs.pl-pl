---
title: Wsadowe przetwarzanie komunikatów EDI jako grupy lub kolekcji — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłanie komunikatów EDI dla przetwarzania wsadowego w aplikacji logiki
keywords: Przetwarzanie wsadowe partii, kodowania partii
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
ms.openlocfilehash: 6340d1fc409b64c5e681f9f69e4e2d0895ede61f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299294"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Wyślij X12 komunikaty w partii, aby partnerami handlowymi

W różnych scenariuszy biznesowych do biznesowych (B2B) partnerzy często wymiany wiadomości w grup lub partii. Do wysyłania wiadomości w grup lub partie do partnerami handlowymi, można utworzyć partii z wielu elementów, a następnie użyj X12 partii akcji przetwarzania tych elementów jako plik wsadowy.


Przetwarzanie wsadowe dla X12 komunikaty, takie jak inne komunikaty, używa wyzwalacza partii i akcji. Również dla X12, partii przechodzi przez X12 Koduj kroku przed przejściem do partnera lub innego miejsca docelowego. Aby uzyskać więcej informacji na temat wyzwalacza partii i akcji, zobacz [komunikaty przetwarzania wsadowego](logic-apps-batch-process-send-receive-messages.md).

W tym temacie pokazano, jak można przetwarzać X12 wiadomości jako partii, wykonując następujące zadania:
* [Tworzenie aplikacji logiki odbiera elementów i tworzy plik wsadowy](#receiver). Ta aplikacja logiki "odbiorcy" wykonuje następujące działania:
 
   * Określa kryteria nazwa i wersja partii do spełnienia przed zwolnieniem elementów jako zadanie wsadowe.

   * Przetwarza lub koduje elementów w partii przy użyciu określonego X12 umowy lub partnera tożsamości.

* [Tworzenie aplikacji logiki, która wysyła elementów na partię](#sender). Ta aplikacja logiki "sender" Określa, gdzie można wysłać elementy dla partii, które muszą być istniejących aplikacji logiki odbiornika.


## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego przykładu, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). W przeciwnym razie możesz [skorzystać z subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/purchase-options/).

* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure

* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) zdefiniowanego w ramach konta integracji. Upewnij się, że każdego partnera używa X12 (standardowy operator alfa kod) kwalifikator we właściwościach partnera jako tożsamości firm.

* [X12 umowy](logic-apps-enterprise-integration-x12.md) jest już zdefiniowany w ramach konta integracji

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Utwórz logikę aplikacji, która odbiera X12 komunikatów i tworzy plik wsadowy

Aby można było wysłać wiadomości do partii, należy najpierw utworzyć aplikacji logiki "odbiorcy" z **partii** wyzwalacza. W ten sposób można wybrać tę aplikację logiki odbiornika podczas tworzenia aplikacji logiki nadawcy. Dla odbiornika, określ nazwę instancji, zwolnij kryteriów, X12 umowy i inne ustawienia. 


1. W [portalu Azure](https://portal.azure.com), tworzenie aplikacji logiki o tej nazwie: "BatchX12Messages".

2. W Projektancie aplikacji logiki, Dodaj **partii** wyzwalacza, która uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wprowadź "partii" jako filtr. Wybierz ten wyzwalacz: **partii — komunikaty wsadowe**

   ![Dodaj wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Podaj nazwę dla partii, a następnie określ kryteria wydanie partii, na przykład:

   * **Nazwa partii**: Nazwa używana do identyfikowania partii, czyli "TestBatch" w tym przykładzie.

   * **Zwolnij kryteria**: kryteria wersji usługi partia zadań, które mogą być oparte na liczbie komunikatów i harmonogram.
   
     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Liczba komunikatów**: liczba komunikatów do przechowywania jako partii przed zwolnieniem do przetwarzania, czyli "5" w tym przykładzie.

     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Harmonogram**: harmonogram zlecenia partii do przetworzenia, czyli "co 10 minut" w tym przykładzie.

     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Dodaj inną akcję, która koduje zgrupowane lub partii komunikatów i tworzy X12 umieścić w zadaniu wsadowym wiadomości. 

   a. Wybierz **+ nowy krok** > **Dodaj akcję**.

   b. W polu wyszukiwania wpisz "X 12 partii" jako filtr i wybierz akcję dla **X12-kodowania partii**. X12, takich jak kodowanie łącznika, istnieje wiele zmian dla partii kodowanie akcji. Można wybrać jedną z nich.

   ![Wybierz X12 kodowania partii akcji](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Ustaw właściwości akcji, którą właśnie został dodany.

   * W **nazwa X12 umowy** wybierz umowę z listy rozwijanej. Jeśli lista jest pusta, upewnij się, nawiąże połączenie z kontem integracji.

   * W **BatchName** wybierz opcję **Nazwa instancji** pole z listy zawartości dynamicznej.
   
   * W **PartitionName** wybierz opcję **nazwa partycji** pole z listy zawartości dynamicznej.

   * W **elementów** wybierz opcję **umieścić w zadaniu wsadowym elementów** na liście zawartości dynamicznej.

   ![Szczegóły działania Koduj partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Do celów testowych, Dodaj akcję HTTP do wysyłania wiadomości wsadów, aby [Bin żądania usługi](https://requestbin.fullcontact.com/). 

   1. Jako filtru w polu wyszukiwania wprowadź "HTTP". Wybierz tę akcję: **HTTP - HTTP**
    
      ![Wybierz akcję HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Z **metody** listy, wybierz **POST**. Aby uzyskać **Uri** , Wygeneruj identyfikator URI bin Twoje żądanie i wpisz tego identyfikatora URI. W **treści** okno, po otwarciu listy dynamicznych wybierz **treści** pole w obszarze **kodowania wsadu według nazwy umowy** sekcji. Jeśli nie widzisz **treści**, wybierz **zobaczyć więcej** obok **kodowania wsadu według nazwy umowy**.

      ![Podaj szczegóły akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Teraz, gdy utworzono aplikację logiki odbiornik, Zapisz aplikację logiki.

    ![Zapisywanie aplikacji logiki](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Partycja ma limit 5000 komunikatów lub 80 MB. Jeśli albo warunek jest spełniony, partii może być zwolnione, nawet wtedy, gdy nie jest spełniony warunek zdefiniowane przez użytkownika.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Tworzenie aplikacji logiki, która wysyła X12 wiadomości do partii

Teraz należy utworzyć co najmniej jedną aplikację logiki, wysyłających elementów do partii zdefiniowane przez aplikację logiki odbiornika. Dla nadawcy należy określić aplikację logiki odbiornik i nazwa usługi partia zadań, zawartość komunikatu i inne ustawienia. Opcjonalnie można podać klucz partycji unikatowy do dzielenia partii na podzestawy służąca do gromadzenia elementów z tym kluczem.

Aplikacje logiki nadawcy muszą wiedzieć, gdzie mają zostać wysłane elementy, podczas gdy aplikacje logiki odbiorcy nie muszą mieć żadnych informacji o nadawcy.


1. Tworzenie aplikacji logiki innej o tej nazwie: "X12MessageSender". Dodawanie tego wyzwalacza do aplikacji logiki: **żądania / odpowiedzi - żądania** 
   
   ![Dodaj wyzwalacza żądania](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Dodaj nowy krok do wysyłania wiadomości do partii.

   1. Wybierz **+ nowy krok** > **Dodaj akcję**.

   2. W polu wyszukiwania wprowadź "partii" jako filtr. 

3. Wybierz tę akcję: **wysyłanie komunikatów do partii — wybierz przepływ pracy aplikacji logiki z wyzwalaczem partii**

   ![Zaznacz pole wyboru "Wyślij komunikaty do przetwarzania wsadowego"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Teraz wybierz "BatchX12Messages" aplikację logiki utworzony wcześniej, która jest teraz wyświetlany jako akcja.

   ![Wybierz aplikację logiki "partii odbiornika"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > Lista zawiera również inne aplikacje logiki mających wyzwalaczy partii.

5. Ustaw właściwości partii.

   * **Nazwa partii**: Nazwa instancji zdefiniowane przez aplikację logiki odbiornika, który jest "TestBatch" w tym przykładzie i sprawdzania poprawności w czasie wykonywania.

     > [!IMPORTANT]
     > Upewnij się, że nie zmieniaj nazwy usługi partia zadań, która musi odpowiadać nazwie partii, określonym przez aplikację logiki odbiornika.
     > Zmiana nazwy partii powoduje, że nadawca aplikację logiki, aby zakończyć się niepowodzeniem.

   * **Zawartość komunikatu**: zawartość komunikatu, który ma zostać wysłany do partii
   
   ![Ustawianie właściwości partii](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Zapisz aplikację logiki. Aplikację logiki nadawcy powinna wyglądać w tym przykładzie:

   ![Zapisz aplikację logiki nadawcy](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązania przetwarzanie wsadowe, post X12 komunikatów do aplikacji logiki nadawcy z [Postman](https://www.getpostman.com/postman) lub narzędzia. Wkrótce, należy rozpocząć pobieranie X12 wiadomości jako partii pięć lub co 10 minut, w Twojej bin żądania — wszystko z tym samym kluczem partycji.

## <a name="next-steps"></a>Kolejne kroki

* [Przetwarzanie komunikatów jako partii](logic-apps-batch-process-send-receive-messages.md) 
* [Tworzenie aplikacji bez serwera w programie Visual Studio z usługi Azure Logic Apps i funkcje](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Obsługa wyjątków i rejestrowania błędów dla usługi logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
