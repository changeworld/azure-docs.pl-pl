---
title: Rozpoczynanie pracy Azure moderatora zawartości | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z moderatora zawartości platformy Azure.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347661"
---
# <a name="get-started-with-content-moderator"></a>Wprowadzenie do pakietu Content Moderator

Rozpoczynanie pracy z zawartości moderatora interfejsów API i narzędzia przeglądu, w następujący sposób:

- [Uruchom narzędzie przeglądu](#start-with-the-review-tool) do utworzenia zarówno klucze interfejsu API i zespołu przeglądu. Eksploruj narzędzie przeglądu i Dowiedz się, jak zintegrować przy użyciu zawartości interfejsów API moderatora.
- [Subskrybowanie zawartości moderatora](#start-with-the-apis) w portalu Azure. Nadal musisz zarejestrować się online w celu tworzenia zespołu przeglądu.
- [Użyj przepływu łącznika i szablony](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) wyewidencjonować szeroką gamę integracji z programem designer łatwy w użyciu.

Niezależnie od wybranej opcji, zobacz [Zarządzanie poświadczeniami](review-tool-user-guide/credentials.md) artykuł, aby znaleźć poświadczeń interfejsu API.

## <a name="start-with-the-review-tool"></a>Uruchom narzędzie przeglądu
[Zarejestruj się](http://contentmoderator.cognitive.microsoft.com/) w witrynie sieci web uzyskiwania informacji na temat narzędzia przeglądu moderatora zawartości.

![Strona główna moderatora zawartości](images/homepage.PNG)

### <a name="create-a-review-team"></a>Tworzenie zespołu przeglądu
Nadaj nazwę zespołu. Aby zaprosić współpracowników, możesz to zrobić, wprowadzając ich adresów e-mail.

![Zaproś członek zespołu](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Przekazywanie obrazów lub wprowadź tekst
Kliknij przycisk **spróbuj > obrazu** lub **spróbuj > tekst**. Przekaż do pięciu przykładowe obrazy lub wprowadź przykładowy tekst łagodzenia.

![Spróbuj użyć obrazu lub łagodzenia tekstu](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Wprowadzane do automatycznego łagodzenia
Przesłania zawartości do automatycznego łagodzenia. Wewnętrznie narzędzie przeglądu wywołuje łagodzenia interfejsów API w celu skanowania zawartości. Po zakończeniu skanowania zostanie wyświetlony komunikat z informacją o wynikach oczekiwania zapoznania się z nimi.

![Umiarkowany plików](images/submitted.png)

### <a name="review-and-confirm-results"></a>Przejrzyj i Potwierdź wyników
Przejrzyj tagi tworzonych automatycznie, zmienić w razie potrzeby i przesyłanie za pomocą **dalej** przycisku. Jak aplikacji biznesowej wywołuje interfejsy API moderatora oznakowanych uruchamia zawartości umieszczanie w kolejce, przygotowanie zrecenzowane przez zespoły człowieka. Szybkie przeglądanie dużych ilości zawartości przy użyciu tej metody.

![Przegląd wyników](images/reviewresults.png)

Dowiedz się, jak korzystać ze wszystkich [Przejrzyj funkcje narzędzia](Review-Tool-User-Guide/human-in-the-loop.md) lub kontynuować następnej sekcji, aby dowiedzieć się więcej na temat interfejsów API. Pomiń krok tworzenia konta, ponieważ ma klucz interfejsu API obsługi administracyjnej dla Ciebie w narzędziu przeglądu, jak pokazano w [Zarządzanie poświadczeniami](review-tool-user-guide/credentials.md) artykułu.

### <a name="use-the-apis"></a>Korzystanie z interfejsów API

Zostały przedstawione łagodzenia zawartości, a następnie przejrzyj narzędzia obsługi, jak zintegrować moderatora zawartości z aplikacji biznesowych. Następująca sekcja służy do Dowiedz się więcej i szybkie zrozumienie z zestawów SDK i liczbą próbek.

## <a name="start-with-the-apis"></a>Uruchom z interfejsami API

[Subskrybowanie zawartości moderatora](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) w portalu Azure. Uruchom jednego z poniższych interfejsów API:

### <a name="image-moderation"></a>Moderowanie obrazów

Rozpoczynać [konsoli interfejsu API](try-image-api.md) lub użyj [szybkiego startu .NET](image-moderation-quickstart-dotnet.md) skanowanie obrazów i wykrywanie potencjalnych zawartość dla dorosłych i luksusowych przy użyciu tagów, wyniki zaufania i inne wyodrębnić informacji.

### <a name="text-moderation"></a>Moderowanie tekstu

Rozpoczynać [konsoli interfejsu API](try-text-api.md) lub użyj [szybkiego startu .NET](text-moderation-quickstart-dotnet.md) do skanowania zawartości tekstowej dla potencjalnych niestosownych wyrażeń, wspierana maszyny niepotrzebnego tekstu klasyfikacji (wersja zapoznawcza) i pozwalają na identyfikację użytkownika dane osobowe. 


### <a name="video-moderation"></a>Moderowanie filmów wideo

Rozpoczynać [szybkiego startu .NET](video-moderation-api.md) do skanowania plików wideo i wykrywania potencjalnych zawartość dla dorosłych i luksusowych. 


### <a name="review-apis"></a>Przegląd interfejsów API

Zacznij tutaj, wybierając zadanie, przejrzyj i interfejsów API przepływu pracy.

- [API zadania](try-review-api-job.md) skanowania zawartości przy użyciu łagodzenia interfejsów API i generuje przeglądy w narzędziu przeglądu. 
- [API przeglądu](try-review-api-review.md) bezpośrednio tworzy obraz, tekst lub przeglądami wideo dla człowieka moderatorów bez pierwszy skanowania zawartości. 
- [API przepływu pracy](try-review-api-workflow.md) tworzy, aktualizuje i pobiera szczegółowe informacje o niestandardowych przepływów pracy tworzonych przez zespół.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o począwszy od zawartości łagodzenia [obrazu łagodzenia interfejsu API](image-moderation-api.md).
