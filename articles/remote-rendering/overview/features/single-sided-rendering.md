---
title: Renderowanie jednostronne
description: Zawiera opis jednostronnych ustawień renderowania i przypadków użycia
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682015"
---
# <a name="single-sided-rendering"></a>Renderowanie jednostronne

Większość rendererów używa [uboju tylnej ściany,](https://en.wikipedia.org/wiki/Back-face_culling) aby poprawić wydajność. Jednak gdy siatki są otwierane za pomocą [wyciętych płaszczyzn,](cut-planes.md)użytkownicy często patrzą na tylną stronę trójkątów. Jeśli te trójkąty są wybite, wynik nie wygląda przekonująco.

Sposobem na niezawodne zapobieganie temu problemowi jest renderowanie trójkątów *dwustronnych.* Ponieważ nie przy użyciu uboju zaplecza ma wpływ na wydajność, domyślnie azure zdalnego renderowania przełącza się tylko do renderowania dwustronnego dla siateczek, które przecinają się z płaszczyzną cięcia.

Jednostronne ustawienie *renderowania* umożliwia dostosowanie tego zachowania.

> [!CAUTION]
> Jednostronne ustawienie renderowania jest funkcją eksperymentalną. W przyszłości może zostać ponownie usunięty. Nie zmieniaj ustawienia domyślnego, chyba że naprawdę rozwiązuje to krytyczny problem w aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Jednostronne ustawienie renderowania ma wpływ tylko na siatki, które zostały `opaqueMaterialDefaultSidedness` [przekonwertowane](../../how-tos/conversion/configure-model-conversion.md) z opcją ustawioną na `SingleSided`. Domyślnie ta opcja `DoubleSided`jest ustawiona na .

## <a name="single-sided-rendering-setting"></a>Jednostronne ustawienie renderowania

Istnieją trzy różne tryby:

**Normalny:** W tym trybie siatki są zawsze renderowane podczas ich konwersji. Oznacza to, że `opaqueMaterialDefaultSidedness` siatki `SingleSided` konwertowane z ustawionym na zawsze będą renderowane z włączonym ubojem tylnej ściany, nawet jeśli przecinają płaszczyznę cięcia.

**DynamiczneDoubleSiding:** W tym trybie, gdy płaszczyzna cięcia przecina siatkę, jest automatycznie przełączana na renderowanie dwustronne. Ten tryb jest trybem domyślnym.

**ZawszeDoubleSided:** Wymusza renderowanie całej geometrii jednostronnej przez cały czas. Ten tryb jest w większości narażony, dzięki czemu można łatwo porównać wpływ na wydajność między renderowaniem jednostronnym i dwustronnym.

Zmiana jednostronnych ustawień renderowania może odbywać się w następujący sposób:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>Następne kroki

* [Płaszczyzny cięte](cut-planes.md)
* [Konfigurowanie konwersji modelu](../../how-tos/conversion/configure-model-conversion.md)
