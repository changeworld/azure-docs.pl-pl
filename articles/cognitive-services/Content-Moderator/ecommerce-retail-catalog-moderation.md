---
title: 'Samouczek: moderowanie obrazów produktów na potrzeby handlu elektronicznego — Content Moderator'
titlesuffix: Azure Cognitive Services
description: Konfigurowanie aplikacji do analizowania i klasyfikowania obrazów produktów przy użyciu określonych etykiet (za pomocą interfejsu API przetwarzania obrazów i usługi Custom Vision na platformie Azure) oraz oznaczania niepożądanych obrazów do dalszego przeglądu (za pomocą usługi Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 900ad8b7f676eb67f9ac0fc808600779f832a102
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699435"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Samouczek: moderowanie obrazów produktów na potrzeby handlu elektronicznego za pomocą usługi Azure Content Moderator

Z tego samouczka dowiesz się, jak używać usług Azure Cognitive Services, w tym usługi Content Moderator, do efektywnego klasyfikowania i moderowania obrazów produktów w scenariuszu handlu elektronicznego. Za pomocą interfejsu API przetwarzania obrazów i usługi Custom Vision zastosujesz różne tagi (etykiety) do obrazów, a następnie utworzysz przegląd zespołowy, łączący technologię uczenia maszynowego usługi Content Moderator z pracą zespołową wykonywaną przez ludzi, aby uzyskać system inteligentnej moderacji.

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

Następnie należy utworzyć tagi niestandardowe w narzędziu do przeprowadzania przeglądu (jeśli potrzebujesz pomocy w tym procesie, zapoznaj się z artykułem [Tags — Tagi](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags)). W tym przypadku zostaną dodane następujące tagi: **celebrity** (osobistości), **USA**, **flag** (flaga), **toy** (zabawka) i **pen** (długopis). Pamiętaj, że nie wszystkie te tagi muszą odpowiadać kategoriom wykrywanym przez interfejs API przetwarzania obrazów (jak **celebrity**). Możesz dodawać też własne tagi niestandardowe, jeśli tylko później wyszkolisz klasyfikator usługi Custom Vision do ich wykrywania.

![Konfigurowanie tagów niestandardowych](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio otwórz okno dialogowe Nowy projekt. Rozwiń węzeł **Zainstalowane** i węzeł **Visual C#**, a następnie wybierz pozycję **Aplikacja konsolowa (.NET Framework)**.
1. Nadaj aplikacji nazwę **EcommerceModeration**, a następnie kliknij przycisk **OK**.
1. Jeśli dodajesz projekt do istniejącego rozwiązania, wybierz ten projekt jako pojedynczy projekt startowy.

W tym samouczku przedstawiono kod najważniejszy dla projektu, ale nie omówiono każdej linijki wymaganego kodu. Skopiuj całą zawartość pliku _Program.cs_ z przykładowego projektu ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) do pliku _Program.cs_ w swoim nowym projekcie. Następnie przejrzyj kolejne sekcje, aby poznać sposób działania tego projektu i dowiedzieć się, jak z niego korzystać.

## <a name="define-api-keys-and-endpoints"></a>Definiowanie kluczy i punktów końcowych interfejsu API

Jak wspomniano powyżej, w tym samouczku używane są trzy usługi Cognitive Services, a zatem potrzebne są trzy odpowiadające im klucze i punkty końcowe interfejsu API. Zwróć uwagę na następujące pola w klasie **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Należy zaktualizować pola `___Key`, używając wartości kluczy subskrypcji (klucz `CustomVisionKey` uzyskasz później). Konieczna może być również zmiana pól `___Uri` w taki sposób, aby zawierały poprawne identyfikatory regionu. W części `YOURTEAMID` pola `ReviewUri` wpisz identyfikator zespołu do przeprowadzania przeglądu, który utworzono wcześniej. Końcową część pola `CustomVisionUri` uzupełnisz później.

## <a name="primary-method-calls"></a>Główne wywołania metod

Zwróć uwagę na następujący kod w metodzie **Main**, wykonujący pętlę na liście adresów URL obrazów. Ten kod analizuje każdy obraz przy użyciu wszystkich trzech usług, zapisuje zastosowane tagi w tablicy **ReviewTags**, a następnie tworzy przegląd dla moderatorów (wysyła obrazy do narzędzia do przeprowadzania przeglądu usługi Content Moderator). Te metody przedstawimy w kolejnych sekcjach. Zwróć uwagę, że jeśli chcesz, możesz tutaj określić, które obrazy będą wysyłane do przeglądu, używając tablicy **ReviewTags** w instrukcji warunkowej w celu sprawdzenia, jakie tagi zostały zastosowane.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Metoda EvaluateAdultRacy

Zwróć uwagę na metodę **EvaluateAdultRacy** w klasie **Program**. Parametrami tej metody są adres URL obrazu i tablica par wartości kluczy. Wywołuje ona interfejs API REST wyszukiwania obrazów usługi Content Moderator w celu pobrania wyników analizy tego obrazu pod kątem treści erotycznych i dla dorosłych. Jeśli wynik w dowolnej z tych kategorii przekracza 0,4 (w zakresie od 0 do 1), w tablicy **ReviewTags** zostaje ustawiona wartość **True** dla odpowiedniego parametru.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Metoda EvaluateComputerVisionTags

Kolejna metoda przyjmuje adres URL obrazu i dane subskrypcji interfejsu API przetwarzania obrazów i analizuje obraz pod kątem obecności znanych osobistości. W przypadku znalezienia co najmniej jednej osobistości w tablicy **ReviewTags** zostaje ustawiona wartość **True** dla odpowiedniego parametru.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Metoda EvaluateCustomVisionTags

Następnie zwróć uwagę na metodę **EvaluateCustomVisionTags**, która klasyfikuje poszczególne produkty &mdash; w tym przypadku flagi, zabawki i długopisy. Postępuj zgodnie z instrukcjami przedstawionymi w przewodniku [How to build a classifier (Tworzenie klasyfikatora)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier), aby utworzyć własny niestandardowy klasyfikator obrazów w celu wykrywania flag, zabawek i długopisów (lub innych przedmiotów, zgodnie z wybranymi przez Ciebie tagami niestandardowymi) na obrazach. Możesz używać obrazów w **przykładowe obrazy** folderu [repozytorium GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) szybko trenować niektóre kategorie, w tym przykładzie.

![Strona internetowa usługi Custom Vision z obrazami szkoleniowymi przedstawiającymi długopisy, zabawki i flagi](images/tutorial-ecommerce-custom-vision.PNG)

Po wyszkoleniu klasyfikatora należy uzyskać klucz predykcyjny i adres URL punktu końcowego przewidywania (zobacz [Pobieranie adresu URL i klucza predykcyjnego](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key), jeśli potrzebujesz pomocy), a następnie przypisać ich wartości odpowiednio do pól `CustomVisionKey` i `CustomVisionUri`. Te wartości są używane w tej metodzie do wysyłania zapytań do klasyfikatora. Jeśli klasyfikator znajdzie na obrazie zawartość odpowiadającą co najmniej jednemu z tych tagów niestandardowych, metoda ustawi wartość **True** dla odpowiednich parametrów w tablicy **ReviewTags**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Tworzenie przeglądów dla narzędzia do przeprowadzania przeglądu

W poprzednich sekcjach omówiono metody używane do skanowania obrazów przychodzących pod kątem treści dla osób dorosłych i treści erotycznych (Content Moderator), celebrytów (przetwarzanie obrazów) i innych obiektów (Custom Vision). Teraz zwróć uwagę na metodę **CreateReview**, która przekazuje obrazy wraz ze wszystkimi zastosowanymi tagami (wprowadzonymi w parametrze _Metadata_) do narzędzia do przeprowadzania przeglądu usługi Content Moderator, aby udostępnić je do przeglądu przez człowieka. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Te obrazy będą widoczne na karcie Review (Przegląd) [narzędzia do przeprowadzania przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Zrzut ekranu przedstawiający narzędzie do przeprowadzania przeglądu usługi Content Moderator wraz z kilkoma obrazami i wyróżnionymi tagami](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Przesyłanie listy obrazów testowych

Jak widać w metodzie **Main**, program szuka katalogu „C:Test” z plikiem _Urls.txt_, zawierającym listę adresów URL obrazów. Utwórz taki katalog i plik lub zmień tę ścieżkę tak, aby wskazywała Twój plik tekstowy, a następnie wypełnij ten plik adresami URL obrazów, których chcesz użyć w teście.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Uruchamianie programu

Po wykonaniu wszystkich powyższych kroków program powinien przetworzyć wszystkie obrazy (wysyłając zapytania do wszystkich trzech usług w celu uzyskania odpowiednich tagów z każdej z nich), a następnie przekazać je wraz z informacjami o tagach do narzędzia do przeprowadzania przeglądu usługi Content Moderator.

## <a name="next-steps"></a>Kolejne kroki

Podczas pracy z tym samouczkiem skonfigurowano program do analizowania obrazów produktów w celu oznaczenia ich tagami określającymi typ produktu i ułatwienia zespołowi przeprowadzającemu przegląd podejmowanie świadomych decyzji dotyczących moderacji treści. Teraz możesz dowiedzieć się więcej o szczegółach moderowania obrazów.

> [!div class="nextstepaction"]
> [Review moderated images (Przeprowadzanie przeglądu moderowanych obrazów)](./review-tool-user-guide/review-moderated-images.md)
