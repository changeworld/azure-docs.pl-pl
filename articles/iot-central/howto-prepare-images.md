---
title: Przekazywanie obrazów do aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako Konstruktor Dowiedz się, jak przygotować i przekazać obrazy do aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c8444fd57fe939dfb212d88c22a071c13dac691e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519220"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Przygotuj i przekazywać obrazy do aplikacji usługi Azure IoT Central

W tym artykule opisano, jak to zrobić, w jako konstruktor, przekazując obrazów niestandardowych można dostosować aplikację usługi Azure IoT Central. Na przykład można dostosować pulpit nawigacyjny urządzenia przy użyciu obrazu urządzenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja usługi Azure IoT Central. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Narzędzie służące do skalowania i zmienianie rozmiaru obrazów, plików.

## <a name="choose-where-to-use-custom-images"></a>Wybierz miejsce używać obrazów niestandardowych

Możesz dodać niestandardowe obrazy w następujących lokalizacjach w aplikacji usługi Azure IoT Central:

* **Aplikacje** strony

    ![Obraz na stronie Menedżera aplikacji](media/howto-prepare-images/applicationmanager.png)

* Pulpit nawigacyjny aplikacji

    ![Obraz na pulpicie nawigacyjnym aplikacji](media/howto-prepare-images/homepage.png)

* Szablon urządzenia

    ![Obraz na szablon urządzenia](media/howto-prepare-images/devicetemplate.png)

* Kafelek na pulpicie nawigacyjnym urządzenia

    ![Obraz na Kafelek urządzenia](media/howto-prepare-images/devicetile.png)

* Kafelek na pulpicie nawigacyjnym zestawu urządzeń

    ![Obraz na kafelku zestawu urządzeń](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Przygotowywanie obrazów

We wszystkich czterech lokalizacjach można używać obrazów PNG, GIF lub JPEG.

W poniższej tabeli przedstawiono rozmiary obrazów, których można użyć:

| Lokalizacja | Rozmiary |
| -------- | ------ |
| Menedżer aplikacji | 268x160 px |
| Szablon urządzenia | 64x64 px |
| Kafelki pulpitu nawigacyjnego | Najmniejszy rozmiar kafelka jest 200 x 200 pikseli, większy kafelków może być kwadratowe lub prostokątny wielokrotności małych fragmentów. Na przykład 200 x 400 pikseli, 400 x 200 pikseli lub 400 x 400 piks. |

Najlepsze wyświetlania w aplikacji należy utworzyć obrazy, które odpowiadają wymiarów przedstawionych w powyższej tabeli.

## <a name="upload-the-images"></a>Przekazywanie obrazów

W poniższych sekcjach opisano sposób przekazywania obrazów w różnych miejscach:

### <a name="application-manager"></a>Menedżer aplikacji

Do przekazania obrazu do użycia na **aplikacje** stronie, przejść do **ustawienia aplikacji** strony w **administracji** sekcji. Musisz być administratorem, aby zakończyć to zadanie:

![Przekaż obraz aplikacji](media/howto-prepare-images/uploadapplicationmanager.png)

Wybierz Kafelek obrazu aplikacji, aby przekazać przygotowany obraz (268 x 160 pikseli) z komputera lokalnego.

### <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji

Aby przekazać obraz na pulpicie nawigacyjnym aplikacji, przejdź do **pulpit nawigacyjny** strony aplikacji i wybierz **Edytuj**. Konstruktor, aby zakończyć to zadanie, musisz być:

![Przekaż obraz pulpitu nawigacyjnego](media/howto-prepare-images/uploadhomepage.png)

W obszarze skonfigurować obrazu wybierz Kafelek obrazu, aby przekazać przygotowany obraz z komputera lokalnego. Najmniejszy rozmiar kafelka jest 200 x 200 pikseli, większy kafelków może być kwadratowe lub prostokątny wielokrotności małych fragmentów. Na przykład 200 x 400 pikseli, 400 x 200 pikseli lub 400 x 400 piks.

**Zapisz** przekazanego obrazu. Możesz zmienić jego rozmiar w trybie edycji. Wybierz **gotowe** po zakończeniu.

### <a name="device-template"></a>Szablon urządzenia

Aby przekazać obraz na podstawie szablonu urządzenia, przejdź do **szablonów urządzeń** i wybierz szablon urządzenia. Konstruktor, aby zakończyć to zadanie, musisz być:

![Przekazywanie obrazu szablonu urządzenia](media/howto-prepare-images/uploaddevicetemplate.png)

Wybierz Kafelek obrazu, aby przekazać przygotowany obraz (64 x 64 pikseli) z komputera lokalnego.

### <a name="device-dashboard"></a>Pulpit nawigacyjny urządzenia

Aby przekazać obraz na pulpicie nawigacyjnym urządzenia, przejdź do **szablonów urządzeń** i wybierz szablon urządzenia. Następnie wybierz **pulpit nawigacyjny** kartę. Konstruktor, aby zakończyć to zadanie, musisz być:

![Przekazywanie obrazu do pulpitu nawigacyjnego urządzeń](media/howto-prepare-images/uploaddevicedashboard.png)

W ramach konfigurowania obrazu wybierz Kafelek obrazu, a następnie wybierz plik do przekazania z komputera lokalnego. Najmniejszy rozmiar kafelka jest 200 x 200 pikseli, większy kafelków może być kwadratowe lub prostokątny wielokrotności małych fragmentów. Na przykład 200 x 400 pikseli, 400 x 200 pikseli lub 400 x 400 piks.

**Zapisz** przekazanego obrazu. Można zmienić rozmiar i zmienić położenie go w trybie edycji. Wybierz **gotowe** po zakończeniu.

### <a name="device-set-dashboard"></a>Pulpit nawigacyjny zestawu urządzeń

Aby przekazać obraz na pulpicie nawigacyjnym zestawu urządzeń, przejdź do **zestawy urządzeń** i wybierz zestaw urządzeń, a następnie urządzenia. Następnie wybierz **pulpit nawigacyjny** strony i wybierz **Edytuj**:

![Przekaż obraz pulpitu nawigacyjnego zestawu urządzeń](media/howto-prepare-images/uploaddevicesetdashboard.png)

W obszarze skonfigurować obrazu wybierz Kafelek obrazu, aby przekazać przygotowany obraz z komputera lokalnego. Najmniejszy rozmiar kafelka jest 200 x 200 pikseli, większy kafelków może być kwadratowe lub prostokątny wielokrotności małych fragmentów. Na przykład 200 x 400 pikseli, 400 x 200 pikseli lub 400 x 400 piks.

**Zapisz** przekazanego obrazu. Można zmienić rozmiar i zmienić położenie go w trybie edycji. Wybierz **gotowe** po zakończeniu.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak przygotować i przekazywać obrazy do aplikacji usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Zarządzanie urządzeniami w usłudze Azure IoT Central aplikacji](howto-manage-devices.md)