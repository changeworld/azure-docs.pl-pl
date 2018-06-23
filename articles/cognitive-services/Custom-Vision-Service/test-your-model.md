---
title: Testowania i ponownie ucz modelu - niestandardowe wizji Service - usług kognitywnych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak obraz testowy, a następnie użyć go do retrain modelu.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 1933b1a45844ac99308baebe59b49687a957abfa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348137"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testowanie i ponownie ucz modelu z usługą wizji niestandardowe

Po nauczenia modelu, można szybko można testować przy użyciu obrazu przechowywane lokalnie lub obrazu w trybie online. Uruchomienie testu używa najbardziej ostatnio przeszkolone iteracji.

## <a name="test-your-model"></a>Testowanie modelu

1. Z [wizji niestandardowe strony sieci web](https://customvision.ai), wybierz projekt. Wybierz **szybkiego testu** po prawej stronie paska menu u góry. Ta akcja powoduje otwarcie okna z etykietą **szybkiego testu**.

    ![Przycisk Test szybkie jest wyświetlany w prawym górnym rogu okna.](./media/test-your-model/quick-test-button.png)

2. W **szybki Test** kliknij w **przesłać obraz** pola, a następnie wprowadź adres URL obrazu, którego chcesz użyć dla testu. Jeśli chcesz użyć obrazu przechowywane lokalnie, kliknij przycisk **Przeglądaj lokalne pliki** i wybrać plik obrazu lokalnego.

    ![Obraz strony przesyłania obrazów](./media/test-your-model/submit-image.png)

Wybrany obraz pojawia się w środku strony. Wyniki są wyświetlane poniżej obrazu w formie tabeli z kolumnami etykietą, a następnie **tagi** i **zaufania**. Po wyświetleniu wyników można zamknąć **szybkiego testu** okna.

Można teraz dodać ten obraz testu do modelu i ponownie ucz modelu.

## <a name="use-the-predicted-image-for-training"></a>Użyć obrazu przewidywane szkolenia.

Aby użyć wcześniej przesłany do trenowania obraz, wykonaj następujące kroki:

1. Aby wyświetlić obrazy przesłane do klasyfikatora, otwórz [wizji niestandardowe strony sieci web](https://customvision.ai) i wybierz __prognoz__ kartę.

    ![Obraz na karcie prognoz](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Widok domyślny pokazuje obrazów z bieżącej iteracji. Można użyć __iteracji__ listy rozwijanej pola, aby wyświetlić obrazy przesłane podczas poprzedniej iteracji.

2. Umieść kursor nad obraz, aby zobaczyć tagi, które zostały przewidzieć klasyfikatora.

    > [!TIP]
    > Obrazy są klasyfikowane, tak aby obrazów, które można przełączyć większości korzyści klasyfikatora znajdowały się u góry. Aby wybrać inną sortowanie, użyj __sortowania__ sekcji.

    Aby dodać obraz do danych szkoleniowych, wybierz obraz, zaznacz tag, a następnie wybierz __Zapisz i Zamknij__. Obraz jest usuwany z __prognoz__ i dodać do obrazów szkolenia. Możesz je wyświetlić, wybierając __obrazów szkolenia__ kartę.

    ![Obraz strony znaczników](./media/test-your-model/tag-image.png)

3. Użyj __pociągu__ przycisk, aby ponownie ucz klasyfikatora.

## <a name="next-steps"></a>Kolejne kroki

[Poprawa klasyfikatora programu](getting-started-improving-your-classifier.md)