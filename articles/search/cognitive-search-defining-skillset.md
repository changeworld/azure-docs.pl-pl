---
title: Tworzenie zestawu umiejętności
titleSuffix: Azure Cognitive Search
description: Zdefiniuj wyodrębnianie danych, przetwarzanie języka naturalnego lub kroki analizy obrazu, aby wzbogacić i wyodrębnić informacje strukturalne z danych do użycia w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754569"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Jak utworzyć zestaw umiejętności w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search 

Wzbogacanie sztucznej inteligencji wyodrębnia i wzbogaca dane, aby można było przeszukiwać w usłudze Azure Cognitive Search. Nazywamy ekstrakcji i wzbogacenia kroki *umiejętności poznawczych,* połączone w *skillset* odwołuje się podczas indeksowania. Zestaw umiejętności może używać [wbudowanych umiejętności](cognitive-search-predefined-skills.md) lub umiejętności niestandardowych (zobacz [przykład: Tworzenie umiejętności niestandardowych w potoku wzbogacania si.](cognitive-search-create-custom-skill-example.md)

W tym artykule dowiesz się, jak utworzyć potok wzbogacania umiejętności, których chcesz użyć. Zestaw umiejętności jest dołączony do [indeksatora](search-indexer-overview.md)usługi Azure Cognitive Search. Jedną z części projektu potoku, omówione w tym artykule, jest konstruowanie skillset się. 

> [!NOTE]
> Inną częścią projektu potoku jest określenie indeksatora, omówione w [następnym kroku](#next-step). Definicja indeksatora zawiera odwołanie do zestaw umiejętności, a także mapowania pola używane do łączenia danych wejściowych do wyjść w indeksie docelowym.

Najważniejsze punkty do zapamiętania:

+ Możesz mieć tylko jeden zestaw umiejętności na indeksator.
+ Zestaw umiejętności musi posiadać co najmniej jedną umiejętność.
+ Można utworzyć wiele umiejętności tego samego typu (na przykład warianty umiejętności analizy obrazu).

## <a name="begin-with-the-end-in-mind"></a>Zacznij od końca na uwadze

Zalecanym krokiem początkowym jest podjęcie decyzji, które dane mają zostać wyodrębnione z nieprzetworzonych danych i jak chcesz użyć tych danych w rozwiązaniu wyszukiwania. Tworzenie ilustracji całego potoku wzbogacania może pomóc w określeniu niezbędnych kroków.

Załóżmy, że jesteś zainteresowany przetwarzaniem zestawu komentarzy analityków finansowych. Dla każdego pliku chcesz wyodrębnić nazwy firm i ogólne nastroje komentarzy. Można również napisać niestandardowy wzbogacacz, który używa usługi Bing Entity Search, aby znaleźć dodatkowe informacje o firmie, takie jak rodzaj firmy, w którą firma jest zaangażowana. Zasadniczo chcesz wyodrębnić informacje, takie jak następujące, indeksowane dla każdego dokumentu:

| tekst rekordu | Firm | Sentyment | opisy firm |
|--------|-----|-----|-----|
|rekord próbki| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation to amerykańska międzynarodowa firma technologiczna ..." , "LinkedIn to sieć społecznościowa zorientowana na biznes i zatrudnienie..."]

Na poniższym diagramie przedstawiono hipotetyczny potok wzbogacania:

![Hipotetyczny rurociąg wzbogacania](media/cognitive-search-defining-skillset/sample-skillset.png "Hipotetyczny rurociąg wzbogacania")


Gdy masz uczciwe pojęcie o tym, co chcesz w potoku, można wyrazić zestaw umiejętności, który zawiera następujące kroki. Funkcjonalnie zestaw umiejętności jest wyrażany podczas przekazywania definicji indeksatora do usługi Azure Cognitive Search. Aby dowiedzieć się więcej o przekazywaniu indeksatora, zobacz [dokumentację indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Na diagramie krok *pękania dokumentu* odbywa się automatycznie. Zasadniczo usługa Azure Cognitive Search wie, jak otwierać dobrze znane pliki i tworzy pole *zawartości* zawierające tekst wyodrębniony z każdego dokumentu. Białe pola są wbudowane w wzbogacacze, a kropkowane pole "Bing Entity Search" reprezentuje niestandardowy wzbogacacz, który tworzysz. Jak pokazano, zestaw umiejętności zawiera trzy umiejętności.

## <a name="skillset-definition-in-rest"></a>Definicja skillset w REST

Zestaw umiejętności jest definiowany jako zestaw umiejętności. Każda umiejętność określa źródło swoich danych wejściowych i nazwę produkowanych wyjść. Za pomocą [interfejsu API Tworzenie restużyń funkcji umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset)można zdefiniować zestaw umiejętności odpowiadający poprzedniemu diagramowi: 

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

Podczas tworzenia zestawu umiejętności można podać opis, aby zestaw umiejętności samodokuńszujący. Opis jest opcjonalny, ale przydatny do śledzenia tego, co robi zestaw umiejętności. Ponieważ skillset jest dokumentem JSON, który nie zezwala `description` na komentarze, należy użyć elementu do tego.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Kolejnym elementem zestawu umiejętności jest szereg umiejętności. Każdą umiejętność można myśleć jako o prymitywie wzbogacenia. Każda umiejętność wykonuje małe zadanie w tym potoku wzbogacania. Każdy z nich ma dane wejściowe (lub zestaw wejść) i zwraca niektóre wyjścia. W następnych kilku sekcjach skupić się na tym, jak określić wbudowane i umiejętności niestandardowe, łączenie umiejętności razem za pomocą danych wejściowych i wyjściowych odwołań. Dane wejściowe mogą pochodzić z danych źródłowych lub z innej umiejętności. Dane wyjściowe mogą być mapowane do pola w indeksie wyszukiwania lub używane jako dane wejściowe do umiejętności niższego rzędu.

## <a name="add-built-in-skills"></a>Dodawanie wbudowanych umiejętności

Spójrzmy na pierwszą umiejętność, która jest wbudowaną [umiejętnością rozpoznawania jednostek:](cognitive-search-skill-entity-recognition.md)

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

* Każda wbudowana umiejętność `input`ma `output` `odata.type`, i właściwości. Właściwości specyficzne dla umiejętności dostarczają dodatkowych informacji mających zastosowanie do tej umiejętności. W przypadku `categories` rozpoznawania jednostek jest jedną encją wśród stałego zestawu typów jednostek, które można rozpoznać wstępnie przeszkolony model.

* Każda umiejętność ```"context"```powinna mieć . Kontekst reprezentuje poziom, na którym odbywają się operacje. W powyższej umiejętności kontekst jest cały dokument, co oznacza, że umiejętność rozpoznawania jednostek jest wywoływana raz na dokument. Produkty są również produkowane na tym poziomie. Dokładniej, ```"organizations"``` są generowane jako ```"/document"```członek . W dalszych umiejętności, można odnieść się ```"/document/organizations"```do tych nowo utworzonych informacji jako .  Jeśli ```"context"``` pole nie jest jawnie ustawione, domyślnym kontekstem jest dokument.

* Umiejętność ma jedno wejście o nazwie "tekst", z wejściem źródłowym ustawionym na ```"/document/content"```. Umiejętność (rozpoznawanie jednostek) działa w polu *zawartości* każdego dokumentu, które jest standardowym polem utworzonym przez indeksator obiektów blob platformy Azure. 

* Umiejętność ma jedną ```"organizations"```wydajność o nazwie . Dane wyjściowe istnieją tylko podczas przetwarzania. Aby łańcuch tego wyjścia do niższego wejścia umiejętności, ```"/document/organizations"```odwołaj się do danych wyjściowych jako .

* Dla określonego dokumentu wartość ```"/document/organizations"``` jest tablicą organizacji wyodrębnionych z tekstu. Przykład:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Niektóre sytuacje wymagają odwoływania się do każdego elementu tablicy oddzielnie. Załóżmy na przykład, że ```"/document/organizations"``` chcesz przekazać każdy element oddzielnie do innej umiejętności (na przykład niestandardowego wzbogacającego wyszukiwania jednostek Bing). Można odwoływać się do każdego elementu tablicy, dodając gwiazdkę do ścieżki:```"/document/organizations/*"``` 

Druga umiejętność wyodrębniania tonacji jest zgodna z tym samym wzorcem co pierwszy wzbogacający. Przyjmuje ```"/document/content"``` jako dane wejściowe i zwraca wynik tonacji dla każdego wystąpienia zawartości. Ponieważ pole nie ```"context"``` zostało wyraźnie ustawione, dane wyjściowe (mySentiment) są teraz podrzędne . ```"/document"```

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

## <a name="add-a-custom-skill"></a>Dodawanie umiejętności niestandardowych

Przypomnij sobie strukturę niestandardowego wzbogacacza wyszukiwania jednostek Bing:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
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
```

Ta definicja jest [niestandardową umiejętnością,](cognitive-search-custom-skill-web-api.md) która wywołuje internetowy interfejs API w ramach procesu wzbogacania. Dla każdej organizacji identyfikowanej przez rozpoznawanie jednostek ta umiejętność wywołuje internetowy interfejs API w celu znalezienia opisu tej organizacji. Aranżacja, kiedy wywołać internetowy interfejs API i sposób przepływu otrzymanych informacji jest obsługiwany wewnętrznie przez aparat wzbogacania. Jednak inicjowanie niezbędne do wywoływania tego niestandardowego interfejsu API musi być podana w JSON (takich jak uri, httpHeaders i oczekiwane dane wejściowe). Aby uzyskać wskazówki dotyczące tworzenia niestandardowego interfejsu API sieci web dla potoku wzbogacania, zobacz [Jak zdefiniować interfejs niestandardowy](cognitive-search-custom-skill-interface.md).

Należy zauważyć, że pole "kontekst" jest ustawione ```"/document/organizations/*"``` z gwiazdką, co oznacza, że krok wzbogacania jest wywoływany dla *każdej* organizacji w obszarze ```"/document/organizations"```. 

Dane wyjściowe, w tym przypadku opis firmy, jest generowany dla każdej zidentyfikowanej organizacji. Podczas odwoływania się do opisu w kroku niższego rzędu (na przykład ```"/document/organizations/*/description"``` w wyodrębnianiu frazy klucza), należy użyć ścieżki, aby to zrobić. 

## <a name="add-structure"></a>Dodawanie struktury

Zestaw umiejętności generuje informacje strukturalne z nieustrukturyzowanych danych. Rozważmy następujący przykład:

*"W czwartym kwartale Microsoft zanotował 1,1 miliarda dolarów przychodów z LinkedIn, firmy zajmującej się sieciami społecznościowymi, którą kupił w zeszłym roku. Przejęcie umożliwia firmie Microsoft łączenie możliwości LinkedIn z możliwościami programu CRM i pakietu Office. Akcjonariusze są podekscytowani dotychczasowymi postępami."*

Prawdopodobnym wynikiem byłaby wygenerowana struktura podobna do poniższej ilustracji:

![Struktura wyjściowa próbki](media/cognitive-search-defining-skillset/enriched-doc.png "Struktura wyjściowa próbki")

Do tej pory ta struktura była tylko wewnętrzna, tylko do pamięci i używane tylko w indeksach usługi Azure Cognitive Search. Dodanie magazynu wiedzy umożliwia zapisanie wzbogacenia kształtów do użycia poza wyszukiwaniem.

## <a name="add-a-knowledge-store"></a>Dodawanie magazynu wiedzy

[Magazyn wiedzy](knowledge-store-concept-intro.md) to funkcja w wersji zapoznawczej w usłudze Azure Cognitive Search do zapisywania wzbogaconego dokumentu. Utworzony magazyn wiedzy, wspierany przez konto magazynu platformy Azure, to repozytorium, w którym wykonywały wzbogacone dane. 

Definicja magazynu wiedzy jest dodawana do zestawu umiejętności. Aby zapoznać się z instruktażem całego procesu, zobacz [Tworzenie magazynu wiedzy w rest](knowledge-store-create-rest.md).

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

Wzbogacone dokumenty można zapisać jako tabele z zachowanymi relacjami hierarchicznymi lub jako dokumenty JSON w magazynie obiektów blob. Dane wyjściowe z dowolnej umiejętności w skillset mogą być pozyskiwane jako dane wejściowe dla projekcji. Jeśli chcesz rzutować dane na określony kształt, zaktualizowana [umiejętność shaper](cognitive-search-skill-shaper.md) może teraz modelować złożone typy do użycia. 

<a name="next-step"></a>

## <a name="next-steps"></a>Następne kroki

Teraz, gdy jesteś zaznajomiony z potoku wzbogacania i skillsets, kontynuuj [jak odwoływać się do adnotacji w skillset](cognitive-search-concept-annotations-syntax.md) lub [Jak mapować dane wyjściowe do pól w indeksie](cognitive-search-output-field-mapping.md). 
