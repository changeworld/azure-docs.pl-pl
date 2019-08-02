---
title: 'Szybki start: Wypróbuj Content Moderator w sieci Web — Content Moderator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz narzędzia do przeglądu Content Moderator w trybie online, aby przetestować podstawowe funkcje Content Moderator bez konieczności pisania kodu.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: e5b20bb45c08330c59fc72712b9e65b488c8c68b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564443"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Szybki start: Wypróbuj Content Moderator w sieci Web

W tym przewodniku szybki start użyjesz narzędzia do przeglądu Content Moderator w trybie online, aby przetestować podstawowe funkcje Content Moderator bez konieczności pisania kodu. Jeśli chcesz szybciej zintegrować tę usługę z aplikacją, zobacz inne Przewodniki Szybki Start w sekcji [następne kroki](#next-steps) .

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarka sieci Web

## <a name="set-up-the-review-tool"></a>Konfigurowanie narzędzia do przeglądu
Narzędzie do przeglądu Content Moderator jest narzędziem opartym na sieci Web, które pozwala recenzentom ludzkim ułatwić korzystanie z usługi poznawczej w podejmowaniu decyzji. W tym przewodniku przedstawiono krótki proces konfigurowania narzędzia do przeglądu, dzięki któremu można zobaczyć, jak działa usługa Content Moderator. Przejdź do witryny [Narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/) i Utwórz konto.

![Strona główna Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Następnie utwórz zespół recenzji. W scenariuszu roboczym będzie to grupa osób, które będą ręcznie sprawdzać decyzje o moderowaniu usługi. Na razie wystarczy utworzyć nazwę zespołu. Jeśli chcesz zaprosić współpracowników do zespołu, możesz to zrobić, wprowadzając tutaj ich adresy e-mail.

![Zaproś członka zespołu](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Przekaż przykładową zawartość

Teraz wszystko jest gotowe do przekazania przykładowej zawartości. Wybierz pozycję **wypróbuj > obraz**, **spróbuj > tekst**lub **spróbuj > wideo**.

![Wypróbuj moderowanie obrazu lub tekstu](images/tryimagesortext.png)

Prześlij zawartość pod kątem moderowania. Wewnętrznie narzędzie do przeglądu wywoła interfejsy API moderowania do skanowania zawartości. Po zakończeniu skanowania zobaczysz komunikat informujący o tym, że wyniki czekają na przegląd.

![Pliki umiarkowane](images/submitted.png)

## <a name="review-moderation-tags"></a>Przejrzyj Tagi moderowania

Przejrzyj zastosowane znaczniki moderowania. Możesz zobaczyć, które Tagi zostały zastosowane do zawartości, i co to jest wynik w każdej kategorii. Zobacz tematy dotyczące moderowania [obrazów](image-moderation-api.md), [tekstu](text-moderation-api.md)i [wideo](video-moderation-api.md) , aby dowiedzieć się więcej na temat tego, co wskazuje różne Tagi zawartości.

![Przegląd wyników](images/reviewresults_text.png)

W projekcie ty lub Twój zespół recenzji może zmienić te znaczniki lub dodać więcej tagów w razie potrzeb. Te zmiany zostaną przesłane przy użyciu przycisku **dalej** . Ponieważ aplikacja biznesowa wywołuje interfejsy API moderatora, otagowana zawartość będzie w tym miejscu gotowa do przejrzenia przez zespoły przeglądów ludzkich. Możesz szybko przejrzeć duże ilości zawartości przy użyciu tego podejścia.

W tym momencie użyto narzędzia do przeglądu Content Moderator, aby zobaczyć przykłady możliwości usługi Content Moderator. Następnie można dowiedzieć się więcej na temat narzędzia do przeglądu i sposobu integrowania go z projektem oprogramowania przy użyciu interfejsów API przeglądu lub można przejść do sekcji [następne kroki](#next-steps) , aby dowiedzieć się, jak używać interfejsów API moderowania w aplikacji.

## <a name="learn-more-about-the-review-tool"></a>Dowiedz się więcej o narzędziu do przeglądu

Aby dowiedzieć się więcej na temat korzystania z narzędzia do przeglądu Content Moderator, zapoznaj się [](Review-Tool-User-Guide/human-in-the-loop.md) z przewodnikiem po narzędziu do przeglądu i zobacz Interfejsy API narzędzia do przeglądu, aby dowiedzieć się, jak dostosować środowisko przeglądu ludzkiego:
- [Interfejs API zadań](try-review-api-job.md) skanuje zawartość przy użyciu interfejsów API moderowania i generuje przeglądy w narzędziu do przeglądu. 
- [Interfejs API przeglądu](try-review-api-review.md) bezpośrednio tworzy obrazy, tekst lub Recenzje wideo dla moderatorów ludzkich bez wcześniejszego skanowania zawartości. 
- [Interfejs API przepływu pracy](try-review-api-workflow.md) tworzy, aktualizuje i pobiera szczegóły dotyczące niestandardowych przepływów pracy tworzonych przez zespół.

Lub przejdź do następnych kroków, aby rozpocząć korzystanie z interfejsów API moderowania w kodzie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać interfejsów API moderowania w aplikacji.
- Implementowanie moderowania obrazu. Użyj [konsoli interfejsu API](try-image-api.md) lub [ C# przewodnika Szybki Start](image-moderation-quickstart-dotnet.md) , aby skanować obrazy i wykrywać potencjalną zawartość dla dorosłych i erotycznej przy użyciu tagów, wyników pewności i innych wyodrębnionych informacji.
- Implementowanie moderowania tekstu. Użyj [konsoli interfejsu API](try-text-api.md) lub Użyj [ C# przewodnika Szybki Start](text-moderation-quickstart-dotnet.md) , aby przeskanować zawartość tekstową w celu uzyskania potencjalnej wulgarności, niepotrzebnej do użycia maszynowo klasyfikacji tekstu (wersja zapoznawcza) i danych osobowych.
- Implementowanie moderowania wideo. Postępuj zgodnie z [przewodnikiem dotyczącym C# moderowania wideo](video-moderation-api.md) , aby skanować wideo i wykrywać potencjalną zawartość dla dorosłych i erotycznej. 
