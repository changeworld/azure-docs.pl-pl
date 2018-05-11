---
title: Zużycie modelu w społeczności Maps Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat modelu zużycie w społeczności Maps Azure
services: azure-maps
keywords: ''
author: subbarayudukamma
ms.author: skamma
ms.date: 5/8/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 146ea084c02bb3de0c74da79ca85021589207de8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="consumption-model"></a>Model zużycie

Online Routing zawiera zestaw parametrów szczegółowy opis modelu zużycie specyficzne dla pojazdów.
W zależności od wartości **vehicleEngineType**, obsługiwane są dwa modele zużycie główna: _spalania_ i _Electric_. Określanie parametrów, które należą do różnych modeli w jednym żądaniu występuje błąd.
Użycie modelu nie można używać z **travelMode** wartości _rowerów_ i _pieszego_.

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

Model zużycie spalania jest używany podczas **vehicleEngineType** ustawiono _spalania_.
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
