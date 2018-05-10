---
title: Utwórz Skillset (REST api-version = 2017-11-11-Preview) — usługa Azure Search | Dokumentacja firmy Microsoft
description: Skillset jest kolekcją kognitywnych umiejętności, wchodzące w skład wzbogacenia potoku.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Utwórz Skillset (interfejs api-version = 2017-11-11-Preview)

**Dotyczy:** api-version-2017-11-11-Preview

Skillset jest kolekcją kognitywnych umiejętności używane do przetwarzania języka naturalnego i innych przekształcenia. Umiejętności obejmują nazwanej jednostki wyodrębniania podziału tekstu na stronach logicznych, między innymi wyodrębniania hasło klucza.

Aby użyć skillset, odwoływać w indeksator usługi Azure Search, a następnie uruchom indeksatora importowania danych, wywołaj przekształcenia i wzbogacenia i mapowanie pól danych wyjściowych do indeksu. Skillset jest zasobów wysokiego poziomu, ale działa tylko w ramach przetwarzania indeksatora. Jako zasób wysokiego poziomu można zaprojektować skillset raz i odwoływanie się wiele indeksatorów. 

Skillset jest wyrażona w usłudze Azure Search za pośrednictwem HTTP PUT lub wysłanie żądania POST. W przypadku PUT treść żądania jest schematu JSON, który określa, które umiejętności są wywoływane. Umiejętności są połączone za pośrednictwem skojarzenia wejścia/wyjścia, gdzie dane wyjściowe jedno przekształcenie staje się dane wejściowe do innego.

Skillset musi mieć co najmniej jeden umiejętności. Nie ma żadnego teoretycznego limitu maksymalnej liczby umiejętności, ale trzech do pięciu jest typowa konfiguracja.  


> [!NOTE]
> Usługa Cognitive Search jest w publicznej wersji zapoznawczej i wykonywanie zestawu umiejętności jest obecnie oferowane bezpłatnie. Cennik dla tej możliwości zostanie opublikowany w późniejszym czasie.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Żądanie  
 HTTPS jest wymagana dla wszystkich żądań obsługi. **Utworzyć Skillset** żądania może być skonstruowany przy użyciu metody PUT, o nazwie skillset jako część adresu URL. Jeśli skillset nie istnieje, jest tworzony. Jeśli już istnieje, jest aktualizowana do nowej definicji. Zwróć uwagę, można tylko umieścić jedną skillset naraz.  

 Nazwa skillset musi spełniać następujące wymagania:

- Można w małe litery
- Zaczynać i kończyć literą lub cyfrą
- Nie ukośniki ani kropek
- Ma mniej niż 128 znaków 

Po uruchomieniu nazwa skillset literą lub cyfrą, pozostałą część nazwy mogą zawierać żadnych litery, liczby i łączniki tak długo, jak nie są następujących po sobie kresek.  

**Wersja interfejsu api** parametr jest wymagany. Jest dostępne tylko w wersji `2017-11-11-Preview`. Zobacz [wersji interfejsu API w usłudze Azure Search](https://go.microsoft.com/fwlink/?linkid=834796) listę wszystkich wersji. 


### <a name="request-headers"></a>Nagłówki żądań  

 W poniższej tabeli opisano nagłówki żądania wymaganych i opcjonalnych.  

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw tę wartość na `application/json`|  
|*klucz interfejsu API:*|Wymagany. `api-key` Służy do uwierzytelniania żądań do usługi wyszukiwania. Jest to wartość ciągu, unikatowe z usługą. **Utworzyć Skillset** żądanie musi zawierać `api-key` ustawione na wartość klucza administratora (w przeciwieństwie do klucza zapytania).|  

Należy również nazwę usługi do konstruowania adresu URL żądania. Można uzyskać nazwy usługi i `api-key` z pulpitu nawigacyjnego usługi w portalu Azure. Zobacz [Tworzenie usługi Azure Search w portalu](search-create-service-portal.md) dla pomocy nawigacji strony.  

### <a name="request-body-syntax"></a>Składnia treść żądania  

Treść żądania zawiera definicję skillset, składające się z jednego lub większej liczby pełni określonych umiejętności, a także nazwę i opis następujące parametry opcjonalne.  

Składnia struktury ładunku żądania ma następującą składnię. Przykładowe żądanie znajduje się w dalszej części tego artykułu oraz w [sposób definiowania skillset](cognitive-search-defining-skillset.md).  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Przykładowe żądanie
 Poniższy przykład tworzy skillset, używany do wzbogacenie kolekcję dokumentów finansowych.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Treść żądania jest dokumentu JSON. Tego konkretnego skillset używa dwóch umiejętności asynchronicznie, niezależnie od siebie przetwarzania treść `contents` jako dwa różne przekształcenia. Alternatywnie można kierować danych wyjściowych jednego transformacji jako dane wejściowe drugiego. Aby uzyskać więcej informacji, zobacz [sposób definiowania skillset](cognitive-search-defining-skillset.md).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>Odpowiedź  

 Na żądanie zakończy się powodzeniem powinien zostać wyświetlony kod stanu "201 utworzono".  

 Domyślnie treść odpowiedzi będzie zawierać JSON definicji skillset, który został utworzony. Jednak jeśli Prefer nagłówek żądania jest ustawiony do zwrócenia = minimalny, treść odpowiedzi będzie pusty, a kod stanu Powodzenie będzie "204 żadnej zawartości" zamiast "201 utworzono". Dotyczy to niezależnie od tego, czy PUT lub POST służy do tworzenia skillset.   

## <a name="see-also"></a>Zobacz także

+ [Omówienie kognitywnych wyszukiwania](cognitive-search-concept-intro.md)
+ [Szybki Start: Spróbuj kognitywnych wyszukiwania](cognitive-search-quickstart-blob.md)
+ [Samouczek: Wzbogacone indeksowanie obiektów blob platformy Azure](cognitive-search-tutorial-blob.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Sposób mapowania pól](cognitive-search-output-field-mapping.md)
+ [Sposób definiowania niestandardowego interfejsu](cognitive-search-custom-skill-interface.md)
+ [Przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md)
+ [Wstępnie zdefiniowane sklls](cognitive-search-predefined-skills.md)