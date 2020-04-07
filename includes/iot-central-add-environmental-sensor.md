---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9618dc1cef8d04cc5906579af0ef372694aeaaa9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673985"
---
## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

Utwórz folder `environmental-sensor` wywoływany na komputerze lokalnym.

Pobierz plik JSON [dotyczący modelu czujnika środowiskowego](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) i zapisz go w folderze. `environmental-sensor`

Użyj edytora tekstu, aby zastąpić `{YOUR_COMPANY_NAME_HERE}` dwa wystąpienia nazwą `EnvironmentalSensorInline.capabilitymodel.json` firmy w pobranym pliku.

W aplikacji Azure IoT Central utwórz szablon urządzenia o `EnvironmentalSensorInline.capabilitymodel.json` nazwie *Czujnik środowiska,* importując plik modelu możliwości urządzenia:

![Szablon urządzenia z zaimportowanym modelem możliwości urządzenia](./media/iot-central-add-environmental-sensor/device-template.png)

Model możliwości urządzenia zawiera dwa interfejsy: standardowy interfejs **informacji o urządzeniu** i niestandardowy interfejs **czujnika środowiska.** Interfejs **czujnika środowiska** definiuje następujące możliwości:

| Typ | Nazwa wyświetlana | Opis |
| ---- | ------------ | ----------- |
| Właściwość | Stan urządzenia     | Stan urządzenia. Dostępne są dwa stany w trybie online/offline. |
| Właściwość (zapisywalna) | Nazwa klienta    | Nazwa klienta aktualnie obsługującego urządzenie. |
| Właściwość (zapisywalna) | Poziom jasności | Poziom jasności światła na urządzeniu. Może być określony jako 1 (wysoki), 2 (średni), 3 (niski). |
| Telemetria | Temperatura | Aktualna temperatura wykryta przez urządzenie. |
| Telemetria | Wilgotność    | Aktualna wilgotność wykryta przez urządzenie. |
| Polecenie | Blink          | Zacznij migać diodą LED na urządzeniu w danym przedziale czasu. |
| Polecenie | turnon         | Włącz diodę LED na urządzeniu. |
| Polecenie | Zjazd        | Wyłącz diodę LED na urządzeniu. |
| Polecenie | diagnostyka rundiagnostyka | To polecenie asynchroniczne uruchamia diagnostykę uruchomioną na urządzeniu. |

Aby dostosować sposób wyświetlania właściwości **Stan urządzenia** w aplikacji IoT Central, wybierz pozycję **Dostosuj** w szablonie urządzenia. Rozwiń wpis **Stan urządzenia,** wprowadź _online_ jako **nazwę True** i _offline_ jako **fałszywą nazwę**. Następnie zapisz zmiany:

![Dostosowywanie szablonu urządzenia](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Tworzenie widoków

Widoki umożliwiają interakcję z urządzeniami podłączonymi do aplikacji IoT Central. Na przykład można mieć widoki, które wyświetlają dane telemetryczne, widoki, które wyświetlają właściwości i widoki, które umożliwiają edycję właściwości zapisywalnych i chmury. Widoki są częścią szablonu urządzenia.

Aby dodać widoki domyślne do szablonu urządzenia **czujnika środowiska,** przejdź do szablonu urządzenia, wybierz pozycję **Widoki**i wybierz kafelek **Generuj widoki domyślne.** Upewnij **się,** że funkcja Przegląd i **informacje** są **włączone,** a następnie wybierz pozycję **Generuj domyślny widok pulpitu nawigacyjnego**. W szablonie zdefiniowano teraz dwa widoki domyślne.

Interfejs **czujnika środowiskowego** zawiera dwie zapisywalne właściwości - **nazwę klienta** i poziom **jasności.** Aby utworzyć widok, można użyć do edycji tych właściwości:

1. Wybierz **pozycję Widoki,** a następnie wybierz polecenie Edytowanie urządzenia i kafelek **danych w chmurze.**

1. Wprowadź _właściwości_ jako nazwę formularza.

1. Wybierz właściwości **Poziom jasności** i **Nazwa klienta.** Następnie wybierz pozycję **Dodaj sekcję**.

1. Zapisz zmiany.

![Dodawanie widoku w celu umożliwienia edycji właściwości](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publikowanie szablonu

Aby można było dodać urządzenie korzystające z szablonu urządzenia **czujnika środowiska,** należy go opublikować.

W szablonie urządzenia wybierz pozycję **Publikuj**. W panelu **Publikowania tego urządzenia** w panelu aplikacji wybierz pozycję **Publikuj**.

Aby sprawdzić, czy szablon jest gotowy do użycia, przejdź do strony **Urządzenia** w aplikacji IoT Central. Sekcja **Urządzenia** zawiera listę opublikowanych urządzeń w aplikacji:

![Opublikowane szablony na stronie urządzenia](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji Azure IoT Central dodaj rzeczywiste urządzenie do szablonu urządzenia utworzonego w poprzedniej sekcji:

1. Na stronie **Urządzenia** wybierz szablon **czujnika środowiska.**

    > [!TIP]
    > Pamiętaj, aby wybrać szablon do użycia przed wybraniem **+ Nowy**, w przeciwnym razie utworzysz urządzenie bez łączeniem.

1. Wybierz **+ Nowy**.

1. Upewnij się, że **symulacja** jest **wyłączona**. Następnie wybierz pozycję **Utwórz**.

Kliknij nazwę urządzenia, a następnie wybierz pozycję **Połącz**. Zanotuj informacje o połączeniu urządzenia na stronie **Połączenie urządzenia** — **zakres identyfikatora,** **identyfikator urządzenia**i **klucz podstawowy**. Podczas tworzenia kodu urządzenia potrzebne są następujące wartości:

![Informacje o połączeniu urządzenia](./media/iot-central-add-environmental-sensor/device-connection.png)
