---
title: 'Model wykrywania anomalii pociągu: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu modelu wykrywania anomalii pociągu do utworzenia wyszkolonego modelu wykrywania anomalii.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502973"
---
# <a name="train-anomaly-detection-model"></a>Model wykrywania anomalii pociągu

W tym artykule opisano, jak użyć modułu **modelu wykrywania anomalii pociągu** w projektancie usługi Azure Machine Learning (wersja zapoznawcza) w celu utworzenia modelu wykrywania uszemionych anomalii.

Moduł przyjmuje jako dane wejściowe zestaw parametrów modelu dla modelu wykrywania anomalii i nieoznakowany zestaw danych. Zwraca wyszkolony model wykrywania anomalii wraz z zestawem etykiet dla danych szkoleniowych.  

Aby uzyskać więcej informacji na temat algorytmów wykrywania anomalii podanych w projektancie, zobacz następujące tematy: 

+ [Wykrywanie anomalii oparte na pca](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Jak skonfigurować model wykrywania anomalii pociągu 

1.  Dodaj moduł **modelu wykrywania anomalii pociągu** do potoku w projektancie. Ten moduł można znaleźć w kategorii **Wykrywanie anomalii.**

2. Podłącz jeden z modułów przeznaczonych do wykrywania anomalii, takich jak [wykrywanie anomalii na podstawie PCA](pca-based-anomaly-detection.md)

    Inne typy modeli nie są obsługiwane; po uruchomieniu potoku pojawi się błąd: wszystkie modele muszą mieć ten sam typ ucznia.  

3.  Skonfiguruj moduł wykrywania anomalii, wybierając kolumnę etykiety i ustawiając inne parametry specyficzne dla algorytmu.  

4.  Dołącz zestaw danych szkoleniowych do po prawej stronie wejścia **modelu wykrywania anomalii pociągu**.  

5.  Prześlij potok.  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić parametry modelu, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**. 

+ Aby utworzyć prognoz, należy użyć [score model](score-model.md) z nowymi danymi wejściowymi.

+ Aby zapisać migawkę uczonego modelu, wybierz moduł i kliknij ikonę **Zarejestruj zestaw danych** w obszarze Dane **wyjściowe +dzienniki** w prawym panelu.   

 
## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 

Zobacz [wyjątki i kody błędów dla projektanta (wersja zapoznawcza)](designer-error-codes.md) dla listy błędów specyficznych dla modułów projektanta.
'