---
title: Przestarzałe umiejętności poznawcze — Azure Search
description: Ta strona zawiera listę umiejętności wyszukiwania poznawczego, które są uważane za przestarzałe i nie będzie obsługiwana w najbliższej przyszłości.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: e149edbf89ae68364439876ee59dd0605d33b882
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183430"
---
# <a name="deprecated-cognitive-search-skills"></a>Przestarzałe umiejętności wyszukiwania poznawczego

W tym dokumencie opisano umiejętności poznawcze, które są uważane za przestarzałe. Skorzystaj z poniższej wskazówki dotyczącej zawartości:

* Nazwa umiejętności: Nazwa umiejętności, która będzie przestarzała, jest mapowana na @odata.type atrybut.
* Ostatnia dostępna wersja interfejsu API: Ostatnia wersja publicznego interfejsu API usługi Azure Search, za pomocą którego można tworzyć/aktualizować umiejętności zawierające odpowiednie przestarzałe umiejętności.
* Koniec wsparcia: Ostatni dzień, po którym odpowiednie kwalifikacje są uznawane za nieobsługiwane. Wcześniej utworzona umiejętności powinna nadal działać, ale użytkownicy są zalecani do migracji od przestarzałej umiejętności.
* Mając Ścieżka migracji do przodu w celu korzystania z obsługiwanej umiejętności. Użytkownicy powinni postępować zgodnie z zaleceniami, aby nadal otrzymywać pomoc techniczną.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Ostatnia dostępna wersja interfejsu API

2017-11-11-Preview

### <a name="end-of-support"></a>Koniec wsparcia

15 lutego 2019

### <a name="recommendations"></a>Zalecenia 

Zamiast tego użyj [Microsoft. umiejętności. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . Zapewnia większość funkcji NamedEntityRecognitionSkill o wyższej jakości. Zawiera także bogatsze informacje w złożonych polach danych wyjściowych.

Aby przeprowadzić migrację do [umiejętności rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md), należy wykonać co najmniej jedną z następujących zmian w definicji umiejętności. Definicję umiejętności można zaktualizować za pomocą [interfejsu API aktualizacji zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Obecnie wynik zaufania nie jest obsługiwany. Ten `minimumPrecision` parametr istnieje `EntityRecognitionSkill` do użytku w przyszłości i w celu zapewnienia zgodności z poprzednimi wersjami.

1. *(Wymagane)* Zmień wartość `@odata.type` z `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` na `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Opcjonalnie)* Jeśli używasz `namedEntities` `EntityRecognitionSkill` danych wyjściowych, użyj złożonego danych wyjściowych kolekcji z zamiast tego. `entities` Można użyć `targetName` w definicji umiejętności, aby zamapować ją na adnotację o nazwie `entities`.

3. *(Opcjonalnie)* Jeśli nie określisz `categories`jawnie `EntityRecognitionSkill` , może zwracać różne typy kategorii poza tymi, `NamedEntityRecognitionSkill`które były obsługiwane przez. Jeśli takie zachowanie jest niepożądane, upewnij się, że `categories` parametr został jawnie ustawiony na. `["Person", "Location", "Organization"]`

    _Przykładowe definicje migracji_

    * Prosta migracja

        _Umożliwić Definicja umiejętności NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _Otrzyma Definicja umiejętności EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Nieznacznie skomplikowana migracja

        _Umożliwić Definicja umiejętności NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _Otrzyma Definicja umiejętności EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md)
