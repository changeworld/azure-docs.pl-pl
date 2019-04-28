---
title: Testowanie i ponowne szkolenie modelu — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak przetestować obraz, a następnie użyć go do Ponowne szkolenie modelu.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: d516cee81aef66ec58399cb5ff23c89db16bf2ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816655"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testowanie i ponowne szkolenie modelu z usługi Custom Vision Service

Po użytkownik nauczenia modelu, możesz szybko przetestować go przy użyciu obraz przechowywany lokalnie lub obrazu w trybie online. Test używa najbardziej niedawno uczonego iteracji.

## <a name="test-your-model"></a>Testowanie modelu

1. Z [strony sieci web Custom Vision](https://customvision.ai), wybierz swój projekt. Wybierz **przeprowadzić szybki Test** po prawej stronie na pasku menu u góry. Ta akcja powoduje otwarcie okna z etykietą **przeprowadzić szybki Test**.

    ![Przycisk przeprowadzić szybki Test jest wyświetlany w prawym górnym rogu okna.](./media/test-your-model/quick-test-button.png)

2. W **szybkie testowanie** kliknij w oknie **przesłać obraz** pola, a następnie wprowadź adres URL obrazu, którego chcesz użyć dla testu. Jeśli chcesz zamiast tego użyj obraz przechowywany lokalnie, kliknij przycisk **Przeglądaj pliki lokalne** przycisku i wybierz plik lokalny obraz.

    ![Obraz strony przesyłania obrazów](./media/test-your-model/submit-image.png)

Obraz, który wybierzesz pojawia się w środku strony. Następnie wyniki są wyświetlane poniżej obrazu w postaci tabeli z dwiema kolumnami, etykietą **tagi** i **ufności**. Po wyświetleniu wyników, możesz zamknąć **przeprowadzić szybki Test** okna.

Możesz teraz dodać ten obraz testowy do modelu, a następnie ponowne szkolenie modelu.

## <a name="use-the-predicted-image-for-training"></a>Korzystanie z obrazu przewidywane potrzeby szkolenia

Aby użyć obrazu przesłane wcześniej, szkolenia, użyj następujących czynności:

1. Aby wyświetlić obrazy przesyłany do usługi klasyfikatora, otwórz [strony sieci web Custom Vision](https://customvision.ai) i wybierz __prognozy__ kartę.

    ![Obraz karty prognozy](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Widok domyślny pokazuje obrazów z bieżącą iterację. Możesz użyć __iteracji__ rozwijane pole, aby wyświetlić obrazy przesłane podczas poprzednich iteracji.

2. Umieść kursor nad obrazu, aby wyświetlić tagi, które zostały przewidywane według klasyfikatora.

    > [!TIP]
    > Obrazy są oceniane tak, aby obrazy, które może przynieść większość zysków klasyfikatora znajdowały się u góry. Aby wybrać inny sortowanie, użyj __sortowania__ sekcji.

    Aby dodać obraz do danych szkoleniowych, wybierz obraz, wybierz znacznik, a następnie wybierz __Zapisz i Zamknij__. Obraz jest usuwany z __prognozy__ i dodane do uczone obrazy. Można je wyświetlić, wybierając __Uczone obrazy__ kartę.

    ![Obraz strony znakowania](./media/test-your-model/tag-image.png)

3. Użyj __Train__ przycisk doskonalenie klasyfikatora.

## <a name="next-steps"></a>Kolejne kroki

[Poprawianie klasyfikatora](getting-started-improving-your-classifier.md)
