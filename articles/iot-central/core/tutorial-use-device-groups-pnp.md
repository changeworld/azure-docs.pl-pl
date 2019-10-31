---
title: Używanie grup urządzeń w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Dowiedz się, jak używać grup urządzeń do analizowania danych telemetrycznych z urządzeń w aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 0f222cf9907dbf768a5f93842ff3df3fddf224a4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180870"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>Samouczek: używanie grup urządzeń do analizowania danych telemetrycznych urządzenia (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym artykule opisano, jak operator, aby użyć grup urządzeń do analizowania danych telemetrycznych urządzenia w aplikacji IoT Central platformy Azure.

Grupa urządzeń to lista urządzeń zgrupowanych ze sobą, ponieważ są one zgodne z określonymi kryteriami. Grupy urządzeń ułatwiają zarządzanie, wizualizowanie i analizowanie urządzeń w dużej skali przez grupowanie urządzeń w mniejsze, logiczne grupy. Na przykład możesz utworzyć grupę urządzeń, aby wyświetlić listę wszystkich urządzeń z warunkiem klimatyzacyjnym w Seattle, aby umożliwić pracownikom znalezienie urządzeń, dla których są odpowiedzialni.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy urządzeń
> * Przeanalizuj dane telemetryczne urządzenia za pomocą grupy urządzeń

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i [dodać symulowane urządzenie do aplikacji IoT Central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) przewodników Szybki Start, aby utworzyć szablon urządzenia **czujnika środowiska** do pracy z programem.

## <a name="create-simulated-devices"></a>Tworzenie symulowanych urządzeń

Przed utworzeniem grupy urządzeń należy dodać co najmniej pięć symulowanych urządzeń z szablonu urządzenia **czujnika środowiska** do użycia w tym samouczku:

![Pięć symulowanych urządzeń czujnika środowiska](./media/tutorial-use-device-groups-pnp/simulated-devices.png)

W przypadku czterech urządzeń czujnika środowiska użyj widoku **Właściwości czujnika środowiska** , aby ustawić nazwę klienta na **contoso**:

![Ustaw nazwę klienta na contoso](./media/tutorial-use-device-groups-pnp/customer-name.png)

## <a name="create-a-device-group"></a>Tworzenie grupy urządzeń

Aby utworzyć grupę urządzeń:

1. W okienku po lewej stronie wybierz pozycję **grupy urządzeń** .

1. Wybierz pozycję **+ Nowy**.

    ![Nowa grupa urządzeń](media/tutorial-use-device-groups-pnp/image1.png)

1. Nadaj grupie urządzeń nazwę, taką jak **urządzenia contoso**. Możesz również dodać opis. Grupa urządzeń może zawierać tylko urządzenia z jednego szablonu urządzenia. Wybierz szablon urządzenia **czujnika środowiska** , który ma być używany dla tej grupy.

1. Utwórz zapytanie, aby zidentyfikować urządzenia należące do firmy **contoso** dla grupy urządzeń, wybierając Właściwość **Nazwa klienta** , operator porównania **równa** się i **contoso** jako wartość. Można dodać wiele zapytań i urządzeń, które spełniają **wszystkie** kryteria, są umieszczane w grupie urządzeń. Utworzona grupa urządzeń jest dostępna dla każdego, kto ma dostęp do aplikacji, więc każda osoba może wyświetlać, modyfikować lub usuwać grupę urządzeń.

    ![Zapytanie grupy urządzeń](media/tutorial-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Grupa urządzeń jest kwerendą dynamiczną. Za każdym razem, gdy oglądasz listę urządzeń, na liście mogą znajdować się różne urządzenia. Lista zależy od tego, które urządzenia spełniają kryteria zapytania.

1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> W przypadku urządzeń Azure IoT Edge wybierz pozycję Szablony Azure IoT Edge, aby utworzyć grupę urządzeń.

## <a name="analytics"></a>Analiza

Możesz użyć **analizy** z grupą urządzeń, aby przeanalizować dane telemetryczne z urządzeń w grupie. Na przykład można wykreślić średnią temperaturę raportowaną przez wszystkie czujniki środowiska firmy Contoso.

Aby przeanalizować dane telemetryczne dla grupy urządzeń:

1. Wybierz pozycję **Analiza** w okienku po lewej stronie.

1. Wybierz utworzoną grupę urządzeń **contoso Devices** . Następnie dodaj typy telemetrii **temperatury** i **wilgotności** :

    ![Tworzenie analizy](./media/tutorial-use-device-groups-pnp/create-analysis.png)

    Użyj ikon koła zębatego obok typów telemetrii, aby wybrać typ agregacji. Wartość domyślna to **Average**. Użyj **podziału przez** , aby zmienić sposób wyświetlania zagregowanych danych. Jeśli na przykład podzieli według identyfikatora urządzenia, zobaczysz wykres dla każdego urządzenia po wybraniu opcji **Analizuj**.

1. Wybierz pozycję **Analizuj** , aby wyświetlić średnie wartości telemetrii:

    ![Wyświetl analizę](./media/tutorial-use-device-groups-pnp/view-analysis.png)

    Możesz dostosować widok, zmienić wyświetlany okres i wyeksportować dane.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać grup urządzeń w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak utworzyć reguły telemetrii](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
