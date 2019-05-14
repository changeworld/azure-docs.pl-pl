---
title: Eksportowanie modelu na urządzenia przenośne — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak wyeksportować modelu do użycia podczas tworzenia aplikacji dla urządzeń przenośnych.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 7bf8217f5076c0a95d4db6c1c7cbea7bc93b91f3
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550538"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Eksportowanie modelu do użytku z urządzeniami przenośnymi

Usługa Custom Vision Service umożliwia klasyfikatorów, które mają zostać wyeksportowane do uruchamiania w trybie offline. Można osadzać klasyfikatora wyeksportowany w aplikacji i uruchom lokalnie na urządzeniu w czasie rzeczywistym klasyfikacji.

Usługa Custom Vision Service obsługuje polecenie eksportuje następujące:

* __Tensorflow__ dla __Android__.
* __CoreML__ dla __system IOS 11__.
* __ONNX__ dla __Windows ML__.
* Windows lub Linux __kontenera__. Kontener zawiera Tensorflow modelu i obsługiwania kodu w celu użycia interfejs API Custom Vision Service. 

> [!IMPORTANT]
> Usługa Custom Vision Service eksportuje jedynie __compact__ domen. Modele generowane przez compact domeny są zoptymalizowane pod kątem ograniczenia klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Klasyfikatorów utworzonych za pomocą compact domeny może być nieco mniej dokładnie niż standardowej w domenę przy użyciu tej samej ilości danych szkoleniowych.
>
> Aby uzyskać informacji dotyczących zwiększania swoje klasyfikatorów, zobacz [ulepszania klasyfikatora](getting-started-improving-your-classifier.md) dokumentu.

## <a name="convert-to-a-compact-domain"></a>Konwertuj na compact domeny

> [!NOTE]
> Kroki opisane w tej sekcji jest stosowane tylko wtedy, jeśli masz istniejące klasyfikatora, który nie jest ustawiony na compact domeny.

Aby przekonwertować domeny istniejącego klasyfikatora, użyj następujących kroków:

1. Z [Custom vision strony](https://customvision.ai), wybierz opcję __Home__ ikonę, aby wyświetlić listę projektów. Można również użyć [ https://customvision.ai/projects ](https://customvision.ai/projects) Aby wyświetlić swoje projekty.

    ![Obraz macierzysty listy ikon i projektów](./media/export-your-model/projects-list.png)

2. Wybierz projekt, a następnie wybierz __koła zębatego__ ikonę w prawym górnym rogu strony.

    ![Obraz ikony koła zębatego](./media/export-your-model/gear-icon.png)

3. W __domen__ zaznacz __compact__ domeny. Wybierz __Zapisz zmiany__ Aby zapisać zmiany.

    ![Obraz przedstawiający Wybór domeny](./media/export-your-model/domains.png)

4. W górnej części strony, wybierz __Train__ doskonalenie używają nowej domeny.

## <a name="export-your-model"></a>Eksportowanie modelu

Aby wyeksportować modelu po ponownego trenowania, należy użyć następujących czynności:

1. Przejdź do **wydajności** kartę, a następnie wybierz pozycję __wyeksportować__. 

    ![Obraz ikony eksportu](./media/export-your-model/export.png)

    > [!TIP]
    > Jeśli __wyeksportować__ wpis nie jest dostępny, a następnie wybraną iterację nie używa compact domeny. Użyj __iteracji__ części tej strony, aby wybrać iterację, która używa compact domeny, a następnie wybierz __wyeksportować__.

2. Wybierz format eksportu, a następnie wybierz __wyeksportować__ można pobrać modelu.

## <a name="next-steps"></a>Kolejne kroki

Integrowanie wyeksportowanego modelu aplikacji za pośrednictwem jednego z następujących artykułów lub próbek:

* [Tensorflow model za pomocą języka Python](export-model-python.md)
* [Użyj modelu ONNX z usługą Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Zobacz przykład dla [CoreML modelu w aplikacji systemu iOS](https://go.microsoft.com/fwlink/?linkid=857726) klasyfikacji obrazów w czasie rzeczywistym za pomocą języka Swift.
* Zobacz przykład dla [Tensorflow modelu w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) klasyfikacji obrazów w czasie rzeczywistym w systemie Android.
* Zobacz przykład dla [modeli CoreML za pomocą platformy Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) klasyfikacji obrazów w czasie rzeczywistym w aplikacji platformy Xamarin.IOS.
