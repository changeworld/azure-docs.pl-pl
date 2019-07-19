---
title: 'Szybki start: analizowanie zawartości tekstowej w języku Python — Content Moderator'
titlesuffix: Azure Cognitive Services
description: Opis sposobu analizowania zawartości tekstowej pod kątem różnych rodzajów niepożądanego materiału przy użyciu zestawu SDK pakietu Content Moderator dla języka Python
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb0e44f83e2101a7b21e7b7ec6fdc75974c6d6d8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333613"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Szybki start: Analizowanie zawartości tekstowej pod kątem niepożądanego materiału za pomocą języka Python

Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci rozpocząć korzystanie z zestawu SDK pakietu Content Moderator dla języka Python. Dowiesz się, jak przeprowadzać filtrowanie oparte na terminach w zawartości tekstowej oraz jej klasyfikację w celu moderowania potencjalnie niepożądanego materiału.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne
- Klucz subskrypcji pakietu Content Moderator. Aby subskrybować pakiet Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- [Środowisko Python w wersji 2.7 lub nowszej albo 3.5 lub nowszej](https://www.python.org/downloads/)
- Narzędzie [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-content-moderator-sdk"></a>Pobierz zestaw SDK Content Moderator

Zainstaluj zestaw SDK Content Moderator dla języka Python, otwierając wiersz polecenia i uruchamiając następujące polecenie:

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importowanie modułów

Utwórz nowy skrypt Python o nazwie _ContentModeratorQS.py_ i dodaj następujący kod, aby zaimportować wymagane części zestawu SDK. Jest on dostępny w celu ułatwienia odczytywania odpowiedzi JSON.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Inicjowanie zmiennych

Następnie dodaj zmienne dla klucza subskrypcji Content Moderator i adresu URL punktu końcowego. Dodaj nazwę `CONTENT_MODERATOR_SUBSCRIPTION_KEY` do zmiennych środowiskowych z kluczem subskrypcji jako wartość. W przypadku podstawowego adresu URL punktu końcowego `CONTENT_MODERATOR_ENDPOINT` Dodaj do zmiennych środowiskowych adres URL specyficzny dla regionu jako jego wartość, na `https://westus.api.cognitive.microsoft.com`przykład. Klucze subskrypcji bezpłatnej wersji próbnej są generowane w regionie **westus**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

Ciąg wielowierszowego tekstu z pliku zostanie moderowany. Dołącz plik [content_moderator_text_moderation. txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) do lokalnego folderu głównego i Dodaj jego nazwę pliku do zmiennych:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Wykonywanie zapytań względem usługi Moderator

Utwórz wystąpienie interfejsu **ContentModeratorClient** przy użyciu klucza subskrypcji i adresu URL punktu końcowego. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Następnie użyj swojego klienta z wystąpieniem elementu członkowskiego **TextModerationOperations** , aby wywołać interfejs API moderowania za `screen_text`pomocą funkcji. Więcej informacji na temat wywoływania tego interfejsu zawiera dokumentacja metody **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** .

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>Sprawdź wydrukowaną odpowiedź

Uruchom próbkę i Potwierdź odpowiedź. Po pomyślnym zakończeniu zostanie zwrócone wystąpienie **ekranu** . Prawidłowy wynik jest przedstawiony poniżej:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono prostą aplikację języka Python, która zwraca odpowiednie informacje o danym tekście przykładowym dzięki użyciu usługi Content Moderator. Następnie dowiedz się więcej na temat znaczenia różnych flag i klasyfikacji, aby móc podejmować decyzje dotyczące potrzebnych danych oraz sposobu ich obsługi przez aplikację.

> [!div class="nextstepaction"]
> [Przewodnik dotyczący moderowania tekstu](text-moderation-api.md)
