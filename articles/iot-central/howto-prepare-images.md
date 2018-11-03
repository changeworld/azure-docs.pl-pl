---
title: Przekazywanie obrazów do aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako Konstruktor Dowiedz się, jak przygotować i przekazać obrazy do aplikacji usługi Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 18c44a3d91a4964d054c8e142394da7d69772ed0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960705"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Przygotuj i przekazywać obrazy do aplikacji usługi Azure IoT Central

W tym artykule opisano, jak to zrobić, w jako konstruktor, przekazując obrazów niestandardowych można dostosować aplikację Microsoft Azure IoT Central. Na przykład można dostosować pulpit nawigacyjny urządzenia przy użyciu obrazu urządzenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja usługi Azure IoT Central. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji Szybki Start](quick-deploy-iot-central.md).
1. Narzędzie służące do skalowania i zmienianie rozmiaru obrazów, plików.

## <a name="choose-where-to-use-custom-images"></a>Wybierz miejsce używać obrazów niestandardowych

Możesz dodać niestandardowe obrazy w następujących lokalizacjach w aplikacji usługi Azure IoT Central:

* **Menedżer aplikacji** strony

    ![Obraz na stronie Menedżera aplikacji](media/howto-prepare-images/applicationmanager.png)

* Strona główna

    ![Obraz na stronie głównej](media/howto-prepare-images/homepage.png)

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
| **Menedżer aplikacji** | 268 x 160 pikseli |
| Szablon urządzenia | 64 x 64 pikseli |
| Strona główna i Kafelki pulpitu nawigacyjnego | Najmniejszy rozmiar kafelka jest 200 x 200 pikseli, większy kafelków może być kwadratowe lub prostokątny wielokrotności małych fragmentów. Na przykład 200 x 400 pikseli, 400 x 200 pikseli lub 400 x 400 piks. |

Najlepsze wyświetlania w aplikacji należy utworzyć obrazy, które odpowiadają wymiarów przedstawionych w powyższej tabeli.

## <a name="upload-the-images"></a>Przekazywanie obrazów

W poniższych sekcjach opisano sposób przekazywania obrazów do wykorzystania w różnych miejscach:

### <a name="application-manager"></a>Menedżer aplikacji

Do przekazania obrazu do użycia na **Menedżer aplikacji**, przejdź do **ustawienia aplikacji** strony w **administracji** sekcji. Musisz być administratorem, aby zakończyć to zadanie:

![Przekaż obraz aplikacji](media/howto-prepare-images/uploadapplicationmanager.png)

Kliknij obraz, przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

### <a name="home-page"></a>Strona główna

Aby przekazać obraz do użycia na stronie głównej, przejdź do **strony głównej** aplikacji i Przełącz tryb projektowania na. Konstruktor, aby zakończyć to zadanie, musisz być:

![Przekaż obraz strony głównej](media/howto-prepare-images/uploadhomepage.png)

Kliknij obraz, przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

Po obraz, który ładuje, możesz zmienić jego rozmiar gdy włączony jest tryb projektowania.

### <a name="device-template"></a>Szablon urządzenia

Aby przekazać obraz do użycia na podstawie szablonu urządzenia, przejdź do **Device Explorer**, wybierz szablon urządzenia, a następnie urządzenia i włączenie trybu projektowania. Konstruktor, aby zakończyć to zadanie, musisz być:

![Przekazywanie obrazu szablonu urządzenia](media/howto-prepare-images/uploaddevicetemplate.png)

Kliknij obraz, przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

### <a name="device-dashboard"></a>Pulpit nawigacyjny urządzenia

Aby przekazać obraz do użycia na pulpicie nawigacyjnym urządzenia, przejdź do **Device Explorer**, wybierz szablon urządzenia, a następnie urządzenia. Następnie wybierz **pulpit nawigacyjny** strony i przełącznik tryb projektowania na. Konstruktor, aby zakończyć to zadanie, musisz być:

![Przekazywanie obrazu do pulpitu nawigacyjnego urządzeń](media/howto-prepare-images/uploaddevicedashboard.png)

Kliknij obraz, przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

Po przekazuje obraz, można zmienić rozmiar i zmienić położenie go podczas **trybu projektowania** jest włączone.

### <a name="device-set-dashboard"></a>Pulpit nawigacyjny zestawu urządzeń

Aby przekazać obraz do użycia na pulpicie nawigacyjnym zestawu urządzeń, przejdź do **zestawy urządzeń** i wybierz zestaw urządzeń, a następnie urządzenia. Następnie wybierz **pulpit nawigacyjny** strony i przełącznik **trybu projektowania** na:

![Przekaż obraz pulpitu nawigacyjnego zestawu urządzeń](media/howto-prepare-images/uploaddevicesetdashboard.png)

Kliknij obraz, przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

Po przekazuje obraz, można zmienić rozmiar i zmienić jej położenie, podczas gdy włączony jest tryb projektowania.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak przygotować i przekazywać obrazy do aplikacji usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Zarządzanie urządzeniami w usłudze Azure IoT Central aplikacji](howto-manage-devices.md)