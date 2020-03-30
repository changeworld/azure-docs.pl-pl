---
title: Przetwarzanie wsadowe wiadomości EDI jako grupa
description: Wysyłanie i odbieranie wiadomości EDI jako partii, grup lub kolekcji przy użyciu przetwarzania wsadowego w usłudze Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666673"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Wymiana wiadomości EDI jako partii lub grup między partnerami handlowymi w usłudze Azure Logic Apps

W scenariuszach między firmami (B2B) partnerzy często wymieniają wiadomości w grupach lub *partiach.* Podczas tworzenia rozwiązania wsadowego z aplikacjami logiki, można wysyłać wiadomości do partnerów handlowych i przetwarzać te wiadomości razem w partiach. W tym artykule pokazano, jak można przetwarzać wsadowe komunikaty EDI, przy użyciu X12 jako przykład, tworząc aplikację logiki "nadawca partii" i "odbiornik wsadowy" aplikacji logiki. 

Przetwarzanie wsadowe X12 wiadomości działa jak przetwarzanie wsadowe innych wiadomości; należy użyć wyzwalacza partii, który zbiera wiadomości do partii i akcji wsadowej, która wysyła komunikaty do partii. Ponadto x12 wsadowania zawiera krok kodowania X12 przed wiadomości przejść do partnera handlowego lub innego miejsca docelowego. Aby dowiedzieć się więcej o wyzwalaczu i akcji partii, zobacz [Komunikaty procesu partii](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

W tym artykule skompilujesz rozwiązanie przetwarzania wsadowego, tworząc dwie aplikacje logiki w ramach tej samej subskrypcji platformy Azure, regionu platformy Azure i zgodnie z tą konkretną kolejnością:

* Aplikacja logiki ["odbiornik wsadowy",](#receiver) która akceptuje i zbiera wiadomości do partii, dopóki nie zostanie spełnione określone kryteria zwalniania i przetwarzania tych wiadomości. W tym scenariuszu odbiornik partii również koduje komunikaty w partii przy użyciu określonej umowy X12 lub tożsamości partnera.

  Upewnij się, że najpierw utworzysz odbiornik wsadowy, aby później można było wybrać miejsce docelowe partii podczas tworzenia nadawcy partii.

* Aplikacja logiki ["nadawca wsadowy",](#sender) która wysyła wiadomości do wcześniej utworzonego odbiornika wsadowego. 

Upewnij się, że odbiorca wsadowy i nadawca partii współużytkują tę samą subskrypcję platformy Azure *i* region platformy Azure. Jeśli tak nie jest, nie można wybrać odbiornika wsadowego podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten przykład, potrzebujesz tych elementów:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Możesz też [zarejestrować się w ramach subskrypcji Pay-As-You-Go](https://azure.microsoft.com/pricing/purchase-options/).

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Istniejące [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure i połączone z aplikacjami logiki

* Co najmniej dwóch istniejących [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) na koncie integracji. Każdy partner musi używać kwalifikatora X12 (Standard Carrier Alpha Code) jako tożsamości biznesowej we właściwościach partnera.

* Istniejąca [umowa X12](../logic-apps/logic-apps-enterprise-integration-x12.md) na koncie integracyjnym

* Aby używać programu Visual Studio zamiast witryny Azure portal, upewnij się, że [program Visual Studio został skonfigurowany do pracy z aplikacjami logiki.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Tworzenie odbiornika wsadowego X12

Przed wysłaniem wiadomości do partii, że partia musi najpierw istnieć jako miejsce docelowe, gdzie można wysłać te wiadomości. Dlatego najpierw należy utworzyć aplikację logiki "odbiornik wsadowy", która rozpoczyna się od wyzwalacza **partii.** W ten sposób podczas tworzenia aplikacji logiki "nadawca partii" można wybrać aplikację logiki odbiornika wsadowego. Odbiornik wsadowy kontynuuje zbieranie wiadomości, dopóki nie zostanie spełnione określone kryteria zwalniania i przetwarzania tych komunikatów. Podczas gdy odbiorniki wsadowe nie muszą wiedzieć nic o nadawców partii, nadawcy partii muszą znać miejsce docelowe, gdzie wysyłają wiadomości. 

Dla tego odbiornika wsadowego należy określić tryb wsadowy, nazwę, kryteria zwolnienia, umowę X12 i inne ustawienia. 

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio utwórz aplikację logiki o tej nazwie: "BatchX12Messages"

2. [Połącz aplikację logiki z kontem integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. W Logic Apps Designer dodaj wyzwalacz **partii,** który uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wpisz "partia" jako filtr. Wybierz ten wyzwalacz: **wiadomości wsadowe**

   ![Dodaj wyzwalacz partii](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Ustaw właściwości odbiornika wsadowego: 

   | Właściwość | Wartość | Uwagi | 
   |----------|-------|-------|
   | **Tryb wsadowy** | Śródwierszowo |  |  
   | **Nazwa partii** | TestBatch (TestBatch) | Dostępne tylko w trybie **wsadowym Inline** | 
   | **Kryteria uwalniania** | Na podstawie liczby wiadomości, harmonogram na podstawie | Dostępne tylko w trybie **wsadowym Inline** | 
   | **Liczba wiadomości** | 10 | Dostępne tylko z kryteriami zwolnienia **opartego na liczbie wiadomości** | 
   | **Interwał** | 10 | Dostępne tylko z kryteriami zwolnienia **opartymi na harmonogramie** | 
   | **Częstotliwości** | minuta | Dostępne tylko z kryteriami zwolnienia **opartymi na harmonogramie** | 
   ||| 

   ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > W tym przykładzie nie skonfigurować partycję dla partii, więc każda partia używa tego samego klucza partycji. Aby dowiedzieć się więcej o partycjach, zobacz [Komunikaty procesu wsadowego](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Teraz dodaj akcję, która koduje każdą partię: 

   1. W obszarze wyzwalacza partii wybierz pozycję **Nowy krok**.

   2. W polu wyszukiwania wpisz "X12 batch" jako filtr i wybierz tę akcję (dowolną wersję): **Kodowanie <*> wersji* partii - X12** 

      ![Wybierz akcję kodowania partii X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Jeśli wcześniej nie nawiązywać połączenia z kontem integracyjnym, utwórz połączenie teraz. Podaj nazwę połączenia, wybierz żądane konto integracji, a następnie wybierz pozycję **Utwórz**.

      ![Tworzenie połączenia między koderem wsadowym a kontem integracji](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Ustaw te właściwości dla akcji kodera wsadowego:

      | Właściwość | Opis |
      |----------|-------------|
      | **Nazwa umowy X12** | Otwórz listę i wybierz istniejącą umowę. <p>Jeśli lista jest pusta, upewnij się, że [aplikacja logiki połączyć konto integracji,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) które ma umowy, które chcesz. | 
      | **Nazwa partii** | Kliknij wewnątrz tego pola, a po wyświetleniu dynamicznej listy zawartości wybierz token **Nazwa partii.** | 
      | **Nazwa partycji** | Kliknij wewnątrz tego pola, a po wyświetleniu dynamicznej listy zawartości wybierz token **Nazwa partycji.** | 
      | **Items** | Zamknij pole szczegółów elementu, a następnie kliknij w tym polu. Po wyświetleniu dynamicznej listy zawartości wybierz token **Elementy wsadowe.** | 
      ||| 

      ![Szczegóły akcji kodowania wsadowego](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      W polu **Przedmioty:**

      ![Elementy akcji Kodowanie wsadowe](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Zapisz aplikację logiki. 

7. Jeśli używasz programu Visual Studio, upewnij się, że [wdrożyłeś aplikację logiki odbiornika wsadowego na platformie Azure.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) W przeciwnym razie nie można wybrać odbiornika wsadowego podczas tworzenia nadawcy partii.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby upewnić się, że odbiorca wsadowy działa zgodnie z oczekiwaniami, można dodać akcję HTTP do celów testowych i wysłać wiadomość wsadową do [usługi Pojemnik żądania](https://requestbin.com/). 

1. W obszarze akcji kodowania X12 wybierz pozycję **Nowy krok**. 

2. W polu wyszukiwania wpisz "http" jako filtr. Wybierz tę akcję: **HTTP - HTTP**
    
   ![Wybierz akcję HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Ustaw właściwości akcji HTTP:

   | Właściwość | Opis | 
   |----------|-------------|
   | **Metoda** | Z tej listy wybierz **pozycję POST**. | 
   | **Identyfikator uri** | Wygeneruj identyfikator URI dla pojemnika z żądaniem, a następnie wprowadź ten identyfikator URI w tym polu. | 
   | **Treść** | Kliknij wewnątrz tego pola, a po otwarciu dynamicznej listy zawartości wybierz token **Treści,** który pojawia się w sekcji **Batch koduje według nazwy umowy**. <p>Jeśli nie widzisz tokenu **treści,** obok pozycji **Batch kodowanie według nazwy umowy,** wybierz pozycję **Zobacz więcej**. | 
   ||| 

   ![Podaj szczegóły akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Zapisz aplikację logiki. 

   Aplikacja logiki odbiornika wsadowego wygląda następująco: 

   ![Zapisywanie aplikacji logiki odbiornika wsadowego](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Tworzenie nadawcy partii X12

Teraz utwórz jedną lub więcej aplikacji logiki, które wysyłają wiadomości do aplikacji logiki odbiornika wsadowego. W każdym nadawcy partii należy określić aplikację logiki odbiornika wsadowego i nazwę partii, zawartość wiadomości i inne ustawienia. Opcjonalnie można podać unikatowy klucz partycji, aby podzielić partię na podzbiory do zbierania wiadomości za pomocą tego klucza. 

* Upewnij się, że odbiornik partii został już [utworzony,](#receiver) więc podczas tworzenia nadawcy partii można wybrać istniejący odbiorca wsadowy jako partię docelową. Podczas gdy odbiorniki wsadowe nie muszą wiedzieć nic o nadawców partii, nadawcy partii muszą wiedzieć, gdzie wysyłać wiadomości. 

* Upewnij się, że odbiorca wsadowy i nadawca partii współużytkują ten sam region platformy Azure *i* subskrypcję platformy Azure. Jeśli tak nie jest, nie można wybrać odbiornika wsadowego podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

1. Utwórz inną aplikację logiki o tej nazwie: "SendX12MessagesToBatch" 

2. W polu wyszukiwania wpisz jako filtr "gdy żądanie http" jest. Wybierz ten wyzwalacz: **po odebraniu żądania HTTP** 
   
   ![Dodawanie wyzwalacza żądania](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Dodaj akcję wysyłania wiadomości do partii.

   1. W obszarze akcji żądania HTTP wybierz pozycję **Nowy krok**.

   2. W polu wyszukiwania wpisz "partia" jako filtr. 
   Wybierz listę **Akcje,** a następnie wybierz tę akcję: **Wybierz przepływ pracy aplikacji logiki z wyzwalaczem wsadowym — Wysyłanie wiadomości do partii**

      ![Wybierz opcję "Wybierz przepływ pracy aplikacji logiki z wyzwalaczem wsadowym"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Teraz wybierz aplikację logiki "BatchX12Messages", która została wcześniej utworzona.

      ![Wybierz aplikację logiki "odbiornik wsadowy"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Wybierz tę czynność: **Batch_messages - <*odbiornika* > wsadowego**

      ![Wybierz akcję "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Ustaw właściwości nadawcy partii.

   | Właściwość | Opis | 
   |----------|-------------| 
   | **Nazwa partii** | Nazwa partii zdefiniowana przez aplikację logiki odbiornika, która jest "TestBatch" w tym przykładzie <p>**Ważne:** Nazwa partii zostanie zweryfikowana w czasie wykonywania i musi być zgodna z nazwą określoną przez aplikację logiki odbiornika. Zmiana nazwy partii powoduje niepowodzenie nadawcy partii. | 
   | **Treść wiadomości** | Zawartość wiadomości, którą chcesz wysłać, która jest tokenem **treści** w tym przykładzie | 
   ||| 
   
   ![Ustawianie właściwości partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Zapisz aplikację logiki. 

   Aplikacja logiki nadawcy wsadowego wygląda następująco:

   ![Zapisywanie aplikacji logiki nadawcy wsadowego](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązanie do przetwarzania wsadowego, opublikuj wiadomości X12 do aplikacji logiki nadawcy wsadowego z [postmana](https://www.getpostman.com/postman) lub podobnego narzędzia. Wkrótce zaczniesz dostawać wiadomości X12 w pojemniku żądania, co 10 minut lub w partiach po 10, wszystkie z tym samym kluczem partycji.

## <a name="next-steps"></a>Następne kroki

* [Przetwarzanie wiadomości jako partii](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
