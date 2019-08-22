---
title: Połącz urządzenie DevKit z aplikacją usługi Azure IoT Central | Microsoft Docs
description: Jako deweloper urządzenia dowiesz się, jak połączyć urządzenie zestawu deweloperskiego IoT DevKit z aplikacją IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81a355cc7c0d1190ee86fac6ed155380e6e5c0d1
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877524"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Łączenie urządzenia zestawu deweloperskiego IoT DevKit z aplikacją usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano, jak deweloper urządzenia łączy urządzenie zestawu deweloperskiego IoT DevKit (DevKit) z aplikacją IoT Central Microsoft Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

1. Aplikacja IoT Central platformy Azure utworzona na podstawie przykładowego szablonu aplikacji **Devkits** . Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Urządzenie DevKit. Aby kupić urządzenie z systemem DevKit, odwiedź stronę [zestawu deweloperskiego IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Przykładowa aplikacja Devkits

Aplikacja utworzona na podstawie przykładowego szablonu aplikacji **Devkits** zawiera szablon urządzenia **zestawu deweloperskiego** , który definiuje następujące właściwości urządzenia:

- Pomiary danych telemetrycznych dla **wilgotności**, **temperatury**, **ciśnienia**, **Magnetometer** (mierzone wzdłuż x, y, z osi z), **przyspieszeniomierz** (mierzone wzdłuż x, y, z osi z) i **żyroskop** (mierzone wzdłuż x, y, osi z).
- Pomiar stanu **urządzenia**.
- Pomiar zdarzeń dla **przycisku B**został naciśnięty.
- Ustawienia **napięcia**, **prądu**, **szybkości wentylatora**i przełącznika **IR** .
- Właściwości urządzenia **numer** i **Lokalizacja urządzenia**, która jest właściwością Location.
- Właściwość chmury **została wyprodukowana w**.
- Polecenia **echo** iodliczanie. Gdy rzeczywiste urządzenie odbiera polecenie **echo** , wyświetla wartość wysłaną na ekranie urządzenia. Gdy rzeczywiste urządzenie odbiera polecenie odliczania, przechodzi przez wzorzec, a urządzenie wysyła odliczane wartości z powrotem do IoT Central.

Aby uzyskać szczegółowe informacje o konfiguracji, zobacz [zestawu deweloperskiego Device Template](#mxchip-device-template-details) Details

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

### <a name="get-your-device-connection-details"></a>Pobierz szczegóły połączenia z urządzeniem

W aplikacji IoT Central platformy Azure Dodaj rzeczywiste urządzenie z szablonu urządzenia **zestawu deweloperskiego** i zanotuj szczegóły połączenia urządzenia: **Identyfikator zakresu, identyfikator urządzenia i klucz podstawowy**:

1. Dodaj **rzeczywiste urządzenie** z Device Explorer, wybierz pozycję **+ Nowy > rzeczywiste** , aby dodać rzeczywiste urządzenie.

    * Wprowadź **Identyfikator urządzenia**małymi literami lub użyj sugerowanego **identyfikatora urządzenia**.
    * Wprowadź **nazwę urządzenia**lub użyj sugerowanej nazwy

    ![Dodaj urządzenie](media/howto-connect-devkit/add-device.png)

1. Aby uzyskać szczegóły połączenia z urządzeniem, **Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy**, wybierz pozycję **Połącz** na stronie urządzenia.

    ![Szczegóły połączenia](media/howto-connect-devkit/device-connect.png)

1. Zanotuj szczegóły połączenia. Po przygotowaniu urządzenia DevKit w następnym kroku nastąpi tymczasowe odłączenie od Internetu.

### <a name="prepare-the-devkit-device"></a>Przygotowywanie urządzenia DevKit

Jeśli urządzenie zostało wcześniej użyte i chcesz zmienić jego konfigurację tak, aby korzystała z innej sieci Wi-Fi, parametrów połączenia lub pomiaru telemetrii, naciśnij jednocześnie przyciski a i **B** . Jeśli to nie zadziała, naciśnij przycisk **Resetuj** i spróbuj ponownie.

#### <a name="to-prepare-the-devkit-device"></a>Aby przygotować urządzenie DevKit

1. Pobierz najnowsze wstępnie skompilowane oprogramowanie układowe IoT Central platformy Azure dla zestawu deweloperskiego ze strony [wydań](https://aka.ms/iotcentral-docs-MXChip-releases) w witrynie GitHub.
1. Podłącz urządzenie DevKit do komputera deweloperskiego przy użyciu kabla USB. W systemie Windows okno Eksploratora plików otwiera się na dysku mapowanym na magazyn na urządzeniu DevKit. Na przykład dysk może mieć nazwę **AZ3166 (D:)** .
1. Przeciągnij plik **iotCentral. bin** do okna dysku. Po zakończeniu kopiowania urządzenie zostanie uruchomione ponownie z nowym oprogramowaniem układowym.

1. Po ponownym uruchomieniu urządzenia DevKit zostanie wyświetlony następujący ekran:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Jeśli na ekranie jest wyświetlana jakakolwiek inna zawartość, zresetuj urządzenie i naciśnij przycisk a i **B** na urządzeniu w tym samym czasie, aby ponownie uruchomić urządzenie.

1. Urządzenie jest teraz w trybie punktu dostępu (AP). Możesz połączyć się z tym punktem dostępu Wi-Fi z komputera lub urządzenia przenośnego.

1. Na komputerze, telefonie lub tablecie z nazwą sieci Wi-Fi wyświetlaną na ekranie urządzenia. Po nawiązaniu połączenia z tą siecią nie masz dostępu do Internetu. Ten stan jest oczekiwany, a połączenie z tą siecią odbywa się tylko przez krótki czas podczas konfigurowania urządzenia.

1. Otwórz przeglądarkę internetową i przejdź do [http://192.168.0.1/start](http://192.168.0.1/start). Zostanie wyświetlona następująca strona sieci Web:

    ![Strona konfiguracji urządzenia](media/howto-connect-devkit/configpage.png)

    Na stronie sieci Web wprowadź:
    - Nazwa sieci Wi-Fi
    - Hasło sieciowe sieci Wi-Fi
    - Kod PIN wyświetlany na ekranie urządzenia
    - **Identyfikator zakresu**szczegółów połączenia, **Identyfikator urządzenia**i **klucz podstawowy** urządzenia (należy go wcześniej zapisać po wykonaniu kroków).
    - Zaznacz wszystkie dostępne pomiary telemetrii

1. Po wybraniu opcji **Konfiguruj urządzenie**zostanie wyświetlona strona:

    ![Urządzenie skonfigurowane](media/howto-connect-devkit/deviceconfigured.png)

1. Naciśnij przycisk **Reset** na urządzeniu.

## <a name="view-the-telemetry"></a>Wyświetlanie telemetrii

Po ponownym uruchomieniu urządzenia DevKit zostanie wyświetlony ekran na urządzeniu:

* Liczba wysłanych komunikatów telemetrycznych.
* Liczba niepowodzeń.
* Liczba odebranych żądanych właściwości i liczba wysłanych raportowanych właściwości.

> [!NOTE]
> Jeśli urządzenie zostanie wyświetlone w pętli w przypadku próby nawiązania połączenia, sprawdź, czy urządzenie jest **zablokowane** w IoT Central, i Odblokuj to urządzenie, aby umożliwić nawiązywanie połączenia z aplikacją.

Przetrząsnąć urządzenie, aby wysłać zgłoszoną właściwość. Urządzenie wysyła liczbę losową jako właściwość urządzenia **numeru** elementu.

Można wyświetlić miary danych telemetrycznych i raportowane wartości właściwości oraz skonfigurować ustawienia na platformie Azure IoT Central:

1. Użyj **Device Explorer** , aby przejść do strony **pomiarów** dla dodanego urządzenia zestawu deweloperskiego:

    ![Przejdź do rzeczywistego urządzenia](media/howto-connect-devkit/realdevicenew.png)

1. Na stronie **pomiary** można zobaczyć dane telemetryczne pochodzące z urządzenia zestawu deweloperskiego:

    ![Wyświetlanie danych telemetrycznych z rzeczywistego urządzenia](media/howto-connect-devkit/devicetelemetrynew.png)

1. Na stronie **Właściwości** można wyświetlić ostatni numer i lokalizację urządzenia zgłoszonego przez urządzenie:

    ![Wyświetl właściwości urządzenia](media/howto-connect-devkit/devicepropertynew.png)

1. Na stronie **Ustawienia** można zaktualizować ustawienia na urządzeniu zestawu deweloperskiego:

    ![Wyświetl ustawienia urządzenia](media/howto-connect-devkit/devicesettingsnew.png)

1. Na stronie **polecenia** można wywołać polecenia **echo** i odliczania :

    ![Wywołania poleceń](media/howto-connect-devkit/devicecommands.png)

1. Na stronie **pulpit nawigacyjny** można zobaczyć mapę lokalizacji.

    ![Wyświetlanie pulpitu nawigacyjnego urządzenia](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Jeśli chcesz eksplorować i modyfikować kod urządzenia, możesz go pobrać z witryny GitHub. Jeśli planujesz zmodyfikować kod, postępuj zgodnie z tymi instrukcjami, aby [przygotować środowisko programistyczne](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) dla systemu operacyjnego komputera.

Aby pobrać kod źródłowy, uruchom następujące polecenie na komputerze stacjonarnym:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Poprzednie polecenie pobiera kod źródłowy do folderu o nazwie `iot-central-firmware`.

> [!NOTE]
> Jeśli w środowisku deweloperskim nie zainstalowano usługi **git** , możesz pobrać ją [https://git-scm.com/download](https://git-scm.com/download)z programu.

## <a name="review-the-code"></a>Przeglądanie kodu

Użyj Visual Studio Code, aby otworzyć `MXCHIP/mxchip_advanced` folder `iot-central-firmware` w folderze:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Aby zobaczyć, jak dane telemetryczne są wysyłane do aplikacji IoT Central platformy Azure, Otwórz plik **Telemetria. cpp** w `src` folderze:

- Funkcja `TelemetryController::buildTelemetryPayload` tworzy ładunek telemetrii JSON przy użyciu danych z czujników na urządzeniu.

- Funkcja `TelemetryController::sendTelemetryPayload` wywołuje `sendTelemetry` **AzureIOTClient. cpp** , aby wysłać ładunek JSON do IoT Hub używanej przez aplikację Azure IoT Central.

Aby zobaczyć, jak wartości właściwości są raportowane do aplikacji IoT Central platformy Azure, Otwórz plik **Telemetria. cpp** w `src` folderze:

- Funkcja `TelemetryController::loop` wysyła właściwość o następującej **lokalizacji** co 30 sekund. Używa `sendReportedProperty` funkcji w pliku źródłowym **AzureIOTClient. cpp** .

- Funkcja `TelemetryController::loop` wysyła Właściwość **dieNumber** raportowany, gdy przyspieszeniomierz urządzenia wykryje podwójne naciśnięcie. Używa `sendReportedProperty` funkcji w pliku źródłowym **AzureIOTClient. cpp** .

Aby sprawdzić, jak urządzenie reaguje na polecenia wywoływane z aplikacji IoT Central, Otwórz plik **registeredMethodHandlers. cpp** w `src` folderze:

- Funkcja **dmEcho** jest programem obsługi dla polecenia **echo** . Przedstawia **displayedValue** w ładunku na ekranie urządzenia.

- Funkcja **dmCountdown** jest programem obsługi dla polecenia odliczania. Zmienia kolor diody LED urządzenia i używa raportowanej właściwości do wysłania odliczanej wartości z powrotem do aplikacji IoT Central. Raportowana właściwość ma taką samą nazwę jak polecenie. Funkcja używa `sendReportedProperty` funkcji w pliku źródłowym **AzureIOTClient. cpp** .

Kod w pliku źródłowym **AzureIOTClient. cpp** używa funkcji z [biblioteki SDK i bibliotek usługi IoT dla języka C Microsoft Azure](https://github.com/Azure/azure-iot-sdk-c) , aby współdziałać z IoT Hub.

Aby uzyskać informacje na temat modyfikowania, kompilowania i przekazywania przykładowego kodu do urządzenia, zobacz plik **README.MD** w `MXCHIP/mxchip_advanced` folderze.

## <a name="mxchip-device-template-details"></a>Szczegóły szablonu urządzenia zestawu deweloperskiego

Aplikacja utworzona na podstawie przykładowego szablonu aplikacji Devkits zawiera szablon urządzenia zestawu deweloperskiego o następujących cechach:

### <a name="measurements"></a>Miary

#### <a name="telemetry"></a>Telemetria

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| przyspieszeniomierz | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Stany 
| Name          | Nazwa wyświetlana   | TYPOW | OSTRZEŻENIE | STANOWIĄ | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stan urządzenia   | Zielony  | Pomarańczowy  | Czerwony    | 

#### <a name="events"></a>Events 
| Name             | Nazwa wyświetlana      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Naciśnięto przycisk B  | 

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Zakres      | setnapięci | Wolty | 0              | 0       | 240     | 0       |
| Bieżący      | SetCurrent | Amper  | 0              | 0       | 100     | 0       |
| Szybkość wentylatorów    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Przełącz ustawienia

| Nazwa wyświetlana | Nazwa pola | Na tekst | Off text | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | WŁĄCZONE      | WYŁĄCZONE      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Type            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwość urządzenia | Numer struktury   | dieNumber  | numer    |
| Właściwość urządzenia | Lokalizacja urządzenia   | location  | location    |
| Text            | Wyprodukowane w     | wytworzono   | ND       |

### <a name="commands"></a>Polecenia

| Nazwa wyświetlana | Nazwa pola | Typ zwracany | Nazwa wyświetlana pola wejściowego | Nazwa pola wejściowego | Typ pola wejściowego |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| ECHA         | echo       | text        | wartość do wyświetlenia         | displayedValue   | text             |
| Licz    | licz  | numer      | Liczba od               | countFrom        | numer           |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć zestawu deweloperskiego IoT DevKit z aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Konfigurowanie niestandardowego szablonu urządzenia](howto-set-up-template.md) dla własnego urządzenia IoT.
