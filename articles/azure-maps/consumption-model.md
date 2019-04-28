---
title: Użycie modelu w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o modelu zużycia w usługi Azure Maps
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5f75f656312c11a4668ca9ef9fe7b2a61a7d13e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60797904"
---
# <a name="consumption-model"></a>Model użycia

Online Routing udostępnia zestaw parametrów szczegółowy opis modelu użycia specyficznych dla pojazdów.
W zależności od wartości **vehicleEngineType**, obsługiwane są dwa modele zużycia jednostki: _Spalania_ i _Electric_. Określanie w jednym żądaniu parametrów, które należą do różnych modeli, jest błędne.
Modelu zużycia nie można używać w połączeniu z parametrem **travelMode** mającym wartości _bicycle_ i _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Ograniczenia parametru dla modelu użycia

W obu modelach przez jawne określenie niektóre parametry wymaga określenia pliku inne również. Te zależności są:

* Wszystkie parametry wymagają **constantSpeedConsumption** określony przez użytkownika. Jest to błąd, aby określić wszelkie inne użycie modelu parametry, z wyjątkiem produktów **vehicleWeight**, jeśli **constantSpeedConsumption** nie zostanie określony.
* **accelerationEfficiency** i **decelerationEfficiency** zawsze muszą być określone jako pary (czyli zarówno lub brak).
* Jeśli **accelerationEfficiency** i **decelerationEfficiency** podano iloczyn wartości nie może być większa niż 1 (Aby zapobiec perpetual ruchu).
* **uphillEfficiency** i **downhillEfficiency** zawsze muszą być określone jako pary (czyli zarówno lub brak).
* Jeśli **uphillEfficiency** i **downhillEfficiency** podano iloczyn wartości nie może być większa niż 1 (Aby zapobiec perpetual ruchu).
* Jeśli \* __wydajność__ parametrów są określone przez użytkownika, następnie **vehicleWeight** musi być także określona. Gdy **vehicleEngineType** jest _spalania_, **fuelEnergyDensityInMJoulesPerLiter** musi być także określona.
* **maxChargeInkWh** i **currentChargeInkWh** zawsze muszą być określone jako pary (czyli zarówno lub brak).

> [!NOTE]
> Jeśli tylko **constantSpeedConsumption** jest określony, żadne inne aspekty użycia, takich jak odcinkach oraz zwiększanie ich szybkości pojazdu są uwzględniane w obliczeniach zużycia.

## <a name="combustion-consumption-model"></a>Model użycia spalania

Model zużycia spalania jest używany, gdy parametr **vehicleEngineType** ma wartość _combustion_.
Lista parametrów, które należą do tego modelu są wyświetlane poniżej. Zapoznaj się z sekcją parametry, aby uzyskać szczegółowy opis.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model Electric zużycie

Electric modelu użycia jest używany podczas **vehicleEngineType** ustawiono _electric_.
Lista parametrów, które należą do tego modelu są wyświetlane poniżej. Zapoznaj się z sekcją parametry, aby uzyskać szczegółowy opis.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Rozsądne wartości zużycie parametrów

Określony zestaw parametrów użycie można odrzucić, mimo że może spełnić, jawna wymagania wymienione powyżej. Zdarza się, gdy wartość określonego parametru lub kombinację wartości kilku parametrów, uznaje doprowadzić do wielkości nieuzasadnione wymaganie wartości zużycie. Jeśli tak się stanie, najprawdopodobniej oznacza błąd danych wejściowych jako prawidłowego jest zadbać, aby pomieścić wszystkie rozsądne wartości zużycie parametrów. W przypadku, gdy określony zestaw parametrów użycia została odrzucona, towarzyszący komunikat o błędzie będzie zawierać tekstową wyjaśnienia przyczyn.
Szczegółowe opisy parametrów zawierają przykłady rozsądne wartości dla obu modeli.
