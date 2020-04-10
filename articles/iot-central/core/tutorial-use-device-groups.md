---
title: Używanie grup urządzeń w aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Jako operator dowiedz się, jak używać grup urządzeń do analizowania danych telemetrycznych z urządzeń w aplikacji Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 58fc71ab05c34e8acd252e7a1984c55996d1b3a7
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999034"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Samouczek: analizowanie telemetrii urządzeń za pomocą grup urządzeń

W tym artykule opisano, jak jako operator używać grup urządzeń do analizowania danych telemetrycznych urządzeń w aplikacji Usługi Azure IoT Central.

Grupa urządzeń to lista urządzeń, które są zgrupowane razem, ponieważ odpowiadają określonym kryteriom. Grupy urządzeń ułatwiają zarządzanie, wizualizowanie i analizowanie urządzeń na dużą skalę przez grupowanie urządzeń w mniejsze, logiczne grupy. Na przykład można utworzyć grupę urządzeń, aby wyświetlić listę wszystkich urządzeń klimatyzatora w Seattle, aby umożliwić technikowi znajdowanie urządzeń, za które są odpowiedzialni.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy urządzeń
> * Analizowanie danych telemetrycznych urządzenia za pomocą grupy urządzeń

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć [tworzenie aplikacji Azure IoT Central](./quick-deploy-iot-central.md) i dodać symulowane urządzenie do szybkiego startu aplikacji [IoT Central,](./quick-create-simulated-device.md) aby utworzyć szablon urządzenia **MXChip IoT DevKit** do pracy.

## <a name="create-simulated-devices"></a>Tworzenie symulowanych urządzeń

Przed utworzeniem grupy urządzeń należy dodać co najmniej pięć symulowanych urządzeń z szablonu urządzenia **MXChip IoT DevKit,** który będzie używany w tym samouczku:

![Pięć symulowanych urządzeń czujników](./media/tutorial-use-device-groups/simulated-devices.png)

W przypadku czterech symulowanych urządzeń czujników użyj widoku **Zarządzaj urządzeniem,** aby ustawić nazwę klienta na *Contoso:*

![Ustawianie nazwy klienta na Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Tworzenie grupy urządzeń

Aby utworzyć grupę urządzeń:

1. Wybierz **pozycję Grupy urządzeń** w lewym okienku.

1. Wybierz **+**:

    ![Nowa grupa urządzeń](media/tutorial-use-device-groups/image1.png)

1. Nadaj grupie urządzeń nazwę *Urządzenia Contoso*. Można również dodać opis. Grupa urządzeń może zawierać tylko urządzenia z jednego szablonu urządzenia. Wybierz szablon urządzenia **MXChip IoT DevKit,** który będzie używany dla tej grupy.

1. Aby dostosować grupę urządzeń tak, aby uwzględniała tylko urządzenia należące do **firmy Contoso,** wybierz **opcję + Filtr**. Wybierz właściwość Nazwa **nabywcy,** operator porównania Równa się i **Contoso** jako wartość. **Customer Name** Można dodać wiele filtrów i urządzeń spełniających **wszystkie** kryteria filtrowania są umieszczane w grupie urządzeń. Utworzona grupa urządzeń jest dostępna dla każdego, kto ma dostęp do aplikacji, dzięki czemu każdy może wyświetlać, modyfikować lub usuwać grupę urządzeń:

    ![Kwerenda grupy urządzeń](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Grupa urządzeń jest kwerendą dynamiczną. Za każdym razem, gdy wyświetlasz listę urządzeń, na liście mogą znajdować się różne urządzenia. Lista zależy od tego, które urządzenia spełniają obecnie kryteria kwerendy.

1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> W przypadku urządzeń usługi Azure IoT Edge wybierz szablony usługi Azure IoT Edge, aby utworzyć grupę urządzeń.

## <a name="analytics"></a>Analiza

**Analytics** z grupą urządzeń służy do analizowania danych telemetrycznych z urządzeń w grupie. Na przykład można wykreślić średnią temperaturę zgłoszoną przez wszystkie czujniki środowiskowe Contoso.

Aby przeanalizować dane telemetryczne dla grupy urządzeń:

1. Wybierz **pozycję Analytics** w lewym okienku.

1. Wybierz utworzoną grupę **urządzeń Contoso.** Następnie dodaj zarówno typy telemetrii **temperatury,** jak i **wilgotności:**

    ![Tworzenie analizy](./media/tutorial-use-device-groups/create-analysis.png)

    Użyj ikon koła zębatego obok typów telemetrii, aby wybrać typ agregacji. Wartość domyślna to **Średnia**. Użyj **podziału przez,** aby zmienić sposób pokazywanych danych agregacji. Na przykład, jeśli podzielisz się według identyfikatora urządzenia, zobaczysz wykres dla każdego urządzenia po **wybraniu opcji Analizuj**.

1. Wybierz **opcję Analizuj,** aby wyświetlić średnie wartości telemetryczne:

    ![Wyświetl analizę](./media/tutorial-use-device-groups/view-analysis.png)

    Można dostosować widok, zmienić wyświetlony okres czasu i wyeksportować dane.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak korzystać z grup urządzeń w aplikacji Azure IoT Central, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak utworzyć reguły telemetryczne](tutorial-create-telemetry-rules.md)
