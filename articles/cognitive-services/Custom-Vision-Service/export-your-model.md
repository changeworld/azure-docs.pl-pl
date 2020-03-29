---
title: Eksportowanie modelu do urządzeń przenośnych — usługa Custom Vision Service
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak wyeksportować model do użycia w tworzeniu aplikacji mobilnych lub uruchomić lokalnie do klasyfikacji w czasie rzeczywistym.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718956"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Eksportowanie modelu do użytku z urządzeniami mobilnymi

Usługa Custom Vision Service umożliwia eksportowanie klasyfikatorów w trybie offline. Eksportowany klasyfikator można osadzić w aplikacji i uruchomić go lokalnie na urządzeniu do klasyfikacji w czasie rzeczywistym.

## <a name="export-options"></a>Opcje eksportu

Usługa Custom Vision Service obsługuje następujące eksporty:

* __Tensorflow__ dla __Androida__.
* __CoreML__ dla __systemu iOS11__.
* __ONNX__ dla __Windows ML__.
* __[Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* __Kontener platformy Docker__ dla architektury Windows, Linux lub ARM. Kontener zawiera model Tensorflow i kod usługi do korzystania z interfejsu API niestandardowej wizji.

> [!IMPORTANT]
> Usługa Custom Vision Service eksportuje tylko domeny __kompaktowe.__ Modele generowane przez domeny kompaktowe są zoptymalizowane pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Klasyfikatory zbudowane z domeny kompaktowej mogą być nieco mniej dokładne niż standardowa domena z taką samą ilością danych szkoleniowych.
>
> Aby uzyskać informacje na temat poprawy klasyfikatorów, zobacz poprawianie dokumentu [klasyfikatora.](getting-started-improving-your-classifier.md)

## <a name="convert-to-a-compact-domain"></a>Konwertowanie na domenę kompaktową

> [!NOTE]
> Kroki opisane w tej sekcji mają zastosowanie tylko wtedy, gdy masz istniejący model, który nie jest ustawiony na domenę kompaktową.

Aby przekonwertować domenę istniejącego modelu, należy wykonać następujące czynności:

1. W [witrynie sieci Web Niestandardowych wizji](https://customvision.ai)wybierz ikonę __Strona główna,__ aby wyświetlić listę projektów.

    ![Obraz ikony głównej i listy projektów](./media/export-your-model/projects-list.png)

1. Wybierz projekt, a następnie wybierz ikonę __Koła zębatego__ w prawym górnym rogu strony.

    ![Obraz ikony koła zębatego](./media/export-your-model/gear-icon.png)

1. W sekcji __Domeny__ wybierz jedną z domen __kompaktowych.__ Wybierz __pozycję Zapisz zmiany,__ aby zapisać zmiany. 

    > [!NOTE]
    > W przypadku zestawu WizjeSciarka projekt musi zostać utworzony przy pomocy domeny __Ogólne (Compact)__ i należy określić opcję **Zestawu deweloperów AI wizji** w sekcji Możliwości **eksportu.**

    ![Obraz wyboru domen](./media/export-your-model/domains.png)

1. U góry strony wybierz pozycję __Trenuj,__ aby przekwalifikować się przy użyciu nowej domeny.

## <a name="export-your-model"></a>Eksportowanie modelu

Aby wyeksportować model po przekwalifikowaniu, należy wykonać następujące czynności:

1. Przejdź do karty **Wydajność** i wybierz pozycję __Eksportuj__. 

    ![Obraz ikony eksportu](./media/export-your-model/export.png)

    > [!TIP]
    > Jeśli wpis __Eksportuj__ nie jest dostępny, wybrana iteracja nie używa domeny kompaktowej. Użyj sekcji __Iteracje__ tej strony, aby wybrać iterację, która używa domeny kompaktowej, a następnie wybierz pozycję __Eksportuj__.

1. Wybierz żądany format eksportu, a następnie wybierz __pozycję Eksportuj,__ aby pobrać model.

## <a name="next-steps"></a>Następne kroki

Zintegruj eksportowany model z aplikacją, eksplorując jeden z następujących artykułów lub przykładów:

* [Użyj modelu Tensorflow z Pythonem](export-model-python.md)
* [Korzystanie z modelu ONNX z uczeniem maszynowym systemu Windows](custom-vision-onnx-windows-ml.md)
* Zobacz przykład dla [modelu CoreML w aplikacji dla systemu iOS](https://go.microsoft.com/fwlink/?linkid=857726) do klasyfikacji obrazów w czasie rzeczywistym z Swift.
* Zobacz przykład dla [modelu Tensorflow w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) do klasyfikacji obrazu w czasie rzeczywistym w systemie Android.
* Zobacz przykład dla [modelu CoreML z xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) klasyfikacji obrazu w czasie rzeczywistym w aplikacji xamarin systemu iOS.
