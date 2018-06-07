---
title: Samouczek X.509 zabezpieczeń w Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy z zabezpieczeń na podstawie X.509 w Centrum Azure IoT w środowisku symulowanym.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: dd5b9f196f911011d9dd606d46f515b88d338531
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635580"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Konfigurowanie zabezpieczeń X.509 w Centrum Azure IoT

W tym samouczku symuluje kroki potrzebne do zabezpieczania, używając Centrum Azure IoT *uwierzytelnianie certyfikatu X.509*. Dla ilustracji firma Microsoft będzie pokazują, jak korzystać z narzędzia typu open source biblioteki OpenSSL w celu utworzenia certyfikatów lokalnie na komputerze z systemem Windows. Zalecane jest użycie tego samouczka tylko do celów testowych. W środowisku produkcyjnym należy zakupić certyfikatów z *główny urząd certyfikacji (CA)*. 

## <a name="prerequisites"></a>Wymagania wstępne
Ten samouczek wymaga gotowy następujące zasoby:

- Utworzono Centrum IoT z subskrypcją platformy Azure. Zobacz [tworzenia Centrum IoT za pośrednictwem portalu](iot-hub-create-through-portal.md) szczegółowy opis kroków. 
- Masz [programu Visual Studio 2015 lub Visual Studio 2017](https://www.visualstudio.com/vs/) zainstalowana na tym komputerze. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>Uzyskiwanie certyfikatów X.509 urzędu certyfikacji
Zabezpieczenia oparte na certyfikatach X.509 w Centrum IoT wymaga do uruchomienia z [łańcucha certyfikatów X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), który zawiera certyfikat główny, a także wszystkich pośrednich certyfikatów do certyfikatu liścia. 

Można wybrać jedną z następujących sposobów uzyskania certyfikatów:
- Zakupu certyfikatów X.509 z *główny urząd certyfikacji (CA)*. Jest to zalecana dla środowisk produkcyjnych.
LUB,
- Tworzenie własnych certyfikatów X.509 przy użyciu narzędzia innej firmy, takich jak [OpenSSL](https://www.openssl.org/). To są odpowiednie do celów testowych i programistycznych. Następujące sekcje *tworzenia certyfikatów X.509* i *łańcucha certyfikatów X.509 utworzyć* w artykule [jak przy użyciu programu PowerShell w celu utworzenia certyfikatów X.509](iot-hub-security-x509-create-certificates.md) szczegółowe za pomocą przykładowy skrypt programu PowerShell w celu utworzenia certyfikatów przy użyciu biblioteki OpenSSL i programu PowerShell. Jeśli wolisz korzystać **Bash** powłoki zamiast programu PowerShell, zobacz sekcje pokrewne z [Zarządzanie próbki certyfikatów urzędu certyfikacji](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). Pozostałej części tego samouczka będą korzystać ze środowiska biblioteki OpenSSL, w tym *jak* przewodnik zawiera instrukcje dotyczące zabezpieczeń na trasie X.509 w usłudze Azure IoT Hub.


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Zarejestruj certyfikaty X.509 urzędu certyfikacji do Centrum IoT

Tych krokach przedstawiono sposób dodawania nowego urzędu certyfikacji do Centrum IoT za pośrednictwem portalu.

1. W portalu Azure, przejdź do Centrum IoT i Otwórz **ustawienia** > **certyfikaty** menu. 
2. Kliknij przycisk **Dodaj** można dodać nowego certyfikatu.
3. Wprowadź przyjazną nazwę wyświetlaną do swojego certyfikatu. Wybierz plik certyfikatu głównego o nazwie *RootCA.cer* utworzony w poprzedniej sekcji, w tym komputerze. Kliknij pozycję **Przekaż**.
4. Gdy otrzymasz powiadomienie o pomyślnym przekazaniu certyfikat, kliknij przycisk **zapisać**.

    ![Przekazywanie certyfikatu](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Wyświetli certyfikat **Explorer certyfikatu** listy. Uwaga **stan** tego certyfikatu jest *Unverified*.

5. Kliknij certyfikat, który dodanym w poprzednim kroku.

6. W **szczegóły certyfikatu** bloku, kliknij przycisk **Generuj kod weryfikacyjny**.

7. Tworzy **kod weryfikacyjny** można zweryfikować własność certyfikatu. Skopiuj kod do Schowka. 

   ![Zweryfikować certyfikatu](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Teraz, musisz zarejestrować to *kod weryfikacyjny* z skojarzenia klucza prywatnego przy użyciu certyfikatu X.509 urzędu certyfikacji, który generuje sygnaturę. Dostępne są dostępne, aby wykonać ten proces podpisywania, na przykład biblioteki OpenSSL narzędzia. Jest to nazywane [dowodu posiadania](https://tools.ietf.org/html/rfc5280#section-3.1). Jeśli użyto nasze przykładowe skrypty programu PowerShell w poprzedniej sekcji, uruchom skrypt wymienionych w sekcji [dowodu posiadania certyfikat X.509 urzędu certyfikacji](iot-hub-security-x509-create-certificates.md#signverificationcode).
 
9. Przekaż wynikowy podpis w kroku 8 powyżej Centrum IoT w portalu. W **szczegóły certyfikatu** bloku w portalu Azure, przejdź do **plik PEM lub .cer certyfikatu weryfikacji**i wybierz podpisu, na przykład *VerifyCert4.cer*utworzone za pomocą polecenia programu PowerShell próbki _Eksploratora plików_ ikona oprócz go.

10. Po pomyślnym przekazaniu certyfikat, kliknij przycisk **Sprawdź**. **Stan** zmian certyfikatu **_zweryfikowano_** w **certyfikaty** bloku. Kliknij przycisk **Odśwież** nie automatycznie aktualizacji.

   ![Przekaż certyfikat weryfikacji](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Utwórz urządzenie X.509 Centrum IoT

1. W portalu Azure, przejdź do Centrum IoT **Explorer urządzenia**.

2. Kliknij przycisk **Dodaj** Aby dodać nowe urządzenie. 

3. Nadaj przyjazną nazwę wyświetlaną dla **identyfikator urządzenia**i wybierz **_podpisany urzędu certyfikacji X.509_** jako **typ uwierzytelniania**. Kliknij pozycję **Zapisz**.

   ![Utwórz urządzenie X.509 w portalu](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Uwierzytelnianie urządzenia X.509 za pomocą certyfikatów X.509

Na potrzeby uwierzytelniania urządzenia X.509, musisz najpierw zarejestrować urządzenie z certyfikatem urzędu certyfikacji. Rejestrowanie urządzeń liścia zwykle odbywa się w zakładzie produkcyjnym, gdzie narzędzia produkcyjnym zostały włączone odpowiednie. Ponieważ urządzenie przechodzi od jednego producenta do innego, akcji podpisywania dla każdego producenta jest przechwytywany jako pośredniego certyfikatu w łańcuchu. W rezultacie jest łańcuch certyfikatów z certyfikatu urzędu certyfikacji do certyfikatu liścia urządzenia. Jeśli używany był naszych skryptów programu PowerShell w poprzednich sekcjach, a następnie można uruchomić skrypt wymienionych w sekcji *Utwórz certyfikat X.509 liścia urządzenia* w artykule [skrypty programu PowerShell Zarządzanie certyfikatami X.509 podpisany przez urząd certyfikacji](iot-hub-security-x509-create-certificates.md) do symulowania tego procesu.

Następnie możemy opisano sposób tworzenia aplikacji C# do symulacji urządzenia X.509 zarejestrowany Centrum IoT. Wyślemy temperatury i wilgotności wartości z symulowane urządzenie do Centrum. Należy pamiętać, że w tym samouczku utworzymy tylko aplikacji z urządzenia. Czytniki do tworzenia aplikacji usługi IoT Hub, która będzie wysyłać odpowiedzi na zdarzenia wysyłane przez to symulowane urządzenie zostanie pozostawiony jako wykonywania. Aplikacji C# przyjęto założenie, że zostały wykonane skrypty programu PowerShell wymienione w artykule [skryptów programu PowerShell do zarządzania certyfikatami X.509 podpisany przez urząd certyfikacji](iot-hub-security-x509-create-certificates.md)

1. W programie Visual Studio Utwórz nowy projekt Visual C# pulpitu systemu Windows klasycznego za pomocą szablonu projektu aplikacji konsoli. Nazwij projekt **SimulateX509Device**.
   ![Utwórz projekt urządzenia X.509 w programie Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateX509Device** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** . W oknie Menedżera pakietów NuGet, wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** do zainstalowania **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura pliki do pobrania, instaluje i dodaje odwołanie do pakietu SDK NuGet urządzenia Azure IoT i jego zależności.
   ![Dodaj pakiet SDK NuGet urządzenia w programie Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

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
   Użyj nazwy urządzenia przyjazną został użyty w poprzedniej sekcji zamiast _< your_device_id >_ symbolu zastępczego.

5. Dodaj następującą funkcję do tworzenia liczb losowych temperatury i wilgotności i wysłać te wartości do Centrum:
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

6. Na koniec Dodaj następujące wiersze kodu do **Main** funkcji, zastępując symbole zastępcze _identyfikator urządzenia_, _your iot hub nazwa-_ i  _Absolute-Path-to-your-Device-PFX-File_ co jest wymagane przez konfigurację.
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "123");
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
   Ten kod łączy się Centrum IoT, tworząc ciąg połączenia dla urządzenia X.509. Po pomyślnym nawiązaniu połączenia, następnie wysyła zdarzenia temperatury i wilgotności do koncentratora i czeka na jego odpowiedzi. 
7. Ponieważ ta aplikacja uzyskuje dostęp do *PFX* plik, trzeba wykonać to w *Admin* tryb. Skompiluj rozwiązanie Visual Studio. Otwórz nowe okno polecenia jako **administratora**, a następnie przejdź do folderu zawierającego tego rozwiązania. Przejdź do *bin/Debug* ścieżka folderu rozwiązania. Uruchom aplikację **SimulateX509Device.exe** z _Admin_ okna poleceń. Powinny pojawić się urządzenie pomyślnie łączenie z Centrum i wysyłania zdarzeń. 
   ![Uruchamianie aplikacji na urządzeniu](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zabezpieczania rozwiązania IoT, zobacz:

* [Najlepsze rozwiązania IoT][lnk-security-best-practices]
* [Architektura zabezpieczeń IoT][lnk-security-architecture]
* [Zabezpieczanie wdrożenia IoT][lnk-security-deployment]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

[lnk-security-best-practices]: iot-hub-security-best-practices.md
[lnk-security-architecture]: iot-hub-security-architecture.md
[lnk-security-deployment]: iot-hub-security-deployment.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
