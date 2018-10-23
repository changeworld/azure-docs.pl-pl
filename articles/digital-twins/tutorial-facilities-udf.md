---
title: Monitorowanie przestrzeni za pomocą usługi Azure Digital Twins | Microsoft Docs
description: Dowiedz się, jak aprowizować zasoby przestrzenne i monitorować warunki pracy za pomocą usługi Azure Digital Twins, wykonując czynności opisane w tym samouczku.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1e5cb18b4e526cd0a0607f5bc93788fcf07430e1
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364239"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Samouczek: aprowizowanie budynku i monitorowanie warunków pracy za pomocą usługi Azure Digital Twins

W tym samouczku przedstawiono, jak za pomocą usługi Azure Digital Twins monitorować przestrzenie pod kątem odpowiedniej temperatury i poziomu komfortu. Po [skonfigurowaniu przykładowego budynku](tutorial-facilities-setup.md) możesz zaaprowizować swój budynek i uruchamiać funkcje niestandardowe na danych z czujników, wykonując czynności opisane w tym samouczki.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Definiowanie warunków, które mają być monitorowane
> * Tworzenie funkcji zdefiniowanej przez użytkownika
> * Symulowanie danych z czujników
> * Pobieranie wyników funkcji zdefiniowanej przez użytkownika

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [skonfigurowano instalator usługi Azure Digital Twins](tutorial-facilities-setup.md). Przed kontynuowaniem upewnij się, że masz:
- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uruchomione wystąpienie usługi Digital Twins. 
- Pobrane i wyodrębnione [przykłady usługi Digital Twins w języku C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) na swoim komputerze służbowym. 
- [Zestaw SDK .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim w celu skompilowania i uruchomienia przykładu. Uruchom polecenie `dotnet --version`, aby sprawdzić, czy zainstalowano prawidłową wersję. 
- Program [Visual Studio Code](https://code.visualstudio.com/) umożliwiający eksplorowanie przykładowego kodu. 

## <a name="define-conditions-to-monitor"></a>Definiowanie warunków, które mają być monitorowane
Możesz zdefiniować zestaw określonych warunków nazywanych elementami **matcher**, które mają być monitorowane w urządzeniu lub w danych z czujników. Możesz zdefiniować funkcje nazywane *funkcjami zdefiniowanymi przez użytkownika*, której wykonują logikę niestandardową na danych pochodzących z przestrzeni oraz urządzeń, gdy wystąpią warunki określone w elementach matcher. Aby uzyskać więcej informacji, przeczytaj [Przetwarzanie danych i funkcje zdefiniowane przez użytkownika](concepts-user-defined-functions.md). 

W przykładowym projekcie **_occupancy-quickstart_** otwórz plik **_src\actions\provisionSample.yaml_** w programie Visual Studio Code. Zwróć uwagę na sekcję, która zaczyna się od typu **matchers**. Każdy wpis w ramach tego typu tworzy element matcher o określonej **nazwie**, który będzie monitorował czujnik typu **dataTypeValue**. Zwróć uwagę, że jest on związany z przestrzenią o nazwie *Focus Room A1*, w której znajduje się węzeł **devices** zawierający kilka **czujników**. Aby aprowizować element matcher, który będzie śledzić jeden z tych czujników, jego typ **dataTypeValue** powinien być zgodny z typem **dataType** czujnika. 

Dodaj następujący element matcher pod istniejącymi elementami matcher i upewnij się, że klucze są wyrównane, a spacje nie są zastąpione tabulatorami:

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Spowoduje to śledzenie czujnika *SAMPLE_SENSOR_TEMPERATURE*, który dodano w [pierwszym samouczku](tutorial-facilities-setup.md). Pamiętaj, że te wiersze są również obecne w pliku *provisionSample.yaml* jako wiersze oznaczone jako komentarze; możesz po prostu usunąć komentarz, usuwając znak „#” na początku każdego wiersza. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Tworzenie funkcji zdefiniowanej przez użytkownika
Funkcje zdefiniowane przez użytkownika (UDF) umożliwiają dostosowanie sposobu przetwarzania danych z czujników. Są one niestandardowym kodem JavaScript, który można uruchamiać w ramach wystąpienia usługi Digital Twins, gdy wystąpią warunki określone w elementach matcher. Możesz utworzyć elementy *matcher* i *funkcje zdefiniowane przez użytkownika* dla każdego czujnika, którego chcesz monitorować. Aby uzyskać więcej szczegółowych informacji, przeczytaj artykuł [Przetwarzanie danych i funkcje zdefiniowane przez użytkownika](concepts-user-defined-functions.md). 

W przykładowym pliku *provisionSample.yaml* znajdź sekcję zaczynającą się od typu **userdefinedfunctions**. W tej sekcji aprowizowana jest funkcja zdefiniowana przez użytkownika o określonej **nazwie**, która działa na listę elementów matcher w obszarze **matcherNames**. Zauważ, że możesz podać własny plik JavaScript dla funkcji UDF jako **skrypt**. Zwróć również uwagę na sekcję o nazwie **roleassignments**. Przypisuje ona rolę *administratora przestrzeni* do funkcji zdefiniowanej przez użytkownika, aby umożliwić dostęp do zdarzeń pochodzących z dowolnej z zaaprowizowanych przestrzeni. 

1. Skonfiguruj funkcję UDF, aby zawierała element matcher temperatury, dodając następujący wiersz `matcherNames` (lub usuwając komentarz) w węźle *pliku provisionSample.yaml*:

    ```yaml
            - Matcher Temperature
    ```

1. Otwórz plik **_src\actions\userDefinedFunctions\availability.js_**  w edytorze. Jest to plik, do którego odwołuje się element **script** pliku *provisionSample.yaml*. Funkcja zdefiniowana przez użytkownika w tym pliku wyszukuje warunki, gdy w pomieszczeniu nie jest wykrywany ruch, a poziom dwutlenku węgla jest niższy niż 1000 ppm. Zmodyfikuj plik JavaScript, aby oprócz innych warunków monitorowana była też temperatura. Dodaj następujące wiersze kodu, aby wyszukiwać warunki, gdy w pomieszczeniu nie jest wykrywany ruch, poziom dwutlenku węgla jest niższy niż 1000 ppm, a temperatura jest niższa niż 78 stopni Fehrenheita (25,5 stopnia Celsjusza).

   > [!NOTE]
   > W tej sekcji jest modyfikowany plik *src\actions\userDefinedFunctions\availability.js*, aby umożliwić Ci szczegółowe poznanie jednego ze sposobów pisania funkcji zdefiniowanej przez użytkownika. Możesz jednak bezpośrednio użyć pliku [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) w swojej konfiguracji. Ten plik zawiera wszystkie zmiany, które są wymagane do celów tego samouczka. Jeśli w zamian użyjesz tego pliku, pamiętaj, aby użyć prawidłowej nazwy pliku dla klucza **_script_** w pliku [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    1. W górnej części pliku dodaj następujące wiersze dla temperatury pod komentarzem `// Add your sensor type here`:

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. Dodaj następujące wiersze po instrukcji definiującej zmienną `var motionSensor` pod komentarzem `// Add your sensor variable here`:

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. Dodaj następujący wiersz po instrukcji definiującej zmienną `var carbonDioxideValue` pod komentarzem `// Add your sensor latest value here`:

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. Usuń następujące wiersze kodu spod komentarza `// Modify this line to monitor your sensor value`: 

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null) {
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
                return;
            }
        ```
       
       Zastąp je z następującymi wierszami:

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
                return;
            }
        ```
    
    1. Usuń następujące wiersze kodu spod komentarza `// Modify these lines as per your sensor`:

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       Zastąp je z następującymi wierszami:

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. Usuń następujące blok kodu *if-else* po komentarzu `// Modify this code block for your sensor`:

        ```JavaScript
            // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
            if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
                log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(availableFresh));
            }
            else {
                log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
            }
        ```
      
       Zastąp go następującym blokiem *if-else*:

        ```JavaScript
            // If sensor values are within range and room is available
            if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
                log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

                // Set up notification for this alert
                parentSpace.Notify(JSON.stringify(alert));
            }
            else {
                log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);
    
                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
            }
        ```
        
        Zmodyfikowana funkcja UDF będzie wyszukiwała warunek, w którym pomieszczenie jest dostępne, a poziom dwutlenku węgla i temperatura mieszczą się w dopuszczalnych limitach. Wygeneruje ona powiadomienie przy użyciu instrukcji `parentSpace.Notify(JSON.stringigy(alert));`, gdy ten warunek zostanie spełniony. Ustawi wartość monitorowanej przestrzeni niezależnie od tego, czy warunek zostanie spełniony, i wyświetli odpowiedni komunikat.
    
    1. Zapisz plik. 
    
1. Otwórz okno polecenia i przejdź do folderu **_occupancy-quickstart\src_**. Uruchom następujące polecenie, aby zaaprowizować wykres analizy przestrzennej i funkcję zdefiniowaną przez użytkownika. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Aby uniemożliwić nieautoryzowany dostęp do interfejsu API zarządzania usługą Digital Twins, aplikacja **_occupancy-quickstart_** wymaga zalogowania się przy użyciu poświadczeń konta platformy Azure. Zapisuje ona Twoje poświadczenia na pewien okres czasu, aby logowanie nie było konieczne przy każdym uruchomieniu. Przy pierwszym uruchomieniu tego programu, a także po wygaśnięciu zapisanych poświadczeń, przekieruje Cię do strony logowania i poda kod specyficzny dla sesji, który należy wprowadzić na tej stronie. Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure.


1. Po uwierzytelnieniu konta aplikacja rozpocznie tworzenie przykładowego wykresu przestrzennego zgodnie z konfiguracją w pliku *provisionSample.yaml*. Zaczekaj na ukończenie aprowizowania; potrwa to kilka minut. Po zakończeniu przyjrzyj się komunikatom w oknie polecenia i spójrz na utworzony wykres przestrzenny. Zauważ, że utworzone zostało centrum IoT w węźle głównym lub w lokalizacji `Venue`. 

1. Z danych wyjściowych w oknie polecenia skopiuj wartość elementu `ConnectionString` w sekcji `Devices` do schowka. Ta wartość będzie potrzebna do symulowania połączenia urządzenia w następnej sekcji.

    ![Przykład aprowizacji](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Jeśli podczas aprowizowania zostanie wyświetlony komunikat podobny do następującego „Operacja We/Wy została przerwana z powodu zakończenia wątku lub żądania aplikacji”, spróbuj ponownie uruchomić polecenie. Może się to zdarzyć, jeśli klient HTTP przekroczy limit czasu ze względu na problem z siecią.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Symulowanie danych z czujników
W tej sekcji użyjesz projektu o nazwie *device-connectivity* zawartego w przykładzie do symulowania danych z czujnika dotyczących wykrywania ruchu, temperatury i dwutlenku węgla. Ten projekt generuje losowe wartości dla czujników i wysyła je do centrum IoT przy użyciu parametrów połączenia urządzenia.

1. W osobnym oknie polecenia przejdź do przykładu usługi Digital Twins, a następnie do folderu **_device-connectivity_**.

1. Uruchom następujące polecenie, aby upewnić się, że zależności dla projektu są prawidłowe:

    ```cmd/sh
    dotnet restore
    ```

1. Otwórz plik *appSettings.json* w edytorze i zmodyfikuj następujące wartości:
    1. *DeviceConnectionString*: przypisz wartość `ConnectionString` w oknie danych wyjściowych z poprzedniej sekcji. Pamiętaj, że trzeba skopiować cały ciąg między cudzysłowami, aby symulator poprawnie nawiązał połączenie z centrum IoT.

    1. *HardwareId* w tablicy *Sensors*: ponieważ symulujesz zdarzenia z czujników zaaprowizowanych w wystąpieniu usługi Digital Twins, identyfikator sprzętu i nazwy czujników w tym pliku powinny być zgodne z węzłem `sensors` zaaprowizowanego pliku *provisionSample.yaml*. Dodaj nowy wpis dla czujnika temperatury; węzeł **Sensors** w pliku *appSettings.json* powinien wyglądać następująco:

        ```JSON
        "Sensors": [{
          "DataType": "Motion",
          "HardwareId": "SAMPLE_SENSOR_MOTION"
        },{
          "DataType": "CarbonDioxide",
          "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
        },{
          "DataType": "Temperature",
          "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
        }]
        ```

1. Uruchom następujące polecenie, aby rozpocząć symulację zdarzeń urządzeń dotyczących temperatury, ruchu i dwutlenku węgla:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE] 
   > Ponieważ przykładowa symulacja nie komunikuje się bezpośrednio z wystąpieniem usługi Digital Twins, uwierzytelnianie nie jest konieczne.

## <a name="get-results-of-user-defined-function"></a>Pobieranie wyników funkcji zdefiniowanej przez użytkownika
Funkcja zdefiniowana przez użytkownika jest uruchamiana za każdym razem, gdy wystąpienie odbiera dane z urządzeń i czujników. W tej sekcji jest wykonywane zapytanie względem wystąpienia usługi Digital Twins w celu uzyskania wyników funkcji zdefiniowanej przez użytkownika. Zobaczysz niemal w czasie rzeczywistym, czy pomieszczenie jest dostępne, powietrze czyste, a temperatura odpowiednia. 

1. Otwórz okno polecenia, którego użyto do aprowizacji przykładu, lub nowe okno polecenia i ponownie przejdź do folderu **_occupancy-quickstart\src_** w przykładzie. 

1. Uruchom następujące polecenie i po pojawieniu się monitu zaloguj się:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

W oknie danych wyjściowych będzie widoczne wykonywanie funkcji zdefiniowanej przez użytkownika oraz przechwytywanie zdarzeń z symulacji urządzenia. 

   ![Wykonanie funkcji zdefiniowanej przez użytkownika](./media/tutorial-facilities-udf/udf-running.png)

W zależności od tego, czy monitorowany warunek zostanie spełniony, funkcja zdefiniowana przez użytkownika ustawia wartość przestrzeni z odpowiednim komunikatem, jak opisano w [powyższej sekcji](#udf), który funkcja `GetAvailableAndFreshSpaces` wyświetla w konsoli. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](http://portal.azure.com) kliknij przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins i kliknij polecenie **Usuń**.
2. Jeśli będzie to konieczne, możesz usunąć także przykładowe aplikacje na komputerze służbowym. 


## <a name="next-steps"></a>Następne kroki

Teraz po zaaprowizowaniu przestrzeni i utworzeniu platformy do wyzwalania niestandardowych powiadomień możesz przejść do dowolnego z następujących samouczków. 

> [!div class="nextstepaction"]
> [Samouczek: otrzymywanie powiadomień z przestrzeni usługi Azure Digital Twins przy użyciu usługi Logic Apps](tutorial-facilities-events.md)

Lub:

> [!div class="nextstepaction"]
> [Samouczek: wizualizowanie i analizowanie zdarzeń z przestrzeni usługi Azure Digital Twins przy użyciu usługi Time Series Insights](tutorial-facilities-analyze.md)
