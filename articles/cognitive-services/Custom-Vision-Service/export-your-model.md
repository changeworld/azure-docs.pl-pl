---
title: Eksportuj model do urządzeń przenośnych Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wyeksportować model do użycia podczas tworzenia aplikacji mobilnych.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 554a392a7f815a6e646927f137b1e6c2856099bd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561088"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Eksportowanie modelu do użytku z urządzeniami przenośnymi

Custom Vision Service zezwala na eksportowanie klasyfikatorów do trybu offline. Możesz osadzić wyeksportowany klasyfikator w aplikacji i uruchomić ją lokalnie na urządzeniu na potrzeby klasyfikacji w czasie rzeczywistym.

Custom Vision Service obsługuje następujące eksporty:

* __Tensorflow__ dla __systemu Android__.
* __CoreML__ dla __iOS11__.
* __ONNX__ dla __systemu Windows ml__.
* __Kontener__systemu Windows lub Linux. Kontener zawiera model Tensorflow i kod usługi do korzystania z interfejsu API Custom Vision Service. 

> [!IMPORTANT]
> Custom Vision Service eksportuje  tylko domeny kompaktowe. Modele generowane przez domeny kompaktowe są zoptymalizowane pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Klasyfikatory skompilowane z kompaktową domeną mogą być nieco mniej dokładne niż domena standardowa z taką samą ilością danych szkoleniowych.
>
> Aby uzyskać informacje na temat ulepszania klasyfikatorów, zobacz ulepszanie dokumentu [klasyfikatora](getting-started-improving-your-classifier.md) .

## <a name="convert-to-a-compact-domain"></a>Konwertuj na kompaktową domenę

> [!NOTE]
> Kroki opisane w tej sekcji mają zastosowanie tylko wtedy, gdy masz istniejący klasyfikator, który nie jest ustawiony na kompaktową domenę.

Aby skonwertować domenę istniejącego klasyfikatora, wykonaj następujące czynności:

1. Na [stronie](https://customvision.ai)niestandardowa wizja wybierz ikonę __główną__ , aby wyświetlić listę projektów. Możesz również użyć, [https://customvision.ai/projects](https://customvision.ai/projects) aby zobaczyć swoje projekty.

    ![Obraz przedstawiający ikonę główną i listę projektów](./media/export-your-model/projects-list.png)

2. Wybierz projekt, a następnie wybierz ikonę __koła zębatego__ w prawym górnym rogu strony.

    ![Obraz ikony koła zębatego](./media/export-your-model/gear-icon.png)

3. W sekcji __domeny__ wybierz kompaktową domenę  . Wybierz pozycję __Zapisz zmiany__ , aby zapisać zmiany.

    ![Obraz wyboru domen](./media/export-your-model/domains.png)

4. W górnej części strony wybierz pozycję uczenie  , aby ponownie przeprowadzić szkolenie przy użyciu nowej domeny.

## <a name="export-your-model"></a>Eksportowanie modelu

Aby wyeksportować model po przekształceniu, wykonaj następujące czynności:

1. Przejdź do karty **wydajność** i wybierz pozycję __Eksportuj__. 

    ![Obraz ikony eksportu](./media/export-your-model/export.png)

    > [!TIP]
    > Jeśli wpis __eksportu__ nie jest dostępny, wybrana iteracja nie korzysta z domeny kompaktowej. Użyj sekcji __iteracje__ na tej stronie, aby wybrać iterację korzystającą z domeny kompaktowej, a następnie wybierz pozycję __Eksportuj__.

2. Wybierz format eksportu, a następnie wybierz pozycję __Eksportuj__ , aby pobrać model.

## <a name="next-steps"></a>Następne kroki

Aby zintegrować wyeksportowany model z aplikacją, należy zapoznać się z jednym z następujących artykułów lub przykładów:

* [Korzystanie z modelu Tensorflow w języku Python](export-model-python.md)
* [Korzystanie z modelu ONNX z systemem Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Zobacz przykład for [CoreML model w aplikacji dla systemu iOS](https://go.microsoft.com/fwlink/?linkid=857726) dla klasyfikacji obrazów w czasie rzeczywistym przy użyciu Swift.
* Zobacz przykład for [Tensorflow model w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) dla klasyfikacji obrazów w czasie rzeczywistym w systemie Android.
* Zobacz przykład for [CoreML model z](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) platformą Xamarin dla klasyfikacji obrazów w czasie rzeczywistym w aplikacji platformy Xamarin dla systemu iOS.
