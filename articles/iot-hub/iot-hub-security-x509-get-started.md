---
title: Samouczek dotyczący zabezpieczeń X.509 w usłudze Azure IoT Hub | Dokumentacja firmy Microsoft
description: Rozpocznij pracę od bezpieczeństwa opartych na standardzie X.509 w usłudze Azure IoT hub w środowisku symulowanym.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.openlocfilehash: 5795cde35d53a64620c4fdb6c3af99a7f56b12d9
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571141"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Konfigurowanie zabezpieczeń X.509 w usłudze Azure IoT hub

Ten samouczek symuluje czynności, musisz zabezpieczyć usługi Azure IoT hub przy użyciu *uwierzytelnianie certyfikatu X.509*. Na potrzeby ilustracji firma Microsoft pokazują, jak tworzyć certyfikaty lokalnie na komputerze Windows przy użyciu narzędzia typu open source OpenSSL. Zalecamy użycie w tym samouczku wyłącznie do celów testowych. W środowisku produkcyjnym, musisz kupić certyfikaty z *główny urząd certyfikacji (CA)*.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga, że masz następujące zasoby gotowe:

* Utworzono Centrum IoT hub z subskrypcją platformy Azure. Zobacz [Tworzenie Centrum IoT za pośrednictwem portalu](iot-hub-create-through-portal.md) szczegółowy opis kroków.

* Masz [Visual Studio 2017 lub Visual Studio 2019](https://www.visualstudio.com/vs/) zainstalowana na tym komputerze.

## <a name="get-x509-ca-certificates"></a>Pobierz certyfikaty X.509 urzędu certyfikacji

Zabezpieczenia oparte na certyfikatach X.509 w usłudze IoT Hub, należy rozpocząć od [łańcucha certyfikatów X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), który zawiera certyfikat główny, a także wszystkie certyfikaty pośrednie przesłaną certyfikatu liścia.

Można wybrać jedną z następujących sposobów, aby uzyskać certyfikaty:

* Zakup certyfikatu x.509 z *główny urząd certyfikacji (CA)*. Jest to zalecane w środowiskach produkcyjnych.

* Tworzenie własnych certyfikatów X.509 przy użyciu narzędzia innych firm, takich jak [OpenSSL](https://www.openssl.org/). Będzie to wystarczające do celów projektowania i testowania. Zobacz [testu Zarządzanie urzędu certyfikacji certyfikatów, przykłady i samouczki](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) informacje na temat generowania testów certyfikatów urzędu certyfikacji przy użyciu programu PowerShell lub Bash. Certyfikaty testów urzędu certyfikacji zgodnie z instrukcjami w korzysta z pozostałej części tego samouczka [testu Zarządzanie urzędu certyfikacji certyfikatów, przykłady i samouczki](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Rejestrowanie certyfikatów X.509 urzędu certyfikacji do usługi IoT hub

Te kroki pokazują, jak dodać nowego urzędu certyfikacji do Centrum IoT hub za pośrednictwem portalu.

1. W witrynie Azure portal przejdź do Centrum IoT hub i Otwórz **ustawienia** > **certyfikaty** menu.

2. Kliknij przycisk **Dodaj** można dodać nowego certyfikatu.

3. Wprowadź przyjazną nazwę wyświetlaną do certyfikatu. Wybierz plik certyfikatu głównego, o nazwie *RootCA.cer* utworzony w poprzedniej sekcji, w tym komputerze. Kliknij pozycję **Przekaż**.

4. Gdy otrzymasz powiadomienie, że Twój certyfikat została pomyślnie przekazana, kliknij przycisk **Zapisz**.

    ![Przekazywanie certyfikatu](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Spowoduje to wyświetlenie certyfikatu w **Eksplorator certyfikatów** listy. Uwaga **stan** tego certyfikatu jest *niezweryfikowane*.

5. Kliknij certyfikat, który zostało dodane w poprzednim kroku.

6. W **szczegóły certyfikatu** bloku kliknij **Generuj kod weryfikacyjny**.

7. Tworzy **kod weryfikacyjny** do weryfikacji własności certyfikatu. Skopiuj kod do Schowka.

   ![Zweryfikuj certyfikat](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Teraz, musisz zarejestrować to *kod weryfikacyjny* z skojarzenia klucza prywatnego za pomocą certyfikatu X.509 urzędu certyfikacji, który generuje sygnaturę. Istnieją narzędzia dostępne, aby wykonać ten proces podpisywania, na przykład OpenSSL. Jest to nazywane [dowodu posiadania](https://tools.ietf.org/html/rfc5280#section-3.1). Krok 3 w [testu Zarządzanie urzędu certyfikacji certyfikatów, przykłady i samouczki](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generuje kod weryfikacyjny.

9. Przekaż wynikowy podpis w kroku 8 powyżej do Centrum IoT w portalu. W **szczegóły certyfikatu** bloku w witrynie Azure portal przejdź do **plik PEM lub cer certyfikatu weryfikacji**i wybierz podpis, na przykład *VerifyCert4.cer*utworzone za pomocą polecenia programu PowerShell przykładowe _Eksploratora plików_ ikonę oprócz go.

10. Po pomyślnym przekazaniu tego certyfikatu, kliknij przycisk **Sprawdź**. **Stan** zmian certyfikatu **_Verified_** w **certyfikaty** bloku. Kliknij przycisk **Odśwież** Jeśli on nie być zaktualizowane automatycznie.

    ![Przekaż certyfikat weryfikacji](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  

## <a name="create-an-x509-device-for-your-iot-hub"></a>Tworzenie urządzenia X.509 dla usługi IoT hub

1. W witrynie Azure portal przejdź do usługi IoT hub **Eksploratory > urządzenia IoT** strony.

2. Kliknij przycisk **+ Dodaj** można dodać nowe urządzenie.

3. Nadaj przyjazną nazwę wyświetlaną dla **identyfikator urządzenia**i wybierz **_podpisany urzędu certyfikacji X.509_** jako **typ uwierzytelniania**. Kliknij pozycję **Zapisz**.

   ![Tworzenie urządzenia X.509 w portalu](./media/iot-hub-security-x509-get-started/create-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Uwierzytelnienia urządzenia X.509 za pomocą certyfikatów X.509

Aby uwierzytelniać urządzenie X.509, musisz najpierw zarejestrować urządzenia przy użyciu certyfikatu urzędu certyfikacji. Rejestrowanie urządzeń liścia zwykle odbywa się w zakładzie produkcyjnym, w którym produkcji narzędzia zostały włączone w związku z tym. Gdy urządzenie z jednego producenta do innego, akcja podpisywania dostarczana przez producenta jest przechwytywany jako pośredniego certyfikatu w łańcuchu. Wynik końcowy jest łańcuch certyfikatów z certyfikatu urzędu certyfikacji do certyfikatu liścia urządzenia. Krok 4 [testu Zarządzanie urzędu certyfikacji certyfikatów, przykłady i samouczki](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) wygeneruje certyfikat z urządzenia.

Następnie pokażemy sposób tworzenia aplikacji języka C# zasymulować urządzenie X.509 zarejestrowanych dla Centrum IoT hub. Firma Microsoft wyśle wartości temperatury i wilgotności z symulowanego urządzenia do Centrum. Należy pamiętać, że w tym samouczku zostanie utworzona tylko aplikację urządzenia. Pozostawia się w charakterze ćwiczenia do czytelników do tworzenia aplikacji usługi IoT Hub, która będzie wysyłać odpowiedzi na zdarzenia wysyłane przez urządzenie symulowane. Aplikacja C# zakłada, że zostały wykonane kroki opisane w [testu Zarządzanie urzędu certyfikacji certyfikatów, przykłady i samouczki](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. W programie Visual Studio Utwórz nowy projekt Visual C# Windows Classic Desktop za pomocą szablonu projektu aplikacji konsoli. Nadaj projektowi nazwę **SimulateX509Device**.

   ![Utwórz projekt urządzenia X.509 w programie Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateX509Device** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** . W oknie Menedżera pakietów NuGet wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Tę procedurę pobierania, instalowania i dodanie odwołania do pakietu NuGet zestawu SDK urządzeń Azure IoT i jego zależności.

   ![Dodawanie pakietu NuGet zestawu SDK urządzenia w programie Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Dodaj następujące wiersze kodu w górnej części *Program.cs* pliku:

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Dodaj następujące wiersze kodu wewnątrz **Program** klasy:

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

     Użyj przyjazną nazwę użyto w poprzedniej sekcji zamiast _< your_device_id >_ symbol zastępczy.

5. Dodaj następującą funkcję umożliwiającą tworzenie liczby losowe dla temperatury i wilgotności i wysłać te wartości do Centrum:

    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Na koniec należy dodać następujące wiersze kodu do **Main** funkcji, zastępując symbole zastępcze _identyfikator urządzenia_, _your-iot-hub-name_ i  _Absolute-Path-to-your-Device-PFX-File_ zgodnie z wymaganiami konfiguracji.

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Ten kod łączy do usługi IoT hub, tworząc parametry połączenia dla urządzenia X.509. Po pomyślnym nawiązaniu połączenia, następnie wysyła zdarzenia temperatury i wilgotności z koncentratorem i czeka na jego odpowiedź. 
7. Ponieważ ta aplikacja uzyskuje dostęp do *PFX* pliku, może być konieczne wykonanie w *administratora* trybu. Twórz rozwiązania Visual Studio. Otwórz nowe okno polecenia jako **administratora**, a następnie przejdź do folderu zawierającego to rozwiązanie. Przejdź do *bin/Debug* ścieżkę w folderze rozwiązania. Uruchom aplikację **SimulateX509Device.exe** z _administratora_ okna poleceń. Powinien zostać wyświetlony urządzenie pomyślnie łączenie z Centrum i wysyłania zdarzeń. 

   ![Uruchamianie aplikacji urządzenia](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zabezpieczania rozwiązania IoT, zobacz:

* [Najlepsze rozwiązania dotyczące zabezpieczeń IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Architektura zabezpieczeń IoT](../iot-fundamentals/iot-security-architecture.md)

* [Zabezpieczanie wdrożenia IoT](../iot-fundamentals/iot-security-deployment.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
