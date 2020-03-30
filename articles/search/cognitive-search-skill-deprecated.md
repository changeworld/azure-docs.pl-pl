---
title: Przestarzałe umiejętności poznawcze
titleSuffix: Azure Cognitive Search
description: Ta strona zawiera listę umiejętności poznawczych, które są uważane za przestarzałe i nie będą obsługiwane w najbliższej przyszłości w narzędziach umiejętności usługi Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792032"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Przestarzałe umiejętności poznawcze w usłudze Azure Cognitive Search

W tym dokumencie opisano umiejętności poznawcze, które są uważane za przestarzałe. Użyj następującego przewodnika dla zawartości:

* Nazwa umiejętności: Nazwa umiejętności, która zostanie przestarzała, @odata.type jest mapowana na atrybut.
* Ostatnia dostępna wersja interfejsu api: ostatnia wersja publicznego interfejsu API usługi Azure Cognitive Search, za pomocą którego można utworzyć/zaktualizować zestawy umiejętności zawierające odpowiednią przestarzałą umiejętność.
* Koniec wsparcia: Ostatni dzień, po którym odpowiednia umiejętność jest uważana za nieobsługiwaną. Wcześniej utworzone zestawy umiejętności powinny nadal działać, ale zaleca się, aby użytkownicy przeprowadzali migrację z przestarzałej umiejętności.
* Zalecenia: Ścieżka migracji do przodu, aby użyć obsługiwanej umiejętności. Zaleca się, aby użytkownicy postępowali zgodnie z zaleceniami, aby nadal otrzymywać pomoc techniczną.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Ostatnia dostępna wersja api

2017-11-11-Preview

### <a name="end-of-support"></a>Koniec wsparcia

15 lutego 2019 r.

### <a name="recommendations"></a>Zalecenia 

Zamiast tego należy użyć [narzędzia Microsoft.Skills.Text.EntityRecognitionSkill.](cognitive-search-skill-entity-recognition.md) Zapewnia większość funkcji NamedEntityRecognitionSkill w wyższej jakości. Ma również bogatsze informacje w złożonych polach wyjściowych.

Aby przeprowadzić migrację do [umiejętności rozpoznawania jednostek,](cognitive-search-skill-entity-recognition.md)należy wykonać jedną lub więcej z następujących zmian w definicji umiejętności. Definicję umiejętności można zaktualizować za pomocą [interfejsu API update skillset](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Obecnie wynik zaufania jako koncepcja nie jest obsługiwana. Parametr `minimumPrecision` istnieje na `EntityRecognitionSkill` do użytku w przyszłości i zgodności z powrotem.

1. *(Wymagane)* Zmień `@odata.type` z `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` `"#Microsoft.Skills.Text.EntityRecognitionSkill"`na .

2. *(Opcjonalnie)* Jeśli korzystasz z `entities` danych wyjściowych, `namedEntities` użyj złożonych `EntityRecognitionSkill` danych wyjściowych kolekcji z zamiast tego. Za pomocą `targetName` definicji umiejętności można zamapować ją `entities`na adnotację o nazwie .

3. *(Opcjonalnie)* Jeśli nie określisz jawnie `categories` `EntityRecognitionSkill` , można zwrócić inny typ kategorii oprócz tych, które były obsługiwane przez `NamedEntityRecognitionSkill`. Jeśli to zachowanie jest niepożądane, upewnij `categories` się, `["Person", "Location", "Organization"]`że parametr jest jawnie ustawiony na .

    _Przykładowe definicje migracji_

    * Prosta migracja

        _(Przed) Definicja umiejętności NamedEntityRecognition_
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
        _(Po) Definicja umiejętności encjiRozpoznania_
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
    
    * Nieco skomplikowana migracja

        _(Przed) Definicja umiejętności NamedEntityRecognition_
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
        _(Po) Definicja umiejętności encjiRozpoznania_
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

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętność rozpoznawania encji](cognitive-search-skill-entity-recognition.md)
