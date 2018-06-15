---
title: Eksportowanie modelu usługi wizji niestandardowe do - niestandardowe wizji Service - Azure kognitywnych usług mobilnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyeksportować modelu podczas tworzenia aplikacji dla urządzeń przenośnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: ce8f42d6239867dd217cddfc61a27d7835dc9c9b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349896"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Eksportowanie modelu do użytku z urządzeniami przenośnymi

Usługa wizji niestandardowa umożliwia klasyfikatory mają zostać wyeksportowane do uruchamiania w trybie offline. Można osadzić z wyeksportowanego klasyfikatora do aplikacji i uruchomić je lokalnie na urządzenie na potrzeby klasyfikacji w czasie rzeczywistym. 

Niestandardowe wizji usługa obsługuje następujące eksportów:

* __Tensorflow__ dla __Android__.
* __CoreML__ dla __iOS11__.
* __ONNX__ dla __Windows ML__.
* Systemu Windows lub Linux __kontenera__. Kontener zawiera Tensorflow modelu i usługi kodu w celu użycia interfejsu API usługi wizji niestandardowe. 

> [!IMPORTANT]
> Usługa niestandardowa wizji eksportuje jedynie te __compact__ domen. Modele generowane przez compact domeny są zoptymalizowane pod kątem ograniczenia klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Klasyfikatory skompilowanej za pomocą compact domeny mogą być nieco mniej dokładne niż standardowej w domenę z tym samym ilością danych szkoleniowych.
>
> Uzyskać informacji na temat zwiększania Twojej klasyfikatory, zobacz [poprawy Twojej klasyfikatora](getting-started-improving-your-classifier.md) dokumentu.

## <a name="convert-to-a-compact-domain"></a>Konwertuj na compact domeny

> [!NOTE]
> Kroki opisane w tej sekcji mają zastosowanie, jeśli masz istniejące klasyfikatora, który nie jest ustawiony na compact domeny.
 
Aby dokonać konwersji domeny istniejącego klasyfikatora, wykonaj następujące kroki:

1. Z [strony wizji niestandardowego](https://customvision.ai), wybierz pozycję __Home__ ikonę, aby wyświetlić listę projektów. Można również użyć [ https://customvision.ai/projects ](https://customvision.ai/projects) aby zobaczyć swoje projekty.

    ![Obraz macierzysty listy ikony, jak i projektów](./media/export-your-model/projects-list.png)

2. Wybierz projekt, a następnie wybierz __koło zębate__ ikonę w prawym górnym rogu strony.

    ![Obraz ikony koło zębate](./media/export-your-model/gear-icon.png)

3. W __domen__ zaznacz __compact__ domeny. Wybierz __Zapisz zmiany__ Aby zapisać zmiany.

    ![Obraz Wybór domeny](./media/export-your-model/domains.png)

4. W górnej części strony, wybierz __pociągu__ do ponownie ucz używają nowej domeny.

## <a name="export-your-model"></a>Eksportowanie modelu

Aby wyeksportować modelu po ponownego trenowania, wykonaj następujące kroki:

1. Przejdź do **wydajności** i wybierz __wyeksportować__. 

    ![Obraz ikony eksportu](./media/export-your-model/export.png)

    > [!TIP]
    > Jeśli __wyeksportować__ wpis nie jest dostępny, a następnie wybrane iteracje nie używa domeny compact. Użyj __iteracji__ części strony, aby wybrać iterację używa compact domeny, a następnie wybierz __wyeksportować__.

2. Wybierz format eksportowania, a następnie wybierz __wyeksportować__ można pobrać modelu.

## <a name="next-steps"></a>Kolejne kroki

Integracja modelu wyeksportowany do aplikacji. Dostępnych jest kilka przykładowych aplikacji:

* Przykład dla [w aplikacji systemu iOS za pomocą wyeksportowanego modelu CoreML](https://go.microsoft.com/fwlink/?linkid=857726) klasyfikacji obrazu w czasie rzeczywistym z Swift
* Przykładowa aplikacja dla systemu iOS dla [przy użyciu modelu CoreML wyeksportowanego za pomocą platformy Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) klasyfikacji obrazu w czasie rzeczywistym 
* Przykładowe do [w aplikacji systemu Android za pomocą wyeksportowanego modelu Tensorflow](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) klasyfikacji obrazu w czasie rzeczywistym 
* [Przy użyciu modelu Tensorflow z systemem Windows](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/export-model-python)
* Przykładowe do [za pomocą wyeksportowanego modelu ONNX przy użyciu systemu Windows Machine Learning](https://azure.microsoft.com/en-us/resources/samples/cognitive-services-onnx-customvision-sample/)
