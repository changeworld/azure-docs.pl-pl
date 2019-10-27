---
title: Połącz urządzenie DevKit z aplikacją usługi Azure IoT Central | Microsoft Docs
description: Jako deweloper urządzenia dowiesz się, jak połączyć urządzenie zestawu deweloperskiego IoT DevKit z aplikacją usługi Azure IoT Central przy użyciu Plug and Play IoT.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: b7d2e1b08653cb8023ef6a5190ab53ecc3d568a6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951512"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Łączenie urządzenia zestawu deweloperskiego IoT DevKit z aplikacją usługi Azure IoT Central

W tym artykule pokazano, jak podłączyć urządzenie zestawu deweloperskiego IoT DevKit (DevKit) do aplikacji IoT Central platformy Azure. Urządzenie używa certyfikowanego modelu Plug and Play IoT dla urządzenia DevKit, aby skonfigurować połączenie z IoT Central.

W tym artykule poznasz następujące informacje:

- Pobierz szczegóły połączenia z aplikacji IoT Central.
- Przygotuj urządzenie i połącz je z aplikacją IoT Central.
- Wyświetl dane telemetryczne i właściwości z urządzenia w IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

1. [Urządzenie DevKit](https://aka.ms/iot-devkit-purchase).
1. Aplikacja IoT Central utworzona na podstawie szablonu **aplikacji w wersji zapoznawczej** . Możesz wykonać kroki opisane w temacie [Tworzenie aplikacji IoT Plug and Play](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="get-device-connection-details"></a>Pobierz szczegóły połączenia z urządzeniem

W aplikacji IoT Central platformy Azure wybierz kartę **Administracja** i wybierz pozycję **połączenie z urządzeniem**. Zanotuj **zakres identyfikatorów** i **klucz podstawowy**.

![Szczegóły połączenia z grupą urządzeń](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>Przygotowywanie urządzenia

1. Pobierz najnowsze [wstępnie skompilowane oprogramowanie układowe Plug and Play IoT Central platformy Azure](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) dla urządzenia DevKit z usługi GitHub.

1. Podłącz urządzenie DevKit do komputera deweloperskiego przy użyciu kabla USB. W systemie Windows okno Eksploratora plików otwiera się na dysku mapowanym na magazyn na urządzeniu DevKit. Na przykład dysk może mieć nazwę **AZ3166 (D:)** .

1. Przeciągnij plik **iotc_devkit. bin** do okna dysku. Po zakończeniu kopiowania urządzenie zostanie uruchomione ponownie z nowym oprogramowaniem układowym.

    > [!NOTE]
    > Jeśli widzisz błędy na ekranie, np. w przypadku **braku sieci Wi-Fi**, jest to spowodowane tym, że usługa DevKit nie została jeszcze połączona z siecią WiFi.

1. Na DevKit, przytrzymaj **przycisk B**, wypchnij i zwolnij przycisk **Reset** , a następnie zwolnij **przycisk b**. Urządzenie jest teraz w trybie punktu dostępu. Aby potwierdzić, na ekranie zostanie wyświetlony komunikat "IoT DevKit-AP" oraz adres IP portalu konfiguracji.

1. Na komputerze lub tablecie Połącz się z nazwą sieci Wi-Fi widoczną na ekranie urządzenia. Sieć Wi-Fi zaczyna się od **AZ-** a po nim adresu Mac. Po nawiązaniu połączenia z tą siecią nie masz dostępu do Internetu. Ten stan jest oczekiwany, a połączenie z tą siecią jest nawiązywane przez krótki czas podczas konfigurowania urządzenia.

1. Otwórz przeglądarkę internetową i przejdź do [http://192.168.0.1/](http://192.168.0.1/). Zostanie wyświetlona następująca strona sieci Web:

    ![Interfejs użytkownika konfiguracji](media/howto-connect-devkit-pnp/config-ui.png)

    Na stronie sieci Web wprowadź:

    - Nazwa sieci Wi-Fi (SSID).
    - Hasło sieciowe sieci Wi-Fi.
    - Szczegóły połączenia: **Identyfikator urządzenia** , który można wybrać, oraz **zakres identyfikatorów** i **klucz podstawowy grupy SAS** zostały wcześniej wykonane.

    > [!NOTE]
    > Obecnie DevKit IoT może łączyć się tylko z 2,4 GHz Wi-Fi, 5 GHz nie jest obsługiwany z powodu ograniczeń sprzętowych.

1. Wybierz pozycję **Konfiguruj urządzenie**, a urządzenie DevKit wykonuje ponowny rozruch i uruchomi aplikację:

    ![Uruchom ponownie interfejs użytkownika](media/howto-connect-devkit-pnp/reboot-ui.png)

    Na ekranie DevKit zostanie wyświetlone potwierdzenie, że aplikacja jest uruchomiona:

    ![DevKit uruchomione](media/howto-connect-devkit-pnp/devkit-running.png)

DevKit najpierw rejestruje nowe urządzenie w IoT Central aplikacji, a następnie uruchamia wysyłanie danych.

## <a name="view-the-telemetry"></a>Wyświetlanie telemetrii

Ten krok umożliwia wyświetlenie danych telemetrycznych w aplikacji IoT Central platformy Azure.

W aplikacji IoT Central wybierz kartę **urządzenia** , wybierz urządzenie, które zostało dodane. Na karcie **Omówienie** można wyświetlić dane telemetryczne z urządzenia DevKit:

   ![Omówienie urządzenia IoT Central](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>Przeglądanie kodu

Aby przejrzeć kod lub zmodyfikować go i skompilować, przejdź do [przykładów kodu](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak podłączyć urządzenie DevKit do aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Konfigurowanie niestandardowego szablonu urządzenia](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) dla własnego urządzenia IoT.
