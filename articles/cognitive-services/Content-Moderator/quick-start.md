---
title: 'Szybki start: Wypróbuj pakiet Content Moderator w sieci web - Content Moderator'
titlesuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start użyjesz narzędzia przeglądu usługi Content Moderator online do przetestowania podstawową funkcjonalność usługi Content Moderator, bez konieczności pisania kodu.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: d1c65b590fd68f68fdaeb110c0fd00e0c5a6649a
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756463"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Szybki start: Wypróbuj pakiet Content Moderator w sieci web

W tym przewodniku Szybki Start użyjesz narzędzia przeglądu usługi Content Moderator online do przetestowania podstawową funkcjonalność usługi Content Moderator, bez konieczności pisania kodu. Jeśli chcesz szybciej zintegrować tę usługę do aplikacji, zobacz inne Przewodniki Szybki Start w [następne kroki](#next-steps) sekcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarki sieci web

## <a name="set-up-the-review-tool"></a>Skonfiguruj narzędzie do przeglądu
Narzędzie do przeglądu usługi Content Moderator to narzędzia opartego na sieci web, umożliwiająca ludzi recenzentom cognitive service o pomoc w podejmowaniu decyzji. W tym przewodniku konieczne będzie przejście przez krótki proces konfigurowania narzędzie do przeglądu, aby zobaczyć, jak działa Usługa Content Moderator. Przejdź do [narzędzie do przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/) lokacji i zarejestruj.

![Strona główna Moderator zawartości](images/homepage.PNG)

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Następnie należy utworzyć zespół przeglądu. W przypadku scenariusza pracy będzie grupa osób, które zostaną ręcznie przejrzeć usługi Moderowanie decyzji. Teraz musisz tylko utworzyć nazwę zespołu. Jeśli chcesz zaprosić współpracowników do zespołu, możesz to zrobić, wprowadzając ich adresy e-mail.

![Zaproś członków zespołu](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Przekaż przykładowej zawartości

Teraz możesz przystąpić do przekazania przykładowej zawartości. Wybierz **spróbuj > obraz**, **spróbuj > tekst**, lub **spróbuj > wideo**.

![Spróbuj użyć obrazu lub Moderowanie tekstu](images/tryimagesortext.png)

Prześlij zawartość na potrzeby moderowania. Wewnętrznie narzędzie do przeglądu będzie wywoływać interfejsy API moderowania do skanowania zawartości. Po zakończeniu skanowania zostanie wyświetlony komunikat z informacją, że są wyniki oczekiwanie zapoznania się z nimi.

![Umiarkowany plików](images/submitted.png)

## <a name="review-moderation-tags"></a>Przejrzyj Moderowanie tagów

Przejrzyj tagi zastosowane moderowania. Widać, jakie znaczniki zostały zastosowane do zawartości i wynik był w każdej kategorii. Zobacz [obraz](image-moderation-api.md), [tekstu](text-moderation-api.md), i [wideo](video-moderation-api.md) wskazują Moderowanie tematy, aby dowiedzieć się więcej na temat tagów różną zawartość.

![Przegląd wyników](images/reviewresults_text.png)

W projekcie Ty lub Twój zespół przeglądu, można zmienić tych znaczników lub dodać więcej tagów, zgodnie z potrzebami. Będziesz przesyłać tych zmian z **dalej** przycisku. Aplikacji biznesowej wywołań interfejsów API Moderator, znaczniki w kolejce w tym miejscu, gotowe do przeglądu przez zespoły przeglądu przez ludzi. Możesz szybko przejrzeć duże ilości zawartości przy użyciu tej metody.

W tym momencie trzeba użyć narzędzia przeglądu usługi Content Moderator, aby zobaczyć przykłady możliwościach usługi Content Moderator. Następnie można albo dowiesz się więcej na temat narzędzia do przeglądu i jak zintegrować ją z projektem oprogramowania przy użyciu interfejsów API Przejrzyj lub możesz przejść do [następne kroki](#next-steps) sekcji, aby dowiedzieć się, jak używać interfejsy API moderowania samodzielnie w swojej aplikacji.

## <a name="learn-more-about-the-review-tool"></a>Dowiedz się więcej na temat narzędzia do przeglądu

Aby dowiedzieć się więcej na temat korzystania z narzędzia Content Moderator analizować, Przyjrzyj się [narzędzie do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md) przewodnik i zobacz, narzędzie do przeglądu interfejsów API, aby dowiedzieć się, jak dostosować proces przeglądu przez ludzi:
- [Zadanie interfejsu API](try-review-api-job.md) skanowania zawartości przy użyciu interfejsy API moderowania i generuje przeglądy w narzędzie do przeglądu. 
- [API przeglądu](try-review-api-review.md) bezpośrednio tworzy obrazu, tekstu lub wideo przeglądy dla człowieka moderatorzy bez pierwszego skanowania zawartości. 
- [API przepływu pracy](try-review-api-workflow.md) tworzy, aktualizuje i pobiera szczegółowe informacje o niestandardowych przepływów pracy tworzonych przez zespół.

Ewentualnie wykonaj kolejne kroki, aby rozpocząć korzystanie z interfejsów API moderowania w kodzie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać interfejsy API moderowania samodzielnie w swojej aplikacji.
- Implementowanie Moderowanie obrazów. Użyj [Konsola interfejsu API](try-image-api.md) lub [ C# Szybki Start](image-moderation-quickstart-dotnet.md) do skanowania obrazów i wykrywania potencjalnych dorosłych i zawartości erotycznej przy użyciu tagów, oceny zaufania i inne wyodrębnionych informacji.
- Implementowanie Moderowanie tekstu. Użyj [Konsola interfejsu API](try-text-api.md) lub użyj [ C# Szybki Start](text-moderation-quickstart-dotnet.md) do skanowania zawartości tekstowej wulgaryzmy, klasyfikacji wspomagane maszynowo niepotrzebnego tekstu (wersja zapoznawcza) i danych osobowych.
- Moderowanie filmów wideo w życie. Postępuj zgodnie z [przewodnik dotyczący instrukcje Moderowanie wideo C# ](video-moderation-api.md) do skanowania filmów wideo i wykrywanie potencjalnych zawartości dla dorosłych. 
