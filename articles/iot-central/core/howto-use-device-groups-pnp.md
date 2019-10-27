---
title: Używanie grup urządzeń w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako operator, jak używać grup urządzeń w aplikacji IoT Central platformy Azure.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 91aae53ae81d2bc7cfda54cefa786e4b1a1aab67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949666"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Używanie grup urządzeń w aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym artykule opisano, jak za pomocą operatora korzystać z grup urządzeń w aplikacji IoT Central platformy Azure.

Grupa urządzeń to lista urządzeń zgrupowanych ze sobą, ponieważ są one zgodne z określonymi kryteriami. Grupy urządzeń ułatwiają zarządzanie, wizualizowanie i analizowanie urządzeń w dużej skali przez grupowanie urządzeń w mniejsze, logiczne grupy. Na przykład możesz utworzyć grupę urządzeń, aby wyświetlić listę wszystkich urządzeń z warunkiem klimatyzacyjnym w Seattle, aby umożliwić pracownikom znalezienie urządzeń, dla których są odpowiedzialni. W tym artykule opisano sposób tworzenia i konfigurowania grup urządzeń.

>  [!NOTE] 
> W przypadku urządzeń Azure IoT Edge należy wybrać Szablony Azure IoT Edge, aby utworzyć grupę urządzeń

## <a name="create-a-device-group"></a>Tworzenie grupy urządzeń

Aby utworzyć grupę urządzeń:

1. W okienku po lewej stronie wybierz pozycję **grupy urządzeń** .

1. Wybierz pozycję **+ Nowy**.

    ![Nowa grupa urządzeń](media/howto-use-device-groups-pnp/image1.png)

1. Nadaj grupie urządzeń nazwę, która jest unikatowa w całej aplikacji. Możesz również dodać opis. Grupa urządzeń może zawierać tylko urządzenia z jednego szablonu urządzenia. Wybierz szablon urządzenia, który ma być używany dla tej grupy.

1. Utwórz zapytanie, aby zidentyfikować urządzenia dla grupy urządzeń, wybierając właściwość, operator porównania i wartość. Można dodać wiele zapytań i urządzeń, które spełniają **wszystkie** kryteria, są umieszczane w grupie urządzeń. Utworzona grupa urządzeń jest dostępna dla każdego, kto ma dostęp do aplikacji, więc każda osoba może wyświetlać, modyfikować lub usuwać grupę urządzeń.

    ![Zapytanie grupy urządzeń](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Grupa urządzeń jest kwerendą dynamiczną. Za każdym razem, gdy oglądasz listę urządzeń, na liście mogą znajdować się różne urządzenia. Lista zależy od tego, które urządzenia spełniają kryteria zapytania.

1. Wybierz pozycję **Zapisz**.

## <a name="analytics"></a>Analiza

Analiza w grupach urządzeń jest taka sama jak główna Karta analizy w lewym okienku. Więcej informacji na temat analiz można znaleźć w artykule na temat [tworzenia analiz](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać grup urządzeń w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak utworzyć reguły telemetrii](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
