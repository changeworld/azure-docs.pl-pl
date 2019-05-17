---
title: Tworzenie zestawu umiejętności w potoku wyszukiwania kognitywnego — usługa Azure Search
description: Zdefiniuj wyodrębnianie danych, przetwarzanie języka naturalnego, lub kroki analizy obrazu, aby wzbogacanie i wyodrębnianie informacji z danych do użycia w usłudze Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 010880304c5011bd5a767650aa0ae6b6103bdde2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540857"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Tworzenie zestawu umiejętności w wzbogacony potok

Usługa cognitive search wyodrębnia i uzupełnia danych umożliwiają wyszukiwanie w usłudze Azure Search. Nazywamy ekstrakcji i wzbogacania *umiejętności poznawcze*, połączone do *zestawu umiejętności* istnieją odwołania podczas indeksowania. Można użyć zestawu umiejętności [wbudowanych umiejętności](cognitive-search-predefined-skills.md) lub umiejętności niestandardowe (zobacz [przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md) Aby uzyskać więcej informacji).

W tym artykule dowiesz się, jak utworzyć potok wzbogacania umiejętności, których chcesz użyć. Zestawu umiejętności jest dołączony do usługi Azure Search [indeksatora](search-indexer-overview.md). Jedną część procesu projektowania, omówione w tym artykule jest konstruowanie samego zestawu umiejętności. 

> [!NOTE]
> Inną częścią projektu potoku jest określenie indeksator omówione w [następnego kroku](#next-step). Definicja indeksatora zawiera odwołanie do zestawu umiejętności oraz mapowania pola służące do połączenia danych wejściowych do danych wyjściowych w indeksu docelowego.

Najważniejsze kwestie należy pamiętać:

+ Może mieć tylko jednego zestawu umiejętności na indeksatora.
+ Zestawu umiejętności musi mieć co najmniej jeden umiejętności.
+ Możesz utworzyć wiele umiejętności tego samego typu (na przykład warianty umiejętności analizy obrazu).

## <a name="begin-with-the-end-in-mind"></a>Rozpocznij z zakończeniem należy pamiętać

Zalecane etap początkowy wybierają danych do wyodrębniania danych pierwotnych, oraz jak chcesz używać tych danych w rozwiązaniu do wyszukiwania. Tworzenie ilustrację całego wzbogacony potok może pomóc w zidentyfikowaniu niezbędne kroki.

Załóżmy, że jesteś zainteresowany przetwarzanie komentarze analityków finansowych. Dla każdego pliku, które mają zostać wyodrębnione nazwy firmy i ogólne opinie, komentarze. Można również napisać niestandardowy enricher, która korzysta z usługi wyszukiwania jednostek Bing, aby uzyskać dodatkowe informacje dotyczące firmy, takich jak jakiego rodzaju firma jest zaangażowana w działalności biznesowej. Zasadniczo, które mają zostać wyodrębnione informacje podobnie do poniższego indeksowane dla poszczególnych dokumentów:

| Rekord tekstu | Firmy | wskaźniki nastrojów klientów | opisy firmy |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0,99. | ["Microsoft Corporation jest firma American międzynarodowej technologii...", "LinkedIn jest i zatrudnienia biznesowego społecznościowych, sieci..."]

Na poniższym diagramie przedstawiono hipotetyczny wzbogacony potok:

![Hipotetyczny wzbogacony potok](media/cognitive-search-defining-skillset/sample-skillset.png "hipotetyczny wzbogacony potok")


Gdy masz uczciwe informacje o tym, co ma w potoku można wyrazić zestawu umiejętności, która zapewnia następujące kroki. Funkcjonalnie zestawu umiejętności jest wyrażona, gdy Twoja definicja indeksatora możesz przekazać do usługi Azure Search. Aby dowiedzieć się więcej o sposobach przekazywania indeksator, zobacz [dokumentacji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Na diagramie *łamania dokumentów* kroku odbywa się automatycznie. Zasadniczo usługi Azure Search umożliwia otwieranie plików dobrze znanych i tworzy *zawartości* pole zawierające tekstu wyodrębnionego z poszczególnych dokumentów. Białych pól są wbudowane enrichers i kropkowana pole "Wyszukiwania jednostek Bing" reprezentuje enricher niestandardowego, który tworzysz. Zgodnie z opisami, zestawu umiejętności zawiera trzy umiejętności.

## <a name="skillset-definition-in-rest"></a>Definicja zestawu umiejętności w REST

Zestawu umiejętności jest zdefiniowany jako tablica umiejętności. Każdej umiejętności definiuje źródło jego danych wejściowych i nazwę generowane dane wyjściowe. Za pomocą [Tworzenie zestawu umiejętności interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-skillset), można zdefiniować zestawu umiejętności, która odnosi się do poprzedniego diagramu: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
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
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

Podczas tworzenia zestawu umiejętności, możesz podać opis, aby upewnić się, dokumentowanie własnym zestawu umiejętności. Opis jest opcjonalny, ale przydatne do śledzenia działania zestawu umiejętności. Ponieważ zestawu umiejętności jest dokumentem JSON, który nie zezwala na komentarze, należy użyć `description` tego elementu.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Następny fragment w zestawu umiejętności jest tablicą umiejętności. Każdej umiejętności można traktować jako podstawowego wzbogacenia. Każdej umiejętności wykonuje pomniejszego zadania w tym wzbogacony potok. Każdy z nich przyjmuje dane wejściowe (lub zestaw danych wejściowych) i zwraca niektórych danych wyjściowych. Kilka następnych sekcji skoncentrować się na sposób określania umiejętności wbudowanych i niestandardowych Łączenie łańcuchowe umiejętności za pomocą odwołania wejściowych i wyjściowych. Dane wejściowe mogą pochodzić ze źródła danych lub z innego umiejętności. Dane wyjściowe można zamapowane do pola w indeksie wyszukiwania lub używany jako dane wejściowe do podrzędnego umiejętności.

## <a name="add-built-in-skills"></a>Dodawanie wbudowanych umiejętności

Przyjrzyjmy się pierwszy umiejętności, które jest wbudowane [umiejętności rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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
    }
```

* Ma co wbudowana umiejętności `odata.type`, `input`, i `output` właściwości. Właściwości specyficzne dla umiejętności zawierają dodatkowe informacje, które dotyczą umiejętności. Do rozpoznawania jednostek `categories` jest jedną jednostkę wśród stały zestaw typów jednostek, rozpoznających pretrained modelu.

* Powinien mieć każdej umiejętności ```"context"```. Kontekst reprezentuje poziomu, w którym działania mają miejsce. W powyższym umiejętności kontekst jest cały dokument, co oznacza, że umiejętności rozpoznawania jednostek jest wywoływana jeden raz dla dokumentu. Dane wyjściowe są również tworzone na tym samym poziomie. W szczególności ```"organizations"``` są generowane jako członek ```"/document"```. W transmisji umiejętności mogą odwoływać się na tę nowo utworzoną informacji jako ```"/document/organizations"```.  Jeśli ```"context"``` pole nie jest jawnie określona, domyślny kontekst jest dokumentu.

* Umiejętności ma jeden zestaw danych wejściowych o nazwie "text", przy użyciu źródłowy zestaw danych wejściowych do ```"/document/content"```. Umiejętności (rozpoznawanie jednostek) działa na *zawartości* pola każdego dokumentu, czyli pola standardowego utworzone przez indeksatora obiektów blob platformy Azure. 

* Umiejętności ma jedno wyjście o nazwie ```"organizations"```. Dane wyjściowe istnieje tylko w trakcie przetwarzania. Aby połączyć w łańcuch te dane wyjściowe podrzędnego umiejętności w danych wejściowych, odwoływać się do danych wyjściowych w formacie ```"/document/organizations"```.

* Dla określonego dokumentu, wartość ```"/document/organizations"``` jest tablicą organizacje wyodrębnione z tekstu. Na przykład:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Niektóre sytuacje wymagają oddzielnie odwołujące się do każdego elementu w tablicy. Załóżmy, że chcesz przekazać każdy element obiektu ```"/document/organizations"``` oddzielnie do innego umiejętności (np. niestandardowe Bing jednostki wyszukiwania enricher). Mogą odwoływać się do każdego elementu tablicy, dodając znak gwiazdki w ścieżce: ```"/document/organizations/*"``` 

Drugi umiejętności dla tonacji, wyodrębnianie następuje tego samego wzorca jako pierwszy enricher. Trwa ```"/document/content"``` jako dane wejściowe i zwraca wskaźnik nastrojów dla każdego wystąpienia elementu content. Ponieważ nie określono ```"context"``` pola jawnie, dane wyjściowe (mySentiment) teraz jest elementem podrzędnym ```"/document"```.

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

## <a name="add-a-custom-skill"></a>Dodaj umiejętności niestandardowe

Odwołaj strukturze niestandardowych enricher wyszukiwania jednostek Bing:

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

Ta definicja jest [umiejętności niestandardowe](cognitive-search-custom-skill-web-api.md) wywołująca internetowy interfejs API w ramach procesu wzbogacania. Dla każdej organizacji identyfikowane przez rozpoznawanie jednostek to umiejętności wywołuje internetowy interfejs API można znaleźć opis tej organizacji. Organizowanie podczas wywołania interfejsu API sieci web i jak przepływ informacji otrzymanych odbywa się wewnętrznie przez aparat wzbogacania. Jednak inicjowania, które są niezbędne do wywoływania niestandardowego interfejsu API musi być podana w formacie JSON (np. Identyfikator uri, elementów httpHeaders i danych wejściowych, oczekiwano). Aby uzyskać wskazówki dotyczące tworzenia niestandardowego internetowego interfejsu API wzbogacony potok, zobacz [jak zdefiniować niestandardowy interfejs](cognitive-search-custom-skill-interface.md).

Należy zauważyć, że pole "kontekst" jest ustawione na ```"/document/organizations/*"``` z gwiazdką, co oznacza kroku wzbogacania jest wywoływana *dla każdego* organizacji w obszarze ```"/document/organizations"```. 

Dane wyjściowe, w tym przypadku opis firmy, jest generowany dla każdej organizacji zidentyfikowane. Przy odwoływaniu się do opisu w kroku podrzędnego (na przykład w wyodrębnianie kluczowych fraz), należy użyć ścieżki ```"/document/organizations/*/description"``` Aby to zrobić. 

## <a name="add-structure"></a>Dodanie struktury

Zestawu umiejętności generuje informacje ze strukturą poza danych bez struktury. Rozważmy następujący przykład:

*"W jego czwarty kwartał Microsoft rejestrowane 1.1 mld USD przychodu z LinkedIn, firma sieci społecznościowych zakupionych w zeszłym roku. Nabycie umożliwia firmie Microsoft połączyć możliwości usługi LinkedIn z jego CRM i możliwościami pakietu Office. Akcjonariuszy Cieszymy się z postępu do tej pory."*

Prawdopodobnie wynik będzie strukturę wygenerowane, podobnie jak na poniższej ilustracji:

![Przykładowe dane wyjściowe struktury](media/cognitive-search-defining-skillset/enriched-doc.png "przykładowe dane wyjściowe struktury")

Do tej pory ta struktura została wewnętrznych, tylko do pamięci i używane tylko w przypadku indeksów usługi Azure Search. Dodawanie magazynu wiedzy zapewnia sposób oszczędzania ukształtowane wzbogacenia do użytku poza wyszukiwania.

## <a name="add-a-knowledge-store"></a>Dodaj magazyn wiedzy

[Magazyn wiedzy](knowledge-store-concept-intro.md) funkcja w wersji zapoznawczej w usłudze Azure Search do zapisywania wzbogaconego dokumentu. Magazyn wiedzy, który tworzysz, wspierany przez konto usługi Azure storage jest repozytorium, gdzie dane wzbogaconego umieszczać swoje dokumenty. 

Definicja magazynu wiedzy jest dodawany do zestawu umiejętności. Aby uzyskać wskazówki dotyczące całego procesu, zobacz [jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Można zapisać dokumenty wzbogaconego jako tabele relacji hierarchicznych zachowane lub jako dokumenty JSON w usłudze blob storage. Dane wyjściowe z dowolnego umiejętności w zestawu umiejętności mogą źródło jako dane wejściowe na potrzeby projekcji. Jeśli szukasz do projektu danych do określonego kształtu, zaktualizowany interfejs [umiejętności shaper](cognitive-search-skill-shaper.md) teraz umożliwia modelowanie złożonych typów do użycia. 

<a name="next-step"></a>

## <a name="next-steps"></a>Kolejne kroki

Teraz, po zapoznaniu się z potoku wzbogacenia i wykorzystując bieżące, kontynuuj [sposobu odwołania adnotacje w zestawu umiejętności](cognitive-search-concept-annotations-syntax.md) lub [sposób mapowania danych wyjściowych do pól w indeksie](cognitive-search-output-field-mapping.md). 
