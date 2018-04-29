---
title: Aktualizacja oprogramowania układowego urządzenia z Centrum IoT Azure (.NET/węzła) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami w usłudze Azure IoT Hub zainicjować aktualizację oprogramowania układowego urządzenia. Przy użyciu urządzenia Azure IoT SDK dla środowiska Node.js aplikacji symulowane urządzenie i usługa Azure IoT SDK dla platformy .NET do implementacji usługi aplikacji, które wyzwala aktualizacji oprogramowania układowego.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: 4f0428fc2cddee0181705cad9675cc31fc38b753
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Umożliwia zarządzanie urządzeniami zainicjować aktualizację oprogramowania układowego urządzenia (.NET/węzeł)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczka przedstawiono sposób użycia [dwie urządzenia] [ lnk-devtwin] i [bezpośrednie metody ] [ lnk-c2dmethod] podstawowych zdalnie ponownego uruchomienia urządzenia. Ten samouczek używa tego samego podstawowych Centrum IoT i pokazuje, jak wykonać aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany w implementacji aktualizacji oprogramowania układowego dla [Pi malina urządzenia implementacji próbki][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji konsoli .NET, która wywołuje metodę firmwareUpdate bezpośrednio w aplikacji symulowane urządzenie za pomocą Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie, który implementuje **firmwareUpdate** metoda bezpośrednia. Ta metoda inicjuje procesem wieloetapowym czeka na pobranie obrazu oprogramowania układowego, pobierze obraz oprogramowania układowego i na koniec dotyczy oprogramowania układowego obrazu. Podczas każdego etapu aktualizacji urządzenie korzysta z właściwości zgłoszony do raport dotyczący postępu.

Na końcu tego samouczka masz urządzenie aplikacji konsoli Node.js i zaplecza aplikacji konsoli .NET (C#):

**dmpatterns_fwupdate_service.js**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie Wyświetla odpowiedzi i okresowo (co 500 MS.) Wyświetla zaktualizowanego zgłoszone właściwości.

**TriggerFWUpdate**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje firmwareUpdate metoda bezpośrednia, uruchamia proces wielu stanu do symulowania, w tym aktualizacji oprogramowania układowego: Oczekiwanie na pobieranie obrazu Pobieranie nowy obraz, a na koniec stosowania obrazu.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Środowisko node.js w wersji 4.0.x lub nowszym <br/>  [Przygotowywanie środowiska projektowego] [ lnk-dev-setup] opisuje sposób instalowania programu Node.js w tym samouczku w systemie Windows lub Linux.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Postępuj zgodnie z [wprowadzenie do zarządzania urządzeniami](iot-hub-csharp-node-device-management-get-started.md) artykuł, aby utworzyć Centrum IoT i pobrać parametry połączenia Centrum IoT.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji służy do tworzenia aplikacji konsoli .NET (przy użyciu języka C#) inicjującym aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja korzysta metoda bezpośrednia zainicjować aktualizację, a zapytania dwie urządzenia okresowo pobrać stanu aktualizacji oprogramowania układowego active.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **TriggerFWUpdate**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createapp]

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **TriggerFWUpdate** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .
1. W oknie **Menedżer pakietów NuGet** wybierz opcję **Przeglądaj**, wyszukaj pozycję **microsoft.azure.devices**, wybierz opcję **Zainstaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices**, i zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT][lnk-nuget-service-sdk] oraz jego zależności.

    ![Okno Menedżera pakietów NuGet][img-servicenuget]
1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Dodaj następujące pola do klasy **Program**: Zastąp symbole zastępcze wielu Centrum IoT ciąg połączenia dla koncentratora, który został utworzony w poprzedniej sekcji oraz identyfikator urządzenia.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Dodaj następującą metodę do klasy **Program**:
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Dodaj następującą metodę do klasy **Program**:

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

1. Na koniec dodaj następujące wiersze do metody **Główne**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. W Eksploratorze rozwiązań Otwórz **projektów startowych ustawić...**  i upewnij się, że **akcji** dla **TriggerFWUpdate** projekt jest **Start**.

1. Skompiluj rozwiązanie.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **manageddevice** folderu, uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. W programie Visual Studio, kliknij prawym przyciskiem myszy **TriggerFWUpdate** projektu, zaznacz **debugowania** i **Start nowe wystąpienie**.

3. Zobaczysz odpowiedź urządzenia do metody bezpośrednio w konsoli.

    ![Pomyślnie zaktualizowano oprogramowania układowego][img-fwupdate]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używana metoda bezpośrednia do wyzwolenia aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postępy aktualizacji oprogramowania układowego zgłoszone właściwości.

Aby dowiedzieć się, jak rozszerzyć IoT, Twoje rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach, zobacz [emisji zadania i harmonogramu] [ lnk-tutorial-jobs] samouczka.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/