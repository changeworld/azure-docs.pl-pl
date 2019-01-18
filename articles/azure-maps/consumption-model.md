---
title: Zużycie modelu w społeczności Maps Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat modelu zużycie w społeczności Maps Azure
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: cf20c7dbfbf7cd3f09579b03b835148c1c295137
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600633"
---
# <a name="consumption-model"></a>Model użycia

Online Routing zawiera zestaw parametrów szczegółowy opis modelu zużycie specyficzne dla pojazdów.
W zależności od wartości **vehicleEngineType**, obsługiwane są dwa modele zużycie główna: _Combustion_ i _Electric_. Określanie parametrów, które należą do różnych modeli w jednym żądaniu występuje błąd.
Użycie modelu nie można używać z **travelMode** wartości _bicycle_ i _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Ograniczenia parametru dla modelu zużycie

W obu modelach przez jawne określenie niektórych parametrów wymaga określenia inne również. Te zależności to:

* Wszystkie parametry wymagają **constantSpeedConsumption** określonych przez użytkownika. Jest błąd, aby parametr żadnych innych zużycie modelu, z wyjątkiem produktów **vehicleWeight**, jeśli **constantSpeedConsumption*** nie jest określona.
* **accelerationEfficiency** i **decelerationEfficiency** zawsze muszą być określone jako pary (tj. oba lub brak).
* Jeśli **accelerationEfficiency** i **decelerationEfficiency** podano iloczyn wartości nie może być większa niż 1 (Aby zapobiec perpetual ruchu).
* **uphillEfficiency** i **downhillEfficiency** zawsze muszą być określone jako pary (tj. oba lub brak).
* Jeśli **uphillEfficiency** i **downhillEfficiency** podano iloczyn wartości nie może być większa niż 1 (Aby zapobiec perpetual ruchu).
* Jeśli \* **wydajności** parametry są określone przez użytkownika, następnie **vehicleWeight** musi być także określona. Gdy **vehicleEngineType** jest _spalania_, **fuelEnergyDensityInMJoulesPerLiter** musi być także określona.
* **maxChargeInkWh** i **currentChargeInkWh** zawsze muszą być określone jako pary (tj. oba lub brak).

> [!NOTE]
> Jeśli tylko **constantSpeedConsumption** jest określony, żadne inne aspekty zużycia, takich jak odcinkach i przyspieszenie vehicle są brane pod uwagę w obliczeniach zużycia.

## <a name="combustion-consumption-model"></a>Model zużycie spalania

Model zużycie spalania jest używany podczas **vehicleEngineType** ustawiono _combustion_.
Lista parametrów, które należą do tego modelu są poniżej. Zapoznaj się z sekcją parametrów, aby uzyskać szczegółowy opis.

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

Electric Model zużycie jest używany podczas **vehicleEngineType** ustawiono _electric_.
Lista parametrów, które należą do tego modelu są poniżej. Zapoznaj się z sekcją parametrów, aby uzyskać szczegółowy opis.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Za pośrednictwem wartości parametrów zużycie

Określony zestaw parametrów zużycie można odrzucić, mimo że może spełnić wszystkie wymagania jawne wymienione powyżej. Występuje, jeśli wartość określonego parametru lub kombinacja wartości kilku parametrów, uznaje się prowadzić do wielkości nadmierne zużycie wartości. Jeśli tak się stanie, prawdopodobnie oznacza błąd danych wejściowych jako prawidłowego jest zadbać, aby uwzględnić wszystkie wartości za pośrednictwem parametrów zużycia. W przypadku odrzucenia określony zestaw parametrów zużycie wyświetlony komunikat o błędzie będzie zawierać tekstową wyjaśnienie z przyczyn.
Szczegółowe opisy parametrów ma przykłady za pośrednictwem wartości dla obu modeli.
