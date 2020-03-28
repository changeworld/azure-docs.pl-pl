---
title: 'Samouczek: Moderowanie obrazów produktów e-commerce - Moderator treści'
titleSuffix: Azure Cognitive Services
description: W tym samouczku pokazano, jak skonfigurować aplikację do analizowania i klasyfikowania obrazów produktów z określonymi etykietami (przy użyciu usługi Azure Computer Vision i Custom Vision). Oznaczanie obrazów budzących zastrzeżenia do dalszego przeglądania (przy użyciu narzędzia Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5e74eda9e30c536c0eba4e847019344c87e10cce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774348"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Samouczek: Moderowanie obrazów produktów e-commerce za pomocą moderatora zawartości platformy Azure

W tym samouczku dowiesz się, jak używać usług Azure Cognitive Services, w tym moderatora zawartości, do klasyfikowania i moderowania obrazów produktów w scenariuszu handlu elektronicznego. Użyjesz wizji komputerowej i wizji niestandardowej, aby zastosować tagi (etykiety) do obrazów, a następnie utworzysz przegląd zespołu, który łączy technologie oparte na uczeniu maszynowym content moderatora z zespołami przeglądu ludzkiego, aby zapewnić inteligentny system moderowania.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Rejestracja w usłudze Content Moderator i tworzenie zespołu do przeprowadzania przeglądu.
> * Używanie interfejsu API obrazów usługi Content Moderator do skanowania materiału pod kątem potencjalnej zawartości erotycznej lub dla dorosłych.
> * Używanie usługi przetwarzania obrazów do skanowania pod kątem znanych osobistości (lub innych tagów wykrywanych przez interfejs API przetwarzania obrazów).
> * Używanie usługi Custom Vision do skanowania pod kątem flag, zabawek, długopisów (lub innych tagów niestandardowych).
> * Przekazywanie połączonych wyników skanowania do przeglądu z udziałem człowieka i ostatecznej decyzji.

Kompletny przykładowy kod jest dostępny w repozytorium [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) (Przykłady dotyczące moderowania katalogu handlu elektronicznego) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji pakietu Content Moderator. Aby zasubskrybować usługę Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Klucz subskrypcji usługi przetwarzania obrazów (instrukcje jak wyżej).
- Dowolna wersja [programu Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).
- Zestaw obrazów dla każdej etykiety używanej przez klasyfikator usługi Custom Vision (w tym przypadku: zabawki, długopisy i flagi USA).

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Zapoznaj się z przewodnikiem Szybki start [try content w sieci Web,](quick-start.md) aby uzyskać instrukcje dotyczące rejestrowania się w narzędziu do przeglądania [moderatora zawartości](https://contentmoderator.cognitive.microsoft.com/) i tworzenia zespołu recenzentów. Zwróć uwagę na wartość **Identyfikator zespołu** na stronie **Poświadczenia**.

## <a name="create-custom-moderation-tags"></a>Tworzenie niestandardowych tagów moderacji

Następnie utwórz niestandardowe znaczniki w narzędziu Recenzja (jeśli potrzebujesz pomocy w tym procesie, zobacz artykuł [Znaczniki).](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) W tym przypadku zostaną dodane następujące tagi: **celebrity** (osobistości), **USA**, **flag** (flaga), **toy** (zabawka) i **pen** (długopis). Nie wszystkie tagi muszą być wykrywalne kategorie w Computer Vision (jak **sławna**); można dodać własne tagi niestandardowe tak długo, jak trenować klasyfikatora wizji niestandardowej, aby wykryć je później.

![Konfigurowanie tagów niestandardowych](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio otwórz okno dialogowe Nowy projekt. Rozwiń węzeł **Zainstalowane** i węzeł **Visual C#**, a następnie wybierz pozycję **Aplikacja konsolowa (.NET Framework)**.
1. Nadaj aplikacji nazwę **EcommerceModeration**, a następnie kliknij przycisk **OK**.
1. Jeśli dodajesz ten projekt do istniejącego rozwiązania, wybierz ten projekt jako pojedynczy projekt startowy.

W tym samouczku wyróżniono kod, który jest kluczowy dla projektu, ale nie obejmie każdego wiersza kodu. Skopiuj całą zawartość pliku _Program.cs_ z przykładowego projektu ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) do pliku _Program.cs_ w swoim nowym projekcie. Następnie przejrzyj kolejne sekcje, aby poznać sposób działania tego projektu i dowiedzieć się, jak z niego korzystać.

## <a name="define-api-keys-and-endpoints"></a>Definiowanie kluczy i punktów końcowych interfejsu API

Ten samouczek korzysta z trzech usług poznawczych; w związku z tym wymaga trzech odpowiednich kluczy i punktów końcowych interfejsu API. Zwróć uwagę na następujące pola w klasie **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Musisz zaktualizować `___Key` pola z wartościami kluczy subskrypcji i musisz zmienić `___Uri` pola na poprawne adresy URL punktów końcowych (później otrzymasz klucz wizji niestandardowej i punkt końcowy). Wartości te można znaleźć na kartach **Szybki start** każdego zasobu platformy Azure. W części `YOURTEAMID` pola `ReviewUri` wpisz identyfikator zespołu do przeprowadzania przeglądu, który utworzono wcześniej. Później wypełnisz ostatnią część `CustomVisionUri` pola.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Główne wywołania metod

Zwróć uwagę na następujący kod w metodzie **Main**, wykonujący pętlę na liście adresów URL obrazów. Analizuje każdy obraz za pomocą trzech różnych usług, rejestruje zastosowane tagi w tablicy **ReviewTags,** a następnie tworzy recenzję dla moderatorów ludzkich, wysyłając obrazy do narzędzia do przeglądu moderatora zawartości. Te metody przedstawimy w kolejnych sekcjach. Jeśli chcesz, można kontrolować, które obrazy są wysyłane do przeglądu, za pomocą **ReviewTags** tablicy w instrukcji warunkowej, aby sprawdzić, które tagi zostały zastosowane.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Metoda EvaluateAdultRacy

Zwróć uwagę na metodę **EvaluateAdultRacy** w klasie **Program**. Parametrami tej metody są adres URL obrazu i tablica par wartości kluczy. Wywołuje ona interfejs API REST wyszukiwania obrazów usługi Content Moderator w celu pobrania wyników analizy tego obrazu pod kątem treści erotycznych i dla dorosłych. Jeśli wynik dla obu jest większy niż 0,4 (zakres wynosi od 0 do 1), ustawia odpowiednią wartość w **ReviewTags** tablicy **true**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Metoda EvaluateComputerVisionTags

Kolejna metoda przyjmuje adres URL obrazu i dane subskrypcji interfejsu API przetwarzania obrazów i analizuje obraz pod kątem obecności znanych osobistości. W przypadku znalezienia co najmniej jednej osobistości w tablicy **ReviewTags** zostaje ustawiona wartość **True** dla odpowiedniego parametru.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Metoda EvaluateCustomVisionTags

Następnie zwróć uwagę na metodę **EvaluateCustomVisionTags**, która klasyfikuje poszczególne produkty &mdash; w tym przypadku flagi, zabawki i długopisy. Postępuj zgodnie z instrukcjami w przewodniku [Jak utworzyć klasyfikator,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) aby utworzyć własny klasyfikator obrazów niestandardowych i wykryć flagi, zabawki i długopisy (lub cokolwiek, co wybrałeś jako niestandardowe tagi) w obrazach. Obrazów w folderze **obrazów przykładowych** [repozytorium GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) można szybko wyszkolić niektóre kategorie w tym przykładzie.

![Strona internetowa usługi Custom Vision z obrazami szkoleniowymi przedstawiającymi długopisy, zabawki i flagi](images/tutorial-ecommerce-custom-vision.PNG)

Po przeszkoleniu klasyfikatora, uzyskać klucz przewidywania i przewidywanie adres URL punktu końcowego (zobacz [Pobierz adres URL i klucz przewidywania,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) jeśli potrzebujesz pomocy w ich pobieraniu) i przypisać te wartości do i `CustomVisionKey` `CustomVisionUri` pola, odpowiednio. Te wartości są używane w tej metodzie do wysyłania zapytań do klasyfikatora. Jeśli klasyfikator znajdzie na obrazie zawartość odpowiadającą co najmniej jednemu z tych tagów niestandardowych, metoda ustawi wartość **True** dla odpowiednich parametrów w tablicy **ReviewTags**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Tworzenie przeglądów dla narzędzia do przeprowadzania przeglądu

W poprzednich sekcjach zbadano, jak aplikacja skanuje przychodzące obrazy w poszukiwaniu treści dla dorosłych i treści rasistowskich (Moderator treści), gwiazd (Wizja komputerowa) i różnych innych obiektów (Custom Vision). Następnie zobacz **CreateReview** metoda, która przesyła obrazy ze wszystkimi ich zastosowane tagi (przekazywane jako _metadane)_ do narzędzia przeglądu moderatora zawartości.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Te obrazy będą widoczne na karcie Review (Przegląd) [narzędzia do przeprowadzania przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Zrzut ekranu przedstawiający narzędzie do przeprowadzania przeglądu usługi Content Moderator wraz z kilkoma obrazami i wyróżnionymi tagami](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Przesyłanie listy obrazów testowych

Jak widać w metodzie **Main**, program szuka katalogu „C:Test” z plikiem _Urls.txt_, zawierającym listę adresów URL obrazów. Utwórz ten plik i katalog lub zmień ścieżkę wskazującą plik tekstowy. Następnie wypełnij ten plik adresami URL obrazów, które chcesz przetestować.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Uruchamianie programu

Jeśli podasz wszystkie powyższe kroki, program powinien przetworzyć każdy obraz (zapytanie wszystkich trzech usług dla odpowiednich tagów), a następnie przesłać obrazy z informacjami o znacznikach do narzędzia do przeglądu moderatora zawartości.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujesz program do analizowania obrazów produktów, oznaczania ich według typu produktu i zezwalania zespołowi recenzentów na podejmowanie świadomych decyzji dotyczących moderowania zawartości. Teraz możesz dowiedzieć się więcej o szczegółach moderowania obrazów.

> [!div class="nextstepaction"]
> [Review moderated images (Przeprowadzanie przeglądu moderowanych obrazów)](./review-tool-user-guide/review-moderated-images.md)
