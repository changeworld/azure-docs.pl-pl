---
title: łagodzenie katalogu handlu elektronicznego z uczenia maszynowego i AI z moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Katalogi automatycznie umiarkowane handlu elektronicznego z uczenia maszynowego i AI
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 6177758eaa3e611ad67da0778d889df48b052d90
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095755"
---
# <a name="ecommerce-catalog-moderation-with-machine-learning"></a>łagodzenie katalogu handlu elektronicznego z uczenia maszynowego

W tym samouczku będziemy Dowiedz się, jak zaimplementować machine learning na podstawie inteligentnego handlu elektronicznego katalogu łagodzenia łącząc technologie AI wspierana maszyny z człowieka łagodzenia do zapewnienia inteligentnego katalogu systemu.

![Obrazy niejawnych produktu](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Scenariusza biznesowego

Użyj technologii wspierana maszyny do klasyfikowania i średnie obrazy produktu w tych kategoriach:

1. Osoba dorosła (nagość)
2. Luksusowych (sugerującą)
3. Znanych osób
4. Flagi stany USA
5. Toys
6. Pióra

## <a name="tutorial-steps"></a>Kroki samouczka

Samouczek prowadzi użytkownika przez następujące kroki:

1. Zarejestruj się i tworzenie zespołu moderatora zawartości.
2. Skonfiguruj łagodzenia tagi (etykiety) dla potencjalnych renomy i Flaga zawartości.
3. Za pomocą interfejsu API obrazu zawartości moderatora skanowania pod kątem potencjalnych zawartość dla dorosłych i luksusowych.
4. Za pomocą interfejsu API przetwarzania obrazów komputera skanowania pod kątem potencjalnych znanych osób.
5. Skanowanie w poszukiwaniu ewentualnej obecności flagi przy użyciu usługi wizji niestandardowe.
6. Przedstawia wyniki skanowania nuanced dla człowieka przeglądu i podejmowania decyzji końcowej.

## <a name="create-a-team"></a>Tworzenie zespołu

Zapoznaj się [szybkiego startu](quick-start.md) stronę, aby utworzyć konto moderatora zawartości i tworzenie zespołu. Uwaga **identyfikator zespołu** z **poświadczenia** strony.


## <a name="define-custom-tags"></a>Zdefiniuj znaczniki niestandardowe

Zapoznaj się [tagi](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) artykuł, aby dodać niestandardowe tagi. Oprócz wbudowanych **dla dorosłych** i **luksusowych** tagów, nowe znaczniki Zezwalaj narzędziu przeglądu do wyświetlenia nazw opisowych tagów.

W tym przypadku definiujemy znaczniki niestandardowe (**renomy**, **flagi**, **nam**, **zabawki**, **pióra**):

![Konfigurowanie niestandardowych tagów](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Lista punktów końcowych i klucze interfejsu API

1. Samouczek używa trzech interfejsów API i odpowiadające im klucze i punkty końcowe interfejsu API.
2. Punkty końcowe z interfejsu API są różne na podstawie regionów Twojej subskrypcji i identyfikator zawartości moderatora przeglądu zespołu.

> [!NOTE]
> Samouczek jest przeznaczony do używania kluczy subskrypcji w regionach, które są widoczne w następujących punktów końcowych. Pamiętaj dopasować klucze interfejsu API z regionu identyfikatorów URI w przeciwnym razie klucze mogą nie działać z następujących punktów końcowych:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Skanowanie pod kątem zawartość dla dorosłych i luksusowych

1. Funkcja przyjmuje adres URL obrazu i Tablica par klucz wartość jako parametry.
2. Wywołuje interfejs API obrazu moderatora zawartości można pobrać wyników dorosłą i Racy.
3. Jeśli wynik jest większa niż 0,4 (zakres to od 0 do 1), ustawia wartość w **ReviewTags** tablicy do **True**.
4. **ReviewTags** tablicy jest używany do zaznacz odpowiedniego tagu w narzędziu przeglądu.

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

## <a name="scan-for-celebrities"></a>Skanowanie pod kątem znanych osób

1. Zaloguj się do [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) z [komputera wizji API](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Kliknij przycisk **uzyskać klucz interfejsu API** przycisku.
3. Zaakceptuj postanowienia.
4. Aby się zalogować, wybierz z listy dostępnych kont Internet.
5. Należy pamiętać, klucze interfejsu API wyświetlany na stronie usługi.
    
   ![Klucz interfejsu API przetwarzania obrazów komputera](images/tutorial-computer-vision-keys.PNG)
    
6. Skorzystaj z kodu źródłowego projektu dla funkcji, która skanuje obrazu przy użyciu interfejsu API przetwarzania obrazów komputera.

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

## <a name="classify-into-flags-toys-and-pens"></a>Klasyfikowanie w flagi, toys i pióra

1. [Zaloguj się](https://azure.microsoft.com/en-us/services/cognitive-services/custom-vision-service/) do [Podgląd niestandardowe wizji API](https://www.customvision.ai/).
2. Użyj [szybkiego startu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) do kompilacji z klasyfikatora niestandardowego do wykrycia obecności potencjalnych flagi, toys i pióra.
   ![Wizja niestandardowe obrazy szkolenia](images/tutorial-ecommerce-custom-vision.PNG)
3. [Pobierz adres URL punktu końcowego prognozowania](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) dla Twojego niestandardowego klasyfikatora.
4. Odwołuje się do kodu źródłowego projektu, aby wyświetlić funkcję, która wywołuje punkt końcowy prognozowania klasyfikatora niestandardowych do skanowania obrazu.

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
 
## <a name="reviews-for-human-in-the-loop"></a>Przeglądy dla człowieka w pętli

1. W poprzednich sekcjach przeprowadzone skanowanie przychodzących obrazy dla dorosłych i luksusowych (moderatora zawartości), flagi (niestandardowe wizji) i znanych osób (wizji komputera).
2. W oparciu o naszych progi dopasowanie dla każdego skanowania, udostępnić nuanced przypadków dla człowieka przeglądu w narzędziu przeglądu.
        publiczne statyczne bool {CreateReview (ciąg ImageUrl, KeyValuePair [] metadanych)

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

## <a name="submit-batch-of-images"></a>Przedstawia partii obrazów

1. Ten samouczek zakłada katalogu "C:Test" z pliku tekstowego, który zawiera listę adresów URL obrazu.
2. Poniższy kod sprawdza istnienie pliku i odczytuje wszystkie adresy URL w pamięci.
            Sprawdź, czy katalog testowy do pliku tekstowego z listą adresów URL obrazu do skanowania var topdir = @"C:\test\"; var Urlsfile = topdir +"Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Inicjowanie skanowania wszystkich

1. Ta funkcja najwyższego poziomu w pętli wszystkich obrazów adresów URL w pliku tekstowym, który wspomniano wcześniej.
2. Skanuje je z każdego interfejsu API i jeśli wynik zaufania dopasowania mieści się w naszej kryteria tworzy Przegląd dla człowieka moderatorów.
             dla każdego obrazu adres URL w pliku... foreach (var adres Url w adresach URL) {/ / Initiatize nowej tablicy tagi przeglądu ReviewTags = nowe KeyValuePair [MAXTAGSCOUNT];

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

Wszystkie zestawy SDK usługi kognitywnych firmy Microsoft i przykłady, jest udzielana z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Kodeks postępowania dewelopera

Przy użyciu usług kognitywnych, łącznie z tej biblioteki klienta & próbki, deweloperzy mają wykonaj "Developer kodu z należy przeprowadzić dla Microsoft kognitywnych usługi", podczas gdy znaleziono w http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Kolejne kroki

Tworzenie i rozszerzanie samouczka za pomocą [pliki źródłowe projektu](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) w witrynie Github.
