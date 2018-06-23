---
title: Uzyskiwanie szczegółowych informacji obrazu | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Przedstawia sposób użycia interfejsu API wyszukiwania usługi Bing obrazu, aby uzyskać więcej informacji o obrazie.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: f651d9f773f475e633aed698e134aa6a7c07393b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349012"
---
# <a name="get-insights-about-an-image"></a>Uzyskiwanie szczegółowych informacji o obrazie

> [!IMPORTANT]
> Zamiast za pomocą punktu końcowego szczegóły/obrazów/uzyskiwanie szczegółowych informacji obrazu, należy użyć [wyszukiwania Visual](../bing-visual-search/overview.md) ponieważ zapewnia bardziej szczegółowe informacje na temat technologii.


Obraz zawiera token insights, który można użyć, aby uzyskać informacje o obrazie. Na przykład można uzyskać Kolekcja powiązanych obrazy, strony sieci web, które zawierają obrazu lub listy sprzedawców której można kupić produktu wskazanego w obrazie.  
  
Aby uzyskać szczegółowe informacje o obrazie, przechwytywania obrazu [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) tokenu w odpowiedzi. 

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

Następnie wywołaj punktu końcowego szczegółów obrazu i ustawić [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr do tokenu w zapytania `imageInsightsToken`.  

Aby określić szczegółowych danych, które chcesz pobrać, ustaw `modules` parametr zapytania. Aby uzyskać wszystkie szczegółowe informacje, należy ustawić `modules` do `All`. Aby uzyskać tylko podpis i zbierania szczegółowych danych, ustaw `modules` do `Caption%2CCollection`. Aby uzyskać pełną listę możliwych szczegółowe informacje, zobacz [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested). Nie wszystkie szczegółowe dane będą dostępne dla wszystkich obrazów. Odpowiedź zawiera wszystkie szczegółowe informacje, które są wymagane, jeśli jest dostępna.

Poniższy przykład żądań wszystkie dostępne wgląd w poprzednim obrazu.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-insights-of-a-known-image"></a>Analizuję znanego obrazu

Jeśli adres URL obrazu, który ma być uzyskiwanie szczegółowych informacji o, użyj [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parametr zamiast zapytania [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr, aby określić obrazu. Lub, jeśli plik obrazu, może wysyłać dane binarne obrazu w treści żądania POST. Jeśli używasz żądanie POST `Content-Type` musi mieć wartość nagłówka `multipart/data-form`. Za pomocą obu opcji rozmiar obrazu nie może przekraczać 1 MB.  
  
Jeśli adres URL obrazu, poniższy przykład pokazuje, jak żądania insights obrazu.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 
  
## <a name="getting-all-image-insights"></a>Wszystkie Analizuję obrazu  

Aby poprosić o wszystkich insights obrazu, należy ustawić [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr do zapytania `All`. Aby uzyskać wyszukiwań pokrewnych, żądania musi zawierać ciąg zapytania użytkownika. W tym przykładzie pokazano, za pomocą [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) do określenia obrazu.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Obiekt najwyższego poziomu jest [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) obiekt zamiast [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) obiektu.  
  
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

## <a name="recognizing-entities-in-an-image"></a>Rozpoznawanie jednostek w obrazie  

Funkcja rozpoznawania jednostek identyfikuje jednostki w obrazie obecnie tylko osoby. Aby zidentyfikować jednostek obrazu, należy ustawić [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr do zapytania `RecognizedEntities`.  

> [!NOTE]
> Nie można określać ten moduł z innego modułu. Jeśli określisz ten moduł z innymi modułami odpowiedzi nie zawiera rozpoznanego jednostek.  
  
Poniżej pokazano, jak określać obrazu przy użyciu [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parametru. Pamiętaj, aby adres URL kodowania parametrów zapytania.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  
  
Poniżej przedstawiono odpowiedź na wcześniejsze żądanie. Ponieważ obraz zawiera dwie osoby, odpowiedź identyfikuje region dla każdej osoby. W takim przypadku osoby zostały rozpoznane w grupach CelebrityAnnotations i CelebRecognitionAnnotations. Bing listy osób w każdej grupie oparte na prawdopodobieństwo, że są one zgodne osoby w oryginalnym obrazie. Lista jest w porządku malejącym zaufania. Grupa CelebRecognitionAnnotations zapewnia najwyższy poziom pewności, czy dopasowanie jest poprawna.  
  
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
  
`region` Pole określa obszar obrazu, w którym Bing rozpoznany jednostki. Dla osób region odpowiada danej osoby.  
  
Wartości prostokąta są względem szerokości i wysokości oryginalnego obrazu i są w zakresie od 0,0 do 1,0. Na przykład jeśli obraz jest 300 x 200 i regionu top, lewym rogu jest w momencie (10, 20) i dolnym rogu jest w momencie (290, 150), a następnie znormalizowane prostokąt jest:  
  
-   Po lewej: 10 / 300 = 0.03333...  
-   Z góry: 20 / 200 = 0,1  
-   Prawej: 290 / 300 = 0.9667...  
-   Dół: 150 / 200 = 0,75  
  
Można użyć obszaru Bing zwraca w wgląd w kolejnych wywołaniach. Na przykład, aby pobrać wizualnie podobne obrazy rozpoznanym jednostki. Aby uzyskać więcej informacji, zobacz [przycinanie obrazów do korzystania z jednostki rozpoznawania modułów i wizualne podobne](#croppingimages). Poniżej przedstawiono mapowanie między region pól i parametrów zapytania można skorzystać do przycinanie obrazów.  
  
-   Mapuje lewej [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   Z góry map do [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   Mapuje prawo [samochodu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   Mapowany do dołu [pliku cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Znajdowanie wizualnie podobne obrazy  

Aby znaleźć obrazy wizualnie podobne do oryginalnego obrazu, należy ustawić [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr do SimilarImages zapytania.  
  
Następujące żądania pokazano, jak pobrać wizualnie podobne obrazy. Żądanie używa [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr, aby zidentyfikować oryginalnego obrazu zapytania. Aby zwiększyć przydatności, powinna zawierać ciąg zapytania użytkownika.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

  
Poniżej przedstawiono odpowiedź na wcześniejsze żądanie.  
  
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
  
## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Przycinanie obrazów do użycia z wizualnie podobne i moduły rozpoznawania jednostek  

Aby określić region obrazu, który korzysta z usługi Bing w celu ustalenia, czy obrazy są wizualnie podobne lub przeprowadzić rozpoznawanie jednostek, użyj [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat), [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)i [samochodu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car) parametry zapytania. Domyślnie Bing używa całego obrazu.  
  
Parametry Określ top, lewym rogu i dolnym rogu regionu, który korzysta z usługi Bing do porównania. Określ wartości jako szerokość i wysokość oryginalnego obrazu. Wartości ułamkowych rozpoczynać (0.0, 0.0) u góry, lewym rogu i kończyć się (1.0, 1.0) w prawym dolnym rogu. Na przykład aby określić górnego, lewego rogu rozpoczęcia kwartału sposób w dół od góry i kwartału w sposób, z lewej strony, należy ustawić `cal` do 0,25 i `cat` 0,25.  
  
Następująca sekwencja wywołań przedstawia wynik określenie obszaru przycinania. Pierwsze wywołanie nie obejmuje przycięciu i Bing rozpoznaje dwie osoby stałego obok siebie w środku obrazu.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Odpowiedź zawiera dwie jednostki rozpoznany.  
  
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
  
Drugie wywołanie Przycina obraz w pionie w dół do środka i Bing rozpoznawane jedna osoba po prawej stronie obrazu.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Odpowiedź zawiera jedną jednostkę rozpoznany.  
  
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
  
## <a name="finding-visually-similar-products"></a>Znajdowanie wizualnie podobnych produktów  

Aby odnaleźć obrazów, które zawierają produktów, które wizualnie podobne do produktów znaleziono w oryginalnym obrazie, [modueles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr do SimilarProducts zapytania.  
  
Następujące żądania pokazano, jak pobrać obrazy wizualnie podobnych produktów. Żądanie używa [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) zapytania parametru, aby określić oryginalny obrazu, który został zwrócony z poprzedniego żądania. Aby zwiększyć przydatności, powinna zawierać ciąg zapytania użytkownika.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```
  
Poniżej przedstawiono odpowiedź na wcześniejsze żądanie. Odpowiedź zawiera obraz podobnego produktu i wskazuje, ile sprzedawców oferować online, czy są dostępne oceny produktu, a znaleziono najniższą cenę (zobacz `aggregateOffer` pól).  
  
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
  
Aby uzyskać listę sprzedawców, które oferują produktu w trybie online (zobacz [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer-offercount) pola), ponownie wywołania interfejsu API i ustaw `modules` do ShoppingSources. Następnie ustaw `insightsToken` znaleziono parametru zapytania do tokenu w obrazie podsumowania produktu.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Poniżej znajduje się odpowiedź na wcześniejsze żądanie.  
  
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
