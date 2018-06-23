---
title: Definiowanie i korzystania z przepływów pracy w zawartości moderatora Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia niestandardowych przepływów pracy opartych na zasadach zawartości.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/07/2018
ms.author: sajagtap
ms.openlocfilehash: dfe3ba8a2ef1bcbc69ef585b504a9367d9420bf0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347016"
---
# <a name="define-test-and-use-workflows"></a>Definiowanie, przetestować i korzystania z przepływów pracy

Azure zawartości moderatora projektanta przepływów pracy i interfejsów API umożliwia definiowanie niestandardowych przepływów pracy i wartości progowe, oparte na zasadach zawartości.

Przepływy pracy "connect" API moderatora zawartości za pomocą łączników. Jeśli łącznik dla tego interfejsu API jest dostępny, można użyć innych interfejsów API. W tym przykładzie używa łącznik moderatora zawartości, który jest domyślnie włączone.

## <a name="browse-to-the-workflows-section"></a>Przejdź do sekcji przepływów pracy

Na **ustawienia** wybierz opcję **przepływy pracy**.

  ![Ustawianie przepływów pracy](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Uruchamianie nowego przepływu pracy

Wybierz **Dodaj przepływ pracy**.

  ![Dodaj przepływ pracy](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Przypisz nazwę i opis

Nazwa przepływu pracy, wprowadź opis i wybierz, czy przepływ pracy obsługuje obrazy lub tekst.

  ![Nazwa przepływu pracy i opis](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Zdefiniuj kryteria oceny ("stanu")

Na poniższym zrzucie ekranu widać pola, a jeśli-to-inaczej wybrane opcje, które trzeba zdefiniować przepływy pracy. Wybierz łącznik. W tym przykładzie użyto **zawartości moderatora**. W zależności od wybranego łącznika zmienić dostępne opcje dla danych wyjściowych.

  ![Zdefiniuj warunek przepływu pracy](images/ocr-workflow-step-2-condition.PNG)

Po wybraniu łącznik i jego dane wyjściowe, które mają wybierz operator i wartość dla warunku.

## <a name="define-the-action-to-take"></a>Zdefiniuj akcję do wykonania

Wybierz akcję do wykonania i spełnienia warunku. Poniższy przykład tworzy przeglądu obrazu, tag przypisuje `a`i zaznacza go dla warunku wyświetlane. Można także połączyć wiele warunków, aby uzyskać wyniki, które mają. Opcjonalnie dodaj alternatywna ścieżka (Else).

  ![Zdefiniuj akcji przepływu pracy](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Zapisywanie przepływu pracy

Na koniec należy zapisać ten przepływ pracy i zanotuj nazwę przepływu pracy. Należy nazwę, aby uruchomić zadanie łagodzenia przy użyciu interfejsu API przeglądu.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

Teraz, gdy zdefiniowano niestandardowego przepływu pracy, przetestować go z zawartością próbki.

Wybierz odpowiedni **wykonania przepływu pracy** przycisku.

  ![Test przepływu pracy](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Przekazywanie pliku

Zapisz [obraz przykładowy](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na dysk lokalny. Aby przetestować przepływ pracy, wybierz **wybierz pliki** i Przekaż obraz.

  ![Przekaż plik obrazu](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Śledzenie przepływu pracy

Śledzenie przepływu pracy podczas wykonywania.

  ![Śledzenie wykonywania przepływu pracy](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Przejrzyj wszystkie obrazy oflagowane dla człowieka łagodzenia

Aby wyświetlić obraz przeglądu, przejdź do **obrazu** w obszarze **Przejrzyj**.

  ![Przegląd obrazów](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Kolejne kroki 

Aby wywołać przepływu pracy z kodu, należy użyć niestandardowych przepływów pracy z [ `Job` szybkiego startu konsoli interfejsu API](../try-review-api-job.md) i [szybkiego startu zestawu .NET SDK](../moderation-jobs-quickstart-dotnet.md).
