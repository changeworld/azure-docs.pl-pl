---
title: 'Szybki start: wypróbuj moderatora zawartości w internecie - Moderator zawartości'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz narzędzia content moderatora do testowania podstawowych funkcji moderatora zawartości bez konieczności pisania kodu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 03/13/2020
ms.author: pafarley
ms.openlocfilehash: 666b70ba8b632cb2cadf20de384e3e615acb2b3d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203579"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Szybki start: wypróbuj moderatora zawartości w internecie

W tym przewodniku Szybki start użyjesz internetowego narzędzia content moderatora do testowania podstawowych funkcji moderatora zawartości bez konieczności pisania kodu. Jeśli chcesz szybciej zintegrować tę usługę z aplikacją, zobacz inne przewodniki Szybki start w sekcji [Następne kroki.](#next-steps)

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarka internetowa

## <a name="set-up-the-review-tool"></a>Konfigurowanie narzędzia do przeglądania
Narzędzie Content Moderator Review to internetowe narzędzie, które umożliwia recenzentom ludzkim pomoc w podejmowaniu decyzji przez służby poznawcze. W tym przewodniku przejdziesz przez krótki proces konfigurowania narzędzia do recenzji, aby zobaczyć, jak działa usługa Content Moderator. Przejdź do [witryny narzędzi content moderatora i](https://contentmoderator.cognitive.microsoft.com/) zarejestruj się.

![Strona główna moderatora zawartości](images/homepage.PNG)

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Następnie utwórz zespół recenzentów. W scenariuszu pracy będzie to grupa osób, które ręcznie przejmą decyzje dotyczące moderacji usługi. Aby utworzyć zespół, musisz wybrać **region**i podać **nazwę zespołu** i **identyfikator zespołu.** Jeśli chcesz zaprosić kolegów do zespołu, możesz to zrobić, wprowadzając ich adresy e-mail tutaj.

> [!NOTE]
> **Nazwa zespołu** to przyjazna nazwa dla zespołu recenzentów. Jest to nazwa wyświetlana w witrynie Azure portal. Identyfikator **zespołu** jest to, co jest używane do identyfikacji zespołu przeglądu programowo.

> [!div class="mx-imgBorder"]
> ![Zaproś członka zespołu](images/create-team.png)

Jeśli zdecydujesz się szyfrować dane przy użyciu klucza zarządzanego przez klienta (CMK), zostanie wyświetlony monit o **identyfikator zasobu Resource** Moderator dla zasobu w warstwie cenowej E0. Zasób, który podasz musi być nowy. 

> [!div class="mx-imgBorder"]
> ![Zaproś członka zespołu z CMK](images/create-team-cmk.png)

Jeśli spróbujesz ponownie użyć zasobu Moderatora zawartości, zobaczysz to ostrzeżenie: 

> [!div class="mx-imgBorder"]
> ![Awaria cmk](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Przekazywanie przykładowej zawartości

Teraz możesz przesłać przykładową zawartość. Wybierz **pozycję Wypróbuj > obraz,** Spróbuj > **tekst**lub Spróbuj **> wideo**.

![Wypróbuj moderowanie obrazu lub tekstu](images/tryimagesortext.png)

Prześlij zawartość w celu moderowania. Wewnętrznie narzędzie do przeglądania wywoła interfejsy API moderowania w celu skanowania zawartości. Po zakończeniu skanowania zostanie wyświetlony komunikat informujący o wynikach oczekiwania na sprawdzenie.

![Moderowanie plików](images/submitted.png)

## <a name="review-moderation-tags"></a>Przeglądanie tagów moderowania

Przejrzyj zastosowane tagi moderowania. Możesz sprawdzić, które tagi zostały zastosowane do Twoich treści i jaki był wynik w każdej kategorii. Zobacz tematy [moderowania obrazów,](image-moderation-api.md) [tekstu](text-moderation-api.md)i [wideo,](video-moderation-api.md) aby dowiedzieć się więcej o tym, co wskazują różne tagi zawartości.

![Przegląd wyników](images/reviewresults_text.png)

W projekcie ty lub twój zespół recenzentów możesz zmienić te tagi lub dodać więcej tagów w razie potrzeby. Te zmiany zostaną przesłane za pomocą przycisku **Dalej.** Gdy aplikacja biznesowa wywołuje interfejsy API moderatora, oznaczona zawartość będzie w kolejce tutaj, gotowa do sprawdzenia przez zespoły przeglądu ludzkiego. Dzięki temu podejściu można szybko przejrzeć duże ilości zawartości.

W tym momencie użyto narzędzia Content Moderator Review, aby zobaczyć przykłady tego, co może zrobić usługa Content Moderator. Następnie możesz dowiedzieć się więcej o narzędziu do przeglądu i jak zintegrować je z projektem oprogramowania przy użyciu interfejsów API przeglądu lub przejść do sekcji [Następne kroki,](#next-steps) aby dowiedzieć się, jak same korzystać z interfejsów API moderowania w aplikacji.

## <a name="learn-more-about-the-review-tool"></a>Dowiedz się więcej o narzędziu do recenzji

Aby dowiedzieć się więcej o korzystaniu z narzędzia Do recenzji moderatora zawartości, zapoznaj się z przewodnikiem po [narzędziach Recenzja](Review-Tool-User-Guide/human-in-the-loop.md) i zobacz interfejsy API narzędzia do przeglądania, aby dowiedzieć się, jak dostosować środowisko oceny ludzkiej:
- [Interfejs API zadania](try-review-api-job.md) skanuje zawartość przy użyciu interfejsów API moderowania i generuje recenzje w narzędziu do przeglądu. 
- Interfejs [API przeglądu](try-review-api-review.md) bezpośrednio tworzy recenzje obrazów, tekstu lub wideo dla moderatorów ludzkich bez uprzedniego skanowania zawartości. 
- Interfejs [API przepływu pracy](try-review-api-workflow.md) tworzy, aktualizuje i pobiera szczegółowe informacje o niestandardowych przepływach pracy, które tworzy zespół.

Lub kontynuuj następne kroki, aby rozpocząć korzystanie z interfejsów API moderowania w kodzie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak samodzielnie korzystać z interfejsów API moderowania w aplikacji.
- Implementowanie moderowania obrazu. Użyj [konsoli interfejsu API](try-image-api.md) lub postępuj zgodnie z [paskiem szybkiego startu .NET SDK,](dotnet-sdk-quickstart.md) aby skanować obrazy i wykrywać potencjalną zawartość dla dorosłych i rasistów przy użyciu tagów, wyników zaufania i innych wyodrębnionych informacji.
- Implementowanie moderowania tekstu. Użyj [konsoli interfejsu API](try-text-api.md) lub użyj [paska szybkiej startu .NET SDK](dotnet-sdk-quickstart.md) do skanowania zawartości tekstowej w poszukiwaniu potencjalnych wulgaryzmów, klasyfikacji niechcianych wiadomości (podgląd) wspomaganej komputerowo i danych osobowych.
- Wdrażanie moderowania wideo. Postępuj zgodnie z instrukcjami dotyczącymi [moderowania wideo w języku C#,](video-moderation-api.md) aby skanować filmy i wykrywać potencjalne treści dla dorosłych i rasistowskie. 
