---
title: Dołącz zasób usług Cognitive Services za pomocą zestawu umiejętności — usługa Azure Search
description: Instrukcje dotyczące dołączania subskrypcji w jednym usług Cognitive Services do cognitive wzbogacony potok w usłudze Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 44f16b3334b991e071fa85ca4cffbc0837f0a6ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244428"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Dołącz zasób usług Cognitive Services za pomocą zestawu umiejętności w usłudze Azure Search 

Dysk algorytmów sztucznej Inteligencji [cognitive potoki indeksowania](cognitive-search-concept-intro.md) wykorzystywanych do wzbogacenia dokumentu w usłudze Azure Search. Algorytmy te są oparte na zasoby usług Azure Cognitive Services, w tym [komputerowej](https://azure.microsoft.com/services/cognitive-services/computer-vision/) analizy obrazów i optyczne rozpoznawanie znaków (OCR) i [analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) rozpoznawania jednostek Wyodrębnianie kluczowych fraz, a inne wzbogacenia. Ponieważ używane przez usługę Azure Search na potrzeby wzbogacania dokumentu, algorytmy są opakowane w *umiejętności*, umieszczone w *zestawu umiejętności*i jest przywoływana przez *indeksatora* podczas indeksowanie.

Ograniczona liczba dokumentów można wzbogacić za darmo. Lub możesz dołączyć płatnych zasobu usług Cognitive Services, aby *zestawu umiejętności* dla obciążeń typu większych i częściej. W tym artykule dowiesz się, jak dołączyć płatnych zasobu usług Cognitive Services w celu wzbogacenia dokumentów w usłudze Azure Search [indeksowania](search-what-is-an-index.md).

> [!NOTE]
> Płatne zdarzenia obejmują wywołania do wyodrębniania interfejsy API usług Cognitive Services i obraz w ramach etapu łamania dokumentów w usłudze Azure Search. Nie ma opłat do wyodrębniania tekstu z dokumentów i umiejętności, które nie wywołują usług Cognitive Services.
>
> Wykonanie płatnych umiejętności wynosi [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/). Obraz wyodrębniania o cenach, zobacz [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Wymaganie tego samego regionu

Firma Microsoft wymaga, że usługa Azure Search i Azure Cognitive Services istnieje w tym samym regionie. W przeciwnym razie zostanie wyświetlony ten komunikat w czasie wykonywania: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Nie ma możliwości przenoszenia usługi między regionami. Jeśli ten błąd, należy utworzyć nowy zasób usług Cognitive Services w tym samym regionie co usługa Azure Search.

## <a name="use-free-resources"></a>Bezpłatne zasoby

Opcja przetwarzania ograniczone, bezpłatna umożliwia wykonać ćwiczenia wyszukiwania kognitywnego samouczek i przewodnik Szybki Start.

Bezpłatna (ograniczony wzbogacenia) zasoby są ograniczone do 20 dokumentów każdego dnia na subskrypcję.

1. Otwórz Kreatora importu danych:

   ![Otwórz Kreatora importu danych](media/search-get-started-portal/import-data-cmd2.png "Otwórz Kreatora importu danych")

1. Wybierz źródło danych i w dalszym ciągu **Dodaj wyszukiwanie kognitywne (opcjonalnie)** . Aby uzyskać przewodnik krok po kroku tego kreatora, zobacz [Import, indeksu i wykonywania zapytań przy użyciu narzędzia portalu](search-get-started-portal.md).

1. Rozwiń **dołączyć usług Cognitive Services** , a następnie wybierz **bezpłatna (ograniczony wzbogacenia)** :

   ![Rozwinięta sekcja dołączyć usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "sekcji rozwinięte dołączyć usług Cognitive Services")

1. Przejdź do kolejnego etapu **Dodaj wzbogacenia**. Aby uzyskać opis umiejętności dostępne w portalu, zobacz [krok 2: Dodaj umiejętności poznawcze](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) w przewodniku Szybki Start usługa cognitive search.

## <a name="use-billable-resources"></a>Korzystać z płatnych zasobów

Dla obciążeń, które utworzyć więcej niż 20 wzbogacenia dziennie upewnij się dołączyć płatnych zasobu usług Cognitive Services. Firma Microsoft zaleca, zawsze dołączać płatnych zasobu usług Cognitive Services, nawet wtedy, gdy zamierzasz nigdy wywoływać interfejsy API usług Cognitive Services. Dołączenie zasobu zastępuje dziennego limitu.

Opłaty są naliczane tylko w przypadku umiejętności, które wywołują interfejsy API usług Cognitive Services. Nie jest wystawiany na [umiejętności niestandardowe](cognitive-search-create-custom-skill-example.md), lub umiejętności, takich jak [funkcja scalająca tekst](cognitive-search-skill-textmerger.md), [rozdzielacz tekstu](cognitive-search-skill-textsplit.md), i [shaper](cognitive-search-skill-shaper.md), które nie są oparte na interfejsie API.

1. Otwórz Kreatora importu danych, wybierz źródło danych i w dalszym ciągu **Dodaj wyszukiwanie kognitywne (opcjonalnie)** .

1. Rozwiń **dołączyć usług Cognitive Services** , a następnie wybierz **Tworzenie nowego zasobu usług Cognitive Services**. Nowa karta zostanie otwarty, w którym można utworzyć zasobu:

   ![Tworzenie zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "tworzenie zasobu usług Cognitive Services")

1. W **lokalizacji** listy, wybierz region, w którym znajduje się usługi Azure Search. Upewnij się, że użyty następujący region ze względu na wydajność. Również przy użyciu tego regionu unieważnia opłatami za przepustowość ruchu wychodzącego między regionami.

1. W **warstwa cenowa** listy wybierz **S0** uzyskać w jednym zbiór funkcji usług Cognitive Services, takich jak funkcje przetwarzania i język, obsługujące wstępnie zdefiniowane umiejętności używane przez usługę Azure Search.

   Dla warstwy S0 można znaleźć stawki dla konkretnych obciążeń na [stronę cennika usługi Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + W **wybierz oferują** liście, upewnij się, że **usług Cognitive Services** jest zaznaczone.
   + W obszarze **języka** funkcji ze stawkami za usługi **Text Analytics standardowa** dotyczą indeksowania sztucznej Inteligencji.
   + W obszarze **wizji** funkcji ze stawkami za usługi **S1 przetwarzania komputera** zastosowania.

1. Wybierz **Utwórz** zainicjować nowy zasób usług Cognitive Services.

1. Wróć do poprzedniej karty, który zawiera Kreatora importu danych. Wybierz **Odśwież** aby pokazać zasobu usług Cognitive Services, a następnie wybierz zasób:

   ![Wybierz zasób usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "wybierz zasób usług Cognitive Services")

1. Rozwiń **Dodaj wzbogacenia** sekcji, aby wybrać określone umiejętności poznawcze, które chcesz uruchomić na podstawie posiadanych danych. Wykonaj pozostałe kreatora. Aby uzyskać opis umiejętności dostępne w portalu, zobacz [krok 2: Dodaj umiejętności poznawcze](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) w przewodniku Szybki Start usługa cognitive search.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Dołącz posiadane umiejętności do zasobu usług Cognitive Services

Jeśli masz posiadane umiejętności, można dołączyć ją do nowego lub innego zasobu usług Cognitive Services.

1. Na **Omówienie usługi** wybierz opcję **dokładniejsze**:

   ![Karta dokładniejsze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartę potencjałem")

1. Wybierz nazwę zestawu umiejętności, a następnie wybierz istniejący zasób lub Utwórz nową. Wybierz **OK** aby potwierdzić zmiany.

   ![Lista zasobów zestawu umiejętności](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "listy zasobów zestawu umiejętności")

   Należy pamiętać, że **bezpłatna (ograniczony wzbogacenia)** opcji ogranicza możliwość dokumentów 20 dni, a można użyć **Tworzenie nowego zasobu usług Cognitive Services** do aprowizowania nowych zasobów płatnych. Jeśli tworzysz nowy zasób, wybierz **Odśwież** Aby odświeżyć listę zasobów usług Cognitive Services, a następnie wybierz zasób.

## <a name="attach-cognitive-services-programmatically"></a>Programowe dołączanie usług Cognitive Services

Podczas programowego definiowania zestawu umiejętności, Dodaj `cognitiveServices` sekcji, aby zestawu umiejętności. W tej sekcji obejmują klucz zasobu usług Cognitive Services, które chcesz skojarzyć z zestawu umiejętności. Należy pamiętać, że zasób musi być w tym samym regionie, co zasób usługi Azure Search. Również obejmować `@odata.type`i ustaw ją na `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Ten wzorzec można znaleźć w poniższym przykładzie. Zwróć uwagę `cognitiveServices` sekcji na końcu definicji.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

Aby oszacować koszty związane z wyszukiwania kognitywnego indeksowania, rozpoczynać oszacowanie dokument średni jak wygląda aby można było uruchomić niektóre liczby. Na przykład może być szacunkowy:

+ 1000 plików PDF.
+ Sześć każdej strony.
+ Jeden obraz na strony (6000 obrazy).
+ 3000 znaków na każdej stronie.

Przyjęto założenie, potok, który składa się z łamania dokumentów każdego wyodrębniania plików PDF, obrazów i tekstu, optyczne rozpoznawanie znaków (OCR), obrazów i rozpoznawania jednostek w organizacji.

Ceny przedstawione w tym artykule są hipotetycznych. Służą one do ilustrują proces szacowania. Z tego względu koszt może być niższa. Aby uzyskać rzeczywiste ceny transakcji, zobacz [cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. W przypadku łamania z zawartością tekstu i obrazów dokumentów wyodrębnianie tekstu jest aktualnie wolne. 6000 obrazów założono $1 dla każdego 1000 obrazów wyodrębnione. To koszt $6.00 dla tego kroku.

2. Optyczne rozpoznawanie znaków 6000 obrazów w języku angielskim, aby uzyskać umiejętności cognitive optyczne rozpoznawanie znaków używa najlepszego algorytmu (DescribeText). Zakładając, że koszt 2,50 USD za 1000 obrazów do analizy, zapłacisz $15,00 dla tego kroku.

3. Do działania funkcji wydobywania podmiotów trzeba łącznie trzy rekordów tekstowych na każdej stronie. Każdy rekord wynosi 1000 znaków. Trzy rekordów tekstowych na każdej stronie pomnożona przez 6000 strony jest równa 18,000 rekordów tekstowych. $2.00 na 1000 rekordów tekstowych, zakładając, że ten krok będzie kosztować 36.00 $.

Łączenie wszystkiego razem, zapłacisz około 57.00 $ pozyskiwać 1000 dokumentów PDF tego typu z opisem zestawu umiejętności.

## <a name="next-steps"></a>Kolejne kroki
+ [Usługa Azure Search, cennik](https://azure.microsoft.com/pricing/details/search/)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
