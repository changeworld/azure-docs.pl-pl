---
title: Testowanie i przekwalifikowanie modelu — usługa niestandardowej wizji
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak przetestować obraz, a następnie użyć go do ponownego przeszkolenia modelu w usłudze Niestandardowej wizji.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: c6ed8869f6d33609381a42fd22d728e9e5542802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73721201"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testowanie i przekwalifikowanie modelu za pomocą usługi Custom Vision Service

Po przeszkoleniu modelu można go szybko przetestować przy użyciu lokalnie przechowywanego obrazu lub obrazu online. Test używa ostatnio przeszkolonych iteracji.

## <a name="test-your-model"></a>Testowanie modelu

1. Na [stronie internetowej Custom Vision](https://customvision.ai)wybierz projekt. Wybierz **szybki test** po prawej stronie górnego paska menu. Ta akcja otwiera okno z etykietą **Szybki test**.

    ![Przycisk Szybki test jest wyświetlany w prawym górnym rogu okna.](./media/test-your-model/quick-test-button.png)

2. W oknie **Szybki test** kliknij pole **Prześlij obraz** i wprowadź adres URL obrazu, którego chcesz użyć do testu. Jeśli zamiast tego chcesz użyć obrazu przechowywanego lokalnie, kliknij przycisk **Przeglądaj pliki lokalne** i wybierz plik obrazu lokalnego.

    ![Obraz strony przesłania obrazu](./media/test-your-model/submit-image.png)

Wybrany obraz pojawi się na środku strony. Następnie wyniki pojawiają się pod obrazem w postaci tabeli z dwiema kolumnami, **oznaczonymi tagami** i **zaufaniem**. Po wyświetleniach wyników można zamknąć okno **Szybki test.**

Teraz można dodać ten obraz testowy do modelu, a następnie przekwalifikować model.

## <a name="use-the-predicted-image-for-training"></a>Użyj przewidywanego obrazu do treningu

Aby użyć obrazu przesłanego wcześniej do szkolenia, należy wykonać następujące kroki:

1. Aby wyświetlić obrazy przesłane do klasyfikatora, otwórz [stronę internetową Usługi Custom Vision](https://customvision.ai) i wybierz kartę __Prognozy.__

    ![Obraz karty prognoz](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Widok domyślny pokazuje obrazy z bieżącej iteracji. Można użyć pola rozwijanej __Iteracja__ do wyświetlania obrazów przesłanych podczas poprzednich iteracji.

2. Umieść wskaźnik myszy na obrazie, aby wyświetlić znaczniki, które były przewidywane przez klasyfikatora.

    > [!TIP]
    > Obrazy są uszeregowane, dzięki czemu obrazy, które mogą przynieść najwięcej korzyści do klasyfikatora, znajdują się na górze. Aby wybrać inne sortowanie, użyj sekcji __Sortowanie.__

    Aby dodać obraz do danych treningowych, zaznacz obraz, zaznacz znacznik, a następnie wybierz pozycję __Zapisz i zamknij__. Obraz zostanie usunięty z __prognoz__ i dodany do obrazów szkoleniowych. Można go wyświetlić, wybierając kartę __Obrazy szkoleniowe.__

    ![Obraz strony tagowania](./media/test-your-model/tag-image.png)

3. Użyj __Train przycisku,__ aby przeszkolić klasyfikatora.

## <a name="next-steps"></a>Następne kroki

[Poprawianie klasyfikatora](getting-started-improving-your-classifier.md)
