---
title: 'Samouczek: moderowanie obrazów produktów na potrzeby handlu elektronicznego — Content Moderator'
titlesuffix: Azure Cognitive Services
description: Konfigurowanie aplikacji do analizowania i klasyfikowania obrazów produktów w określonej etykiety (przy użyciu przetwarzania obrazów platformy Azure i Custom Vision). Tag niepożądanych obrazów do przejrzane (przy użyciu usługi Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: ec17f9f0206ef639bd47d694880c064a012ea1cf
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604189"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Samouczek: moderowanie obrazów produktów na potrzeby handlu elektronicznego za pomocą usługi Azure Content Moderator

W tym samouczku dowiesz się, jak używać usług Azure Cognitive Services, usługi Content Moderator, w tym do klasyfikowania i obrazy produktów średni scenariusz handlu elektronicznego. Korzystając z przetwarzania obrazów i Custom Vision zastosować znaczniki (etykiety) do obrazów, a następnie będzie Utwórz przeglądu zespołu łączy technologie oparte na nauce maszyny Content Moderator z zespołami przeglądu przez ludzi, aby zapewnić systemu Moderowanie inteligentnego.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Rejestracja w usłudze Content Moderator i tworzenie zespołu do przeprowadzania przeglądu.
> * Używanie interfejsu API obrazów usługi Content Moderator do skanowania materiału pod kątem potencjalnej zawartości erotycznej lub dla dorosłych.
> * Używanie usługi przetwarzania obrazów do skanowania pod kątem znanych osobistości (lub innych tagów wykrywanych przez interfejs API przetwarzania obrazów).
> * Używanie usługi Custom Vision do skanowania pod kątem flag, zabawek, długopisów (lub innych tagów niestandardowych).
> * Przekazywanie połączonych wyników skanowania do przeglądu z udziałem człowieka i ostatecznej decyzji.

Kompletny przykładowy kod jest dostępny w repozytorium [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) (Przykłady dotyczące moderowania katalogu handlu elektronicznego) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji pakietu Content Moderator. Aby zasubskrybować usługę Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Klucz subskrypcji usługi przetwarzania obrazów (instrukcje jak wyżej).
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).
- Zestaw obrazów dla każdej etykiety używanej przez klasyfikator usługi Custom Vision (w tym przypadku: zabawki, długopisy i flagi USA).

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Zapoznaj się [spróbuj pakietu Content Moderator w sieci web](quick-start.md) szybkiego startu, aby uzyskać instrukcje, jak zarejestrować się w celu [Content Moderator analizować narzędzie](https://contentmoderator.cognitive.microsoft.com/) i tworzenie zespołu. Zwróć uwagę na wartość **Identyfikator zespołu** na stronie **Poświadczenia**.

## <a name="create-custom-moderation-tags"></a>Tworzenie niestandardowych tagów moderacji

Następnie należy utworzyć niestandardowe znaczniki w narzędzie do przeglądu (zobacz [tagi](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) artykułu, jeśli potrzebujesz pomocy z tym procesem). W tym przypadku zostaną dodane następujące tagi: **celebrity** (osobistości), **USA**, **flag** (flaga), **toy** (zabawka) i **pen** (długopis). Nie wszystkie tagi muszą być wykrywalny kategorii w wizualizacji komputerowej (takich jak **osobistości**); można dodać własne niestandardowe tagi, tak długo, jak uczyć klasyfikatory Custom Vision, wykrywać je później.

![Konfigurowanie tagów niestandardowych](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio otwórz okno dialogowe Nowy projekt. Rozwiń węzeł **Zainstalowane** i węzeł **Visual C#** , a następnie wybierz pozycję **Aplikacja konsolowa (.NET Framework)** .
1. Nadaj aplikacji nazwę **EcommerceModeration**, a następnie kliknij przycisk **OK**.
1. Jeśli dodajesz ten projekt do istniejącego rozwiązania, wybierz ten projekt jako pojedynczy projekt startowy.

Ten samouczek przedstawia proces kod, który stanowi podstawę do projektu, ale go nie obejmują wszystkich wierszy kodu. Skopiuj całą zawartość pliku _Program.cs_ z przykładowego projektu ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) do pliku _Program.cs_ w swoim nowym projekcie. Następnie przejrzyj kolejne sekcje, aby poznać sposób działania tego projektu i dowiedzieć się, jak z niego korzystać.

## <a name="define-api-keys-and-endpoints"></a>Definiowanie kluczy i punktów końcowych interfejsu API

Ten samouczek używa trzech usług cognitive services; w związku z tym wymaga trzech odpowiadających im kluczy i punkty końcowe interfejsu API. Zwróć uwagę na następujące pola w klasie **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Musisz zaktualizować `___Key` pola z wartościami klucze subskrypcji (otrzymasz `CustomVisionKey` później), i może być konieczna zmiana `___Uri` pól, dzięki czemu zawierają identyfikatory poprawny region. W części `YOURTEAMID` pola `ReviewUri` wpisz identyfikator zespołu do przeprowadzania przeglądu, który utworzono wcześniej. Będzie wypełniana w końcowej części `CustomVisionUri` pola później.

## <a name="primary-method-calls"></a>Główne wywołania metod

Zwróć uwagę na następujący kod w metodzie **Main**, wykonujący pętlę na liście adresów URL obrazów. Analizuje każdy obraz w trzech różnych usług, rejestruje zastosować znaczniki w **ReviewTags** tablicy, a następnie tworzy recenzję dla moderatorów ludzi, wysyłając obrazy do zawartości, przejrzyj narzędzia moderatora. Te metody przedstawimy w kolejnych sekcjach. Jeśli chcesz, możesz kontrolować obrazów, które są wysyłane do przeglądu, przy użyciu **ReviewTags** tablicy w instrukcji warunkowej, aby sprawdzić, jakie znaczniki zostały zastosowane.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Metoda EvaluateAdultRacy

Zwróć uwagę na metodę **EvaluateAdultRacy** w klasie **Program**. Parametrami tej metody są adres URL obrazu i tablica par wartości kluczy. Wywołuje ona interfejs API REST wyszukiwania obrazów usługi Content Moderator w celu pobrania wyników analizy tego obrazu pod kątem treści erotycznych i dla dorosłych. Jeśli wynik dla dowolnego jest większa niż Update 0.4 (zakres to od 0 do 1), ustawia odpowiednią wartość w **ReviewTags** tablicy do **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Metoda EvaluateComputerVisionTags

Kolejna metoda przyjmuje adres URL obrazu i dane subskrypcji interfejsu API przetwarzania obrazów i analizuje obraz pod kątem obecności znanych osobistości. W przypadku znalezienia co najmniej jednej osobistości w tablicy **ReviewTags** zostaje ustawiona wartość **True** dla odpowiedniego parametru.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Metoda EvaluateCustomVisionTags

Następnie zwróć uwagę na metodę **EvaluateCustomVisionTags**, która klasyfikuje poszczególne produkty &mdash; w tym przypadku flagi, zabawki i długopisy. Postępuj zgodnie z instrukcjami w [sposób tworzenia klasyfikatora](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) Przewodnik tworzenia klasyfikatora niestandardowego obrazu i wykrywać flagi, zabawki i pióra (lub niezależnie od wybrania jako niestandardowe znaczniki) na obrazach. Możesz używać obrazów w **przykładowe obrazy** folderu [repozytorium GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) szybko trenować niektóre kategorie, w tym przykładzie.

![Strona internetowa usługi Custom Vision z obrazami szkoleniowymi przedstawiającymi długopisy, zabawki i flagi](images/tutorial-ecommerce-custom-vision.PNG)

Po już uczony klasyfikatora, należy uzyskać klucz prognoz i adres URL punktu końcowego prognozowania (zobacz [uzyskać adres URL i prognozowania klucz](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) Jeśli potrzebujesz pomocy, wraz z ich pobieraniem) i przypisz tych wartości, aby Twoje `CustomVisionKey` i `CustomVisionUri` pola, odpowiednio. Te wartości są używane w tej metodzie do wysyłania zapytań do klasyfikatora. Jeśli klasyfikator znajdzie na obrazie zawartość odpowiadającą co najmniej jednemu z tych tagów niestandardowych, metoda ustawi wartość **True** dla odpowiednich parametrów w tablicy **ReviewTags**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Tworzenie przeglądów dla narzędzia do przeprowadzania przeglądu

W poprzednich sekcjach zostały przedstawione, jak aplikacji przeskanuje przychodzące obrazów (Content Moderator) wyszukania zawartości erotycznej i przeznaczonej dla osób dorosłych, osobistości (przetwarzania obrazów) i różnych innych obiektów (Custom Vision). Następnie możesz zapoznać się **CreateReview** metody, która przekazuje obrazy ze wszystkimi ich zastosować znaczniki (przekazana jako _metadanych_) aby narzędzie do przeglądu Content Moderator.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Te obrazy będą widoczne na karcie Review (Przegląd) [narzędzia do przeprowadzania przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Zrzut ekranu przedstawiający narzędzie do przeprowadzania przeglądu usługi Content Moderator wraz z kilkoma obrazami i wyróżnionymi tagami](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Przesyłanie listy obrazów testowych

Jak widać w metodzie **Main**, program szuka katalogu „C:Test” z plikiem _Urls.txt_, zawierającym listę adresów URL obrazów. Utwórz ten plik i katalog, lub zmień ścieżkę, tak aby wskazywał plik tekstowy. Wypełnij ten plik przy użyciu adresów URL obrazów, które chcesz przetestować.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Uruchamianie programu

Po wykonaniu wszystkich powyższych kroków program powinien przetworzyć każdy obraz (zapytań wszystkie trzy usługi ich odpowiednie tagi) i następnie Przekaż obrazy przy użyciu informacji dotyczących tagu do narzędzia do przeglądu Content Moderator.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku ustawiono program do analizowania obrazy produktów, oznaczyć je według typu produktu i zespół przeglądu do podejmowania świadomych decyzji dotyczących moderowanie zawartości. Teraz możesz dowiedzieć się więcej o szczegółach moderowania obrazów.

> [!div class="nextstepaction"]
> [Review moderated images (Przeprowadzanie przeglądu moderowanych obrazów)](./review-tool-user-guide/review-moderated-images.md)
