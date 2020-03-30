---
title: Rozszerzanie usługi Azure IoT Central o niestandardowe reguły i powiadomienia | Dokumenty firmy Microsoft
description: Jako deweloper rozwiązania skonfiguruj aplikację IoT Central do wysyłania powiadomień e-mail, gdy urządzenie przestaje wysyłać dane telemetryczne. To rozwiązanie używa usługi Azure Stream Analytics, Azure Functions i SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158150"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Rozszerzanie usługi Azure IoT Central o niestandardowe reguły przy użyciu usługi Stream Analytics, Azure Functions i SendGrid



W tym przewodniku przedstawiono, jako deweloper rozwiązania, jak rozszerzyć aplikację IoT Central o niestandardowe reguły i powiadomienia. W przykładzie pokazano wysyłanie powiadomień do operatora, gdy urządzenie przestaje wysyłać dane telemetryczne. Rozwiązanie używa kwerendy [usługi Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) do wykrywania, gdy urządzenie przestało wysyłać dane telemetryczne. Zadanie usługi Stream Analytics używa [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/) do wysyłania wiadomości e-mail z powiadomieniami przy użyciu [sendgrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

W tym przewodniku pokazano, jak rozszerzyć IoT Central poza to, co można już zrobić z wbudowanymi regułami i akcjami.

W tym przewodniku instrukcja, dowiedzieć się, jak:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportowania danych*.
* Utwórz kwerendę usługi Stream Analytics, która wykrywa, kiedy urządzenie przestało wysyłać dane.
* Wyślij powiadomienie e-mail przy użyciu usług Azure Functions i SendGrid.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

### <a name="iot-central-application"></a>Aplikacja IoT Central

Utwórz aplikację IoT Central w witrynie sieci Web [menedżera aplikacji Usługi Azure IoT Central](https://aka.ms/iotcentral) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan cenowy | Standardowa |
| Szablon aplikacji | Analityka w sklepie – monitorowanie stanu |
| Nazwa aplikacji | Zaakceptuj wartość domyślną lub wybierz własną nazwę |
| Adres URL | Zaakceptuj domyślny lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Dzierżawa usługi Azure Active Directory |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region | Twój najbliższy region |

Przykłady i zrzuty ekranu w tym artykule używają regionu **Stany Zjednoczone.** Wybierz lokalizację blisko siebie i upewnij się, że wszystkie zasoby są tworzęne w tym samym regionie.

Ten szablon aplikacji zawiera dwa symulowane urządzenia termostatu, które wysyłają dane telemetryczne.

### <a name="resource-group"></a>Grupa zasobów

Użyj [witryny Azure Portal, aby utworzyć grupę zasobów](https://portal.azure.com/#create/Microsoft.ResourceGroup) o nazwie **DetectStoppedDevices,** aby zawierać inne zasoby, które tworzysz. Utwórz zasoby platformy Azure w tej samej lokalizacji co aplikacja IoT Central.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [witryny Azure Portal, aby utworzyć obszar nazw Centrum zdarzeń](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę obszaru nazw |
| Warstwa cenowa | Podstawowa (Basic) |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | WykrywanieStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="stream-analytics-job"></a>Zadanie usługi Stream Analytics

Użyj [witryny Azure Portal, aby utworzyć zadanie usługi Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę swojej pracy |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | WykrywanieStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Środowisko hostingu | Chmura |
| Jednostki przesyłania strumieniowego | 3 |

### <a name="function-app"></a>Aplikacja funkcji

Użyj [witryny Azure Portal, aby utworzyć aplikację funkcji](https://portal.azure.com/#create/Microsoft.FunctionApp) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa aplikacji    | Wybierz nazwę aplikacji funkcji |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | WykrywanieStoppedDevices |
| System operacyjny | Windows |
| Plan hostingu | Plan zużycia |
| Lokalizacja | Wschodnie stany USA |
| Stos środowiska uruchomieniowego | .NET |
| Magazyn | Tworzenie nowego elementu |

### <a name="sendgrid-account"></a>Konto SendGrid

Użyj [witryny Azure Portal, aby utworzyć konto SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę swojego konta SendGrid |
| Hasło | Tworzenie hasła |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | WykrywanieStoppedDevices |
| Warstwa cenowa | Bezpłatna F1 |
| Informacje kontaktowe | Wypełnij wymagane informacje |

Po utworzeniu wszystkich wymaganych zasobów grupa zasobów **DetectStoppedDevices** wygląda następująco:

![Wykrywanie grupy zasobów zatrzymanych urządzeń](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Aplikację IoT Central można skonfigurować do ciągłego eksportowania danych telemetrycznych do centrum zdarzeń. W tej sekcji utworzysz Centrum zdarzeń, aby odbierać dane telemetryczne z aplikacji IoT Central. Centrum zdarzeń dostarcza dane telemetryczne do zadania usługi Stream Analytics do przetwarzania.

1. W witrynie Azure portal przejdź do obszaru nazw Centrum zdarzeń i wybierz **+ Centrum zdarzeń**.
1. Nazwij centrum zdarzeń **centralexport**i wybierz pozycję **Utwórz**.

Obszar nazw centrum zdarzeń wygląda następująco:

![Przestrzeń nazw usługi Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Pobierz klucz interfejsu API SendGrid

Aplikacja funkcji potrzebuje klucza interfejsu API SendGrid do wysyłania wiadomości e-mail. Aby utworzyć klucz interfejsu API SendGrid:

1. W witrynie Azure portal przejdź do konta SendGrid. Następnie wybierz pozycję **Zarządzaj,** aby uzyskać dostęp do swojego konta SendGrid.
1. Na koncie SendGrid wybierz pozycję **Ustawienia,** a następnie **klawisze INTERFEJSU API**. Wybierz **pozycję Utwórz klucz interfejsu API:**

    ![Utwórz klucz interfejsu API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na stronie **Tworzenie klucza interfejsu API** utwórz klucz o nazwie **AzureFunctionAccess** z uprawnieniami **pełnego dostępu.**
1. Zanotuj klucz interfejsu API, który jest potrzebny podczas konfigurowania aplikacji funkcji.

## <a name="define-the-function"></a>Zdefiniuj funkcję

To rozwiązanie używa aplikacji usługi Azure Functions do wysyłania powiadomień e-mail, gdy zadanie usługi Stream Analytics wykryje zatrzymane urządzenie. Aby utworzyć aplikację funkcji:

1. W witrynie Azure portal przejdź do wystąpienia **usługi app service** w grupie zasobów **DetectStoppedDevices.**
1. Wybierz, **+** aby utworzyć nową funkcję.
1. Na stronie **WYBIERZ ŚRODOWISKO PROGRAMISTYCZNE** wybierz pozycję **W portalu,** a następnie wybierz pozycję **Kontynuuj**.
1. Na stronie **TWORZENIE FUNKCJI** wybierz pozycję **Element webhook + INTERFEJS API,** a następnie wybierz pozycję **Utwórz**.

Portal tworzy domyślną funkcję o nazwie **HttpTrigger1**:

![Domyślna funkcja wyzwalania HTTP](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurowanie powiązań funkcji

Aby wysyłać wiadomości e-mail za pomocą SendGrid, należy skonfigurować powiązania dla swojej funkcji w następujący sposób:

1. Wybierz **opcję Zintegruj**, wybierz wyjściowy **PROTOKÓŁ HTTP ($return),** a następnie wybierz pozycję **Usuń**.
1. Wybierz **pozycję + Nowe dane wyjściowe**, a następnie wybierz polecenie **SendGrid**, a następnie wybierz pozycję **Wybierz**. Wybierz **pozycję Zainstaluj,** aby zainstalować rozszerzenie SendGrid.
1. Po zakończeniu instalacji wybierz opcję **Użyj wartości zwracanej funkcji**. Dodaj prawidłowy **adres Do,** aby otrzymywać powiadomienia e-mail.  Dodaj prawidłowy **adres Od,** aby użyć go jako nadawcy wiadomości e-mail.
1. Wybierz **nowy** obok **ustawienia aplikacji klucza interfejsu API SendGrid**. Wprowadź **SendGridAPIKey** jako klucz i klucz interfejsu API SendGrid, który wcześniej został odnotowany jako wartość. Następnie wybierz pozycję **Utwórz**.
1. Wybierz **pozycję Zapisz,** aby zapisać wiązania SendGrid dla swojej funkcji.

Ustawienia integracji wyglądają następująco:

![Integracja aplikacji funkcji](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Dodawanie kodu funkcji

Aby zaimplementować funkcję, dodaj kod C#, aby przeanalizować przychodzące żądanie HTTP i wysłać wiadomości e-mail w następujący sposób:

1. Wybierz funkcję **HttpTrigger1** w aplikacji funkcji i zastąp kod C# następującym kodem:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Komunikat o błędzie może zostać wyświetlony do momentu zapisania nowego kodu.

1. Wybierz **pozycję Zapisz,** aby zapisać funkcję.

### <a name="test-the-function-works"></a>Test działa funkcja

Aby przetestować funkcję w portalu, najpierw wybierz **dzienniki** u dołu edytora kodu. Następnie wybierz **opcję Testuj** po prawej stronie edytora kodu. Użyj następującego JSON jako **żądania treści:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Komunikaty dziennika funkcji są wyświetlane w panelu **Dzienniki:**

![Wyjście dziennika funkcji](media/howto-create-custom-rules/function-app-logs.png)

Po kilku minutach adres e-mail **to** otrzymuje wiadomość e-mail z następującą zawartością:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Dodawanie kwerendy usługi Stream Analytics

To rozwiązanie używa kwerendy usługi Stream Analytics do wykrywania, gdy urządzenie przestaje wysyłać dane telemetryczne na dłużej niż 120 sekund. Kwerenda używa danych telemetrycznych z Centrum zdarzeń jako jego danych wejściowych. Zadanie wysyła wyniki kwerendy do aplikacji funkcji. W tej sekcji można skonfigurować zadanie usługi Stream Analytics:

1. W witrynie Azure portal przejdź do zadania usługi Stream Analytics, w obszarze **Topologia zadań** wybierz pozycję **Wejścia**, wybierz **pozycję + Dodaj dane wejściowe strumienia**, a następnie wybierz pozycję **Centrum zdarzeń**.
1. Użyj informacji w poniższej tabeli, aby skonfigurować dane wejściowe za pomocą utworzonego wcześniej centrum zdarzeń, a następnie wybierz pozycję **Zapisz:**

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wejściowych | centralna telemetria |
    | Subskrypcja | Twoja subskrypcja |
    | Przestrzeń nazw centrum zdarzeń | Obszar nazw Centrum zdarzeń |
    | Nazwa centrum zdarzeń | Użyj istniejącego - **centralexport** |

1. W obszarze **Topologia zadań**wybierz pozycję **Dane wyjściowe**, wybierz **pozycję + Dodaj**, a następnie wybierz pozycję **Azure function**.
1. Użyj informacji w poniższej tabeli, aby skonfigurować dane wyjściowe, a następnie wybierz pozycję **Zapisz:**

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wyjściowych | powiadomienia e-mail |
    | Subskrypcja | Twoja subskrypcja |
    | Aplikacja funkcji | Twoja aplikacja funkcji |
    | Funkcja  | Funkcja HttpTrigger1 |

1. W obszarze **Topologia zadań**wybierz pozycję **Kwerenda** i zastąp istniejącą kwerendę następującym sqlem:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Wybierz **pozycję Zapisz**.
1. Aby uruchomić zadanie Usługi Stream Analytics, wybierz pozycję **Przegląd**, a następnie **rozpocznij**, a następnie **pozycję Teraz**, a następnie **rozpocznij**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Konfigurowanie eksportu w centrum IoT

W witrynie sieci Web [menedżera aplikacji usługi Azure IoT Central](https://aka.ms/iotcentral) przejdź do aplikacji IoT Central utworzonej na podstawie szablonu Contoso. W tej sekcji można skonfigurować aplikację do przesyłania strumieniowego danych telemetrycznych z symulowanych urządzeń do centrum zdarzeń. Aby skonfigurować eksport:

1. Przejdź do strony **Eksportowanie danych,** wybierz pozycję **+ Nowy**, a następnie **centrum zdarzeń platformy Azure**.
1. Użyj następujących ustawień, aby skonfigurować eksport, a następnie wybierz pozycję **Zapisz:**

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa wyświetlana | Eksportowanie do centrów zdarzeń |
    | Enabled (Włączony) | Włączone |
    | Przestrzeń nazw usługi Event Hubs | Nazwa obszaru nazw Centrów zdarzeń |
    | Centrum zdarzeń | centralexport |
    | Miary | Włączone |
    | Urządzenia | Wyłączone |
    | Szablony urządzeń | Wyłączone |

![Ciągła konfiguracja eksportu danych](media/howto-create-custom-rules/cde-configuration.png)

Przed kontynuowaniem poczekaj, aż stan eksportu **zostanie uruchomiony.**

## <a name="test"></a>Testowanie

Aby przetestować rozwiązanie, można wyłączyć ciągły eksport danych z usługi IoT Central na symulowane zatrzymane urządzenia:

1. W aplikacji IoT Central przejdź do strony **Eksportowanie danych** i wybierz konfigurację eksportu **eksportu centrów zdarzeń eksportuj eksport.**
1. Ustaw włącz opcję **Wył.** **Enabled** **Save**
1. Po co najmniej dwóch minutach adres e-mail **to** odbiera co najmniej jedną wiadomość e-mail, która wygląda jak następująca przykładowa zawartość:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Posprząt

Aby uporządkować po tym instrukcje i uniknąć niepotrzebnych kosztów, usuń **DetectStoppedDevices** grupy zasobów w witrynie Azure portal.

Aplikację IoT Central można usunąć ze strony **Zarządzanie** w aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku instrukcja, dowiedziałeś się, jak:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportowania danych*.
* Utwórz kwerendę usługi Stream Analytics, która wykrywa, kiedy urządzenie przestało wysyłać dane.
* Wyślij powiadomienie e-mail przy użyciu usług Azure Functions i SendGrid.

Teraz, gdy wiesz, jak tworzyć niestandardowe reguły i powiadomienia, sugerowanym następnym krokiem jest dowiedzenie się, jak [rozszerzyć usługę Azure IoT Central za pomocą analizy niestandardowej.](howto-create-custom-analytics.md)
