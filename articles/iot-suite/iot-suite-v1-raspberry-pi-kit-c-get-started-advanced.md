---
title: Pi malina nawiązać połączenie za pomocą C do obsługi aktualizacji oprogramowania układowego pakiet IoT Azure | Dokumentacja firmy Microsoft
description: Za pomocą usługi Microsoft Azure IoT Starter Kit malinowe pi 3 i pakiet Azure IoT. C używany do nawiązania połączenia zdalnego rozwiązanie monitorowania sieci Pi malina wysyłania danych telemetrycznych z czujników do chmury, a w przypadku aktualizowania zdalnego oprogramowania układowego.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 8160752b0116c3ef3e6b6ab7920bb35e471f180b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012064"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>Twoje malina Pi 3 nawiązać połączenie zdalne rozwiązanie monitorowania i Włącz aktualizacje oprogramowania układowego zdalnego za pomocą C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

W tym samouczku przedstawiono sposób użycia programu Microsoft Azure IoT Starter Kit dla 3 Pi malina do:

* Opracowywanie czytnik temperatury i wilgotności, który może komunikować się z chmurą.
* Włącz i wykonywanie zdalnego oprogramowania układowego aktualizacja lub aktualizacja aplikacji klienckiej na malina Pi.

W samouczku:

* Raspbian systemu operacyjnego, język programowania C i Microsoft Azure IoT SDK dla języka C do zaimplementowania urządzenia próbki.
* Pakiet IoT zdalne monitorowanie wstępnie skonfigurowane rozwiązanie jako zaplecza opartej na chmurze.

## <a name="overview"></a>Omówienie

W tym samouczku zostaną wykonane następujące kroki:

* Wdrożenie wystąpienia programu zdalnego wstępnie skonfigurowane rozwiązanie monitorowania do subskrypcji platformy Azure. Ten krok automatycznie wdraża i konfiguruje wiele usług platformy Azure.
* Konfigurowanie czujniki i urządzenia do komunikowania się z komputerem i zdalnego rozwiązanie monitorowania.
* Zaktualizuj przykładowy kod urządzenia do nawiązania połączenia zdalnego rozwiązanie monitorowania i wysłać dane telemetryczne, które można wyświetlić na pulpicie nawigacyjnym rozwiązania.
* Aby zaktualizować aplikację klienta, należy użyć przykładowego kodu urządzenia.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Zdalne rozwiązanie monitorowania udostępnia zestaw usług platformy Azure w ramach subskrypcji platformy Azure. Wdrożenie odzwierciedla architektury przedsiębiorstwa prawdziwe. Aby uniknąć niepotrzebnych wykorzystania platformy Azure, opłat, należy usunąć wystąpienia wstępnie skonfigurowanego rozwiązania na azureiotsuite.com po zakończeniu z nim. Jeśli potrzebujesz ponownie wstępnie skonfigurowanego rozwiązania, można go łatwo odtworzyć. Aby uzyskać więcej informacji na temat zmniejszenie zużycia podczas wykonywania zdalnego rozwiązanie monitorowania, zobacz [Konfigurowanie pakiet IoT Azure wstępnie rozwiązań dla celów demonstracyjnych][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Pobierz i skonfiguruj próbki

Można teraz pobrać i konfigurowania zdalnego monitorowania aplikacji klienckiej na Twoje malina Pi.

### <a name="clone-the-repositories"></a>Klonowanie repozytoria

Jeśli jeszcze tego nie zrobiono tego wcześniej, klonowanie wymagane repozytoriów, uruchamiając następujące polecenia na Twoje Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Zaktualizuj parametry połączenia urządzenia

Otwórz przykładowy plik konfiguracji w **nano** edytora za pomocą następującego polecenia:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Zastąp symbole zastępcze identyfikator i Centrum IoT informacji o urządzeniu utworzony i zapisany na początku tego samouczka.

Gdy wszystko będzie gotowe, zawartość pliku deviceinfo powinna wyglądać następująco:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Zapisz zmiany (**Ctrl-O**, **Enter**) i zamknij Edytor (**Ctrl-X**).

## <a name="build-the-sample"></a>Tworzyć przykładowy kod

Jeśli jeszcze nie, uruchamiając następujące polecenia w terminalu na Pi malina należy zainstalować wstępnie wymagane pakiety dla zestawu SDK programu Microsoft Azure IoT urządzenia dla języka C:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Przykładowe rozwiązanie można teraz tworzyć na Pi malina:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

Teraz możesz uruchomić program przykładowy na malina Pi. Wprowadź polecenie:

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

Następujące przykładowe dane wyjściowe jest przykład danych wyjściowych, widocznej na Pi malina w wierszu polecenia:

![Dane wyjściowe z aplikacji malinowe Pi][img-raspberry-output]

Naciśnij klawisz **Ctrl-C** aby zamknąć program w dowolnym momencie.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. Na pulpicie nawigacyjnym rozwiązania kliknij **urządzeń** do odwiedzenia **urządzeń** strony. Wybierz użytkownika malinowe Pi w **listę urządzeń**. Następnie wybierz pozycję **metody**:

    ![Lista urządzeń na pulpicie nawigacyjnym][img-list-devices]

1. Na **wywołania metody** wybierz pozycję **InitiateFirmwareUpdate** w **metody** listy rozwijanej.

1. W **FWPackageURI** wprowadź **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**. Ten plik archiwum zawiera implementacji oprogramowania układowego w wersji 2.0.

1. Wybierz **InvokeMethod**. Aplikacja na Pi malina wysyła potwierdzenie z powrotem do pulpitu nawigacyjnego rozwiązania. Następnie uruchamia proces aktualizacji oprogramowania układowego pobierając nowej wersji oprogramowania układowego:

    ![Pokaż historię — metoda][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Obserwowanie procesu aktualizacji oprogramowania układowego

Można obserwować oprogramowanie układowe zaktualizować procesu, ponieważ była uruchamiana na urządzeniu i wyświetlając właściwości zgłoszony na pulpicie nawigacyjnym rozwiązania:

1. Postęp w proces aktualizacji można wyświetlić na Pi malina:

    ![Pokaż postęp aktualizacji][img-update-progress]

    > [!NOTE]
    > Zdalnej aplikacji monitorowania uruchamia ponownie dyskretnie po ukończeniu aktualizacji. Użyj polecenia `ps -ef` można zweryfikować jest uruchomiona. Jeśli chcesz zakończyć ten proces, użyj `kill` polecenie z identyfikatorem procesu.

1. Można wyświetlić stan aktualizacji oprogramowania układowego, zgłoszonych przez urządzenia, w portalu rozwiązania. Poniższy zrzut ekranu przedstawia stan i czas trwania każdego etapu procesu aktualizacji i nowa wersja oprogramowania układowego:

    ![Pokaż stan zadania][img-job-status]

    Jeśli przejdziesz do pulpitu nawigacyjnego, możesz sprawdzić, czy urządzenie jest nadal wysyła dane telemetryczne po aktualizacji oprogramowania układowego.

> [!WARNING]
> Pozostawienie zdalnego monitorowania działającej na koncie Azure są rozliczane dla przy uruchomieniu. Aby uzyskać więcej informacji na temat zmniejszenie zużycia podczas wykonywania zdalnego rozwiązanie monitorowania, zobacz [Konfigurowanie pakiet IoT Azure wstępnie rozwiązań dla celów demonstracyjnych][lnk-demo-config]. Usuwanie wstępnie skonfigurowane rozwiązanie z konta platformy Azure po zakończeniu korzystania z niego.

## <a name="next-steps"></a>Następne kroki

Odwiedź stronę [Centrum deweloperów systemu Azure IoT](https://azure.microsoft.com/develop/iot/) więcej przykłady i dokumentacja Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md