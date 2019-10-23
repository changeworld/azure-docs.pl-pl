---
title: Dołącz zasób Cognitive Services z zestawu umiejętności Azure Search
description: Instrukcje dotyczące dołączania Cognitive Services wszystkich subskrypcji do potoku wzbogacania poznawczego w Azure Search.
manager: nitinme
author: LuisCabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.openlocfilehash: 113286f829b628d4740fbba34e7279741a934aef
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71265929"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Dołącz zasób Cognitive Services za pomocą zestawu umiejętności w Azure Search 

Algorytmy AI dysku [potoki indeksowania poznawcze](cognitive-search-concept-intro.md) używane do wzbogacania dokumentów w Azure Search. Algorytmy te są oparte na zasobach Cognitive Services platformy Azure, w tym [Przetwarzanie obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/) do analizy obrazów i optycznego rozpoznawania znaków (OCR) oraz [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) do rozpoznawania jednostek, wyodrębniania kluczowych fraz i innych wzbogaceń . Jak używane przez Azure Search do wzbogacania dokumentów, algorytmy są opakowane w ramach *umiejętności*, umieszczane w *zestawu umiejętności*i odwołujące się do *indeksatora* podczas indeksowania.

Możesz wzbogacić ograniczoną liczbę dokumentów bezpłatnie. Można też dołączyć zasób Cognitive Services rozliczany do *zestawu umiejętności* w celu uzyskania większych i bardziej częstych obciążeń. W tym artykule dowiesz się, jak dołączyć zasób Cognitive Services rozliczany do wzbogacania dokumentów podczas [indeksowania](search-what-is-an-index.md)Azure Search.

> [!NOTE]
> Zdarzenia obciążane obejmują wywołania interfejsy API usług Cognitive Services i wyodrębniania obrazów w ramach etapu łamania dokumentu w Azure Search. Nie jest naliczana opłata za Wyodrębnianie tekstu z dokumentów lub umiejętności, które nie wywołują Cognitive Services.
>
> Do realizacji umiejętności rozliczanych jest [Cognitive Services cena płatności zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Aby zapoznać się z cennikiem wyodrębniania obrazów, zobacz [stronę z cennikiem Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Wymóg tego samego regionu

Firma Microsoft wymaga, aby Azure Search i Azure Cognitive Services istnieć w tym samym regionie. W przeciwnym razie zostanie wyświetlony komunikat w czasie wykonywania: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Nie ma możliwości przenoszenia usługi między regionami. Jeśli zostanie wyświetlony ten błąd, należy utworzyć nowy zasób Cognitive Services w tym samym regionie co Azure Search.

> [!NOTE]
> Niektóre wbudowane umiejętności są oparte na Cognitive Services nieregionalnych (na przykład [umiejętność tłumaczenia tekstu](cognitive-search-skill-text-translation.md)). Należy pamiętać, że jeśli dodasz dowolne z tych umiejętności do zestawu umiejętności, że dane nie będą zagwarantowane w tym samym regionie co Azure Search lub Cognitive Services zasobów. Aby uzyskać więcej informacji, zobacz [stronę stanu usługi](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Korzystanie z bezpłatnych zasobów

Możesz użyć ograniczonej, bezpłatnej opcji przetwarzania, aby ukończyć samouczek wyszukiwania poznawczego i ćwiczenia szybkiego startu.

Bezpłatny (ograniczone wzbogacanie) zasoby są ograniczone do 20 dokumentów dziennie na subskrypcję.

1. Otwórz Kreatora importowania danych:

   ![Otwórz Kreatora importowania danych](media/search-get-started-portal/import-data-cmd.png "Otwórz Kreatora importowania danych")

1. Wybierz źródło danych i Kontynuuj **Dodawanie wyszukiwania poznawczego (opcjonalnie)** . Aby zapoznać się z przewodnikiem krok po kroku tego kreatora, zobacz [Importowanie, indeksowanie i zapytanie przy użyciu narzędzi portalu](search-get-started-portal.md).

1. Rozwiń węzeł **dołącz Cognitive Services** a następnie wybierz pozycję **wolne (ograniczone wzbogacania)** :

   ![Rozwinięta sekcja Cognitive Services Attach](./media/cognitive-search-attach-cognitive-services/attach1.png "Rozwinięta sekcja Cognitive Services Attach")

1. Przejdź do następnego kroku, **Dodaj wzbogacania**. Aby uzyskać opis umiejętności dostępnych w portalu, zobacz [krok 2. Dodawanie umiejętności poznawczych](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) w przewodniku szybki start do wyszukiwania poznawczego.

## <a name="use-billable-resources"></a>Korzystanie z zasobów rozliczanych

W przypadku obciążeń, które tworzą więcej niż 20 wzbogacań dziennie, pamiętaj, aby dołączyć do rozliczanego zasobu Cognitive Services. Zalecamy, aby zawsze dołączać Cognitive Services do rozliczeń, nawet jeśli nie ma potrzeby wywoływania interfejsy API usług Cognitive Services. Dołączanie zasobu przesłania dzienny limit.

Opłata jest naliczana tylko za umiejętności, które wywołują interfejsy API usług Cognitive Services. Nie są naliczane opłaty za [niestandardowe umiejętności](cognitive-search-create-custom-skill-example.md)ani umiejętności takie jak [łączenie tekstu](cognitive-search-skill-textmerger.md), [rozdzielacz tekstu](cognitive-search-skill-textsplit.md)i [kształtowanie](cognitive-search-skill-shaper.md), które nie są oparte na interfejsie API.

1. Otwórz Kreatora importowania danych, wybierz źródło danych i Kontynuuj **Dodawanie wyszukiwania poznawczego (opcjonalnie)** .

1. Rozwiń węzeł **dołącz Cognitive Services** a następnie wybierz pozycję **utwórz nowy zasób Cognitive Services**. Zostanie otwarta nowa karta, aby można było utworzyć zasób:

   ![Tworzenie zasobu Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Tworzenie zasobu usług Cognitive Services")

1. Z listy **Lokalizacja** wybierz region, w którym znajduje się usługa Azure Search. Upewnij się, że ten region jest używany ze względu na wydajność. Użycie tego regionu również unieważnia opłaty za przepustowość wychodzącą w różnych regionach.

1. Na liście **warstwa cenowa** wybierz pozycję **S0** , aby uzyskać kolekcję "All in-in-one" Cognitive Services funkcje, w tym funkcje wizji i języka, które z powrotem wykorzystali wstępnie zdefiniowane umiejętności używane przez Azure Search.

   W przypadku warstwy S0 można znaleźć stawki dla konkretnych obciążeń na [stronie cennika Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Na liście **Wybierz ofertę** upewnij się, że **Cognitive Services** jest zaznaczone.
   + W obszarze Funkcje **językowe** stawki dla **Analiza tekstu Standard** dotyczą indeksowania AI.
   + W obszarze Funkcje **wizji** obowiązują stawki za **Przetwarzanie obrazów S1** .

1. Wybierz pozycję **Utwórz** , aby udostępnić nowy zasób Cognitive Services.

1. Wróć do poprzedniej karty, która zawiera Kreatora importu danych. Wybierz pozycję **Odśwież** , aby wyświetlić zasób Cognitive Services, a następnie wybierz zasób:

   ![Wybierz zasób Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Wybierz zasób Cognitive Services")

1. Rozwiń sekcję **Dodawanie wzbogaceń** , aby wybrać konkretne umiejętności poznawcze, które mają być uruchamiane na danych. Ukończ resztę kreatora. Aby uzyskać opis umiejętności dostępnych w portalu, zobacz [krok 2. Dodawanie umiejętności poznawczych](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) w przewodniku szybki start do wyszukiwania poznawczego.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Dołącz istniejący zestawu umiejętności do zasobu Cognitive Services

Jeśli masz istniejący zestawu umiejętności, możesz dołączyć go do nowego lub innego zasobu Cognitive Services.

1. Na stronie **Przegląd usługi** wybierz pozycję **umiejętności**:

   ![Karta umiejętności](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Karta umiejętności")

1. Wybierz nazwę zestawu umiejętności, a następnie wybierz istniejący zasób lub Utwórz nowy. Wybierz **przycisk OK** , aby potwierdzić zmiany.

   ![Lista zasobów zestawu umiejętności](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista zasobów zestawu umiejętności")

   Należy pamiętać, że opcja **bezpłatne (ograniczone wzbogacanie)** pozwala na codzienne 20 dokumentów i umożliwia **utworzenie Cognitive Services nowego** zasobu do rozliczania. W przypadku utworzenia nowego zasobu wybierz pozycję **Odśwież** , aby odświeżyć listę zasobów Cognitive Services, a następnie wybierz zasób.

## <a name="attach-cognitive-services-programmatically"></a>Dołącz Cognitive Services programowo

Podczas programistycznego definiowania zestawu umiejętności, Dodaj sekcję `cognitiveServices` do zestawu umiejętności. W tej sekcji należy uwzględnić klucz zasobu Cognitive Services, który ma zostać skojarzony z zestawu umiejętności. Należy pamiętać, że zasób musi znajdować się w tym samym regionie co zasób Azure Search. Należy również uwzględnić `@odata.type` i ustawić ją na `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Poniższy przykład pokazuje ten wzorzec. Zwróć uwagę na sekcję `cognitiveServices` na końcu definicji.

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

## <a name="example-estimate-costs"></a>Przykład: oszacowanie kosztów

Aby oszacować koszty związane z indeksowaniem wyszukiwania poznawczego, Zacznij od pomysłu dotyczącego wyglądu średniej dokumentu, aby można było uruchamiać pewne liczby. Na przykład może to być przybliżone:

+ 1 000 plików PDF.
+ Każda z sześciu stron.
+ Jeden obraz na stronę (obrazy 6 000).
+ 3 000 znaków na stronę.

Założenie potoku, który składa się z łamania dokumentu dla każdego pliku PDF, wyodrębniania obrazów i tekstu, optycznego rozpoznawania znaków (OCR) obrazów oraz rozpoznawania jednostek w organizacji.

Ceny przedstawione w tym artykule są hipotetyczne. Są one używane do zilustrowania procesu szacowania. Twoje koszty mogą być niższe. W przypadku rzeczywistych cen transakcji zobacz temat [Cognitive Services Cennik](https://azure.microsoft.com/pricing/details/cognitive-services).

1. W przypadku dokumentu z zawartością tekstową i obrazem Wyodrębnianie tekstu jest obecnie bezpłatne. W przypadku obrazów 6 000 przyjmuje się, że $1 dla 1 000 każdego obrazu, który został wyodrębniony. Jest to koszt $6,00 dla tego kroku.

2. W przypadku OCR obrazów 6 000 w języku angielskim, umiejętność optycznego rozpoznawania znaków używa najlepszego algorytmu (DescribeText). Przy założeniu, że koszt $2,50 na 1 000 obrazów do przeanalizowania, należy uregulować $15,00 w tym kroku.

3. W przypadku wyodrębniania jednostek masz łączną liczbę trzech rekordów tekstowych na stronę. Każdy rekord ma 1 000 znaków. Trzy rekordy tekstowe na stronę pomnożone przez 6 000 stron równą 18 000 rekordów tekstowych. Przy założeniu, że $2,00 na 1 000 rekordów tekstowych, ten krok będzie kosztować $36,00.

Wszystkie te informacje są umieszczane w artykule o $57,00 do pobierania 1 000 dokumentów PDF tego typu przy użyciu opisanego zestawu umiejętności.

## <a name="next-steps"></a>Następne kroki
+ [Strona cennika Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak zmapować wzbogacone pola](cognitive-search-output-field-mapping.md)
