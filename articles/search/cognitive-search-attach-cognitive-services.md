---
title: Dołącz zasób usług Cognitive Services za pomocą zestawu umiejętności — usługa Azure Search
description: Instrukcje dotyczące dołączania subskrypcji w jednym usług Cognitive Services do cognitive wzbogacony potok w usłudze Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 09695f764ff71b274e125e90835f5314eb25c980
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683974"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Dołącz zasób usług Cognitive Services za pomocą zestawu umiejętności w usłudze Azure Search 

Dysk algorytmów sztucznej Inteligencji [cognitive potoki indeksowania](cognitive-search-concept-intro.md) użyte do przetwarzania danych bez struktury w usłudze Azure Search. Algorytmy te są oparte na [zasobów usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), w tym [komputerowej](https://azure.microsoft.com/services/cognitive-services/computer-vision/) analizy obrazów i optyczne rozpoznawanie znaków (OCR) i [analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/)rozpoznawanie jednostek, wyodrębnianie kluczowych fraz i innych wzbogacenia.

Możesz wzbogacić ograniczoną liczbę dokumentów za darmo albo w przypadku większych i częstszych obciążeń dołączyć zasób usług Cognitive Services do rozliczenia. W tym artykule, Dowiedz się, jak skojarzyć zasobu usług Cognitive Services za pomocą usługi cognitive zestawu umiejętności, aby wzbogacić dane podczas [indeksowanie usługi Azure Search](search-what-is-an-index.md).

Jeśli Potok składa się z umiejętności niezwiązanych ze sobą na interfejsy API usług Cognitive Services, możesz nadal dołączyć zasobu usług Cognitive Services. Sposób zastąpienia tak **bezpłatna** zasób, który ogranicza na małą ilość wzbogacenia dziennie. Nie ma opłat za umiejętności, które nie są powiązane z interfejsów API usług Cognitive Services. Tych umiejętności, obejmują: [umiejętności niestandardowe](cognitive-search-create-custom-skill-example.md), [funkcja scalająca tekst](cognitive-search-skill-textmerger.md), [rozdzielacz tekstu](cognitive-search-skill-textsplit.md), i [shaper](cognitive-search-skill-shaper.md).

> [!NOTE]
> Ponieważ zakres jest rozwiniesz przez zwiększenie częstotliwości przetwarzania, dodając więcej dokumentów lub dodanie więcej algorytmów sztucznej Inteligencji, należy dołączyć płatnych zasobu usług Cognitive Services. Opłaty są naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etap łamania dokumentów w usłudze Azure Search. Opłaty nie będą naliczane do wyodrębniania tekstu z dokumentów.
>
> Wykonywanie [wbudowanych umiejętności cognitive](cognitive-search-predefined-skills.md) wykonywania będzie naliczana opłata w [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services), w taki sam szybkości tak, jakby zadanie było wykonywane bezpośrednio. Obraz wyodrębniania jest opłata za usługę Azure Search, odzwierciedlone na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="use-free-resources"></a>Bezpłatne zasoby

Opcja przetwarzania ograniczone, bezpłatna umożliwia wykonać ćwiczenia wyszukiwania kognitywnego samouczek i przewodnik Szybki Start. 

**Bezpłatnie (ograniczony wzbogacenia)** są ograniczone do 20 dokumentów każdego dnia na subskrypcję.

1. Otwórz **importowania danych** kreatora.

   ![Polecenie importu danych](media/search-get-started-portal/import-data-cmd2.png "polecenie importu danych")

1. Wybierz źródło danych i w dalszym ciągu **Dodaj wyszukiwanie kognitywne (opcjonalnie)**. Aby uzyskać przewodnik krok po kroku tego kreatora, zobacz [importu, indeksu i odpytywanie za pomocą narzędzia portalu](search-get-started-portal.md).

1. Rozwiń **dołączyć usług Cognitive Services** i wybierz **bezpłatna (ograniczony wzbogacenia)**.

   ![Rozwinięta sekcja dołączyć usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozwinięte dołączyć usług Cognitive Services")

Przejdź do kolejnego etapu **Dodaj wzbogacenia**. Aby uzyskać opis umiejętności dostępne w portalu, zobacz ["krok 2: Dodaj umiejętności cognitive"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) w przewodniku Szybki Start usługa cognitive search.

## <a name="use-billable-resources"></a>Korzystać z płatnych zasobów

W przypadku obciążeń numerowanie wzbogacenia ponad 20 dni należy dołączyć płatnych zasobu usług Cognitive Services. 

Opłaty są naliczane tylko za umiejętności, które wywołują interfejsy API usług Cognitive Services. Oparte na interfejsie API bez umiejętności, takich jak [umiejętności niestandardowe](cognitive-search-create-custom-skill-example.md), [funkcja scalająca tekst](cognitive-search-skill-textmerger.md), [rozdzielacz tekstu](cognitive-search-skill-textsplit.md), i [shaper](cognitive-search-skill-shaper.md) umiejętności nie są rozliczane.

1. Otwórz **importowania danych** kreatora, wybierz źródło danych i w dalszym ciągu **Dodaj wyszukiwanie kognitywne (opcjonalnie)**. 

1. Rozwiń **dołączyć usług Cognitive Services** , a następnie wybierz **Tworzenie nowego zasobu usług Cognitive Services**. Nowa karta zostanie otwarty, w którym można utworzyć zasobu. 

   ![Tworzenie zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "tworzenie zasobu usług Cognitive Services")

1. W lokalizacji wybierz tego samego regionu co usługa Azure Search, aby uniknąć naliczania opłat przepustowość ruchu wychodzącego między regionami.

1. W warstwa cenowa wybierz **S0** uzyskać w jednym zbiór funkcji usług Cognitive Services, takich jak funkcje przetwarzania i język, obsługujące wstępnie zdefiniowane umiejętności używane przez usługę Azure Search. 

   Dla warstwy S0 można znaleźć stawki dla konkretnych obciążeń na [stronę cennika usługi Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + W **wybierz oferują**, upewnij się, że *usług Cognitive Services* jest zaznaczone.
   + W obszarze funkcje językowe, ze stawkami za usługi *Text Analytics standardowa* dotyczą indeksowania sztucznej Inteligencji. 
   + W obszarze funkcje przetwarzania, ze stawkami za usługi *S1 przetwarzania komputera* są stosowane.

1. Kliknij przycisk **Utwórz** zainicjować nowy zasób usług Cognitive Services. 

1. Wróć do poprzedniej karty zawierające **importowania danych** kreatora. Kliknij przycisk **Odśwież** aby pokazać zasobu usług Cognitive Services, a następnie wybierz zasób.

   ![Wybrano zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "wybrane zasobu usług Cognitive Service")

1. Rozwiń **Dodaj wzbogacenia** sekcji, aby wybrać określone umiejętności poznawcze, które chcesz uruchamiać swoje dane i kontynuować pozostałej części przepływu. Aby uzyskać opis umiejętności dostępne w portalu, zobacz ["krok 2: Dodaj umiejętności cognitive"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) w przewodniku Szybki Start usługa cognitive search.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Dołącz posiadane umiejętności do zasobu usług Cognitive Services

Jeśli masz posiadane umiejętności, można dołączyć ją do nowego lub innego zasobu usług Cognitive Services.

1. Na **Omówienie usługi** kliknij **dokładniejsze**.

   ![Karta dokładniejsze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartę potencjałem")

1. Kliknij nazwę zestawu umiejętności, a następnie wybierz istniejący zasób lub Utwórz nową. Kliknij przycisk **OK** aby potwierdzić zmiany. 

   ![Lista zasobów zestawu umiejętności](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "listy zasobów zestawu umiejętności")

Pamiętamy **bezpłatna (ograniczony wzbogacenia)** jest ograniczona do 20 dokumentów codziennie i które **Tworzenie nowego zasobu usług Cognitive Services** jest używany do aprowizowania nowych zasobów płatnych. Jeśli tworzysz nowy zasób, wybierz **Odśwież** Aby odświeżyć listę zasobów usług Cognitive Services, a następnie wybierz zasób.

## <a name="attach-cognitive-services-programmatically"></a>Programowe dołączanie usług Cognitive Services

Podczas programowego definiowania zestawu umiejętności, Dodaj `cognitiveServices` sekcji, aby zestawu umiejętności. W sekcji obejmują klucz zasobu usług Cognitive Services, które chcesz skojarzyć z zestawu umiejętności. Odwołaj zasobu musi należeć do tego samego regionu Azure Search. Również obejmować `@odata.type`i ustaw ją na `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

Ten wzorzec można znaleźć w poniższym przykładzie. Zwróć uwagę, w sekcji cognitiveServices w dolnej części definicji

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Przykład: Szacowanie kosztów

Aby oszacować koszty związane z wyszukiwania kognitywnego indeksowania, rozpoczynać oszacowanie dokument średni jak wygląda tak, aby uruchomić niektóre liczby. Na przykład celach szacunkowych, użytkownik może być określenie w przybliżeniu:

+ 1000 dokumentów PDF
+ Sześć stron
+ Jeden obraz na strony (6000 obrazy)
+ 3000 znaków na stronie

Przyjęto założenie, potok składający się z dokumentu łamania każdego pliku PDF z wyodrębniania tekstowych i obrazów, optyczne rozpoznawanie znaków (OCR) obrazów, a o nazwie rozpoznawania jednostek w organizacji. 

W tym ćwiczeniu używamy najbardziej kosztowne cenę transakcji. Faktyczne koszty może być niższa, ze względu na ceny z rabatem progresywnym. Zobacz [cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. W przypadku łamania z zawartością tekstu i obrazów dokumentów wyodrębnianie tekstu jest aktualnie wolne. 6000 obrazów założono $1 dla każdego 1000 obrazów wyodrębnione, wyceny $6.00 dla tego kroku.

2. Optyczne rozpoznawanie znaków 6000 obrazów w języku angielskim, aby uzyskać umiejętności cognitive optyczne rozpoznawanie znaków używa najlepszego algorytmu (DescribeText). Zakładając, że koszt 2,50 USD za 1000 obrazów do analizy, zapłacisz $15,00 dla tego kroku.

3. Do działania funkcji wydobywania podmiotów trzeba łącznie 3 rekordów tekstowych na każdej stronie. Każdy rekord wynosi 1000 znaków. Trzy rekordów tekstowych na każdej stronie * 6000 strony = 18,000 rekordów tekstowych. $2.00 na 1000 rekordów tekstowych, zakładając, że ten krok będzie kosztować 36.00 $.

Łączenie wszystkiego razem, zapłacisz około 57.00 $ pozyskiwać 1000 dokumentów PDF, tego rodzaju z opisem zestawu umiejętności. 

## <a name="next-steps"></a>Kolejne kroki
+ [Usługa Azure Search, cennik](https://azure.microsoft.com/pricing/details/search/)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
