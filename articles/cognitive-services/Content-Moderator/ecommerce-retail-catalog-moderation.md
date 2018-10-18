---
title: 'Samouczek: moderowanie katalogu handlu elektronicznego — Content Moderator'
titlesuffix: Azure Cognitive Services
description: Automatycznie moderuj katalogi handlu elektronicznego przy użyciu uczenia maszynowego i sztucznej inteligencji.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 0bd61c3f1a4f660076be4e87bb5443302e5dc013
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363998"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>Samouczek: moderowanie katalogu handlu elektronicznego za pomocą uczenia maszynowego

W tym samouczku opisano, jak wdrożyć inteligentne moderowanie katalogu handlu elektronicznego oparte na uczeniu maszynowym przez połączenie technologii sztucznej inteligencji wspomaganych maszynowo z moderowaniem z udziałem człowieka, aby zapewnić inteligentny system katalogów.

![Sklasyfikowane obrazy produktów](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Scenariusz biznesowy

Technologie wspomagane maszynowo umożliwiają klasyfikowanie i moderowanie obrazów produktów w następujących kategoriach:

1. Dla dorosłych (nagość)
2. Erotyka (o charakterze seksualnym)
3. Celebryci
4. Flagi USA
5. Zabawki
6. Pióra

## <a name="tutorial-steps"></a>Kroki Samouczka

Ten samouczek zawiera instrukcje wykonania następujących kroków:

1. Rejestracja i tworzenie zespołu dla usługi Content Moderator.
2. Konfigurowanie tagów moderowania (etykiet) pod kątem potencjalnej zawartości, w tym celebrytów i flag.
3. Używanie interfejsu API obrazów usługi Content Moderator do skanowania materiału pod kątem potencjalnej zawartości erotycznej lub dla dorosłych.
4. Używanie interfejsu API przetwarzania obrazów do skanowania materiału pod kątem potencjalnych celebrytów.
5. Używanie usługi Custom Vision do skanowania pod kątem możliwej obecności flag.
6. Przekazywanie szczegółowych wyników skanowania do przeglądu z udziałem człowieka i ostatecznej decyzji.

## <a name="create-a-team"></a>Tworzenie zespołu

Zapoznaj się z informacjami na stronie [Szybki start](quick-start.md), zarejestruj się w usłudze Content Moderator i utwórz zespół. Zanotuj **identyfikator zespołu** ze strony **Poświadczenia**.


## <a name="define-custom-tags"></a>Definiowanie tagów niestandardowych

Zapoznaj się z informacjami w artykule [Tagi](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) i dodaj tagi niestandardowe. Po dodaniu do wbudowanych tagów dla **dorosłych** i **erotycznych** nowe tagi umożliwiają narzędziu do przeglądu wyświetlanie nazw opisowych tagów.

W naszym przypadku zdefiniujemy te tagi niestandardowe (**celebrity**, **flag**, **us**, **toy**, **pen**):

![Konfigurowanie tagów niestandardowych](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Podawanie listy kluczy i punktów końcowych interfejsu API

1. W Samouczku użyto trzech interfejsów API oraz odpowiadających im kluczy i punktów końcowych interfejsu API.
2. Punkty końcowe interfejsu API są różne w zależności od regionów subskrypcji oraz identyfikatora zespołu do przeprowadzania przeglądu usługi Content Moderator.

> [!NOTE]
> W Samouczku użyto kluczy subskrypcji w regionach widocznych w następujących punktach końcowych. Pamiętaj, aby dopasować klucze interfejsu API do identyfikatorów URI regionu, w przeciwnym razie klucze mogą nie działać z następującymi punktami końcowymi:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Skanowanie pod kątem zawartości erotycznej i dla osób dorosłych

1. Parametrami funkcji są adres URL obrazu i tablica par wartości kluczy.
2. Wywołuje interfejs API wyszukiwania obrazów usługi Content Moderator w celu pobrania wyników dla treści erotycznych i dla dorosłych.
3. Jeśli wynik przekracza 0,4 (w zakresie od 0 do 1), w tablicy **ReviewTags** zostaje ustawiona wartość **True**.
4. Tablica **ReviewTags** służy do wyróżniania odpowiadającego tagu w narzędziu do przeglądu.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Skanowanie pod kątem celebrytów

1. Zarejestruj się w [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) [interfejsu API przetwarzania obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Kliknij przycisk **Pobierz klucz interfejsu API**.
3. Zaakceptuj warunki.
4. Aby się zalogować, wybierz pozycję z listy dostępnych kont internetowych.
5. Zanotuj klucze interfejsu API wyświetlone na stronie usługi.
    
   ![Klucze interfejsu API przetwarzania obrazów](images/tutorial-computer-vision-keys.PNG)
    
6. Zapoznaj się z kodem źródłowym projektu funkcji, która skanuje obraz za pomocą interfejsu API przetwarzania obrazów.

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Klasyfikowanie na flagi, zabawki i pióra

1. [Zaloguj się](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) do [wersji zapoznawczej interfejsu API usługi Custom Vision](https://www.customvision.ai/).
2. Użyj przewodnika [Szybki Start](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) do utworzenia klasyfikatora niestandardowego, który będzie służył do wykrywania potencjalnej obecności flag, zabawek i piór.
   ![Obrazy ze szkolenia dotyczącego usługi Custom Vision](images/tutorial-ecommerce-custom-vision.PNG)
3. [Pobierz adres URL punktu końcowego przewidywania](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) dla klasyfikatora niestandardowego.
4. Sprawdź kod źródłowy projektu, aby zobaczyć funkcję, która wywołuje punkt końcowy przewidywania klasyfikatora niestandardowego, i przeskanować obraz.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>Przeglądy wymagające udziału człowieka

1. W poprzednich sekcjach przeprowadziliśmy skanowanie obrazów przychodzących pod kątem treści dla osób dorosłych (Content Moderator), celebrytów (przetwarzanie obrazów) i flag (Custom Vision).
2. W oparciu o progi dopasowania dla każdego skanowania udostępnij szczegóły przypadków do przeglądu przez człowieka w narzędziu do przeglądu.
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>Przesyłanie partii obrazów

1. W tym Samouczku przyjęto, że jest używany katalog „C:Test” zawierający plik tekstowy z listą adresów URL obrazów.
2. Poniższy kod sprawdza istnienie pliku i odczytuje wszystkie adresy URL do pamięci.
            // Sprawdź katalog testowy pod kątem pliku testowego zawierającego listę adresów URL obrazów, aby przeskanować var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Inicjowanie wszystkich skanowań

1. Ta funkcja najwyższego poziomu obsługuje pętlę przez wszystkie adresy URL obrazów we wcześniej wspomnianym pliku tekstowym.
2. Skanuje je za pomocą każdego interfejsu API i jeśli dopasowany współczynnik ufności pasuje do naszych kryteriów, tworzy przegląd dla użytkowników pełniących rolę moderatorów.
             // dla każdego adresu URL obrazu w pliku... foreach (var Url in Urls) { // Zainicjuj nową tablicę tagów przeglądu ReviewTags = nowe KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Licencja

Wszystkie zestawy SDK usługi Microsoft Cognitive Services i przykłady podlegają licencji MIT. Aby uzyskać więcej szczegółów, zobacz [LICENSE](https://microsoft.mit-license.org/) (LICENCJA).

## <a name="developer-code-of-conduct"></a>Kodeks postępowania dewelopera

Deweloperzy korzystający z usługi Cognitive Services, łącznie z tą biblioteką kliencką i przykładami powinni postępować zgodnie z dokumentem „Developer Code of Conduct for Microsoft Cognitive Services” („Kodeks postępowania dla deweloperów usługi Microsoft Cognitive Services”) znajdującym się pod adresem http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Następne kroki

Opracowuj i poszerzaj samouczek przy użyciu [plików źródłowych projektu](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) w portalu Github.
