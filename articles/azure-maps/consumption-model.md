---
title: Modele zużycia pojazdów dla routingu | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje dotyczące modeli zużycia pojazdów do routingu w Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911715"
---
# <a name="consumption-model"></a>Model użycia

Routing online zawiera zestaw parametrów dla szczegółowego opisu modelu zużycia specyficznego dla pojazdu.
W zależności od wartości parametru **vehicleEngineType** obsługiwane są dwa główne modele zużycia: _Combustion_ i _Electric_. Określanie w jednym żądaniu parametrów, które należą do różnych modeli, jest błędne.
Modelu zużycia nie można używać w połączeniu z parametrem **travelMode** mającym wartości _bicycle_ i _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Ograniczenia parametrów dla modelu zużycia

W obu modelach użycia jawnie Określanie niektórych parametrów wymaga również określenia innych. Te zależności są następujące:

* Wszystkie parametry wymagają określenia **constantSpeedConsumption** przez użytkownika. Wystąpił błąd podczas określania dowolnego innego parametru modelu zużycia, z wyjątkiem **vehicleWeight**, jeśli **constantSpeedConsumption** nie jest określony.
* **accelerationEfficiency** i **decelerationEfficiency** muszą być zawsze określone jako para (tj. oba lub None).
* Jeśli **accelerationEfficiency** i **decelerationEfficiency** są określone, iloczyn ich wartości nie może być większy niż 1 (w celu uniknięcia bezterminowego ruchu).
* **uphillEfficiency** i **downhillEfficiency** muszą być zawsze określone jako para (tj. oba lub None).
* Jeśli **uphillEfficiency** i **downhillEfficiency** są określone, iloczyn ich wartości nie może być większy niż 1 (w celu uniknięcia bezterminowego ruchu).
* Jeśli użytkownik określił \*parametry __wydajności__ , należy również określić **vehicleWeight** . Gdy **vehicleEngineType** jest _spalany_, należy również określić **fuelEnergyDensityInMJoulesPerLiter** .
* **maxChargeInkWh** i **currentChargeInkWh** muszą być zawsze określone jako para (tj. oba lub None).

> [!NOTE]
> Jeśli **constantSpeedConsumption** jest określony, żadne inne aspekty użycia, takie jak zbocze i przyspieszenie pojazdów, są brane pod uwagę w przypadku obliczeń zużycia.

## <a name="combustion-consumption-model"></a>Model użycia spalania

Model zużycia spalania jest używany, gdy parametr **vehicleEngineType** ma wartość _combustion_.
Poniżej znajduje się lista parametrów należących do tego modelu. Szczegółowy opis można znaleźć w sekcji parametry.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model zużycia elektrycznego

Model zużycia elektrycznego jest używany, gdy **vehicleEngineType** jest ustawiony na wartość _elektryczną_.
Poniżej znajduje się lista parametrów należących do tego modelu. Szczegółowy opis można znaleźć w sekcji parametry.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Rozsądne wartości parametrów zużycia

Określony zestaw parametrów zużycia można odrzucić, nawet jeśli może spełnić wszystkie jawne wymagania określone powyżej. Dzieje się tak, gdy wartość określonego parametru lub kombinacja wartości kilku parametrów jest uznawana za potencjalną do nieuzasadnionej wielkości wartości zużycia. Jeśli tak się stanie, najprawdopodobniej wskazuje to na błąd danych wejściowych, ponieważ należy zwrócić uwagę na wszystkie rozsądne wartości parametrów zużycia. W przypadku odrzucenia określonego zestawu parametrów zużycia, towarzyszący komunikat o błędzie będzie zawierać tekstowe wyjaśnienie przyczyn.
Szczegółowe opisy parametrów zawierają przykłady rozsądnych wartości dla obu modeli.
