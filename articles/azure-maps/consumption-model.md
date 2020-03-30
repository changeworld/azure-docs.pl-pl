---
title: Modele zużycia pojazdów do wyznaczania tras | Mapy platformy Microsoft Azure
description: Z tego artykułu dowiesz się o modelach zużycia pojazdów do routingu w usługach Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190251"
---
# <a name="consumption-model"></a>Model użycia

Usługa Routing udostępnia zestaw parametrów szczegółowego opisu modelu zużycia specyficznego dla pojazdu.
W zależności od wartości **pojazduEngineType**obsługiwane są dwa główne modele zużycia: _combustion_ i _electric._ Jest niepoprawna, aby określić parametry, które należą do różnych modeli w tym samym żądaniu. Ponadto parametry modelu zużycia nie mogą być używane z następującymi **wartościami travelMode:** _rower_ i _pieszy_.

## <a name="parameter-constraints-for-consumption-model"></a>Ograniczenia parametrów dla modelu zużycia

W obu modelach zużycia istnieją pewne zależności podczas określania parametrów. Oznacza to, że jawnie określające niektóre parametry mogą wymagać określenia niektórych innych parametrów. Oto następujące zależności, o których należy pamiętać:

* Wszystkie parametry wymagają **stałejSpeedConsumption** być określone przez użytkownika. Jest to błąd, aby określić inny parametr modelu zużycia, jeśli **constantSpeedConsumption** nie jest określony. Parametr **vehicleWeight** jest wyjątkiem dla tego wymogu.
* **przyspieszenieWydajność** i **zwalnianieZespoczywość** musi być zawsze określona jako para (czyli obie lub żadna).
* Jeśli **określono przyspieszenieWyefektywność** i **zwalnianieWydanie,** iż ich wartości nie mogą być większe niż 1 (aby zapobiec ciągłemu ruchowi).
* **wydajność** pod górę i **zjazdySkutowanie** musi być zawsze określone jako para (to jest, zarówno lub żaden).
* Jeśli **podgóręWydanie** i **zjazdYskuteczność** są określone, iloczyn ich wartości nie może być większa niż 1 (aby zapobiec bezterminowy ruch).
* \*Jeśli parametry __wydajności__ są określone przez użytkownika, należy również określić **vehicleWeight.** Gdy **pojazdEngineType** jest _spalanie_, **paliwaEnergyDensityInMJoulesPerLiter** muszą być określone, jak również.
* **maxChargeInkWh** i **currentChargeInkWh** musi być zawsze określona jako para (czyli obie lub żadna).

> [!NOTE]
> Jeśli określono tylko **stałąSpeedConsumption,** żadne inne aspekty zużycia, takie jak nachylenie i przyspieszenie pojazdu, nie są brane pod uwagę przy obliczaniu zużycia.

## <a name="combustion-consumption-model"></a>Model zużycia spalania

Model zużycia spalin jest używany, gdy **vehicleEngineType** jest ustawiony na _spalanie_.
Poniżej znajduje się lista parametrów należących do tego modelu. Szczegółowe opis można znaleźć w sekcji Parametry.

* stałaSpeedConsumptionInLitersPerHundredkm
* pojazdWędk
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* paliwoEnergyDensityInMJoulesPerLiter
* przyspieszenieWydajność
* efektywność zwalniania
* pod góręWydajność
* zjazdWydajność

## <a name="electric-consumption-model"></a>Model zużycia energii elektrycznej

Model zużycia energii elektrycznej jest używany, gdy **vehicleEngineType** jest ustawiony na _elektryczny._
Poniżej znajduje się lista parametrów należących do tego modelu. Szczegółowe opis można znaleźć w sekcji Parametry.

* stałaSpeedConsumptionInkWhPerHundredkm
* pojazdWędk
* currentChargeInkWh
* maxChargeInkWh
* pomocniczyPowerInkW
* przyspieszenieWydajność
* efektywność zwalniania
* pod góręWydajność
* zjazdWydajność

## <a name="sensible-values-of-consumption-parameters"></a>Rozsądne wartości parametrów zużycia

Określony zestaw parametrów zużycia można odrzucić, nawet jeśli zestaw może spełniać wszystkie jawne wymagania. Dzieje się tak, gdy wartość określonego parametru lub kombinacji wartości kilku parametrów jest uważana za prowadzącą do nieuzasadnionych wielkości wartości zużycia. Jeśli tak się stanie, najprawdopodobniej wskazuje błąd wejściowy, ponieważ należy dopilna dołów, aby uwzględnić wszystkie rozsądne wartości parametrów zużycia. W przypadku odrzucenia określonego zestawu parametrów zużycia towarzyszący komunikat o błędzie będzie zawierał tekstowe wyjaśnienie przyczyn.
Szczegółowe opisy parametrów mają przykłady rozsądnych wartości dla obu modeli.
