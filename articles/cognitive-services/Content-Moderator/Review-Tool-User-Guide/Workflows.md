---
title: Definiowanie i korzystanie z przepływów pracy moderowanie zawartości - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator projektanta przepływów pracy i interfejsów API umożliwia definiowanie niestandardowych przepływów pracy i wartości progowe, oparte na zawartości zasad.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 265d5a2bd6a256d287e6ae164e883ade783f960f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262408"
---
# <a name="define-test-and-use-workflows"></a>Zdefiniuj, testowanie i korzystanie z przepływów pracy

Azure Content Moderator projektanta przepływów pracy i interfejsów API umożliwia definiowanie niestandardowych przepływów pracy i wartości progowe, oparte na zawartości zasad.

Przepływy pracy "Połącz" Content Moderator interfejsu API za pomocą łączników. Jeśli łącznik dla tego interfejsu API jest dostępny, można użyć innych interfejsów API. W tym przykładzie używa łącznika pakietu Content Moderator, który jest domyślnie włączone.

## <a name="browse-to-the-workflows-section"></a>Przejdź do sekcji przepływów pracy

Na **ustawienia** zaznacz **przepływy pracy**.

  ![Ustawienie przepływów pracy](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Rozpocznij nowy przepływ pracy

Wybierz **Dodaj przepływ pracy**.

  ![Dodawanie przepływu pracy](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Przypisz nazwę i opis

Nazwa przepływu pracy, a następnie wprowadź opis i wybierz, czy przepływ pracy obsługuje obrazy lub tekst.

  ![Nazwa przepływu pracy i opis](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Zdefiniuj kryteria oceny ("warunek")

Na następującym zrzucie ekranu widać, pola i If-Then-Else wybrane opcje, które musisz zdefiniować przepływy pracy. Wybierz łącznik. W tym przykładzie użyto **pakietu Content Moderator**. W zależności od wybranych łącznika zmienić dostępne opcje danych wyjściowych.

  ![Zdefiniuj warunek przepływu pracy](images/ocr-workflow-step-2-condition.PNG)

Po wybraniu łącznika i jego danych wyjściowych, który ma, wybierz operator i wartość dla warunku.

## <a name="define-the-action-to-take"></a>Zdefiniuj akcję do wykonania

Wybierz akcję do wykonania i spełnienia warunku. Poniższy przykład tworzy przeglądu obrazu, tag przypisuje `a`i jego warunku wyświetlane. Możesz również połączyć wiele warunków w celu uzyskania wyników, który ma. Opcjonalnie dodaj alternatywna ścieżka (w przeciwnym razie).

  ![Definiowanie akcji przepływu pracy](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Zapisz przepływ pracy

Na koniec Zapisz przepływ pracy i zanotuj nazwę przepływu pracy. Potrzebna jest nazwa, aby uruchomić zadanie Moderowanie przy użyciu interfejsu API przeglądu.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

Skoro zdefiniowano niestandardowego przepływu pracy, można ją przetestować przy użyciu przykładowej zawartości.

Następnie wybierz odpowiedni **wykonania przepływu pracy** przycisku.

  ![Test przepływu pracy](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Przekazywanie pliku

Zapisz [przykładowy obraz](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na dysk lokalny. Aby przetestować przepływ pracy, zaznacz **wybierz pliki** i przekazaniem obrazu.

  ![Przekaż plik obrazu](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Śledzenie przepływu pracy

Śledzenie przepływu pracy, podczas wykonywania.

  ![Śledzenia wykonywania przepływu pracy](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Przejrzyj oflagowanych ludzi Moderowanie obrazów

Aby wyświetlić obraz przeglądu, przejdź do **obraz** karcie **Przejrzyj**.

  ![Przegląd obrazów](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Kolejne kroki 

Aby wywołać przepływu pracy od kodu, należy użyć niestandardowych przepływów pracy za pomocą [ `Job` Przewodnik Szybki Start interfejs API konsoli](../try-review-api-job.md) i [szybkiego startu zestawu .NET SDK](../moderation-jobs-quickstart-dotnet.md).
