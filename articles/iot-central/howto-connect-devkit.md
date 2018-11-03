---
title: Podłącz urządzenie Mxchip z aplikacją usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia Dowiedz się, jak połączyć urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: cca475f552495a42aabc60faf0bd57382f148120
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958585"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central

W tym artykule opisano jak Deweloper urządzenia podłączenia urządzenia zestawu deweloperskiego IoT Mxchip (Mxchip) do aplikacji Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji Szybki Start](quick-deploy-iot-central.md).
1. Urządzenie Mxchip. Aby zakupić urządzenia Mxchip, odwiedź stronę [zestawu deweloperskiego IoT Mxchip](http://mxchip.com/az3166).


## <a name="sample-devkits-application"></a>**Przykładowy Devkits** aplikacji

Aplikacja utworzone na podstawie **Devkits przykładowe** szablon aplikacji zawiera **zestawu deweloperskiego** szablon urządzenia o następującej charakterystyce: 

- Dane telemetryczne, zawierającą pomiarów urządzenia **wilgotności**, **temperatury**, **wykorzystanie**, **Magnometer** (mierzoną wzdłuż X Y, osi Z), **Accelorometer** (mierzoną wzdłuż X, Y osi Z) i **Żyroskop** (mierzoną wzdłuż X, Y osi Z).
- Stan, który zawiera przykład miary **stan urządzenia**.
- Miara zdarzeń z **użycia B przycisku** zdarzeń. 
- Ustawień przedstawiający **napięcia**, **bieżącego**, **szybkość wentylator**i **IR** przełącznika.
- Właściwości zawierający właściwości urządzenia **zdechną numer** i **lokalizacji urządzenia** czyli lokalizacji właściwość również jako **wyprodukowany w** właściwości w chmurze. 


Można znaleźć szczegółowe informacje o konfiguracji [Szczegóły szablonu zestawu deweloperskiego urządzenia](howto-connect-devkit.md#mxchip-device-template-details)


## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **zestawu deweloperskiego** szablon urządzenia i zanotować szczegółów połączenia urządzeń (**identyfikator zakresu, identyfikator urządzenia i podstawowego klucza**).

1. Dodaj **rzeczywistego urządzenia** z Device Explorer kliknij **+ nowy > rzeczywistych** dodać rzeczywistego urządzenia.
    * Wprowadź identyfikator urządzenia **<span style="color:Red">(powinno wskazywać na małe litery)</span>** lub sugerowane identyfikator urządzenia.
    * Wprowadź nazwę urządzenia, lub użyć sugerowanej nazwy
    
    ![Dodaj urządzenie](media\concepts-connectivity\add-device.png)


1. Pobierz szczegóły połączenia, takich jak **identyfikator zakresu, identyfikator urządzenia i podstawowego klucza** dla dodanego urządzenia, klikając **Connect** na stronie urządzenia.
 
    ![Szczegóły połączenia](media\concepts-connectivity\device-connect.PNG)

3. Upewnij się zapisać te informacje, jak będzie temporaritly uzyskać połączenia z Internetem podczas przygotowywania urządzenia Mxchip. 


### <a name="prepare-the-devkit-device"></a>Przygotuj urządzenie Mxchip

> [!NOTE]
> Jeśli poprzednio korzystano z urządzenia lub sieci Wi-Fi poświadczenia przechowywane i chcesz ponownie skonfigurować urządzeniu na korzystanie z innej sieci Wi-Fi, parametry połączenia lub pomiaru danych telemetrycznych, naciśnij jednocześnie **A** i **B** przyciski na tablicy jednocześnie. Jeśli to nie zadziała, naciśnij klawisz **resetowania** przycisk, a następnie spróbuj ponownie.



#### <a name="to-prepare-the-devkit-device"></a>Aby przygotować urządzenie Mxchip:


1. Pobierz najnowszy wstępnie skompilowanych usługi Azure IoT Central oprogramowania układowego dla zestawu deweloperskiego z [zwalnia](http://aka.ms/iotcentral-docs-MXChip-releases) strony w witrynie GitHub.
1. Podłącz urządzenie Mxchip do komputera deweloperskiego za pomocą kabla USB. W Windows na dysku mapowane na pamięć na urządzeniu Mxchip zostanie otwarte okno Eksploratora plików. Na przykład dysk może być wywoływana **az3166 usługi (D:)**.
1. Przeciągnij **iotCentral.bin** pliku na okno dysku. Po zakończeniu kopiowania urządzenie uruchamia się ponownie z nowym oprogramowaniem układowym.

1. Po ponownym uruchomieniu urządzenia Mxchip, wyświetli się następujący ekran:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Jeśli na ekranie są wyświetlane inaczej, zresetować urządzenie i naciśnij klawisz **A** i **B** przyciski na urządzeniu, w tym samym czasie, aby ponownie uruchomić urządzenie. 

1. Urządzenie jest teraz w trybie punktu dostępu. Można połączyć z tym punktem dostępu do sieci Wi-Fi, z komputera lub urządzenia przenośnego.

1. Na komputerze telefonie lub tablecie połączyć do nazwy sieci Wi-Fi, które są wyświetlane na ekranie urządzenia. Po nawiązaniu połączenia z tą siecią, nie masz dostępu do Internetu. Ten stan jest oczekiwany, a jedynie połączenia z tą siecią, przez krótki czas podczas konfigurowania urządzenia.

1. Otwórz przeglądarkę internetową i przejdź do [ http://192.168.0.1/start ](http://192.168.0.1/start). Następujące wyświetla stronę sieci web:

    ![Strona konfiguracji urządzenia](media/howto-connect-devkit/configpage.png)

    Na stronie sieci web: 
    - Dodaj nazwę sieci Wi-Fi 
    - Twoje hasło dostępu do sieci Wi-Fi
    - Kod PIN na urządzeniu LCD 
    - Szczegóły połączenia **identyfikator zakresu, identyfikator urządzenia i podstawowego klucza** urządzenia (powinien został już zapisany zgodnie z krokami)      
    - Wybierz wszystkie dostępne dane telemetryczne pomiary! 

1. Po wybraniu **Konfigurowanie urządzenia**, zostanie wyświetlona następująca strona:

    ![Urządzenia skonfigurowane](media/howto-connect-devkit/deviceconfigured.png)

1. Naciśnij klawisz **resetowania** przycisk na urządzeniu.


## <a name="view-the-telemetry"></a>Widok danych telemetrycznych

Po ponownym uruchomieniu urządzenia Mxchip pokazuje ekran na urządzeniu:

* Liczba komunikaty telemetryczne wysyłane.
* Liczba błędów.
* Liczba żądanych właściwości odebranych i liczbę zgłaszanych właściwości wysyłanych z.

> [!NOTE]
> Jeśli urządzenie znajduje się na pętli podczas sprawdzania connect, jeśli urządzenie jest *zablokowane* w IoT Central i *odblokowanie* urządzenia, dzięki czemu można połączyć ją do aplikacji.

Potrząśnij przyrost liczbę zgłaszanych właściwości wysyłanych z urządzenia. Urządzenie wysyła losową liczbę jako **zdechną numer** właściwości urządzenia.

Wyświetlanie danych telemetrycznych pomiarów i wartości zgłaszanych właściwości i skonfiguruj ustawienia w usłudze Azure IoT Central:

1. Użyj **Device Explorer** można przejść do **pomiarów** strona rzeczywistego urządzenia zestawu deweloperskiego został dodany:

    ![Przejdź do rzeczywistego urządzenia](media/howto-connect-devkit/realdevicenew.png)

1. Na **pomiarów** strony, możesz zobaczyć dane telemetryczne pochodzące z urządzeń zestawu deweloperskiego:

    ![Wyświetlanie telemetrii z rzeczywistego urządzenia](media/howto-connect-devkit/devicetelemetrynew.png)

1. Na **właściwości** strony, można wyświetlić ostatni numer struktury i lokalizacji urządzenia zgłoszona przez urządzenie:

    ![Wyświetl właściwości urządzenia](media/howto-connect-devkit/devicepropertynew.png)

1. Na **ustawienia** strony, możesz zaktualizować ustawienia na urządzeniu zestawu deweloperskiego:

    ![Wyświetl ustawienia urządzenia](media/howto-connect-devkit/devicesettingsnew.png)

1. Na **pulpit nawigacyjny** stronie widać, lokalizacja, mapy

    ![Wyświetl pulpit nawigacyjny z urządzenia](media/howto-connect-devkit/devicedashboardnew.png)


## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Jeśli chcesz eksplorować i modyfikować kod urządzenia, możesz ją pobrać z witryny GitHub. Jeśli zamierzasz zmodyfikować kod, należy wykonać te instrukcje, aby [przygotowywanie środowiska deweloperskiego](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) pulpitu w systemie operacyjnym.

Aby pobrać kod źródłowy, uruchom następujące polecenie na komputerze stacjonarnym:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Poprzednie polecenie pobiera kod źródłowy w folderze o nazwie `iot-central-firmware`. 

> [!NOTE]
> Jeśli **git** nie jest zainstalowany w środowisku deweloperskim, możesz ją pobrać z [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Przeglądanie kodu

Użyj programu Visual Studio Code, który został zainstalowany podczas przygotowywania środowiska deweloperskiego, aby otworzyć `AZ3166` folderze `iot-central-firmware` folderu: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Aby zobaczyć, jak dane telemetryczne są wysyłane do usługi Azure IoT Central aplikacji, otwórz **main_telemetry.cpp** pliku w folderze źródłowym.

Funkcja `buildTelemetryPayload` tworzy ładunek danych telemetrycznych w formacie JSON przy użyciu danych z czujników na urządzeniu.

Funkcja `sendTelemetryPayload` wywołania `sendTelemetry` w **iotHubClient.cpp** wysyłać ładunek JSON usługi IoT Hub używane przez aplikację usługi Azure IoT Central.

Aby zobaczyć, jak wartości właściwości są zgłaszane do usługi Azure IoT Central aplikacji, otwórz **main_telemetry.cpp** pliku w folderze źródłowym.

Funkcja `telemetryLoop` wysyła **doubleTap** zgłaszane właściwości, gdy przyspieszeniomierza wykryje naciśnij dwukrotnie. Używa ona `sendReportedProperty` działa w programach **iotHubClient.cpp** pliku źródłowego.

Kod w **iotHubClient.cpp** plik źródłowy używa funkcji z [ Microsoft Azure IoT SDK i bibliotek dla języka C](https://github.com/Azure/azure-iot-sdk-c) do interakcji z usługą IoT Hub.

Aby uzyskać informacje o tym, jak można zmodyfikować, kompilacji i Przekaż przykładowy kod do Twojego urządzenia, zobacz **readme.md** w pliku `AZ3166` folderu.

## <a name="mxchip-device-template-details"></a>Szczegóły szablonu zestawu deweloperskiego urządzenia 

Aplikacja utworzone na podstawie szablonu aplikacji przykładowej Devkits zawiera szablon zestawu deweloperskiego urządzenia o następującej charakterystyce:

### <a name="measurements"></a>Miary

#### <a name="telemetry"></a>Telemetria 

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| Temp           | C     | -40     | 120     | 0              |
| pressure       | hPa pakietu    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | grupą zarządzania     | -2000   | 2000    | 0              |
| accelerometerY | grupą zarządzania     | -2000   | 2000    | 0              |
| accelerometerZ | grupą zarządzania     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |


#### <a name="states"></a>Stany 
| Name (Nazwa)          | Nazwa wyświetlana   | NORMALNY | UWAGA | ZAGROŻENIA | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stan urządzenia   | Zielony  | Orange  | Czerwony    | 

#### <a name="events"></a>Zdarzenia 
| Name (Nazwa)             | Nazwa wyświetlana      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Naciśnięty przycisk B  | 

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napięcie      | setVoltage | V | 0              | 0       | 240     | 0       |
| Bieżący      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

Ustawienia przełącznika

| Nazwa wyświetlana | Nazwa pola | W tekście | Wyłącz tekstu | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | WYŁĄCZONE      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Właściwości urządzenia | Lokalizacji urządzenia   | location  | location    |
| Tekst            | Produkowane w     | manufacturedIn   | ND       |



## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy zawarto informacje dotyczące połączenia z urządzeniem Mxchip z aplikacją usługi Azure IoT Central, Oto zalecane kolejne kroki:

* [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
