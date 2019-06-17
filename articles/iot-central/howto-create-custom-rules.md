---
title: Rozszerzanie usługi Azure IoT Central za pomocą reguł niestandardowych i powiadomienia | Dokumentacja firmy Microsoft
description: Jako deweloper rozwiązania należy skonfigurować aplikację IoT Central do wysyłania powiadomień e-mail, gdy urządzenia zatrzymuje wysyłania danych telemetrycznych. To rozwiązanie korzysta z usługi Azure Stream Analytics i Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5248b9546ffe931b72123778d0d23574e5238405
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742414"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Rozszerzanie usługi Azure IoT Central za pomocą zasad niestandardowych, których wysyłanie powiadomień

Ten poradnik pokazuje, deweloper rozwiązania, jak rozszerzyć aplikację IoT Central, za pomocą niestandardowych reguł i powiadomień. W przykładzie, wysyłania powiadomienia operatorowi, gdy urządzenia zatrzymuje wysyłania danych telemetrycznych. Rozwiązanie używa [usługi Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) zapytanie, aby wykryć, kiedy urządzenie zostało zatrzymane, wysyłają dane telemetryczne. Usługi Stream Analytics zadanie używa [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/) można wysłać powiadomienia o wysłanie wiadomości e-mail przy użyciu [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Ten poradnik pokazuje, jak go już czynności wbudowane reguły i akcje są bogatsze IoT Central.

W tym przewodniku dowiesz się, jak:

* Stream dane telemetryczne z usługi IoT Central aplikacji za pomocą *ciągły Eksport danych*.
* Utwórz kwerendę usługi Stream Analytics, która wykrywa, gdy urządzenie zostało zatrzymane, wysyłają dane.
* Wyślij wiadomość e-mail z powiadomieniem za pomocą usługi Azure Functions i usługi SendGrid.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>Aplikacja IoT Central

Tworzenie aplikacji IoT Central [usługi Azure IoT Central — aplikacje](https://aka.ms/iotcentral) strony z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan płatności | Płatność zgodnie z rzeczywistym użyciem |
| Szablon aplikacji | Przykład dotyczący firmy Contoso |
| Nazwa aplikacji | Zaakceptuj wartości domyślne lub wybierz swoją własną nazwę |
| Adres URL | Zaakceptuj wartości domyślne lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Dzierżawy usługi Azure Active Directory |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region | Wschodnie stany USA |

Przykłady i zrzuty ekranu, w tym artykule korzystają **wschodnie stany USA** regionu. Wybierz bliską lokalizację i upewnij się, że wszystkie zasoby zostały utworzone w tym samym regionie.

### <a name="resource-group"></a>Grupa zasobów

Użyj [witryny Azure portal, aby utworzyć grupę zasobów](https://portal.azure.com/#create/Microsoft.ResourceGroup) o nazwie **DetectStoppedDevices** zawiera inne zasoby, które tworzysz. Tworzenie zasobów platformy Azure w tej samej lokalizacji co aplikację IoT Central.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [witryny Azure portal do utworzenia przestrzeni nazw usługi Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Name (Nazwa)    | Wybierz nazwę przestrzeni nazw |
| Warstwa cenowa | Podstawowa |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="stream-analytics-job"></a>Zadanie usługi Stream Analytics

Użyj [witryny Azure portal, aby utworzyć zadanie usługi Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Name (Nazwa)    | Wybierz nazwę swojego zadania |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Środowisko hostingu | Chmura |
| Jednostki przesyłania strumieniowego | 3 |

### <a name="function-app"></a>Aplikacja funkcji

Użyj [witryny Azure portal, aby utworzyć aplikację funkcji](https://portal.azure.com/#create/Microsoft.FunctionApp) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa aplikacji    | Wybierz nazwę swojej aplikacji funkcji |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| System operacyjny | Windows |
| Plan hostingu | Plan zużycia |
| Lokalizacja | Wschodnie stany USA |
| Stosu środowiska uruchomieniowego | .NET |
| Magazyn | Tworzenie nowego elementu |

### <a name="sendgrid-account"></a>Konto usługi SendGrid

Użyj [witryny Azure portal, aby utworzyć konto usługi SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Name (Nazwa)    | Wybierz nazwę swojego konta usługi SendGrid |
| Hasło | Utwórz hasło |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| Warstwa cenowa | Bezpłatna F1 |
| Informacje kontaktowe | Wpisz wymagane informacje |

Po utworzeniu wszystkich wymaganych zasobów usługi **DetectStoppedDevices** grupy zasobów wygląda jak poniższy zrzut ekranu:

![Wykrywanie grupy zasobów urządzenia zatrzymania](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Można skonfigurować aplikację IoT Central ciągły Eksport danych telemetrycznych do Centrum zdarzeń. W tej sekcji utworzysz Centrum zdarzeń w taki sposób, aby odbierać dane telemetryczne z aplikacji IoT Central. Centrum zdarzeń zapewnia dane telemetryczne do zadania usługi Stream Analytics do przetworzenia.

1. W witrynie Azure portal przejdź do przestrzeni nazw usługi Event Hubs, a następnie wybierz pozycję **+ Centrum zdarzeń**.
1. Nazwa Centrum zdarzeń **centralexport**i wybierz **Utwórz**.

Przestrzeń nazw usługi Event Hubs będzie wyglądać jak poniższy zrzut ekranu:

![Przestrzeń nazw usługi Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Uzyskiwanie klucza interfejsu API usługi SendGrid

Aplikacja funkcji wymaga klucza interfejsu API usługi SendGrid do wysyłania wiadomości e-mail. Aby utworzyć klucz interfejsu API usługi SendGrid:

1. W witrynie Azure portal przejdź do swojego konta SendGrid. Następnie wybierz **Zarządzaj** dostępu do Twojego konta SendGrid.
1. Na Twoim koncie usługi SendGrid wybierz **ustawienia**, następnie **klucze interfejsu API**. Wybierz **Utwórz klucz interfejsu API**:

    ![Utwórz klucz interfejsu API usługi SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na **Utwórz klucz interfejsu API** stronie, Utwórz klucz o nazwie **AzureFunctionAccess** z **pełny dostęp** uprawnienia.
1. Zanotuj klucz interfejsu API, będzie on potrzebny podczas konfigurowania aplikacji funkcji.

## <a name="define-the-function"></a>Definiowanie funkcji

To rozwiązanie używa aplikacji usługi Azure Functions do wysyłania wiadomości e-mail z powiadomieniem, gdy zadania usługi Stream Analytics wykrywa zatrzymania urządzenia. Aby utworzyć aplikację funkcji:

1. W witrynie Azure portal przejdź do **usługi App Service** wystąpienia w **DetectStoppedDevices** grupy zasobów.
1. Wybierz **+** tworzenie nowych funkcji.
1. Na **wybierz środowisko PROGRAMISTYCZNE** wybierz **w portalu** , a następnie wybierz **Kontynuuj**.
1. Na **CREATE FUNCTION** wybierz **element Webhook i interfejs API** , a następnie wybierz **Utwórz**.

Portal tworzy domyślną funkcję o nazwie **HttpTrigger1**:

![Funkcja wyzwalacza HTTP domyślne](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurowanie powiązań funkcji

Aby wysyłać wiadomości e-mail za pomocą usługi SendGrid, należy skonfigurować powiązania funkcji w następujący sposób:

1. Wybierz **integracja**, wybierz dane wyjściowe **HTTP ($return)** , a następnie wybierz pozycję **Usuń**.
1. Wybierz **+ nowe dane wyjściowe**, następnie wybierz **SendGrid**, a następnie wybierz **wybierz**. Wybierz **zainstalować** można zainstalować rozszerzenia usługi SendGrid.
1. Po zakończeniu instalacji wybierz **Użyj wartości zwracanej funkcji**. Dodaj prawidłowy **adres** Aby otrzymywać powiadomienia e-mail.  Dodaj prawidłowy **z adresu** do użycia jako nadawcy wiadomości e-mail.
1. Wybierz **nowe** obok **ustawienia aplikacji klucz interfejsu API usługi SendGrid**. Wprowadź **SendGridAPIKey** jako klucz i klucz interfejsu API usługi SendGrid zanotowanymi wcześniej jako wartość. Następnie wybierz przycisk **Utwórz**.
1. Wybierz **Zapisz** można zapisać powiązania usługi SendGrid w funkcji.

Ustawienia integracji wyglądać jak poniższy zrzut ekranu:

![Integracja aplikacji — funkcja](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Dodaj kod, funkcja

Aby zaimplementować funkcję, należy dodać C# kod, aby przeanalizować przychodzące żądania HTTP i wysyłanie wiadomości e-mail w następujący sposób:

1. Wybierz **HttpTrigger1** funkcji w aplikacji funkcji i Zastąp C# kod następującym kodem:

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

    Zobaczysz komunikat o błędzie, dopóki nie zostanie zapisany nowy kod.

1. Wybierz **Zapisz** można zapisać funkcji.

### <a name="test-the-function-works"></a>Testowanie działania — funkcja

Aby przetestować tę funkcję w portalu, należy najpierw wybrać **dzienniki** w dolnej części edytora kodu. Następnie wybierz **testu** po prawej stronie edytora kodu. Użyj następujące dane JSON jako **treść żądania**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Komunikaty dziennika funkcji są wyświetlane w **dzienniki** panelu:

![Dane wyjściowe dziennika — funkcja](media/howto-create-custom-rules/function-app-logs.png)

Po kilku minutach **do** wiadomości e-mail adres jest wysyłana wiadomość e-mail o następującej zawartości:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Dodawanie zapytania usługi Stream Analytics

To rozwiązanie wymaga zapytań usługi Stream Analytics, aby wykryć, kiedy urządzenia zatrzymuje wysyłania danych telemetrycznych przez ponad 120 sekund. Zapytanie używa danych telemetrycznych z Centrum zdarzeń jako dane wejściowe. Zadanie wysyła wyniki zapytania do aplikacji funkcji. W tej sekcji należy skonfigurować zadania usługi Stream Analytics:

1. W witrynie Azure portal przejdź do zadania usługi Stream analytics w obszarze **topologia zadań** wybierz **dane wejściowe**, wybierz **+ Dodaj wejście strumienia**, a następnie wybierz **zdarzeń Centrum**.
1. Skorzystaj z informacji w poniższej tabeli, aby skonfigurować dane wejściowe, za pomocą Centrum zdarzeń, który został wcześniej utworzony, a następnie wybierz **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wejściowych | centraltelemetry |
    | Subskrypcja | Twoja subskrypcja |
    | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw Centrum zdarzeń |
    | Nazwa centrum zdarzeń | Użyj istniejącej - **centralexport** |

1. W obszarze **topologia zadań**, wybierz opcję **dane wyjściowe**, wybierz **+ Dodaj**, a następnie wybierz **funkcji platformy Azure**.
1. Skorzystaj z informacji w poniższej tabeli, aby skonfigurować dane wyjściowe, a następnie wybierz **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wyjściowych | EmailNotification |
    | Subskrypcja | Twoja subskrypcja |
    | Aplikacja funkcji | Aplikacja funkcji |
    | Funkcja  | HttpTrigger1 |

1. W obszarze **topologia zadań**, wybierz opcję **zapytania** i Zastąp istniejące zapytanie SQL następujące:

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

1. Wybierz pozycję **Zapisz**.
1. Aby uruchomić zadanie usługi Stream Analytics, wybierz **Przegląd**, następnie **Start**, następnie **teraz**, a następnie **Start**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Skonfiguruj Eksport w IoT Central

Przejdź do [aplikacji IoT Central](https://aka.ms/iotcentral) utworzonym na podstawie szablonu firmy Contoso. W tej sekcji skonfigurujesz aplikację do przesyłania danych telemetrycznych z jego symulowanych urządzeń do Centrum zdarzeń. Aby skonfigurować eksportu:

1. Przejdź do **ciągły Eksport danych** wybierz opcję **+ nowy**, a następnie **usługi Azure Event Hubs**.
1. Użyj następujących ustawień, aby skonfigurować eksportu, a następnie wybierz **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa wyświetlana | Eksportowanie do usługi Event Hubs |
    | Enabled (Włączony) | Włączone |
    | Przestrzeń nazw usługi Event Hubs | Nazwa przestrzeni nazw usługi Event Hubs |
    | Centrum zdarzeń | centralexport |
    | Miary | Włączone |
    | Urządzenia | Wyłączone |
    | Szablony urządzeń | Wyłączone |

![Konfiguracji eksportu ciągłego danych](media/howto-create-custom-rules/cde-configuration.png)

Zaczekaj, aż stan eksportu **systemem** przed kontynuowaniem.

## <a name="test"></a>Testowanie

Aby przetestować rozwiązanie, możesz wyłączyć ciągły Eksport danych z IoT Central symulowanych urządzeń zatrzymana:

1. W aplikacji IoT Central, przejdź do **ciągły Eksport danych** strony i wybierz **eksportowanie do usługi Event Hubs** eksportowanie konfiguracji.
1. Ustaw **włączone** do **poza** i wybierz polecenie **Zapisz**.
1. Po co najmniej dwie minuty **do** adres e-mail odbiera co najmniej jeden wiadomości e-mail, które wyglądają jak w poniższym przykładzie zawartości:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Uporządkować dane

Aby uporządkować dane po instruktaż i uniknąć niepotrzebnych kosztów, Usuń **DetectStoppedDevices** grupy zasobów w witrynie Azure portal.

Możesz usunąć aplikację IoT Central **zarządzania** strony w aplikacji.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób:

* Stream dane telemetryczne z usługi IoT Central aplikacji za pomocą *ciągły Eksport danych*.
* Utwórz kwerendę usługi Stream Analytics, która wykrywa, gdy urządzenie zostało zatrzymane, wysyłają dane.
* Wyślij wiadomość e-mail z powiadomieniem za pomocą usługi Azure Functions i usługi SendGrid.

Skoro już wiesz, jak utworzyć niestandardowe reguły i powiadomień, sugerowane następnym krokiem jest Dowiedz się, jak [rozszerzenie usługi Azure IoT Central przy użyciu niestandardowych analytics](howto-create-custom-analytics.md).
