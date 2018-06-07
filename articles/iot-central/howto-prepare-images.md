---
title: Przekaż obrazy do aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Jako konstruktora Dowiedz się, jak przygotować i przekazywanie obrazów do aplikacji Azure IoT centralnej.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7fd9c8ed5559b00bc755e3f04c768dceeb487562
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628025"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Przygotowanie i przekazywanie obrazów do aplikacji Azure IoT centralnej

W tym artykule opisano, jak to zrobić, w jako konstruktora, przekazując niestandardowych obrazów można dostosować aplikację Microsoft Azure IoT centralnej. Na przykład można dostosować pulpit nawigacyjny urządzenia z obrazu urządzenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja Azure IoT centralnej. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji centralnej Azure IoT](howto-create-application.md).
1. Narzędzie do skalowania i zmiany rozmiaru plików obrazów.

## <a name="choose-where-to-use-custom-images"></a>Wybierz miejsce, do używania niestandardowych obrazów

W następujących lokalizacjach w aplikacji Azure IoT centralnej, można dodać niestandardowe obrazy:

* **Menedżer aplikacji** strony

    ![Obraz strony Menedżera aplikacji](media/howto-prepare-images/applicationmanager.png)

* Strona główna

    ![Obraz strony głównej](media/howto-prepare-images/homepage.png)

* Szablon urządzenia

    ![Obraz szablonu urządzenia](media/howto-prepare-images/devicetemplate.png)

* Kafelka na pulpicie nawigacyjnym urządzenia

    ![Obraz na kafelku urządzenia](media/howto-prepare-images/devicetile.png)

* Kafelka na pulpicie nawigacyjnym zestawu urządzeń

    ![Obraz na kafelku zestawu urządzeń](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Przygotowanie obrazów

We wszystkich lokalizacjach czterech można użyć obrazów PNG, GIF lub JPEG.

Poniższa tabela zawiera podsumowanie rozmiary obrazów, których można użyć:

| Lokalizacja | Rozmiary |
| -------- | ------ |
| **Menedżer aplikacji** | 268 x 160 pikseli |
| Szablon urządzenia | 64 x 64 pikseli |
| Strona główna i kafelka pulpitu nawigacyjnego | Najmniejszy rozmiar kafelka jest 200 x 200 pikseli, większe Kafelki można kwadratowy lub prostokątny wielokrotności małych fragmentów. Na przykład 200 x 400 pikseli, 400 x 200 pikseli lub 400 x 400 pikseli |

Najlepsze wyświetlana w aplikacji należy utworzyć obrazy pasujące wymiarów przedstawionych w powyższej tabeli.

## <a name="upload-the-images"></a>Przekazywanie obrazów

W poniższych sekcjach opisano sposób przekazywania obrazów do użycia w różnych miejscach:

### <a name="application-manager"></a>Menedżer aplikacji

Aby przekazać obraz do użycia na **Menedżer aplikacji**, przejdź do **ustawienia aplikacji** strony **administracji** sekcji. Musisz być administratorem, aby zakończyć to zadanie:

![Przekaż obraz aplikacji](media/howto-prepare-images/uploadapplicationmanager.png)

Kliknij obraz przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

### <a name="home-page"></a>Strona główna

Aby przekazać obraz do użycia na stronie głównej, przejdź do **strony głównej** aplikacji i tryb projektowania przełącznika. Musi być konstruktora do zakończenia tego zadania:

![Przekaż obraz strony głównej](media/howto-prepare-images/uploadhomepage.png)

Kliknij obraz przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

Po operacji przekazywania obrazu, można zmienić podczas, gdy włączony jest tryb projektowania.

### <a name="device-template"></a>Szablon urządzenia

Aby przekazać obraz do użycia w szablonie urządzeń, przejdź do **Explorer urządzenia**, wybierz szablon urządzenia, a następnie urządzenia i Włącz tryb projektowania. Musi być konstruktora do zakończenia tego zadania:

![Przekazywanie obrazu szablonu urządzenia](media/howto-prepare-images/uploaddevicetemplate.png)

Kliknij obraz przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

### <a name="device-dashboard"></a>Pulpit nawigacyjny urządzenia

Aby przekazać obraz do użycia na pulpicie nawigacyjnym, urządzeń, przejdź do **Explorer urządzenia**, wybierz szablon urządzenia i urządzenia. Następnie wybierz pozycję **pulpitu nawigacyjnego** tryb projektowania strony i przełącznik na. Musi być konstruktora do zakończenia tego zadania:

![Przekaż obraz pulpitu nawigacyjnego urządzenia](media/howto-prepare-images/uploaddevicedashboard.png)

Kliknij obraz przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

Po operacji przekazywania obrazu, można zmieniać rozmiar i zmienić jej podczas położenie **tryb projektowania** jest włączone.

### <a name="device-set-dashboard"></a>Urządzenie ustawione pulpitu nawigacyjnego

Aby przekazać obraz do użycia na pulpicie nawigacyjnym zestawu urządzeń, przejdź do **zestawy urządzeń** i wybierz zestaw urządzenia, a następnie urządzenia. Następnie wybierz pozycję **pulpitu nawigacyjnego** strony i przełącznik **tryb projektowania** na:

![Przekaż obraz pulpitu nawigacyjnego zestawu urządzenia](media/howto-prepare-images/uploaddevicesetdashboard.png)

Kliknij obraz przekazywania, a następnie wybierz plik do przekazania z komputera lokalnego.

Po przekazuje obrazu, można zmienić rozmiar i zmienić jej położenie, podczas gdy włączony jest tryb projektowania.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz przygotowaniu i przekazywanie obrazów do aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Zarządzanie urządzeniami w aplikacji Azure IoT centralnej](howto-manage-devices.md)