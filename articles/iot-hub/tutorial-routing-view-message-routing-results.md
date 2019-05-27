---
title: Wyświetlanie usługi Azure IoT Hub komunikatów wyniki dotyczące tras (.NET) | Dokumentacja firmy Microsoft
description: Wyświetl wyniki dotyczące tras komunikatów usługi Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 1417ecdaf6a85f491e1accfb9564e27d15e13445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162764"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Samouczek: Część 2 — Wyświetl komunikaty trasowane

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Reguły routingu komunikatów

Są to reguły do rozsyłania wiadomości; zostały one skonfigurowane w części 1 tego samouczka, a zobaczysz ich działania w drugiej części.

|value |Wynik|
|------|------|
|level=„storage” |Zapis w usłudze Azure Storage.|
|level=„critical” |Zapis w kolejce usługi Service Bus. Aplikacja logiki pobiera komunikat z kolejki i używa usługi Office 365 do wysłania go pocztą e-mail.|
|domyślny |Wyświetlanie tych danych przy użyciu usługi Power BI.|

Teraz możesz tworzyć zasoby, do których będą kierowane, uruchamiania aplikacji w celu wysyłania komunikatów do Centrum wiadomości i zobacz routing w działaniu.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki  

Kolejka usługi Service Bus jest używana do odbierania komunikatów oznaczonych jako krytyczne. Skonfiguruj aplikację logiki, aby monitorować kolejkę usługi Service Bus i wysyłać wiadomość e-mail po dodaniu komunikatu do kolejki.

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **+ Utwórz zasób**. W polu wyszukiwania wpisz ciąg **aplikacja logiki** i naciśnij klawisz Enter. Z listy wyników wyszukiwania jest wyświetlane, wybierz aplikację logiki, a następnie wybierz **Utwórz** w dalszym ciągu **tworzenie aplikacji logiki** okienka. Wypełnij pola.

   **Nazwa**: To pole zawiera nazwę aplikacji logiki. W tym samouczku jest używana nazwa **ContosoLogicApp**.

   **Subskrypcja**: Wybierz swoją subskrypcję platformy Azure.

   **Grupa zasobów**: Wybierz **Użyj istniejącej** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

   **Lokalizacja**: Użyj swojej lokalizacji. W tym samouczku jest używany region **Zachodnie stany USA**.

   **Log Analytics**: Ten przełącznik powinien być wyłączony.

   ![Ekran tworzenia aplikacji logiki](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Wybierz pozycję **Utwórz**.

2. Teraz przejdź do aplikacji logiki. Najprostszym sposobem, aby uzyskać dostęp do aplikacji logiki jest wybranie **grup zasobów**, wybierz grupę zasobów (w tym samouczku **ContosoResources**), następnie wybierz aplikację logiki z listy zasobów. Zostanie wyświetlona strona Projektanta aplikacji usługi Logic Apps (w celu wyświetlenia całej strony może być konieczne przewinięcie w prawo). Na stronie Projektant aplikacji logiki, przewiń w dół aż pojawi się Kafelek, który jest wyświetlany komunikat **pusta aplikacja logiki +** i wybierz ją. Kartę domyślną jest "Dla użytkownika". Jeśli to okienko jest pusty, wybierz **wszystkich** Aby wyświetlić wszystkie łączniki i wyzwalacze dostępne.

3. Wybierz **usługi Service Bus** z listy łączników.

   ![Lista łączników](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Zostanie wyświetlona lista wyzwalaczy. Wybierz **gdy wiadomość zostaje odebrana w kolejce (Automatyczne zakończenie) / usługi Service Bus**.

   ![Na liście wyzwalaczy dla usługi Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Na następnym ekranie wprowadź nazwę połączenia. W tym samouczku jest używana nazwa **ContosoConnection**.

   ![Konfigurowanie połączenia dla kolejki usługi Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Wybierz przestrzeń nazw usługi Service Bus. W tym samouczku jest używana nazwa **ContosoSBNamespace**. Po wybraniu przestrzeni nazw portal wykonuje zapytanie w przestrzeni nazw usługi Service Bus w celu pobrania kluczy. Wybierz **RootManageSharedAccessKey** i wybierz **Utwórz**.

   ![Trwa kończenie konfigurowania połączenia](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Na następnym ekranie wybierz nazwę kolejki (w tym samouczku jest używana kolejka **contososbqueue**) z listy rozwijanej. W pozostałych polach można użyć wartości domyślnych.

   ![Opcje kolejki](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Teraz skonfiguruj akcję wysyłania wiadomości e-mail po odebraniu komunikatu w kolejce. W Projektancie aplikacji logiki, wybierz **+ nowy krok** Aby dodać krok, następnie wybierz pozycję **wszystkich** Aby wyświetlić wszystkie dostępne opcje. W **wybierz akcję** okienko, Znajdź i wybierz **usługi Office 365 Outlook**. Na ekranie wyzwalaczy wybierz **Wyślij wiadomość e-mail / Office 365 Outlook**.  

   ![Opcje usługi Office 365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Zaloguj się do swojego konta usługi Office 365, aby skonfigurować połączenie. Jeśli to upłynie limit czasu, po prostu spróbuj ponownie. Podaj adresy e-mail adresatów wiadomości e-mail. Określ temat, a następnie wpisz treść wiadomości, którą ma zobaczyć adresat. Na potrzeby testowania wprowadź własny adres e-mail jako adresata.

   Wybierz **Dodaj zawartość dynamiczną** do wyświetlenia zawartości z komunikatu, który może zawierać. Wybierz pozycję **Zawartość** — będzie ona obejmować komunikat z wiadomości e-mail.

   ![Opcje poczty e-mail dla aplikacji logiki](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Wybierz pozycję **Zapisz**. Następnie zamknij Projektanta aplikacji logiki.

## <a name="set-up-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics

Aby wyświetlić dane w wizualizacji usługi Power BI, najpierw skonfiguruj zadanie usługi Stream Analytics w celu pobrania danych. Pamiętaj, że tylko komunikaty, w których właściwość **level** ma wartość **normal**, są wysyłane do domyślnego punktu końcowego i zostaną pobrane przez zadanie usługi Stream Analytics na potrzeby wizualizacji usługi Power BI.

### <a name="create-the-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Utwórz zasób** > **Internet of Things** > **zadania usługi Stream Analytics**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: Nazwa zadania. Nazwa musi być unikatowa w skali globalnej. W tym samouczku jest używana nazwa **contosoJob**.

   **Subskrypcja**: Subskrypcja platformy Azure, używane dla tego samouczka.

   **Grupa zasobów**: Użyj grupy zasobów używanej już w centrum IoT Hub. W tym samouczku jest używana grupa **ContosoResources**.

   **Lokalizacja**: Użyj lokalizacji stosowanej już w skrypcie konfiguracji. W tym samouczku jest używany region **Zachodnie stany USA**.

   ![Utwórz zadanie usługi stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Wybierz **Utwórz** do utworzenia zadania. Aby wrócić do zadania, wybierz pozycję **grup zasobów**. W tym samouczku jest używana grupa **ContosoResources**. Wybierz grupę zasobów, a następnie wybierz zadanie usługi Stream Analytics na liście zasobów.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

4. W obszarze **topologia zadań**, wybierz opcję **dane wejściowe**.

5. W **dane wejściowe** okienku wybierz **Dodaj wejście strumienia** i wybierz Centrum IoT Hub. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias danych wejściowych**: W tym samouczku jest używany alias **contosoinputs**.

   **Wybierz Centrum IoT Hub z subskrypcji**: Wybierz tę opcję przycisku radiowego.

   **Subskrypcja**: Wybierz subskrypcję platformy Azure, używane na potrzeby tego samouczka.

   **IoT Hub**: Wybierz centrum usługi IoT Hub. W tym samouczku jest używane centrum **ContosoTestHub**.

   **Punkt końcowy**: Wybierz pozycję **Obsługa komunikatów**. (W przypadku wybrania pozycji Monitorowanie operacji otrzymasz dane telemetryczne dotyczące centrum IoT Hub, a nie wysyłane dane). 

   **Nazwa zasad dostępu współdzielonego**: Wybierz pozycję **iothubowner**. Portal wypełni klucz wstępny zasad dostępu współdzielonego za Ciebie.

   **Grupy użytkowników**: Wybierz grupy odbiorców ustawione w kroku 1 w tym samouczku. W tym samouczku jest używana grupa **contosoconsumers**.
   
   W pozostałych polach zaakceptuj wartości domyślne. 

   ![Konfigurowanie danych wejściowych dla zadania usługi stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **topologia zadań**, wybierz opcję **dane wyjściowe**.

2. W **dane wyjściowe** okienku wybierz **Dodaj**, a następnie wybierz pozycję **usługi Power BI**. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias danych wyjściowych**: Unikatowy alias danych wyjściowych. W tym samouczku jest używany alias **contosooutputs**. 

   **Nazwa zestawu danych**: Nazwa zestawu danych do użycia w usłudze Power BI. W tym samouczku jest używany zestaw danych **contosodataset**. 

   **Nazwa tabeli**: Nazwa tabeli do użycia w usłudze Power BI. W tym samouczku jest używana tabela **contosotable**.

   Zaakceptuj wartości domyślne w pozostałych polach.

3. Wybierz **Autoryzuj**i zaloguj się do konta usługi Power BI. (Może to potrwać więcej niż jedna próba.)

   ![Konfigurowanie danych wyjściowych dla zadania usługi stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania. W tym samouczku jest używany alias **contosoinputs**.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania. W tym samouczku jest używany alias **contosooutputs**.

   ![Ustawienie kwerendy dla zadania usługi stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Wybierz pozycję **Zapisz**.

5. Zamknij okienko Zapytanie. Możesz powrócić do widoku zasobów w grupie zasobów. Wybierz zadanie usługi Stream Analytics. W tym samouczku nosi ono nazwę **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W ramach zadania usługi Stream Analytics wybierz **Start** > **teraz** > **Start**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

Do skonfigurowania raportu usługi Power BI potrzebujesz danych, dlatego po utworzeniu urządzenia i uruchomieniu aplikacji do symulacji urządzenia skonfigurujesz usługę Power BI.

## <a name="run-simulated-device-app"></a>Uruchamianie aplikacji symulowanego urządzenia

W części 1 tego samouczka możesz Konfigurowanie urządzenia, aby zasymulować, korzystanie z urządzenia IoT. W tej sekcji, możesz pobrać aplikacji konsolowej .NET, która symuluje urządzenie wysyłanie komunikatów z urządzenia do chmury do Centrum IoT, przy założeniu, że już nie został pobrany z aplikacji i zasobów w części 1 tego samouczka.

Ta aplikacja wysyła wiadomości dla każdej z metod routingu inny komunikat. Dostępna jest również folder do pobrania, który zawiera pełny szablon usługi Azure Resource Manager i pliku parametrów, a także wiersza polecenia platformy Azure i skryptów programu PowerShell.

Jeśli pliki nie zostały pobrane z repozytorium w kroku 1 procedury w tym samouczku, przejdź dalej i pobierać je z [symulacji urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Wybranie tego łącza spowoduje pobranie repozytorium z kilku aplikacji rozwiązania, którego szukasz, jest iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Kliknij dwukrotnie plik rozwiązania (IoT_SimulatedDevice.sln), aby otworzyć kod w programie Visual Studio, a następnie otwórz plik Program.cs. Zastąp element `{iot hub hostname}` nazwą hosta centrum IoT Hub. Format nazwy hosta centrum IoT Hub to **{nazwa-centrum-iot}.azure-devices.net**. W tym samouczku jest używana nazwa hosta centrum **ContosoTestHub.azure-devices.net**. Następnie zastąp ciąg `{device key}` kluczem urządzenia, który został wcześniej zapisany podczas konfigurowania urządzenia symulowanego. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Uruchamianie i testowanie

Uruchom aplikację konsolową. Zaczekaj kilka minut. Możesz zobaczyć komunikaty wysyłane na ekranie konsoli aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Poziom jest losowo przypisywany jako `critical` lub `storage`, co powoduje kierowanie komunikatu do konta magazynu lub do kolejki usługi Service Bus (która wyzwala aplikację logiki, aby wysłać wiadomość e-mail). Odczyty domyślne (`normal`) zostaną wyświetlone w raporcie analizy biznesowej konfigurowanym w kolejnym kroku.

Jeśli wszystko zostanie prawidłowo skonfigurowane, w tym momencie powinny być widoczne następujące wyniki:

1. Możesz zacząć otrzymywać wiadomości e-mail dotyczące komunikatów o krytycznym znaczeniu.

   ![Wynikowy wiadomości e-mail](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Ten wynik oznacza, że spełnione są następujące instrukcje. 

   * Routing do kolejki usługi Service Bus działa poprawnie.
   * Pobieranie komunikatu z kolejki usługi Service Bus do aplikacji logiki działa poprawnie.
   * Łącznik aplikacji logiki do programu Outlook działa poprawnie. 

2. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **grup zasobów** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. Wybierz konto magazynu, wybierz pozycję **obiektów blob**, i wybierz kontener. W tym samouczku jest używany kontener **contosoresults**. Powinien pojawić się folder, w którym będzie można przechodzić do szczegółów katalogów do momentu wyświetlenia co najmniej jednego pliku. Otwórz jeden z tych plików; zawierają one wpisy kierowane do konta magazynu. 

   ![Pliki wyników w magazynie](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Ten wynik oznacza, że następująca instrukcja ma wartość true.

   * Routing do konta magazynu działa poprawnie.

Teraz — przy nadal działającej aplikacji — skonfiguruj wizualizację usługi Power BI, aby widzieć komunikaty przechodzące przez trasę domyślną.

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurowanie wizualizacji usługi Power BI

1. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/).

2. Przejdź do obszaru **Obszary robocze** i wybierz obszar roboczy ustawiony podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana nazwa **Mój obszar roboczy**. 

3. Wybierz **zestawów danych**. Jeśli nie masz żadnych zestawów danych, zaczekaj kilka minut, a następnie sprawdź ponownie.

   Powinien pojawić się wymieniony na liście zestaw danych, który został wybrany podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używany zestaw danych **contosodataset**. (Wyświetlenie zestawu danych po raz pierwszy może potrwać 5–10 minut).

4. W obszarze **akcje**, zaznacz pierwszą ikonę, aby utworzyć raport.

   ![Obszar roboczy usługi BI zasilania z wyróżnioną ikoną działania i raportów](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   * Na stronie tworzenia raportu należy dodać wykres liniowy, wybierając ikonę wykresu liniowego.

     ![Wizualizacje i pola](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana tabela **contosotable**.

   * Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   * Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

   Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

6. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby skonfigurować drugi wykres, wykonaj te same kroki, co powyżej, i umieść element **EventEnqueuedUtcTime** na osi X, a element **humidity** (Wilgotność) na osi Y.

   ![Końcowe raportu usługi Power BI przy użyciu dwóch wykresów](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Wybierz **Zapisz** Aby zapisać raport.

Powinny być widoczne dane na obydwu wykresach. Ten wynik oznacza, że spełnione są następujące instrukcje:

   * Routing do domyślnego punktu końcowego działa poprawnie.
   * Zadanie usługi Azure Stream Analytics jest poprawnie przesyłane strumieniowo.
   * Wizualizacja usługi Power BI została prawidłowo skonfigurowana.

Można odświeżać wykresy, aby zobaczyć najnowsze dane, wybierając przycisk odświeżania w górnej części okna usługi Power BI. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli chcesz usunąć wszystkie zasoby, które zostały utworzone za pomocą obie części tego samouczka, Usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W naszym przypadku nastąpi usunięcie centrum IoT Hub, przestrzeni nazw i kolejki usługi Service Bus, aplikacji logiki, konta magazynu i samej grupy zasobów. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Oczyszczanie zasobów w wizualizacji usługi Power BI

Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/). Przejdź do swojego obszaru roboczego. W tym samouczku jest używana nazwa **Mój obszar roboczy**. Aby usunąć wizualizacji usługi Power BI, przejdź do zestawów danych, a następnie wybierz Kosz można ikonę, aby usunąć zestaw danych. W tym samouczku jest używany zestaw danych **contosodataset**. Podczas usuwania zestawu danych usuwany jest również raport.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Użyj wiersza polecenia platformy Azure, aby wyczyścić zasoby

Aby usunąć grupę zasobów, użyj polecenia [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` ustawiono **ContosoResources** ponownie na początku tego samouczka.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Aby wyczyścić zasoby przy użyciu programu PowerShell

Aby usunąć grupę zasobów, użyj polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` ustawiono **ContosoResources** ponownie na początku tego samouczka.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku 2-częściowych pokazaliśmy ci, jak używać routing komunikatów do rozsyłania komunikatów usługi IoT Hub do różnych miejsc docelowych, wykonując następujące czynności.  

**Część I: Tworzenie zasobów, skonfigurować routing wiadomości**
> [!div class="checklist"]
> * Tworzenie zasobów — Centrum IoT hub, konto magazynu, kolejki usługi Service Bus i symulowanego urządzenia.
> * Punkty końcowe i skonfigurować trasy wiadomości w usłudze IoT Hub dla konta magazynu i kolejki usługi Service Bus.

**Part II: Wysyłanie komunikatów do Centrum, wyświetlanie wyników trasowane**
> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która jest wyzwalana i wysyła wiadomość e-mail po dodaniu komunikatu do kolejki usługi Service Bus.
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum przy użyciu różnych opcji routingu.
> * Tworzenie wizualizacji usługi Power BI na potrzeby danych wysyłanych do domyślnego punktu końcowego.
> * Przeglądanie wyników...
> * ...w kolejce usługi Service Bus i wiadomościach e-mail.
> * ...na koncie magazynu.
> * ...w wizualizacji usługi Power BI.

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Konfigurowanie i używanie metryk i diagnostyki w usłudze IoT Hub](tutorial-use-metrics-and-diags.md)
