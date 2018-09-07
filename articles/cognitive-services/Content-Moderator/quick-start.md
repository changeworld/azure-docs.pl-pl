---
title: Wprowadzenie do usługi Azure Content Moderator | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługi Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: 39727b4d97ade67b854fe525afad565451cc3d77
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024515"
---
# <a name="get-started-with-content-moderator"></a>Wprowadzenie do pakietu Content Moderator

Rozpocznij pracę z usługi Content Moderator, w następujący sposób:

- [Uruchom za pomocą narzędzia do przeglądu](#start-with-the-review-tool) uzyskiwanie klucza interfejsu API i tworzenia zespołu przeglądu. Korzyścią jest to, że wywołać interfejsy API moderowania skanowania zawartości i interfejsy API przeglądu generowania przeglądy, bez dodatkowych kroków, można użyć klucza interfejsu API.
- [Subskrybowanie do pakietu Content Moderator](#start-with-the-apis) na platformie Azure, aby uzyskać klucz interfejsu API. Zapoznaj się z [dokumentacja interfejsu API](api-reference.md) i [zestawów SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Nadal należy zarejestrować się online do utworzenia zespołu.
- [Za pomocą łącznika usługi Flow i szablonów](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) wyewidencjonować szereg integracje z łatwego w użyciu projektanta.

Niezależnie od wybranej opcji, zobacz [zarządzania poświadczeniami](review-tool-user-guide/credentials.md) artykuł, aby znaleźć poświadczenia interfejsu API.

## <a name="start-with-the-review-tool"></a>Uruchom za pomocą narzędzia do przeglądu
[Zarejestruj](http://contentmoderator.cognitive.microsoft.com/) w witrynie sieci web uzyskiwania informacji na temat narzędzia do przeglądu usługi Content Moderator.

![Strona główna Moderator zawartości](images/homepage.PNG)

### <a name="create-a-review-team"></a>Tworzenie zespołu przeglądu
Nazwij Twojego zespołu. Aby zaprosić współpracowników, możesz to zrobić, wprowadzając ich adresy e-mail.

![Zaproś członków zespołu](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Przekazywanie obrazów lub wprowadzania tekstu
Kliknij przycisk **spróbuj > obraz** lub **spróbuj > tekst**. Przekaż do pięciu przykładowe obrazy, lub wprowadź tekst przykładowy moderowania.

![Spróbuj użyć obrazu lub Moderowanie tekstu](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Prześlij do zalet zautomatyzowanego moderowania
Prześlij zawartość na potrzeby zalet zautomatyzowanego moderowania. Wewnętrznie narzędzie do przeglądu wywołuje interfejsy API moderowania do skanowania zawartości. Gdy skanowanie zostanie zakończone, zobaczysz komunikat z informacją, o wynikach oczekiwanie zapoznania się z nimi.

![Umiarkowany plików](images/submitted.png)

### <a name="review-and-confirm-results"></a>Przejrzyj i Potwierdź wyników
Przejrzyj tagów tworzonych automatycznie, zmienić w razie potrzeby i przesłać przy użyciu **dalej** przycisku. Aplikacji biznesowej wywołań interfejsów API Moderator oznakowane zawartości rozpoczyna się umieszczanie w kolejce, gotowe do przeglądu przez zespoły przeglądu przez ludzi. Możesz szybko sprawdzić dużych ilości zawartości przy użyciu tej metody.

![Przegląd wyników](images/reviewresults.png)

Dowiedz się, jak korzystać ze wszystkich [Przejrzyj funkcje narzędzia](Review-Tool-User-Guide/human-in-the-loop.md) lub przejdź do następnej sekcji, aby dowiedzieć się więcej na temat interfejsów API. Pominąć krok Zarejestruj się, ponieważ masz klucz interfejsu API zainicjowane w narzędzie do przeglądu pokazany na [zarządzania poświadczeniami](review-tool-user-guide/credentials.md) artykułu.

### <a name="use-the-apis"></a>Przy użyciu interfejsów API

Dowiedz się, jak zintegrować pakietu Content Moderator z aplikacji biznesowych. Zapoznaj się z [dokumentacja interfejsu API](api-reference.md) i [zestawów SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>Subskrybuj w witrynie Azure portal

[Subskrybowanie do pakietu Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) w witrynie Azure portal. Rozpocznij od jednego z poniższych interfejsów API:

### <a name="image-moderation"></a>Moderowanie obrazów

Rozpoczynać [Konsola interfejsu API](try-image-api.md) lub użyj [Szybki Start .NET](image-moderation-quickstart-dotnet.md) do skanowania obrazów i wykrywania potencjalnych dorosłych i zawartości erotycznej przy użyciu tagów, oceny zaufania i inne wyodrębnionych informacji.

### <a name="text-moderation"></a>Moderowanie tekstu

Rozpoczynać [Konsola interfejsu API](try-text-api.md) lub użyj [.NET Przewodnik Szybki Start](text-moderation-quickstart-dotnet.md) do skanowania zawartości tekstu dla wulgaryzmy klasyfikacji wspomagane maszynowo niepotrzebnego tekstu (wersja zapoznawcza) i identyfikowalnych danych dane osobowe. 


### <a name="video-moderation"></a>Moderowanie filmów wideo

Rozpoczynać [Szybki Start .NET](video-moderation-api.md) do skanowania filmów wideo i wykrywanie potencjalnych zawartości dla dorosłych. 


### <a name="review-apis"></a>Przegląd interfejsów API

Zacznij tutaj, wybierając z zadania, przejrzyj i interfejsów API przepływu pracy.

- [Zadanie interfejsu API](try-review-api-job.md) skanowania zawartości przy użyciu interfejsy API moderowania i generuje przeglądy w narzędzie do przeglądu. 
- [API przeglądu](try-review-api-review.md) bezpośrednio tworzy obrazu, tekstu lub wideo przeglądy dla człowieka moderatorzy bez pierwszego skanowania zawartości. 
- [API przepływu pracy](try-review-api-workflow.md) tworzy, aktualizuje i pobiera szczegółowe informacje o niestandardowych przepływów pracy tworzonych przez zespół.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [dokumentacja interfejsu API](api-reference.md) i [zestawów SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Szybko Rozpocznij pracę z integracją z [przykłady zestawu SDK platformy .NET](sdk-and-samples.md#net-sdk-samples), [interfejsu API REST przykłady w języku C#](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) i [samouczki](sdk-and-samples.md#tutorials).
