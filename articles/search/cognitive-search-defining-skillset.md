---
title: Utwórz skillset w potoku kognitywnych wyszukiwania (Azure Search) | Dokumentacja firmy Microsoft
description: Zdefiniuj wyodrębniania danych, przetwarzanie, języka naturalnego lub obrazu analizy kroki, aby uzupełnić i wyodrębniania informacji z danych do użycia w usłudze Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: luisca
ms.openlocfilehash: 997b106f748a2f18e8141f77f3b9ff8bb6b9d971
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268234"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Jak utworzyć skillset w potoku wzbogacenia

Wyszukiwanie kognitywnych wyodrębnia i wzbogaca danych umożliwia wyszukiwanie w usłudze Azure Search. Nazywamy ekstrakcji i wzbogacenia *kognitywnych umiejętności*, łączonych w *skillset* istnieją odwołania podczas indeksowania. Skillset można użyć [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) lub niestandardowych umiejętności (zobacz [przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md) Aby uzyskać więcej informacji).

W tym artykule należy Dowiedz się, jak utworzyć potok wzbogacenia umiejętności, którego chcesz użyć. Skillset jest dołączony do usługi Azure Search [indeksatora](search-indexer-overview.md). Konstruuje skillset, się częścią procesu projektowania, omówione w tym artykule. 

> [!NOTE]
> Innej części projektu potoku jest określenie indeksatora, których dotyczy [następnego kroku](#next-step). Definicja indeksatora zawiera odwołanie do skillset, a także mapowania pola służące do połączenia danych wejściowych w celu wyjścia w indeksu docelowego.

Klucz wskazuje Pamiętaj:

+ Może mieć tylko jeden skillset na indeksatora.
+ Skillset musi mieć co najmniej jeden umiejętności.
+ Można utworzyć wiele umiejętności tego samego typu (na przykład warianty umiejętności analizy obraz), ale każdej umiejętności może być użyte tylko raz w tym samym skillset.

## <a name="begin-with-the-end-in-mind"></a>Zaczyna się od zakończenia pamiętać

Zalecanym krokiem początkowej podejmowania decyzji o dane, których można wyodrębnić danych pierwotnych i jak chcesz użyć tych danych w rozwiązaniu do wyszukiwania. Tworzenie ilustrację potoku całego wzbogacenia mogą ułatwić identyfikację niezbędne kroki.

Załóżmy, że planuje się przetwarzanie zestaw komentarze analityków finansowych. Dla każdego pliku które mają zostać wyodrębnione nazwy firmy i wskaźniki nastrojów klientów ogólne komentarze. Można także zapisać niestandardowe enricher, korzystającą z usługi Bing encji wyszukiwania, aby uzyskać dodatkowe informacje o firmie, takie jak jakiego rodzaju firm Firma jest zaangażowana w. Zasadniczo, które mają zostać wyodrębnione informacje, na przykład następujące polecenie, indeksowanym dla każdego dokumentu:

| Rekord tekstu | Firmy | Wskaźniki nastrojów klientów | opisy firmy |
|--------|-----|-----|-----|
|Przykładowy rekord| ["Microsoft", "LinkedIn"] | 0,99. | ["Microsoft Corporation jest American międzynarodowej technologii firmy...", "LinkedIn i zatrudnienia biznesowego społecznościowych sieci..."]

Na poniższym diagramie przedstawiono potoku hipotetyczny wzbogacenia:

![Potok hipotetyczny wzbogacenia](media/cognitive-search-defining-skillset/sample-skillset.png "potoku hipotetyczny wzbogacenia")


Masz raz odpowiedniego informacje o tym, co ma w potoku, można wyrazić skillset, która zapewnia następujące kroki. Funkcjonalnie skillset jest wyrażona, podczas przekazywania definicję indeksatora do usługi Azure Search. Aby uzyskać więcej informacji o przekazywaniu indeksator, zobacz [dokumentacji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Na diagramie *łamania dokumentu* krok odbywa się automatycznie. Zasadniczo usługi Azure Search umożliwia otwieranie plików dobrze znanych i tworzy *zawartości* pola zawierające tekst wyodrębniony z każdego dokumentu. Białe pola są wbudowane enrichers i enricher niestandardowych, które tworzysz reprezentuje kropkowanej pola "Wyszukiwania usługi Bing jednostki". Zgodnie z opisami, skillset zawiera trzy umiejętności.

## <a name="skillset-definition-in-rest"></a>Definicja Skillset w REST

Skillset jest zdefiniowany jako tablicę umiejętności. Każdy umiejętności definiuje źródło wejścia i nazwę utworzone dane wyjściowe. Przy użyciu [tworzenie Skillset interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-skillset), można zdefiniować skillset, która odpowiada do poprzedniego diagramu: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "context": "/document",
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
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Tworzenie zestawu umiejętności

Podczas tworzenia skillset, musisz podać opis skillset pomocniczy. Opis jest opcjonalny, ale są przydatne w celu śledzenia jest skillset. Ponieważ skillset jest dokumentem JSON, która nie zezwala na komentarze, należy użyć `description` tego elementu.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Następny element w skillset jest tablicą umiejętności. Każdej umiejętności można traktować jako podstawowy wzbogacenia. Każdy umiejętności wykonuje małych zadań w tym potoku wzbogacenia. Każda z nich dane wejściowe (lub zestaw danych wejściowych) i zwraca niektóre dane wyjściowe. Następne sekcje kilka skupić się na sposobu określania umiejętności wstępnie zdefiniowanych i niestandardowych łańcucha umiejętności ze sobą za pośrednictwem odwołania wejściowych i wyjściowych. Dane wejściowe mogą pochodzić ze źródła danych lub z innego umiejętności. Dane wyjściowe może być zamapowane do pola w indeksie wyszukiwania lub używana jako dane wejściowe podrzędne umiejętności.

## <a name="add-predefined-skills"></a>Dodaj wstępnie zdefiniowany umiejętności

Przyjrzyjmy się pierwszy umiejętności, który jest wstępnie zdefiniowane [o nazwie umiejętności rozpoznawania jednostki](cognitive-search-skill-named-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Każdy wstępnie zdefiniowanych umiejętności ma `odata.type`, `input`, i `output` właściwości. Właściwości specyficzne dla umiejętności zawierają dodatkowe informacje dotyczące umiejętności. Rozpoznawania jednostek `categories` jest jedną jednostkę między ustalony zbiór typy jednostek, które rozpoznają pretrained modelu.

* Każdy umiejętności powinny mieć ```"context"```. Kontekst reprezentuje poziomu, w którym działania mają miejsce. W powyższym umiejętności kontekst jest całego dokumentu, co oznacza, że umiejętności rozpoznawania nazwanej jednostki jest wywoływana raz na dokument. Dane wyjściowe są również tworzone na tym poziomie. W szczególności ```"organizations"``` są generowane jako element członkowski ```"/document"```. W podrzędnym umiejętności mogą odwoływać się do nowo utworzonej informacji jako ```"/document/organizations"```.  Jeśli ```"context"``` pola nie jest jawnie ustawiona, domyślny kontekst jest dokumentem.

* Umiejętności ma jeden danych wejściowych o nazwie "text", przy użyciu zestawu wejściowego źródła do ```"/document/content"```. Umiejętności (o nazwie rozpoznawanie jednostek) działa na *zawartości* pola każdego dokumentu, który jest standardowego pola utworzone przez indeksator obiektów blob platformy Azure. 

* Umiejętności ma jedno wyjście o nazwie ```"organizations"```. Dane wyjściowe istnieje tylko podczas przetwarzania. To połączenie te dane wyjściowe do wprowadzania podrzędne umiejętności, odwołują się dane wyjściowe jako ```"/document/organizations"```.

* Dla określonego dokumentu, wartość ```"/document/organizations"``` jest tablicą organizacji wyodrębniony z tekstu. Na przykład:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Niektóre sytuacje wymagają oddzielnie odwołujące się do każdego elementu tablicy. Załóżmy na przykład, które chcesz przekazać każdy element ```"/document/organizations"``` oddzielnie do innego umiejętności (np. niestandardowe Bing encji wyszukiwania enricher). Może się odwoływać do każdego elementu tablicy, dodając znak gwiazdki na ścieżkę: ```"/document/organizations/*"``` 

Drugi umiejętności w celu wyodrębnienia wskaźniki nastrojów klientów jest zgodny ze wzorcem samej jako pierwszy enricher. Trwa ```"/document/content"``` jako dane wejściowe i zwraca wynik każdego wystąpienia elementu content wskaźniki nastrojów klientów. Ponieważ nie określono ```"context"``` jawnie pola, dane wyjściowe (mySentiment) teraz jest elementem podrzędnym ```"/document"```.

```json
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
```

## <a name="add-a-custom-skill"></a>Dodawanie niestandardowych kwalifikacji

Odwołaj struktury niestandardowych enricher wyszukiwania jednostki Bing:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      }
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Ta definicja jest niestandardowe umiejętności, która wywołuje interfejs API sieci web w ramach procesu wzbogacenia. Dla każdej organizacji identyfikowana na podstawie rozpoznawania nazwanej jednostki to umiejętności wymaga składnika web API, aby znaleźć opis tej organizacji. Orchestration sytuacji, w których do wywołania interfejsu API sieci web oraz sposób przepływu otrzymanych informacji jest obsługiwany wewnętrznie przez aparat wzbogacenia. Jednak inicjowania niezbędne do wywoływania ten niestandardowy interfejs API należy podać w formacie JSON (na przykład identyfikator uri, httpHeaders i wejść oczekiwano). Aby uzyskać wskazówki dotyczące tworzenia niestandardowego interfejsu API sieci web dla potoku wzbogacenia, zobacz [sposób definiowania niestandardowy interfejs](cognitive-search-custom-skill-interface.md).

Należy zauważyć, że ma ustawioną wartość w polu "context" ```"/document/organizations/*"``` gwiazdką, co oznacza wzbogacenia krok jest nazywany *dla każdego* organizacji w obszarze ```"/document/organizations"```. 

Dane wyjściowe, w tym przypadku opis firmy, jest generowany dla każdej organizacji zidentyfikowane. Podczas odwoływania się z opisem w kroku podrzędnych (na przykład w wyodrębniania klucza frazy), należy użyć ścieżki ```"/document/organizations/*/description"``` Aby to zrobić. 

## <a name="enrichments-create-structure-out-of-unstructured-information"></a>Wzbogacenia utworzyć strukturę poza niestrukturalnych informacji

Skillset generuje informacje o strukturze danych bez struktury. Rozważmy następujący przykład:

*"W jego czwarty kwartał Microsoft rejestrowane $1.1 MLD przychody z LinkedIn, firmy sieci społecznościowych go zakupione w ciągu ostatniego roku. Nabycie umożliwia łączenie możliwości LinkedIn z jego CRM i możliwości pakietu Office przez firmę Microsoft. Akcjonariuszy są radością o postępie do tej pory."*

Prawdopodobnie wynik będzie wygenerowany strukturę podobną do poniższej ilustracji:

![Przykładowe dane wyjściowe struktury](media/cognitive-search-defining-skillset/enriched-doc.png "przykładowe dane wyjściowe — struktura")

Odwołaj się, że ta struktura jest wewnętrzny. Faktycznie nie można pobrać tego wykresu w kodzie.

<a name="next-step"></a>
## <a name="next-steps"></a>Kolejne kroki

Teraz, po zapoznaniu się z potoku wzbogacenia i skillsets, kontynuuj [jak odwołania adnotacje w skillset](cognitive-search-concept-annotations-syntax.md) lub [sposób mapowania danych wyjściowych do pól w indeksie](cognitive-search-output-field-mapping.md). 
