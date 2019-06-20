---
title: Batch przetwarzania komunikatów EDI jako grupy lub kolekcji — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłanie komunikatów EDI wsadowo w usłudze logic apps
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: d6d3a7111f3a5e49e32eba8ca4f09d692538cb87
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64715791"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Wysyłanie komunikatów EDI w partiach do obrotu partnerzy korzystający z usługi Azure Logic Apps

W przypadkach firmami (B2B) partnerów często wymieniać wiadomości w grupach lub *partie*. Podczas kompilowania rozwiązania przetwarzania wsadowego za pomocą aplikacji logiki można wysyłać komunikaty do partnerów handlowych i przetworzyć te komunikaty razem w partii. Ten artykuł pokazuje, jak możesz wsadowego przetwarzania komunikatów EDI, używając X12 na przykład tworzenia aplikacji logiki "sender usługi batch" i aplikacji logiki "odbiornik usługi batch". 

Przetwarzanie wsadowe X12 komunikatów działa podobnie jak inne komunikaty; dzielenia na partie Możesz użyć wyzwalacza usługi batch, który gromadzi informacje o wiadomości w zadaniu wsadowym i działanie usługi batch, która wysyła komunikaty do usługi batch. Ponadto X12 dzielenia na partie obejmuje X12 kodowanie kroku przed komunikaty partner handlowy lub innego miejsca docelowego. Aby dowiedzieć się więcej na temat akcji i wyzwalaczy usługi batch, zobacz [komunikaty przetwarzania wsadowego](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

W tym artykule będziesz tworzyć rozwiązania przetwarzania wsadowego, tworząc dwie aplikacje logiki w ramach tej samej subskrypcji platformy Azure, region platformy Azure i poniżej następującej kolejności:

* A ["odbiornik usługi batch"](#receiver) aplikację logiki, która akceptuje i zbiera komunikaty w partii, aż do spełnienia określone kryteria zwalniania i przetwarzania wiadomości. W tym scenariuszu odbiornika usługi batch są również kodowane komunikatów w partii przy użyciu określonego X12 umowy lub partnera tożsamości.

  Upewnij się, że należy najpierw utworzyć odbiornik usługi batch, docelowej usługi batch można wybrać później, po utworzeniu nadawcy usługi batch.

* A ["sender usługi batch"](#sender) aplikację logiki, która wysyła komunikaty do odbiorcy wcześniej utworzonej usługi batch. 

Upewnij się, że usługi batch odbiorcy i nadawcy partii udostępnianie tej samej subskrypcji platformy Azure *i* region platformy Azure. Jeśli nie, nie możesz wybrać odbiornik usługi batch podczas tworzenia nadawcy partii, ponieważ nie są widoczne dla siebie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przykładu, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Ewentualnie [logowania do subskrypcji płatności](https://azure.microsoft.com/pricing/purchase-options/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Istniejące [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) który jest skojarzony z subskrypcją platformy Azure i jest połączona z aplikacji logiki

* Co najmniej dwa istniejące [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) na koncie integracji. Wszystkich partnerów, należy użyć X12 (standardowy kod operatora alfa) kwalifikator jako tożsamość firmy we właściwościach partnera.

* Istniejące [X12 umowy](../logic-apps/logic-apps-enterprise-integration-x12.md) na koncie integracji

* Aby korzystać z programu Visual Studio, a nie w witrynie Azure portal, upewnij się, że [Konfigurowanie programu Visual Studio do pracy z usługą Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Utwórz X12 odbiornika usługi batch

Przed może wysyłać komunikaty do usługi batch, tej partii najpierw musi istnieć jako miejsca docelowego, gdzie wysłać te komunikaty. Dlatego najpierw należy utworzyć aplikację logiki "odbiornik usługi batch", która rozpoczyna się od **partii** wyzwalacza. W ten sposób podczas tworzenia aplikacji logiki "sender usługi batch", możesz wybrać aplikację logiki odbiornika usługi batch. Odbiornik usługi batch kontynuuje zbieranie wiadomości do momentu spełnienia określone kryteria zwalniania i przetwarzania wiadomości. Podczas odbiorniki usługi batch nie muszą nic wiedzieć o nadawców usługi batch, nadawców usługi batch musi znać miejsca docelowego, gdzie one wysyłać wiadomości. 

Dla tego odbiornika usługi batch, należy określić tryb usługi batch, nazwa, kryteriów wersji X12 umowy i inne ustawienia. 

1. W [witryny Azure portal](https://portal.azure.com) lub Visual Studio, tworzenie aplikacji logiki o tej nazwie: "BatchX12Messages"

2. [Połącz swoją aplikację logiki z kontem integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. W Projektancie aplikacji logiki, Dodaj **partii** wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wprowadź "batch" jako filtr. Wybierz ten wyzwalacz: **Komunikaty przetwarzania wsadowego**

   ![Dodawanie wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Ustaw partii właściwości odbiorcy: 

   | Właściwość | Wartość | Uwagi | 
   |----------|-------|-------|
   | **Tryb partii** | wbudowane |  |  
   | **Nazwa partii** | TestBatch | Dostępne tylko w przypadku **wbudowane** tryb usługi batch | 
   | **Kryteria zwalniania** | Na podstawie harmonogramu na podstawie liczby komunikatów | Dostępne tylko w przypadku **wbudowane** tryb usługi batch | 
   | **Liczba komunikatów** | 10 | Dostępne tylko w przypadku **komunikat na podstawie liczby** kryteria zwalniania | 
   | **Interval** | 10 | Dostępne tylko w przypadku **na podstawie harmonogramu** kryteria zwalniania | 
   | **Częstotliwość** | Minuta | Dostępne tylko w przypadku **na podstawie harmonogramu** kryteria zwalniania | 
   ||| 

   ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > W tym przykładzie nie skonfigurować pod kątem partycji dla usługi batch, dzięki czemu w każdej partii używa tego samego klucza partycji. Aby dowiedzieć się więcej na temat partycji, zobacz [komunikaty przetwarzania wsadowego](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Teraz Dodaj akcję, która koduje każdej partii: 

   1. W obszarze wyzwalacza usługi batch wybierz **nowy krok**.

   2. W polu wyszukiwania wpisz "X 12 usługi batch" jako filtr, a następnie wybierz tę akcję (dowolna wersja): **Kodowanie zbiorcze <*wersji*>-X12** 

      ![Wybierz X12 kodowanie zbiorcze akcji](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Jeśli nie została wcześniej nawiąż połączenie z kontem integracji, należy utworzyć połączenie teraz. Podaj nazwę połączenia, wybierz konto integracji, a następnie wybierz **Utwórz**.

      ![Utwórz połączenie między usługą batch encoder i konto integracji](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Ustaw te właściwości akcji encoder usługi batch:

      | Właściwość | Opis |
      |----------|-------------|
      | **Nazwa X12 umowy** | Otwórz listę, a następnie wybierz swoje istniejące umowy. <p>Jeśli lista jest pusta, upewnij się, że [połączyć swoją aplikację logiki z konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) zawierającej Umowy ma. | 
      | **BatchName** | Kliknij wewnątrz tego pola, a po wyświetleniu listy zawartości dynamicznej wybierz **Nazwa partii** tokenu. | 
      | **PartitionName** | Kliknij wewnątrz tego pola, a po wyświetleniu listy zawartości dynamicznej wybierz **nazwa partycji** tokenu. | 
      | **Elementy** | Zamknij okno Szczegóły elementu, a następnie kliknij wewnątrz tego pola. Po wyświetleniu listy zawartości dynamicznej wybierz **partii elementów** tokenu. | 
      ||| 

      ![Szczegóły akcji Koduj usługi Batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Aby uzyskać **elementów** pola:

      ![Czynności do wykonania Koduj usługi Batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Zapisz aplikację logiki. 

7. Jeśli używasz programu Visual Studio, upewnij się, że [wdrażanie aplikacji logiki odbiornika usługi batch na platformie Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). W przeciwnym razie nie można wybrać odbiornik usługi batch podczas tworzenia nadawcy usługi batch.

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Zapewnienie usługi batch odbiorcy działa zgodnie z oczekiwaniami, można dodać akcji HTTP do celów testowych i wysłać wiadomość wsadowej [Bin żądania usługi](https://requestbin.fullcontact.com/). 

1. W obszarze X12 kodowanie akcji, wybierz polecenie **nowy krok**. 

2. W polu wyszukiwania wprowadź "http" jako filtr. Wybierz tę akcję: **PROTOKÓŁ HTTP - HTTP**
    
   ![Wybieranie akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Ustaw właściwości dla akcji HTTP:

   | Właściwość | Opis | 
   |----------|-------------|
   | **— Metoda** | Na tej liście, wybierz **WPIS**. | 
   | **Identyfikator URI** | Wygenerować identyfikator URI dla pojemnika swoje żądania, a następnie wprowadź tego identyfikatora URI, w tym polu. | 
   | **Treść** | Kliknij wewnątrz tego pola, a po otwarciu listy zawartości dynamicznej wybierz **treści** token, który znajduje się w sekcji, **kodowanie zbiorcze według nazwy umowy**. <p>Jeśli nie widzisz **treści** token obok **kodowanie zbiorcze według nazwy umowy**, wybierz opcję **Zobacz więcej**. | 
   ||| 

   ![Podaj szczegóły akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Zapisz aplikację logiki. 

   Twoja aplikacja logiki odbiornika usługi batch wygląda następująco: 

   ![Zapisz aplikację logiki odbiornika usługi batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Utwórz X12 nadawcy usługi batch

Teraz należy utworzyć co najmniej jedną aplikację logiki, wysyłania wiadomości do aplikacji logiki odbiornika usługi batch. W każdej partii nadawcy określasz aplikacji logiki odbiornika usługi batch i Nazwa instancji, zawartość komunikatu i inne ustawienia. Opcjonalnie możesz podać unikatowego klucza partycji do dzielenia partii na podzestawy zbierać komunikaty przy użyciu tego klucza. 

* Upewnij się, że masz już [utworzony odbiornik usługi batch](#receiver) , podczas tworzenia usługi batch nadawcy jako miejsce docelowe można wybrać istniejący odbiornik usługi batch. Gdy odbiorniki usługi batch nie muszą nic wiedzieć o nadawców usługi batch, nadawców partii musisz wiedzieć, gdzie wysyłać wiadomości. 

* Upewnij się, usługi batch odbiorcy i nadawcy partii udostępnić tym samym regionie Azure *i* subskrypcji platformy Azure. Jeśli nie, nie możesz wybrać odbiornik usługi batch podczas tworzenia nadawcy partii, ponieważ nie są widoczne dla siebie.

1. Tworzenie innej aplikacji logiki o tej nazwie: "SendX12MessagesToBatch" 

2. W polu wyszukiwania wprowadź "podczas żądania http" jako filtr. Wybierz ten wyzwalacz: **Po odebraniu żądania HTTP** 
   
   ![Dodawanie wyzwalacza żądania](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Dodawanie akcji do wysyłania komunikatów do partii.

   1. W obszarze akcji żądania HTTP, wybierz opcję **nowy krok**.

   2. W polu wyszukiwania wprowadź "batch" jako filtr. 
   Wybierz **akcje** listy, a następnie wybierz tę akcję: **Wybierz przepływ pracy usługi Logic Apps z wyzwalaczem partii — Wyślij komunikaty do przetwarzania zbiorczego**

      ![Wybierz pozycję "Wybierz przepływ pracy aplikacji logiki z wyzwalaczem partii"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Teraz wybierz aplikację logiki "BatchX12Messages", która została wcześniej utworzona.

      ![Wybieranie aplikacji logiki "odbiornik usługi batch"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Wybierz tę akcję: **Batch_messages — <*odbiornika usługi batch*>**

      ![Wybierz akcję "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Ustaw partii właściwości nadawcy.

   | Właściwość | Opis | 
   |----------|-------------| 
   | **Nazwa partii** | Nazwa partii, zdefiniowane przez aplikację logiki odbiornik, który jest "TestBatch" w tym przykładzie <p>**Ważne**: Nazwa partii zostanie zweryfikowane w czasie wykonywania i musi pasować do nazwy określonej przez aplikację logiki odbiorcy. Zmienianie nazwy usługi batch powoduje, że nadawca partii nie powiedzie się. | 
   | **Zawartość komunikatu** | Zawartość komunikatu chcesz wysłać, która jest **treści** tokenu w tym przykładzie | 
   ||| 
   
   ![Ustawianie właściwości usługi batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Zapisz aplikację logiki. 

   Twoja aplikacja logiki nadawcy partii wygląda następująco:

   ![Zapisz aplikację logiki nadawcy usługi batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązania do przetwarzania wsadowego, post X12 komunikaty przetwarzania wsadowego nadawcy aplikacji logiki [Postman](https://www.getpostman.com/postman) lub podobnego narzędzia. Po chwili uruchomić wprowadzenie X12 wiadomości w pojemniku Twoje żądanie, co 10 minut lub w partiach 10, wszystko w tym samym kluczem partycji.

## <a name="next-steps"></a>Kolejne kroki

* [Przetwarzanie komunikatów jako partie](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
