---
title: Użyj Centrum IoT zdarzenia wyzwalacza Azure Logic Apps | Dokumentacja firmy Microsoft
description: Za pomocą usługi routingu zdarzeń siatki zdarzeń platformy Azure, Utwórz zautomatyzowanych procesów do wykonywania akcji aplikacje logiki platformy Azure na podstawie zdarzeń Centrum IoT.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 4fed42a45f8d291bd3ba1e4fd5d636b7d0b0fbfc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Wysyłanie powiadomień e-mail o zdarzeniach Centrum IoT Azure za pomocą aplikacji logiki

Azure siatki zdarzeń umożliwia reagowania na zdarzenia w Centrum IoT przez wyzwalają akcje w aplikacjach biznesowych podrzędne.

Ten artykuł przeprowadzi Cię przez konfigurację próbki, która korzysta z Centrum IoT i siatki zdarzeń. Na koniec należy Azure logiki aplikacji zdefiniowano Wyślij wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie dodane do Centrum IoT. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail z każdego dostawcy poczty e-mail, który jest obsługiwany przez usługi Azure Logic Apps, takich jak program Outlook pakietu Office 365, Outlook.com lub Gmail. To konto e-mail służy do wysyłania powiadomień o zdarzeniach. Aby uzyskać pełną listę obsługiwanych łączników aplikacji logiki, zobacz [łączniki — omówienie](https://docs.microsoft.com/connectors/)
* Aktywne konto platformy Azure. Jeśli nie masz, możesz [utworzyć bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/).
* Centrum Iot na platformie Azure. Jeśli nie utworzono jedną jeszcze, zobacz [Rozpoczynanie pracy z Centrum IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) przewodnik. 

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Najpierw należy utworzyć aplikację logiki i dodać wyzwalacz siatki zdarzeń, który monitoruje grupy zasobów dla maszyny wirtualnej. 

### <a name="create-a-logic-app-resource"></a>Utwórz zasób aplikacji logiki


1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **nowy** > **integracji przedsiębiorstwa** > **aplikacji logiki**.

   ![Tworzenie aplikacji logiki](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Nadaj nazwę, która jest unikatowa w ramach subskrypcji aplikację logiki, a następnie wybierz subskrypcji, grupy zasobów i lokalizacji jako centrum IoT. 
3. Jeśli wszystko jest gotowe, wybierz **Przypnij do pulpitu nawigacyjnego**i wybierz polecenie **Utwórz**.

   Teraz został utworzony zasób platformy Azure dla Twojej aplikacji logiki. Po wdrożeniu Twojej aplikacji logiki na platformie Azure projektant aplikacji usługi Logic Apps pokaże szablony dla typowych wzorców, więc możesz szybciej rozpocząć pracę.

   > [!NOTE] 
   > Po wybraniu **Przypnij do pulpitu nawigacyjnego**, aplikację logiki automatycznie otwiera w Projektancie aplikacji logiki. W przeciwnym razie ręcznie znaleźć i otworzyć aplikację logiki.

4. W Projektancie aplikacji logiki w obszarze **szablony**, wybierz **pustą aplikację logiki** , dzięki czemu można utworzyć aplikację logiki od początku.

## <a name="select-a-trigger"></a>Wybierz wyzwalacz

Wyzwalacz jest konkretnego zdarzenia, który uruchamia aplikację logiki. W tym samouczku wyzwalaczy, które powoduje rozpoczęcie przepływu pracy odbiera żądanie za pośrednictwem protokołu HTTP.  

1. W łączniki i wyzwalaczy pasek wyszukiwania wpisz **HTTP**.
2. Wybierz **żądania - zostanie odebrane żądanie HTTP podczas** jako wyzwalacz. 

   ![Wybierz wyzwalacz żądania HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Wybierz **ładunku próbki używany do generowania schematu**. 

   ![Wybierz wyzwalacz żądania HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Wklej poniższy przykładowy kod JSON w polu tekstowym, a następnie wybierz **gotowe**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<IoT hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice",
       "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
       "opType": "DeviceCreated"
     },
     "dataVersion": "",
     "metadataVersion": "1"
   }]
   ```
5. Otrzymasz wyskakujące powiadomienie, które jest wyświetlany komunikat **Pamiętaj, aby uwzględnić nagłówka Content-Type ustawioną application/json w żądaniu.** Można bezpiecznie zignorować ten uwag i przejść do następnej sekcji. 


### <a name="create-an-action"></a>Tworzenie akcji

Akcje są wszystkie kroki, które są wykonywane po wyzwalacz uruchamia przepływ pracy aplikacji logiki. W tym samouczku akcja jest aby wysłać wiadomość e-mail z powiadomieniem przez dostawcę poczty e-mail. 

1. Wybierz **nowy krok** następnie **Dodaj akcję**. 

   ![Nowy krok, Dodaj akcję](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. Wyszukaj **E-mail**. 
3. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. W tym samouczku używana **Office 365 Outlook**. Te kroki dla innych dostawców poczty e-mail są podobne. 

   ![Wybierz łącznik dostawcy poczty e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Wybierz **wysłać wiadomość e-mail** akcji. 
5. W przypadku wyświetlenia monitu zaloguj się do swojego konta poczty e-mail. 
6. Utwórz szablon e-mail. 
   * **Aby**: Wprowadź adres e-mail, aby odbierać powiadomienia e-mail. W tym samouczku Użyj konta e-mail, do której będziesz mieć dostęp do testowania. 
   * **Temat** i **treści**: zapisywanie tekstu dla adresu e-mail. Wybierz właściwości JSON z narzędzia Selektor do zawierać zawartość dynamiczną, na podstawie danych zdarzenia.  

   Szablon wiadomości e-mail może wyglądać następująco:

   ![Wypełnij informacje pocztą e-mail.](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Zapisz aplikację logiki. 

### <a name="copy-the-http-url"></a>Skopiuj adres URL protokołu HTTP

Zanim opuścisz projektanta aplikacji logiki, skopiuj adres URL, nasłuchiwania aplikacje logiki wyzwalacza. Ten adres URL są używane do konfigurowania zdarzeń siatki. 

1. Rozwiń węzeł **zostanie odebrane żądanie HTTP podczas** okno konfiguracji wyzwalacza, klikając go. 
2. Skopiuj wartość **adresu URL HTTP POST** wybierając przycisk Kopiuj obok niej. 

   ![Skopiuj adres URL protokołu HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Zapisz ten adres URL, dzięki czemu mogą odwoływać się do niego w następnej sekcji. 

## <a name="publish-an-event-from-iot-hub"></a>Publikowanie zdarzenia z Centrum IoT

W tej sekcji skonfigurujesz Centrum IoT do publikowania zdarzeń w miarę ich występowania. 

1. W portalu Azure przejdź do Centrum IoT. 
2. Wybierz **zdarzenia**.

   ![Otwórz szczegóły zdarzeń siatki](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Wybierz **subskrypcji zdarzeń**. 

   ![Utwórz nową subskrypcję zdarzeń](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Tworzenie subskrypcji zdarzeń z następującymi wartościami: 
   * **Nazwa**: Podaj opisową nazwę.
   * **Subskrypcja dla wszystkich typów zdarzeń**: Usuń zaznaczenie pola wyboru.
   * **Typy zdarzeń**: Wybierz **DeviceCreated**.
   * **Typ subskrybenta**: Wybierz **haku Web**.
   * **Punkt końcowy subskrybenta**: Wklej adres URL, które zostały skopiowane z aplikacji logiki. 

   Możesz zapisać tutaj subskrypcji zdarzeń i otrzymywać powiadomienia dla wszystkich urządzeń, która jest tworzona w Centrum IoT. W tym samouczku jednak Użyjmy opcjonalne pola do filtrowania dla konkretnych urządzeń: 

   * **Prefiks filtru**: wprowadź `devices/Building1_` do filtrowania dla zdarzeń urządzenia z tworzeniem 1.
   * **Filtr sufiks**: wprowadź `_Temperature` filtrowania dla urządzenia zdarzenia związane z temperatury.

   Gdy wszystko będzie gotowe, formularz powinien wyglądać jak w następującym przykładzie: 

   ![Przykładowe zdarzenie subskrypcji formularza](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Wybierz **Utwórz** można zapisać subskrypcji zdarzeń.

## <a name="create-a-new-device"></a>Tworzenie nowego urządzenia

Przetestuj aplikację logiki, tworząc nowe urządzenie do wyzwolenia zdarzenia powiadomień e-mail. 

1. Wybierz z Centrum IoT, **urządzenia IoT**. 
2. Wybierz pozycję **Dodaj**.
3. Aby uzyskać **identyfikator urządzenia**, wprowadź `Building1_Floor1_Room1_Temperature`.
4. Wybierz pozycję **Zapisz**. 
5. Można dodać wiele urządzeń z innego urządzenia identyfikatorów do testowania filtrów subskrypcji zdarzeń. Wypróbuj te przykłady: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Po dodaniu kilka urządzeń do Centrum IoT, Sprawdź pocztę e-mail, aby zobaczyć, które z nich wyzwalane aplikacji logiki. 

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Zamiast korzystać z portalu Azure, można wykonać kroki Centrum IoT przy użyciu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz strony wiersza polecenia platformy Azure dla [tworzenia subskrypcji zdarzeń](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) i [tworzenia urządzenia IoT](https://docs.microsoft.com/cli/azure/iot/device)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym samouczku użyte zasoby, które naliczenie opłat w ramach subskrypcji platformy Azure. Gdy wszystko będzie gotowe wypróbować samouczek i testowania wyniki, wyłączyć lub usunąć zasoby, które chcesz zachować. 

Jeśli nie chcesz stracić pracy na aplikację logiki, należy ją wyłączyć, zamiast usuwania. 

1. Przejdź do aplikacji logiki.
2. Na **omówienie** wybierz bloku **usunąć** lub **wyłączyć**. 

Każda subskrypcja może mieć jeden bezpłatne Centrum IoT. Jeśli bezpłatne Centrum zostało utworzone w tym samouczku, nie należy ją usunąć, aby uniknąć naliczania opłat.

1. Przejdź do Centrum IoT. 
2. Na **omówienie** wybierz bloku **usunąć**. 

Nawet jeśli zachowasz Centrum IoT może chcesz usunąć utworzoną subskrypcję zdarzeń. 

1. W Centrum IoT, wybierz **siatki zdarzeń**.
2. Wybierz subskrypcję zdarzeń, który chcesz usunąć. 
3. Wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [reaguje na zdarzenia Centrum IoT przy użyciu siatki zdarzeń do akcje wyzwalacza](../iot-hub/iot-hub-event-grid.md).

Dowiedz się więcej o tym, co można zrobić za pomocą [siatki zdarzeń](overview.md).


