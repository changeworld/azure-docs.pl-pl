---
title: Przekazywanie obrazów do aplikacji usługi Azure IoT Central | Microsoft Docs
description: Jako Konstruktor zapoznaj się z tematem przygotowywanie i przekazywanie obrazów do aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 255033f03acd40580fb4d4a92b0aa9b3e16969f3
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850160"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Przygotowywanie i przekazywanie obrazów do aplikacji IoT Central platformy Azure

W tym artykule opisano, jak w konstruktorze można dostosować aplikację IoT Central platformy Azure, przekazując obrazy niestandardowe. Na przykład możesz dostosować pulpit nawigacyjny urządzenia za pomocą obrazu urządzenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja IoT Central platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Narzędzie do skalowania i zmiany rozmiarów plików obrazów.

## <a name="choose-where-to-use-custom-images"></a>Wybieranie miejsca używania obrazów niestandardowych

Możesz dodać niestandardowe obrazy do następujących lokalizacji w aplikacji IoT Central platformy Azure:

* Strona **Moje aplikacje**

    ![Obraz na stronie Menedżera aplikacji](media/howto-prepare-images/applicationmanager.png)

* Pulpit nawigacyjny aplikacji

    ![Obraz na pulpicie nawigacyjnym aplikacji](media/howto-prepare-images/homepage.png)

* Szablon urządzenia

    ![Obraz w szablonie urządzenia](media/howto-prepare-images/devicetemplate.png)

* Kafelek na pulpicie nawigacyjnym urządzenia

    ![Obraz na kafelku urządzenia](media/howto-prepare-images/devicetile.png)

* Kafelek na pulpicie nawigacyjnym zestawu urządzeń

    ![Obraz na kafelku zestawu urządzeń](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Przygotowywanie obrazów

We wszystkich czterech lokalizacjach można używać obrazów PNG, GIF lub JPEG.

W poniższej tabeli zestawiono rozmiary obrazów, których można użyć:

| Location | Rozmiary |
| -------- | ------ |
| Menedżer aplikacji | 268x160 px |
| Szablon urządzenia | 64x64 px |
| Kafelki pulpitu nawigacyjnego | Kafelek o najmniejszym rozmiarze to 200x200 piks. większe kafelki mogą być kwadratowe lub prostokątne wielokrotności małych fragmentów. Na przykład 200x400 px, 400x200 px lub 400x400 pikseli |

Aby najlepiej wyświetlać w aplikacji, należy utworzyć obrazy zgodne z wymiarami podanymi w poprzedniej tabeli.

## <a name="upload-the-images"></a>Przekaż obrazy

W poniższych sekcjach opisano sposób przekazywania obrazów w różnych lokalizacjach:

### <a name="application-manager"></a>Menedżer aplikacji

Aby przekazać obraz do użycia na stronie **Moje aplikacje** , przejdź do strony **Ustawienia aplikacji** w sekcji **Administracja** . Aby wykonać to zadanie, musisz być administratorem:

![Załaduj obraz aplikacji](media/howto-prepare-images/uploadapplicationmanager.png)

Wybierz kafelek **obraz aplikacji** , aby przekazać obraz (268x160 px) z komputera lokalnego.

### <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji

Aby przekazać obraz na pulpicie nawigacyjnym aplikacji, przejdź do strony **pulpit nawigacyjny** aplikacji i wybierz pozycję **Edytuj**. Aby wykonać to zadanie, musisz być konstruktorem:

![Przekaż obraz pulpitu nawigacyjnego](media/howto-prepare-images/uploadhomepage.png)

W obszarze **Konfigurowanie obrazu**wybierz kafelek **obraz** , aby przekazać obraz z komputera lokalnego. Kafelek o najmniejszym rozmiarze to 200x200 piks. większe kafelki mogą być kwadratowe lub prostokątne wielokrotności małych fragmentów. Na przykład 200x400 px, 400x200 px lub 400x400 pikseli.

**Zapisz** przekazany obraz. Można zmienić jego rozmiar w trybie edycji. Po zakończeniu wybierz pozycję **gotowe** .

### <a name="device-template"></a>Szablon urządzenia

Aby przekazać obraz do szablonu urządzenia, przejdź do **szablonów urządzeń** i wybierz szablon urządzenia. Aby wykonać to zadanie, musisz być konstruktorem:

![Załaduj obraz szablonu urządzenia](media/howto-prepare-images/uploaddevicetemplate.png)

Wybierz kafelek obrazu, aby przekazać obraz (64x64 px) z komputera lokalnego.

### <a name="device-dashboard"></a>Pulpit nawigacyjny urządzenia

Aby przekazać obraz na pulpicie nawigacyjnym urządzenia, przejdź do **szablonów urządzeń** i wybierz szablon urządzenia. Następnie wybierz kartę **pulpit nawigacyjny** . Aby wykonać to zadanie, musisz być konstruktorem:

![Przekaż obraz pulpitu nawigacyjnego urządzenia](media/howto-prepare-images/uploaddevicedashboard.png)

W obszarze **Konfigurowanie obrazu**wybierz kafelek **obraz** , a następnie wybierz plik do przekazania z komputera lokalnego. Kafelek o najmniejszym rozmiarze to 200x200 piks. większe kafelki mogą być kwadratowe lub prostokątne wielokrotności małych fragmentów. Na przykład 200x400 px, 400x200 px lub 400x400 pikseli.

**Zapisz** przekazany obraz. Można zmienić rozmiar i położenie go w trybie edycji. Po zakończeniu wybierz pozycję **gotowe** .

### <a name="device-set-dashboard"></a>Pulpit nawigacyjny zestawu urządzeń

Aby przekazać obraz na pulpicie nawigacyjnym zestawu urządzeń, przejdź do opcji **Zestawy urządzeń** i wybierz zestaw urządzeń, a następnie urządzenie. Następnie wybierz stronę **pulpit nawigacyjny** i wybierz pozycję **Edytuj**:

![Obraz pulpitu nawigacyjnego przekazywania zestawu urządzeń](media/howto-prepare-images/uploaddevicesetdashboard.png)

W obszarze **Konfigurowanie obrazu**wybierz kafelek **obraz** , aby przekazać obraz z komputera lokalnego. Kafelek o najmniejszym rozmiarze to 200x200 piks. większe kafelki mogą być kwadratowe lub prostokątne wielokrotności małych fragmentów. Na przykład 200x400 px, 400x200 px lub 400x400 pikseli.

**Zapisz** przekazany obraz. Można zmienić rozmiar i położenie go w trybie edycji. Po zakończeniu wybierz pozycję **gotowe** .

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz już, jak przygotować i przekazać obrazy do aplikacji IoT Central platformy Azure, poniżej przedstawiono sugerowane następne kroki:

* [Dostosowywanie interfejsu użytkownika usługi Azure IoT Central](./howto-customize-ui.md)
* [Konfigurowanie pulpitu nawigacyjnego aplikacji](./howto-configure-homepage.md)
* [Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure](howto-manage-devices.md)