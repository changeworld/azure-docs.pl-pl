---
title: Tworzenie pulpitu nawigacyjnego do klasyfikacji danych kondycji za pomocą usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć pulpit nawigacyjny klasyfikacji danych kondycji przy użyciu szablonów aplikacji usługi Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021648"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Samouczek: Tworzenie pulpitu nawigacyjnego dostawcy usługi Power BI



Podczas tworzenia rozwiązania do ciągłego monitorowania pacjenta można również utworzyć pulpit nawigacyjny dla zespołu opieki szpitalej w celu wizualizacji danych pacjentów. W tym samouczku dowiesz się, jak utworzyć pulpit nawigacyjny przesyłania strumieniowego w czasie rzeczywistym usługi Power BI z szablonu aplikacji ciągłego monitorowania pacjentów usługi IoT Central.

>[!div class="mx-imgBorder"]
>![Panel GIF](media/dashboard-gif-3.gif)

Podstawowa architektura będzie podążać za tą strukturą:

>[!div class="mx-imgBorder"] 
>![Pulpit nawigacyjny klasyfikacji dostawców](media/dashboard-architecture.png)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Eksportowanie danych z usługi Azure IoT Central do usługi Azure Event Hubs
> * Konfigurowanie zestawu danych przesyłania strumieniowego usługi Power BI
> * Łączenie aplikacji logiki z centrum zdarzeń platformy Azure
> * Przesyłanie strumieniowe danych do usługi Power BI z aplikacji Logic
> * Zbuduj pulpit nawigacyjny w czasie rzeczywistym dla pacjentów witach

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Szablon aplikacji ciągłego monitorowania pacjentów usługi Azure IoT Central. Jeśli jeszcze go nie masz, możesz wykonać kroki, aby [wdrożyć szablon aplikacji](overview-iot-central-healthcare.md).

* Obszar nazw usługi Azure [Event Hubs i Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* Aplikacja logiki, która ma uzyskać dostęp do Centrum zdarzeń. Aby uruchomić aplikację logiki za pomocą wyzwalacza usługi Azure Event Hubs, potrzebujesz [pustej aplikacji logiki.](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)

* Konto usługi Power BI. Jeśli jeszcze go nie masz, możesz [utworzyć bezpłatne konto próbne dla usługi Power BI.](https://app.powerbi.com/) Jeśli nie używano wcześniej usługi Power BI, warto przejść przez wprowadzenie do [usługi Power BI](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Konfigurowanie ciągłego eksportowania danych do usługi Azure Event Hubs
Najpierw musisz skonfigurować ciągły eksport danych z szablonu aplikacji Usługi Azure IoT Central do usługi Azure Event Hub w ramach subskrypcji. Można to zrobić, wykonując kroki opisane w tym samouczku Azure IoT Central do [eksportowania do centrów zdarzeń](https://docs.microsoft.com/azure/iot-central/core/howto-export-data). Na potrzeby tego samouczka należy tylko wyeksportować dane telemetryczne.

## <a name="create-a-power-bi-streaming-dataset"></a>Tworzenie zestawu danych przesyłania strumieniowego usługi Power BI

1. Zaloguj się do swojego konta usługi Power BI.

2. W preferowanym obszarze roboczym utwórz nowy zestaw danych przesyłania strumieniowego, wybierając przycisk **+ Utwórz** w prawym górnym rogu paska narzędzi. Należy utworzyć oddzielny zestaw danych dla każdego pacjenta, który ma mieć na pulpicie nawigacyjnym.

    >[!div class="mx-imgBorder"] 
    >![Tworzenie zestawu danych przesyłania strumieniowego](media/create-streaming-dataset.png)

3. Wybierz **interfejs API** dla źródła zestawu danych.

4. Wprowadź **nazwę** (na przykład nazwę pacjenta) dla zestawu danych, a następnie wypełnij wartości ze strumienia. Możesz zobaczyć poniższy przykład na podstawie wartości pochodzących z symulowanych urządzeń w szablonie aplikacji ciągłego monitorowania pacjenta. W przykładzie ma dwóch pacjentów:

    * Teddy Silvers, który ma dane z Smart Knee Brace
    * Yesenia Sanford, która ma dane z Smart Vitals Patch

    >[!div class="mx-imgBorder"] 
    >![Wprowadzanie wartości zestawu danych](media/enter-dataset-values.png)

Aby dowiedzieć się więcej o zestawach danych przesyłania strumieniowego w usłudze Power BI, możesz przeczytać ten dokument podczas [przesyłania strumieniowego w czasie rzeczywistym w usłudze Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Łączenie aplikacji logiki z centrum zdarzeń platformy Azure
Aby połączyć aplikację logiki z usługą Azure Event Hubs, należy postępować zgodnie z instrukcjami opisanymi w tym dokumencie w sprawie [wysyłania zdarzeń za pomocą usługi Azure Event Hubs i usługi Azure Logic Apps.](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action) Oto kilka sugerowanych parametrów:

|Parametr|Wartość|
|---|---|
|Typ zawartości|application/json|
|Interval|3|
|Częstotliwość|Sekunda|

Na końcu tego kroku projektant aplikacji logiki powinien wyglądać następująco:

>[!div class="mx-imgBorder"] 
>![Aplikacje logiki łączą się z Centrum zdarzeń](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Przesyłanie strumieniowe danych do usługi Power BI z aplikacji Logic
Następnym krokiem będzie przeanalizowanie danych pochodzących z centrum zdarzeń w celu przesyłania strumieniowego ich do utworzonych wcześniej zestawów danych usługi Power BI.

1. Zanim będzie można to zrobić, należy zrozumieć ładunek JSON, który jest wysyłany z urządzenia do Centrum zdarzeń. Można to zrobić, patrząc na ten [przykładowy schemat](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) i modyfikując go, aby dopasować schemat lub za pomocą [eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) do sprawdzania wiadomości. Jeśli korzystasz z aplikacji do ciągłego monitorowania pacjentów, twoje wiadomości będą wyglądać następująco:

**Telemetria Smart Vitals Patch**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Inteligentna telemetria klamry kolanowej**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Właściwości**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. Teraz, gdy sprawdziłeś ładunki JSON, wróć do projektanta aplikacji Logika i wybierz **+ Nowy krok**. Wyszukaj i dodaj **zmienną Inicjuj** jako następny krok i wprowadź następujące parametry:

    |Parametr|Wartość|
    |---|---|
    |Nazwa|Nazwa interfejsu|
    |Typ|Ciąg|

    Wybierz pozycję **Zapisz**. 

3. Dodaj kolejną zmienną o nazwie **Treść** z typem jako **ciągiem**. Aplikacja logiki będzie mieć dodane następujące akcje:

    >[!div class="mx-imgBorder"]
    >![Inicjowanie zmiennych](media/initialize-string-variables.png)
    
4. Wybierz **+ Nowy krok** i dodaj akcję **Parse JSON.** Zmień nazwę tej nazwy na **Właściwości analizy**. W przypadku zawartości wybierz pozycję **Właściwości** pochodzące z Centrum zdarzeń. Wybierz **opcję Użyj przykładowego ładunku, aby wygenerować schemat** u dołu, i wklej ładunek próbki z sekcji Właściwości powyżej.

5. Następnie wybierz akcję **Ustaw zmienną** i zaktualizuj zmienną **Nazwa interfejsu** o nazwę **interfejsu iothub** z przeanalizowanych właściwości JSON.

6. Dodaj **formant podziału** jako następną akcję i wybierz zmienną **Nazwa interfejsu** jako parametr On. Użyjesz tego do ścieżki danych do właściwego zestawu danych.

7. W aplikacji Azure IoT Central znajdź nazwę interfejsu dla danych kondycji poprawki Smart Vitals i dane kondycji inteligentnej klamry kolanowej w widoku **Szablony urządzeń.** Utwórz dwa różne przypadki dla **kontrolki Przełączania** dla każdej nazwy interfejsu i zmień odpowiednią zmianę nazwy formantu. Można ustawić domyślną sprawę, aby użyć **kontrolki Zakończ** i wybrać stan, który chcesz pokazać.

    >[!div class="mx-imgBorder"] 
    >![Sterowanie dzieleniem](media/split-by-interface.png)

8. W przypadku **poprawki Smart Vitals** dodaj akcję **Parse JSON.** W przypadku zawartości wybierz pozycję **Zawartość** pochodząca z Centrum zdarzeń. Skopiuj i wklej przykładowe ładunki dla poprawki Smart Vitals powyżej, aby wygenerować schemat.

9. Dodaj akcję **ustaw zmienną** i zaktualizuj zmienną **Body** z **treścią** z analizowanego JSON w kroku 7.

10. Dodaj **formant stanu** jako następną akcję i ustaw warunek na **Ciało**, **zawiera**, **HeartRate**. Dzięki temu przed zapełnieniem zestawu danych usługi Power BI masz odpowiedni zestaw danych pochodzących z poprawki Smart Vitals Patch. Kroki 7-9 będą wyglądać następująco:

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals dodaje stanu](media/smart-vitals-pbi.png)

11. W przypadku **prawdziwego** przypadku warunku dodaj akcję, która wywołuje **dodawanie wierszy do** zestawu danych power bi. W tym celu musisz zalogować się do usługi Power BI. Twoja **fałszywa** sprawa może ponownie użyć **Terminate** kontroli.

12. Wybierz odpowiedni **obszar roboczy,** **zestaw danych**i **tabelę**. Mapuj parametry określone podczas tworzenia zestawu danych przesyłania strumieniowego w usłudze Power BI na przeanalizowane wartości JSON pochodzące z centrum zdarzeń. Wypełnione działania powinny wyglądać następująco:

    >[!div class="mx-imgBorder"] 
    >![Dodawanie wierszy do usługi Power BI](media/add-rows-yesenia.png)

13. W przypadku przełącznika **Smart Knee Brace** dodaj akcję **Parse JSON,** aby przeanalizować zawartość, podobnie jak krok 7. Następnie **dodaj wiersze do zestawu danych,** aby zaktualizować zestaw danych Teddy Silvers w usłudze Power BI.

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals dodaje stanu](media/knee-brace-pbi.png)

14. Naciśnij **klawisz Zapisz,** a następnie uruchom aplikację Logika.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Zbuduj pulpit nawigacyjny w czasie rzeczywistym dla pacjentów witach
Teraz wróć do usługi Power BI i wybierz pozycję **+ Utwórz,** aby utworzyć nowy **pulpit nawigacyjny**. Nadaj swojemu pulpitowi nawigacyjnemu nazwę i naciśnij pozycję **Utwórz**.

Zaznacz trzy kropki na górnym pasku nawigacyjnym, a następnie wybierz polecenie **+ Dodaj kafelek**.

>[!div class="mx-imgBorder"] 
>![Dodawanie kafelka do pulpitu nawigacyjnego](media/add-tile.png)

Wybierz typ kafelka, który chcesz dodać, i dostosuj aplikację, jak chcesz.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń zasoby, wykonując następujące czynności:

1. Z witryny Azure Portal można usunąć utworzone zasoby Centrum zdarzeń i aplikacji logiki.

2. W przypadku aplikacji IoT Central przejdź do karty Administracja i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [wytycznymi dotyczącymi architektury ciągłego monitorowania pacjentów](concept-continuous-patient-monitoring-architecture.md).
