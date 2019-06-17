---
title: Batch przetwarzania komunikatów, grupę lub kolekcji — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłania i odbierania wiadomości jako partie w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: c33b1d46ecf710f050fc998ce27f6448337c6b78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60683708"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Wysyłanie, odbieranie i wsadowe przetwarzanie komunikatów w usłudze Azure Logic Apps

Wysyłanie i przetwarzać komunikaty ze sobą w określony sposób jako grupy, można utworzyć rozwiązania przetwarzania wsadowego, które zbiera komunikaty do *partii* aż określone kryteria są spełnione dla zwalniając i przetwarzanie komunikatów wsadowej. Przetwarzanie wsadowe może zmniejszyć, jak często aplikacja logiki przetwarza wiadomości. W tym artykule pokazano, jak zbudować rozwiązanie do przetwarzania wsadowego, tworząc dwie aplikacje logiki w ramach tej samej subskrypcji platformy Azure, region platformy Azure, a po następującej kolejności: 

* ["Odbiornik usługi batch"](#batch-receiver) aplikację logiki, która akceptuje i zbiera komunikaty w partii, aż do spełnienia określone kryteria zwalniania i przetwarzania wiadomości.

  Upewnij się, że należy najpierw utworzyć odbiornik usługi batch, docelowej usługi batch można wybrać później, po utworzeniu nadawcy usługi batch.

* Co najmniej jeden ["sender usługi batch"](#batch-sender) aplikacji logiki, które wysyłają komunikaty do odbiorcy wcześniej utworzonej usługi batch. 

   Można również określić unikatowy klucz, takie jak numer klienta, który *partycje* lub dzieli partii docelowego na podzestawy logiczną na podstawie tego klucza. W ten sposób aplikacja odbiorcy można zbierać wszystkie elementy z tego samego klucza i przetwarzać je ze sobą.

Upewnij się, że usługi batch odbiorcy i nadawcy partii udostępnianie tej samej subskrypcji platformy Azure *i* region platformy Azure. Jeśli nie, nie możesz wybrać odbiornik usługi batch podczas tworzenia nadawcy partii, ponieważ nie są widoczne dla siebie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przykładu, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Ewentualnie [logowania do subskrypcji płatności](https://azure.microsoft.com/pricing/purchase-options/).

* Konto e-mail ze wszystkimi [dostawcy poczty e-mail obsługiwanego przez usługę Azure Logic Apps](../connectors/apis-list.md)

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Aby korzystać z programu Visual Studio, a nie w witrynie Azure portal, upewnij się, że [Konfigurowanie programu Visual Studio do pracy z usługą Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Utwórz odbiornik usługi batch

Przed może wysyłać komunikaty do usługi batch, tej partii najpierw musi istnieć jako miejsca docelowego, gdzie wysłać te komunikaty. Dlatego najpierw należy utworzyć aplikację logiki "odbiornik usługi batch", która rozpoczyna się od **partii** wyzwalacza. W ten sposób podczas tworzenia aplikacji logiki "sender usługi batch", możesz wybrać aplikację logiki odbiornika usługi batch. Odbiornik usługi batch kontynuuje zbieranie wiadomości do momentu spełnienia określone kryteria zwalniania i przetwarzania wiadomości. Podczas odbiorniki usługi batch nie muszą nic wiedzieć o nadawców usługi batch, nadawców usługi batch musi znać miejsca docelowego, gdzie one wysyłać wiadomości. 

1. W [witryny Azure portal](https://portal.azure.com) lub Visual Studio, tworzenie aplikacji logiki o tej nazwie: "BatchReceiver" 

2. W Projektancie aplikacji logiki, Dodaj **partii** wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wprowadź "batch" jako filtr. Wybierz ten wyzwalacz: **Komunikaty przetwarzania wsadowego**

   ![Dodawanie wyzwalacza "Partii komunikatów"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Ustaw następujące właściwości dla odbiornika usługi batch: 

   | Właściwość | Opis | 
   |----------|-------------|
   | **Tryb partii** | - **Wbudowane**: Definiowanie kryteriów wersji wewnątrz wyzwalacza partii <br>- **Konto integracji**: Definiowanie wielu wersji konfiguracji kryteria za pośrednictwem [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Z kontem integracji możesz zachować te konfiguracje, wszystko w jednym miejscu, a nie w oddzielnych logic apps. | 
   | **Nazwa partii** | Nazwa usługi batch, jest "TestBatch" w tym przykładzie, która ma zastosowanie tylko do **wbudowane** tryb usługi batch |  
   | **Kryteria zwalniania** | Ma zastosowanie tylko do **wbudowane** usługi batch w trybie i wybiera odpowiednie kryteria, aby spełnić przed przetworzeniem każdej partii: <p>- **Na podstawie liczby komunikatów**: Zwolnij wsadowego na podstawie liczby komunikaty zebrane przez partię zakończoną. <br>- **Rozmiar na podstawie**: Zwolnij oparte na całkowity rozmiar w bajtach dla wszystkie komunikaty zebrane przez tej partii usługi batch. <br>- **Harmonogram**: Wersja opierają na harmonogramie cyklu, który określa interwał i częstotliwość usługi batch. Zaawansowane opcje można również wybierz strefę czasową i podaj datę i godzinę. <br>- **Zaznacz wszystko**: Użyj określone kryteria. | 
   | **Liczba komunikatów** | Liczba wiadomości do zbierania w usłudze batch, na przykład 10 wiadomości. Batch limit wynosi 8000 komunikatów. | 
   | **Batch Size** | Całkowity rozmiar w bajtach, aby zebrać w usłudze batch, na przykład 10 MB. Limit rozmiaru partii wynosi 80 MB. | 
   | **Harmonogram** | Interwał i częstotliwość między wersjami usługi batch, na przykład 10 minut. Minimalny cykl jest 60 sekund lub 1 minutę. Skutecznie ułamkach minut jest zaokrąglana do 1 minuty. Aby określić strefę czasową lub Data i godzina rozpoczęcia, wybierz **Pokaż opcje zaawansowane**. | 
   ||| 

   > [!NOTE]
   > 
   > Jeśli zmienisz kryteriach zwalniania, gdy wyzwalacz ma nadal wsadowej ale niewysłanych wiadomości, wyzwalacz będzie używać kryteria zaktualizowanej wersji obsługi niewysłanych wiadomości. 

   W tym przykładzie przedstawiono wszystkie kryteria, ale na potrzeby własnych testów, spróbuj tylko jedno kryterium:

   ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Teraz Dodaj jedną lub więcej akcji, które przetwarzają każdej partii. 

   W tym przykładzie Dodaj akcję, która wysyła wiadomość e-mail po aktywowaniu wyzwalacza usługi batch. 
   Wyzwalacz jest uruchamiany i wysyła wiadomość e-mail, gdy usługi batch ma 10 wiadomości, osiągnie 10 MB albo po upływie 10 minut przekazywania.

   1. W obszarze wyzwalacza usługi batch wybierz **nowy krok**.

   2. W polu wyszukiwania wprowadź „wyślij wiadomość e-mail” jako filtr.
   Oparta na dostawcy poczty e-mail, wybierz łącznik poczty e-mail.

      Na przykład, jeśli masz konto osobiste, takie jak @outlook.com lub @hotmail.com, wybierz łącznik usługi Outlook.com. 
      W przypadku kont usługi Gmail wybierz łącznik usługi Gmail. 
      W tym przykładzie użyto usługi Office 365 Outlook. 

   3. Wybierz tę akcję: **Wyślij wiadomość e-mail — <*dostawcy poczty e-mail*>**

      Na przykład:

      ![Wybierz akcję "Wyślij wiadomość e-mail" dla dostawcy poczty e-mail](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. W razie potrzeby zaloguj się do swojego konta e-mail. 

6. Ustaw właściwości dla akcji, którą dodałeś.

   * W polu **Do** wprowadź adres e-mail adresata. 
   Do celów testowych możesz użyć własnego adresu e-mail.

   * W **podmiotu** pole, po wyświetleniu listy zawartości dynamicznej wybierz **nazwa partycji** pola.

     ![Z listy zawartości dynamicznej wybierz pozycję "Nazwa partycji"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Później w nadawcy usługi batch można określić unikatowego klucza partycji, batch docelowej są podzielone na logiczne podzbiorów, gdzie może wysyłać komunikaty o. 
     Każdy zestaw ma unikatowy numer, który jest generowany przez aplikację logiki nadawcy usługi batch. 
     Ta funkcja umożliwia pojedyncza partia za pomocą wielu podzestawy i zdefiniować każdy podzbiór nazwą, której należy podać.

     > [!IMPORTANT]
     > Partycja obowiązuje limit 5000 wiadomości lub 80 MB. Spełnieniu tych warunków Logic Apps może zwolnić partii, nawet wtedy, gdy nie jest spełniony warunek określonych wersji.

   * W **treści** pole, po wyświetleniu listy zawartości dynamicznej wybierz **identyfikator komunikatu** pola. 

     Projektant aplikacji logiki automatycznie dodaje pętlę "For each" wokół akcji Wyślij wiadomość e-mail, ponieważ ta akcja traktuje dane wyjściowe z poprzednich akcji jako kolekcję, a nie w zadaniu wsadowym. 

     !["Treść" Wybierz "Identyfikator komunikatu"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Zapisz aplikację logiki. Utworzono odbiornik usługi batch.

    ![Zapisywanie aplikacji logiki](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Jeśli używasz programu Visual Studio, upewnij się, że [wdrażanie aplikacji logiki odbiornika usługi batch na platformie Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). W przeciwnym razie nie można wybrać odbiornik usługi batch podczas tworzenia nadawcy usługi batch.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Tworzenie nadawcy usługi batch

Teraz należy utworzyć co najmniej jedną aplikację logiki nadawcy usługi batch, wysyłania wiadomości do aplikacji logiki odbiornika usługi batch. W każdej partii nadawcy Określ odbiornik usługi batch i Nazwa instancji, zawartość komunikatu i inne ustawienia. Opcjonalnie możesz podać unikatowego klucza partycji do dzielenia partii na logiczne podzestawy pobierania komunikatów za pomocą tego klucza. 

* Upewnij się, że masz już [utworzony odbiornik usługi batch](#batch-receiver) , podczas tworzenia usługi batch nadawcy jako miejsce docelowe można wybrać istniejący odbiornik usługi batch. Gdy odbiorniki usługi batch nie muszą nic wiedzieć o nadawców usługi batch, nadawców partii musisz wiedzieć, gdzie wysyłać wiadomości. 

* Upewnij się, usługi batch odbiorcy i nadawcy partii udostępnić tym samym regionie Azure *i* subskrypcji platformy Azure. Jeśli nie, nie możesz wybrać odbiornik usługi batch podczas tworzenia nadawcy partii, ponieważ nie są widoczne dla siebie.

1. Tworzenie innej aplikacji logiki o tej nazwie: "BatchSender"

   1. W polu wyszukiwania wprowadź ciąg "cyklicznie" jako filtr. 
   Wybierz ten wyzwalacz: **Cykl - harmonogramu**

      ![Dodawanie wyzwalacza "Harmonogram — cykl"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Ustaw częstotliwość i interwał uruchamiania nadawcy aplikacji logiki na minutę.

      ![Ustaw częstotliwość i interwał cyklu wyzwalacza](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Dodaj nową akcję do wysyłania komunikatów do partii.

   1. W obszarze wyzwalacza, wybierz opcję **nowy krok**.

   2. W polu wyszukiwania wprowadź "batch" jako filtr. 
   Wybierz **akcje** listy, a następnie wybierz tę akcję: **Wybierz przepływ pracy usługi Logic Apps z wyzwalaczem partii — Wyślij komunikaty do przetwarzania zbiorczego**

      ![Wybierz pozycję "Wybierz przepływ pracy aplikacji logiki z wyzwalaczem partii"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Wybierz swoją aplikację logiki odbiornika usługi batch, która została wcześniej utworzona.

      ![Wybieranie aplikacji logiki "odbiornik usługi batch"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > Lista zawiera również inne aplikacje logiki, które może mieć wyzwalaczy usługi batch. 
      > 
      > Jeśli używasz programu Visual Studio, a nie widać żadnych odbiorników usługi batch wybierz pozycję, sprawdź, czy odbiornik usługi batch jest wdrożona na platformie Azure. Jeśli jeszcze tego nie, Dowiedz się, jak [wdrażanie aplikacji logiki odbiornika usługi batch na platformie Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Wybierz tę akcję: **Batch_messages — <*odbiornika usługi batch*>**

      ![Wybierz tę akcję: "Batch_messages — < Twój logic-app >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Ustaw partii właściwości nadawcy:

   | Właściwość | Opis | 
   |----------|-------------| 
   | **Nazwa partii** | Nazwa partii, zdefiniowane przez aplikację logiki odbiornik, który jest "TestBatch" w tym przykładzie <p>**Ważne**: Nazwa partii zostanie zweryfikowane w czasie wykonywania i musi pasować do nazwy określonej przez aplikację logiki odbiorcy. Zmienianie nazwy usługi batch powoduje, że nadawca partii nie powiedzie się. | 
   | **Zawartość komunikatu** | Zawartość wiadomości, do której chcesz wysłać | 
   ||| 

   W tym przykładzie należy dodać to wyrażenie, który wstawia bieżącą datę i godzinę na zawartość komunikatu, który możesz wysłać do usługi batch:

   1. Kliknij wewnątrz **zawartość wiadomości** pole. 

   2. Po wyświetleniu listy zawartości dynamicznej wybierz **wyrażenie**. 

   3. Wprowadź wyrażenie `utcnow()`, a następnie wybierz **OK**. 

      ![W "Zawartość komunikatu" "Wyrażenie", wprowadź "utcnow()" lub wybrać "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Teraz należy skonfigurować partycji dla usługi batch. W akcji "BatchReceiver" Wybierz **Pokaż opcje zaawansowane** i ustaw następujące właściwości:

   | Właściwość | Opis | 
   |----------|-------------| 
   | **Nazwa partycji** | Opcjonalne unikatowego klucza partycji dla dzielenia partii docelowego na podzestawy logicznych oraz zbierać komunikaty, na podstawie tego klucza | 
   | **Identyfikator komunikatu** | Identyfikator opcjonalną wiadomość, który jest wygenerowany Unikatowy identyfikator globalny (GUID), gdy jest pusty | 
   ||| 

   W tym przykładzie w **nazwa partycji** Dodaj wyrażenie, które generuje losową liczbę między jednym i 5. Pozostaw **identyfikator komunikatu** puste pole.
   
   1. Kliknij wewnątrz **nazwa partycji** pola tak, aby wyświetlić listę zawartości dynamicznej. 

   2. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**.
   
   3. Wprowadź wyrażenie `rand(1,6)`, a następnie wybierz **OK**.

      ![Konfigurowanie partycji dla usługi batch docelowej](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      To **rand** funkcja generuje liczbę od jednej do pięciu. 
      Dlatego są podzielenie tej partii na pięć numerowane partycje, które dynamicznie ustawia to wyrażenie.

5. Zapisz aplikację logiki. Twoja aplikacja logiki nadawcy powinna wyglądać następująco:

   ![Zapisz aplikację logiki nadawcy](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązania do przetwarzania wsadowego, pozostaw aplikacje logiki uruchomione przez kilka minut. Szybko możesz rozpocząć pobieranie wiadomości e-mail w grupach pięć wszystko w tym samym kluczem partycji.

Twoja aplikacja logiki nadawcy partii jest uruchamiany co minutę, generuje losową liczbę między jednym i 5 i używa tego numeru wygenerowany jako klucza partycji dla usługi batch docelowej, w której są wysyłane wiadomości. Każdorazowo usługi batch ma pięć elementów mających taki sam klucz partycji, aplikacja logiki odbiornika usługi batch wyzwala i wysyła wiadomość e-mail dla każdego komunikatu.

> [!IMPORTANT]
> Po zakończeniu testowania, upewnij się, aby wyłączyć aplikację logiki BatchSender, aby zatrzymać wysyłanie komunikatów i nie przeciążać skrzynki odbiorczej.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie partii i wysyłanie komunikatów EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Tworzenie definicji aplikacji logiki przy użyciu pliku JSON](../logic-apps/logic-apps-author-definitions.md)
* [Tworzenie aplikacji bezserwerowej w programie Visual Studio za pomocą usługi Azure Logic Apps i Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Obsługa wyjątków i rejestrowania błędów dla usługi logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
