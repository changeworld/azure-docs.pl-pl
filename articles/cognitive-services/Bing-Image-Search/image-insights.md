---
title: Uzyskaj szczegółowe informacje o obrazie — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak uzyskać więcej informacji na temat obrazu za pomocą interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: f84c6329c2a4dd0a9ad9e81f3700c9e31de95a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883428"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Uzyskaj szczegółowe informacje o obrazach za pomocą interfejsu API wyszukiwania obrazów usługi Bing

> [!IMPORTANT]
> Zamiast używać /images/details punkt końcowy, aby uzyskać wgląd w obrazy, należy użyć [wyszukiwania wizualnego,](../bing-visual-search/overview.md) ponieważ zapewnia bardziej kompleksowe spostrzeżenia.


Każdy obraz zawiera token szczegółowych informacji, którego można użyć do uzyskania informacji o obrazie. Można na przykład uzyskać kolekcję powiązanych obrazów, stron internetowych, które zawierają obraz, lub listę sprzedawców, w których można kupić produkt pokazany na obrazie.  

Aby uzyskać szczegółowe informacje o obrazie, przechwyć [obrazInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) token w odpowiedzi.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Następnie należy wywołać punkt końcowy szczegółów obrazu i ustawić parametr `imageInsightsToken`zapytania [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) na token w programie .  

Aby określić szczegółowe informacje, które `modules` chcesz uzyskać, ustaw parametr kwerendy. Aby uzyskać wszystkie `modules` szczegółowe `All`informacje, ustaw na . Aby uzyskać tylko statystyki podpisu `modules` `Caption%2CCollection`i kolekcji, ustaw na . Aby uzyskać pełną listę możliwych spostrzeżeń, zobacz [moduły](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Nie wszystkie statystyki są dostępne dla wszystkich obrazów. Odpowiedź zawiera wszystkie żądane szczegółowe informacje, jeśli są dostępne.

W poniższym przykładzie żąda wszystkich dostępnych szczegółowych informacji dla poprzedniego obrazu.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Uzyskiwanie szczegółowych informacji o znanym obrazie

Jeśli masz adres URL do obrazu, który chcesz uzyskać szczegółowe informacje, użyj parametru zapytania [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) zamiast [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametr, aby określić obraz. Lub, jeśli masz plik obrazu, możesz wysłać plik binarny obrazu w treści żądania POST. Jeśli używasz żądania POST, `Content-Type` nagłówek musi `multipart/data-form`być ustawiony na . W obu przypadkach rozmiar obrazu nie może przekraczać 1 MB.  

Jeśli masz adres URL do obrazu, w poniższym przykładzie pokazano, jak zażądać szczegółowych informacji o obrazie.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Uzyskiwanie wszystkich szczegółowych informacji o obrazie  

Aby zażądać wszystkich szczegółowych informacji o obrazie, ustaw parametr zapytania [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na `All`. Aby uzyskać powiązane wyszukiwania, żądanie musi zawierać ciąg zapytania użytkownika. W tym przykładzie pokazano przy użyciu [insightsToken,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) aby określić obraz.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Obiekt najwyższego poziomu jest [obiektem ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) zamiast obiektu [Images.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Rozpoznawanie encji na obrazie  

Funkcja rozpoznawania jednostek identyfikuje jednostki na obrazie, obecnie tylko osoby. Aby zidentyfikować jednostki obrazu, należy ustawić parametr `RecognizedEntities` [kwerendy modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na .  

> [!NOTE]
> Nie można określić tego modułu z żadnym innym modułem. Jeśli określisz ten moduł z innymi modułami, odpowiedź nie zawiera rozpoznawanych jednostek.  

Poniżej przedstawiono sposób określania obrazu przy użyciu parametru [imgUrl.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) Pamiętaj, aby kodować adres URL parametrów kwerendy.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Poniżej przedstawiono odpowiedź na poprzednie żądanie. Ponieważ obraz zawiera dwie osoby, odpowiedź identyfikuje region dla każdej osoby. W tym przypadku, ludzie zostali uznani w CelebrityAnnotations i CelebRecognitionAnnotations grup. Bing wyświetla listę osób w każdej grupie na podstawie prawdopodobieństwa, że pasują do osoby w oryginalnym obrazie. Lista jest w porządku malejącym zaufania. Grupa CelebRecognitionAnnotations zapewnia najwyższy poziom pewności, że dopasowanie jest poprawne.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

Pole `region` identyfikuje obszar obrazu, w którym Bing rozpoznał encję. Dla ludzi region reprezentuje twarz danej osoby.  

Wartości prostokąta są względem szerokości i wysokości oryginalnego obrazu i znajdują się w zakresie od 0,0 do 1,0. Na przykład, jeśli obraz ma wartość 300x200, a górny górny, lewy narożnik regionu znajduje się w punkcie (10, 20), a dolny, prawy róg znajduje się w punkcie (290, 150), wówczas znormalizowanym prostokątem jest:  

-   Po lewej: 10 / 300 = 0,03333...  
-   Góra: 20 / 200 = 0,1  
-   Po prawej: 290 / 300 = 0,9667...  
-   Dół: 150 / 200 = 0,75  

Można użyć regionu, który Bing zwraca w kolejnych wezwań insights. Na przykład, aby uzyskać wizualnie podobne obrazy rozpoznanej jednostki. Aby uzyskać więcej informacji, zobacz Przycinanie obrazów do użycia z modułami wizualnie podobne i rozpoznawania jednostek. Poniżej przedstawiono mapowanie między polami regionu i parametry kwerendy, które służą do kadrowania obrazów.  

-   Lewe mapy do [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   Najlepsze mapy do [kota](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Prawa mapy do [samochodu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   Dolne mapy do [kabiny](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Znajdowanie obrazów podobnych wizualnie  

Aby znaleźć obrazy, które są wizualnie podobne do oryginalnego obrazu, ustaw parametr zapytania [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) similarimages.  

Poniższe żądanie pokazuje, jak uzyskać wizualnie podobne obrazy. Żądanie używa [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametr kwerendy do identyfikowania oryginalnego obrazu. Aby poprawić trafność, należy dołączyć ciąg zapytania użytkownika.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Poniżej przedstawiono odpowiedź na poprzednie żądanie.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Przycinanie obrazów do użycia z modułami rozpoznawania wizualnie podobnych i jednostek  

Aby określić region obrazu używany przez bing do określenia, czy obrazy są wizualnie podobne, czy do rozpoznawania jednostek, należy użyć parametrów [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [cat,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat) [cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)i [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) query. Domyślnie bing używa całego obrazu.  

Parametry określają górny, lewy i prawy róg regionu, którego bing używa do porównania. Określ wartości jako ułamki szerokości i wysokości oryginalnego obrazu. Wartości ułamkowe zaczynają się od (0,0, 0,0) w górnym, lewym rogu i kończą się (1,0, 1,0) w prawym dolnym rogu. Na przykład, aby określić, że górny, lewy narożnik rozpoczyna się o jedną czwartą drogi `cal` w dół od góry `cat` i jedną czwartą drogi od lewej strony, ustawioną na 0,25 i 0,25.  

Następująca sekwencja wywołań pokazuje efekt określania regionu przycinania. Pierwsze wywołanie nie obejmuje przycinania, a bing rozpoznaje dwie osoby stojące obok siebie w środku obrazu.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Odpowiedź pokazuje dwa rozpoznane jednostki.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

Drugie wywołanie przycina obraz pionowo w dół środka, a Bing rozpoznał jedną osobę po prawej stronie obrazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Odpowiedź pokazuje jedną rozpoznaną jednostkę.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Znajdowanie podobnych wizualnie produktów  

Aby znaleźć obrazy, które zawierają produkty, które są wizualnie podobne do produktów znalezionych w oryginalnym obrazie, ustaw parametr zapytania [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na SimilarProducts.  

Poniższe żądanie pokazuje, jak uzyskać obrazy podobnych wizualnie produktów. Żądanie używa [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) query parametr do identyfikowania oryginalnego obrazu, który został zwrócony w poprzednim żądaniu. Aby poprawić trafność, należy dołączyć ciąg zapytania użytkownika.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Poniżej przedstawiono odpowiedź na poprzednie żądanie. Odpowiedź zawiera obraz podobnego produktu i wskazuje, ilu sprzedawców oferuje produkt online, czy istnieją oceny produktów `aggregateOffer` i najniższą znalezioną cenę (patrz pole).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Aby uzyskać listę sprzedawców, którzy oferują produkt w trybie online (zobacz `modules` pole [offerCount),](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) zadzwoń ponownie do interfejsu API i ustaw shoppingsources. Następnie ustaw `insightsToken` parametr kwerendy na token znaleziony w obrazie podsumowania produktu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Poniżej znajduje się odpowiedź na poprzednie żądanie.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
