---
title: Szybsze oznaczanie obrazów za pomocą inteligentnego labelera
titleSuffix: Azure Cognitive Services
description: W tym przewodniku dowiesz się, jak używać inteligentnego labelera do generowania sugerowanych tagów obrazów. Dzięki temu można oznaczyć dużą liczbę obrazów szybciej podczas szkolenia modelu niestandardowej wizji.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647750"
---
# <a name="label-images-faster-with-smart-labeler"></a>Szybsze oznaczanie obrazów za pomocą inteligentnego labelera

W tym przewodniku dowiesz się, jak używać inteligentnego labelera do generowania sugerowanych tagów obrazów. Dzięki temu można oznaczyć dużą liczbę obrazów szybciej podczas szkolenia modelu niestandardowej wizji.

Podczas oznaczania obrazów dla modelu niestandardowej wizji usługa używa najnowszej uczonej iteracji modelu do przewidywania etykiet obrazów nieoznakowanych. Następnie pokazuje te prognozy jako sugerowane tagi, na podstawie wybranego progu zaufania i niepewności przewidywania. Następnie można potwierdzić lub zmienić sugestie, przyspieszając proces ręcznego oznaczania obrazów do treningu.

## <a name="when-to-use-smart-labeler"></a>Kiedy używać Inteligentnego Labelera

Należy pamiętać o następujących ograniczeniach:

* Należy poprosić tylko sugerowane tagi dla obrazów, których zawartość została już przeszkolona raz. Nie otrzyj sugestii dotyczących nowego tagu, który dopiero zaczynasz trenować.

> [!IMPORTANT]
> Funkcja Smart Labeler używa tego samego [modelu cenowego,](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) co regularne prognozy. Przy pierwszym uruchomieniu sugerowanych tagów dla zestawu obrazów zostanie naliczona opłata taka sama, jak w przypadku wywołań prognozowania. Następnie usługa przechowuje wyniki dla wybranych obrazów w bazie danych przez 30 dni i można uzyskać do nich dostęp w dowolnym momencie za darmo w tym okresie. Po 30 dniach zostanie naliczona opłata, jeśli ponownie poprosisz o ich sugerowane tagi.

## <a name="smart-labeler-workflow"></a>Przepływ pracy inteligentnego labelera

W poniższych krokach pokazano, jak używać inteligentnego labelera:

1. Prześlij wszystkie obrazy treningowe do projektu Custom Vision.
1. Oznacz część zestawu danych, wybierając taką samą liczbę obrazów dla każdego tagu.
    > [!TIP]
    > Upewnij się, że używasz wszystkich tagów, dla których chcesz sugestie później.
1. Rozpocznij proces szkolenia.
1. Po zakończeniu szkolenia przejdź do widoku **Bez znaczników** i wybierz przycisk **Pobierz sugerowane znaczniki** w lewym okienku.
    > [!div class="mx-imgBorder"]
    > ![Przycisk Sugerowane znaczniki jest wyświetlany na karcie obrazy nieoznakowane.](./media/suggested-tags/suggested-tags-button.png)
1. W wyświetlonym oknie podręcznym ustaw liczbę obrazów, dla których chcesz sugestie. Informacje o początkowych tagach powinny być wyświetlane tylko dla części nieoznakowanych obrazów. Będziesz otrzymywać lepsze sugestie tagów podczas iteracji tego procesu.
1. Potwierdź sugerowane tagi, naprawiając wszystkie, które nie są poprawne.
    > [!TIP]
    > Obrazy z sugerowanymi tagami są sortowane według ich niepewności przewidywania (niższe wartości wskazują na większą pewność). Kolejność sortowania można zmienić za pomocą opcji **Sortuj według niepewności.** Jeśli ustawisz kolejność na **wysoką na niską**, możesz najpierw skorygować prognozy o wysokiej niepewności, a następnie szybko potwierdzić te o niskiej niepewności.
    * W projektach klasyfikacji obrazów można wybierać i potwierdzać znaczniki w partiach. Filtruj widok według danego sugerowanego znacznika, usuń zaznaczenie obrazów, które są nieprawidłowo oznakowane, a następnie potwierdź resztę w partii.
        > [!div class="mx-imgBorder"]
        > ![Sugerowane znaczniki są wyświetlane w trybie wsadowym dla ic z filtrami.](./media/suggested-tags/ic-batch-mode.png)

        Sugerowane znaczniki można również użyć w trybie poszczególnych obrazów, wybierając obraz z galerii.

        ![Sugerowane znaczniki są wyświetlane w trybie pojedynczego obrazu dla ic.](./media/suggested-tags/ic-individual-image-mode.png)
    * W projektach wykrywania obiektów potwierdzenia wsadowe nie są obsługiwane, ale nadal można filtrować i sortować według sugerowanych tagów, aby uzyskać bardziej zorganizowane środowisko etykietowania. Miniatury obrazów nieoznakowanych będą wyświetlane nakładka obwiedni wskazująca lokalizacje sugerowanych tagów. Jeśli nie wybierzesz sugerowanego filtru znaczników, wszystkie nieoznakowane obrazy pojawią się bez nakładania obwiedni.
        > [!div class="mx-imgBorder"]
        > ![Sugerowane znaczniki są wyświetlane w trybie wsadowym dla OD z filtrami.](./media/suggested-tags/od-batch-mode.png)

        Aby potwierdzić znaczniki wykrywania obiektów, należy zastosować je do każdego pojedynczego obrazu w galerii.

        ![Sugerowane znaczniki są wyświetlane w trybie pojedynczego obrazu dla OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Ponownie rozpocznij proces szkolenia.
1. Powtarzaj poprzednie kroki, aż będziesz zadowolony z jakości sugestii.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem Szybki start, aby rozpocząć tworzenie i szkolenie projektu Custom Vision.

* [Tworzenie klasyfikatora](getting-started-build-a-classifier.md)
* [Tworzenie detektora obiektu](get-started-build-detector.md)