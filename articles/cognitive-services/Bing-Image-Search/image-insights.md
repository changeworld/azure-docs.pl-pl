---
title: Uzyskaj szczegółowe informacje o obrazach — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wyszukiwania obrazów Bing, aby uzyskać więcej informacji o pliku obrazu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 8521566087690523359b753b800268e75437a257
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384269"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Uzyskaj szczegółowe informacje o obrazach za pomocą interfejsu API wyszukiwania obrazów Bing

> [!IMPORTANT]
> Zamiast używania / obrazy/szczegóły punktu końcowego, aby uzyskać szczegółowe informacje o obrazach, skorzystaj z [wyszukiwania wizualnego](../bing-visual-search/overview.md) , ponieważ zapewnia bardziej szczegółowe informacje.


Każdy obraz zawiera token szczegółowe informacje, który można użyć, aby uzyskać informacje o obrazie. Na przykład możesz uzyskać zbiór powiązanych obrazów i stron sieci web, które obejmują obraz lub listę temu handlowcy mogą tworzyć pozwalający na zakup produktów na obrazie.  

Aby uzyskać szczegółowe informacje o pliku obrazu, przechwytywania obrazu [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) tokenu w odpowiedzi.

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

Następnie należy wywołać punkt końcowy szczegółowe informacje dotyczące obrazu i ustaw [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametr do tokenu w zapytania `imageInsightsToken`.  

Aby określić, które chcesz, aby uzyskać szczegółowe informacje, należy ustawić `modules` parametr zapytania. Aby uzyskać wszystkie szczegółowe informacje, należy ustawić `modules` do `All`. Aby uzyskać tylko podpis i zbieranie szczegółowych danych, należy ustawić `modules` do `Caption%2CCollection`. Aby uzyskać pełną listę możliwości wglądu w szczegółowe dane, zobacz [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Nie wszystkie szczegółowe informacje są dostępne dla wszystkich obrazów. Odpowiedź zawiera wszystkie szczegółowe informacje, które są wymagane, jeśli jest dostępny.

Poniższy przykład żądań wszystkie dostępne szczegółowe informacje na powyższej ilustracji.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Uzyskiwanie szczegółowych danych znane obrazu

Jeśli masz adres URL obrazu, który chcesz, aby uzyskać szczegółowe informacje dotyczące, użyj [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) parametr zamiast zapytania [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametru do określenia obrazu. Lub, jeśli masz plik obrazu, możesz wysłać plik binarny obrazu w treści żądania POST. Jeśli używasz żądanie POST `Content-Type` nagłówka musi być równa `multipart/data-form`. Niezależnie od wybranej opcji rozmiar obrazu nie może przekraczać 1 MB.  

Jeśli masz adres URL obrazu, poniższy przykład pokazuje, jak do żądania szczegółowe informacje dotyczące obrazu.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Pobieranie wszystkich szczegółowe informacje o obrazach  

Aby zażądać wszystkie szczegółowe informacje dotyczące obrazu, należy ustawić [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) parametr do zapytania `All`. Aby uzyskać powiązane wyszukiwania, żądanie musi zawierać ciąg zapytania użytkownika. W tym przykładzie za pomocą [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) do określenia obrazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Jest obiektem najwyższego poziomu [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) zamiast obiektu [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obiektu.  

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

## <a name="recognizing-entities-in-an-image"></a>Rozpoznawanie jednostek do obrazu  

Funkcja rozpoznawania jednostek identyfikuje jednostki w obrazie obecnie tylko osoby. Aby zidentyfikować jednostek do obrazu, należy ustawić [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) parametr do zapytania `RecognizedEntities`.  

> [!NOTE]
> Ten moduł nie można określić za pomocą innego modułu. Jeśli określisz w tym module z innymi modułami odpowiedzi nie zawiera rozpoznanego jednostek.  

Poniżej pokazano sposób określania obrazu za pomocą [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) parametru. Pamiętaj, aby adres URL zakodować parametry zapytania.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Poniżej przedstawiono odpowiedź na poprzednie żądanie. Ponieważ obraz, który zawiera dwie osoby, odpowiedź identyfikuje region dla każdej osoby. W tym przypadku osoby zostały rozpoznane w grupach CelebrityAnnotations i CelebRecognitionAnnotations. Bing zawiera listę osób w każdej grupie, w oparciu o prawdopodobieństwo, że są one zgodne osoby, oryginalnym obrazie. Lista jest w porządku malejącym zaufania. Grupa CelebRecognitionAnnotations zapewnia najwyższy poziom pewności, czy dopasowanie jest poprawna.  

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

`region` Pole określa obszar obrazu, w którym Bing rozpoznawane jednostki. Dla osób region odpowiada danej osoby.  

Wartości prostokąta są względne wobec szerokość i wysokość oryginalnego obrazu i należą do zakresu od 0,0 do 1,0. Na przykład, jeśli obraz jest 300 x 200 i od góry regionu, lewym rogu jest w momencie (10, 20) i prawym rogu w dolnej części jest w momencie (290, 150), a następnie znormalizowane prostokąt jest:  

-   Po lewej stronie: 10 / 300 = 0.03333...  
-   Do góry:  20 / 200 = 0.1  
-   Po prawej stronie: 290 / 300 = 0.9667...  
-   Dół: 150 / 200 = 0.75  

Możesz użyć regionie, w którym Wyszukiwarka Bing zwróci w szczegółowych informacji w kolejnych wywołaniach. Na przykład, aby pobrać podobnych obrazach rozpoznawanym jednostki. Aby uzyskać więcej informacji zobacz przycinanie obrazów do użycia przy użyciu podobnych wizualnie i jednostki rozpoznawania modułów. Poniżej przedstawiono mapowanie między polami region i parametry zapytania zostanie wykorzystany do kadrować obrazy.  

-   Po lewej stronie mapuje [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   Najważniejsze mapuje do [cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Po prawej stronie mapuje [samochód](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   Mapuje do dołu [pliku cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Wyszukiwanie podobnych obrazach  

Aby znaleźć obrazy, które są wizualnie podobnych do oryginalnego obrazu, ustaw [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) parametr do SimilarImages zapytania.  

Następujące żądanie pokazano, jak uzyskać podobnych obrazach. Żądanie używa [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) zapytania parametru do identyfikacji oryginalnego obrazu. Aby zwiększyć znaczenie, należy dołączyć ciąg zapytania użytkownika.  

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

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Przycinanie obrazów do użycia przy użyciu podobnych wizualnie i moduły rozpoznawania jednostek  

Aby określić region obraz, który używa usługi Bing, aby ustalić, czy wizualnie podobnych obrazów lub wykonać rozpoznawanie jednostek, użyj [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)i [samochodu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) parametry zapytania. Domyślnie usługa Bing używa całego obrazu.  

Parametry Określ górny, lewym rogu i dolny, prawy róg regionie, w którym usługa Bing używa do porównania. Należy określić wartości jako użycie ułamkowych części szerokość i wysokość oryginalnego obrazu. Ułamkowe wartości rozpoczynać (0.0, 0.0) w górnym lewym rogu i kończyć się (1.0, 1.0) w prawym dolnym rogu. Na przykład, aby określić górnego, lewego rogu uruchamia kwartał sposobu, w dół, z góry i kwartału w sposób, z lewej strony, należy ustawić `cal` do 0,25 i `cat` 0,25.  

Poniższa sekwencja wywołań pokazuje wpływ określenie obszaru przycinania. Pierwsze wywołanie nie obejmuje przycinanie i Bing rozpoznaje dwie osoby stałego obok siebie w środku obrazu.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Odpowiedź zawiera dwie jednostki rozpoznane.  

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

Drugie wywołanie Przycina obraz w pionie w dół do środka i Bing rozpoznana jedna osoba po prawej stronie obrazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Odpowiedź zawiera jedną jednostkę rozpoznane.  

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

## <a name="finding-visually-similar-products"></a>Wyszukiwanie podobnych produktów  

Aby znaleźć obrazów zawierających produktów, które wizualnie podobnych produktów w oryginalnego obrazu, ustaw [modułów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) parametr do SimilarProducts zapytania.  

Następujące żądanie pokazano, jak można pobrać obrazów wizualnie podobnych produktów. Żądanie używa [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) zapytania parametru do identyfikacji oryginalnego obrazu, który został zwrócony z poprzedniego żądania. Aby zwiększyć znaczenie, należy dołączyć ciąg zapytania użytkownika.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Poniżej przedstawiono odpowiedź na poprzednie żądanie. Odpowiedź zawiera obraz podobne produktu i wskazuje, ile temu handlowcy mogą tworzyć oferty produktów w trybie online, czy istnieją klasyfikacji produktu i najniższa cena znaleziono (zobacz `aggregateOffer` pola).  

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

Aby uzyskać listę temu handlowcy mogą tworzyć, które oferują produkt w trybie online (zobacz [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) pola), ponownie wywołania interfejsu API i ustawić `modules` do ShoppingSources. Następnie ustaw `insightsToken` parametr zapytania z tokenem znajdującej się obrazie podsumowania produktu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Poniżej przedstawiono odpowiedzi na poprzednie żądanie.  

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
