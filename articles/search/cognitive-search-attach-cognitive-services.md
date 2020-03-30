---
title: Dołączanie usług Cognitive Services do zestawu umiejętności
titleSuffix: Azure Cognitive Search
description: Instrukcje dołączania subskrypcji all-in-one usług Cognitive Services do potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472455"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Dołączanie zasobu usług Cognitive Services do zestawu umiejętności w usłudze Azure Cognitive Search 

Podczas konfigurowania potoku wzbogacania w usłudze Azure Cognitive Search można bezpłatnie wzbogacić ograniczoną liczbę dokumentów. W przypadku większych i częstszych obciążeń należy dołączyć zasób usług Cognitive Services podlegania rozliczanemu.

W tym artykule dowiesz się, jak dołączyć zasób, przypisując klucz do zestawu umiejętności, który definiuje potok wzbogacania.

## <a name="resources-used-during-enrichment"></a>Zasoby używane podczas wzbogacania

Usługa Azure Cognitive Search jest zależna od usług Cognitive Services, w tym [przetwarzania obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/) i optycznego rozpoznawania znaków (OCR), [analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) do przetwarzania języka naturalnego i innych wzbogaceń, takich jak [tłumaczenie tekstu.](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) W kontekście wzbogacenia w usłudze Azure Cognitive Search te algorytmy sztucznej inteligencji są zawijane wewnątrz *umiejętności,* umieszczone w *zestawie umiejętności*i odwołuje się *indeksator* podczas indeksowania.

## <a name="how-billing-works"></a>Sposób działania rozliczeń

+ Usługa Azure Cognitive Search używa klucza zasobów usług Cognitive Services, który udostępniasz na zestawie umiejętności, aby rozliczać wzbogacenie obrazu i tekstu. Wykonanie umiejętności rozliczanych jest w [cenie płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/)

+ Wyodrębnianie obrazu jest operacją usługi Azure Cognitive Search, która występuje, gdy dokumenty są pęknięte przed wzbogacenia. Wyodrębnianie obrazu jest rozliczane. Aby uzyskać informacje o cenach wyodrębniania obrazów, zobacz [stronę cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).

+ Wyodrębnianie tekstu odbywa się również podczas pękania frazy dokumentu. To nie jest rozliczane.

+ Umiejętności, które nie wywołują usług Cognitive Services, w tym umiejętności warunkowe, shaper, scalanie tekstu i dzielenie tekstu, nie podlegają rozliczaniu.

## <a name="same-region-requirement"></a>Wymóg dotyczący tego samego regionu

Wymagamy, aby usługi Azure Cognitive Search i Usługi Azure Cognitive Services istniały w tym samym regionie. W przeciwnym razie zostanie wyświetlony ten komunikat w czasie wykonywania:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Nie ma możliwości przenoszenia usługi między regionami. Jeśli pojawi się ten błąd, należy utworzyć nowy zasób usług Cognitive Services w tym samym regionie co usługa Azure Cognitive Search.

> [!NOTE]
> Niektóre wbudowane umiejętności są oparte na nieregionalnych usługach kognitywnych (na przykład [umiejętności tłumaczenia tekstu).](cognitive-search-skill-text-translation.md) Przy użyciu umiejętności nieregionalnych oznacza, że żądanie może być obsługiwane w regionie innym niż region usługi Azure Cognitive Search. Aby uzyskać więcej informacji na temat usług nieregionalnych, zobacz [stronę produktu usług Cognitive Services według regionów.](https://aka.ms/allinoneregioninfo)

## <a name="use-free-resources"></a>Korzystanie z bezpłatnych zasobów

Możesz użyć ograniczonej, bezpłatnej opcji przetwarzania, aby ukończyć samouczek wzbogacania SI i ćwiczenia szybki start.

Bezpłatne (ograniczone wzbogacenia) zasoby są ograniczone do 20 dokumentów dziennie, na indeksator. Można usunąć i ponownie utworzyć indeksator, aby zresetować licznik.

1. Otwórz Kreatora importu danych:

   ![Otwieranie Kreatora importowania danych](media/search-get-started-portal/import-data-cmd.png "Otwieranie Kreatora importowania danych")

1. Wybierz źródło danych i kontynuuj dodawanie **wzbogacania AI (opcjonalnie).** Aby zapoznać się z instrukcją krok po kroku tego kreatora, zobacz [Tworzenie indeksu w witrynie Azure portal](search-get-started-portal.md).

1. Rozwiń **Dołącz usługi Cognitive Services,** a następnie wybierz **bezpłatne (ograniczone wzbogacenia)**:

   ![Rozwinięta sekcja Dołącz usługi Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "Rozwinięta sekcja Dołącz usługi Cognitive Services")

1. Teraz możesz przejść do kolejnych kroków, w tym **dodaj umiejętności poznawcze**.

## <a name="use-billable-resources"></a>Korzystanie z zasobów rozliczanych

W przypadku obciążeń, które tworzą więcej niż 20 wzbogacenia dziennie, upewnij się, że dołączyć do rozliczeń zasobów usług Cognitive Services. Zaleca się, aby zawsze dołączyć rozliczane zasobów usług Cognitive Services, nawet jeśli nigdy nie zamierzasz wywoływać interfejsów API usług Cognitive Services. Dołączanie zasobu zastępuje dzienny limit.

Opłata jest pobierana tylko za umiejętności, które wywołują interfejsy API usług Cognitive Services. Nie są rozliczane za [umiejętności niestandardowe](cognitive-search-create-custom-skill-example.md)lub umiejętności, takie jak [połączenie tekstu,](cognitive-search-skill-textmerger.md) [rozdzielacz tekstu](cognitive-search-skill-textsplit.md)i [shaper](cognitive-search-skill-shaper.md), które nie są oparte na interfejsie API.

1. Otwórz Kreatora importu danych, wybierz źródło danych i kontynuuj dodawanie **wzbogacania SI (opcjonalnie).**

1. Rozwiń **pozycję Dołącz usługi Cognitive Services,** a następnie wybierz pozycję **Utwórz nowy zasób usług Cognitive Services**. Zostanie otwarta nowa karta, aby można było utworzyć zasób:

   ![Tworzenie zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Tworzenie zasobu usług Cognitive Services")

1. Na liście **Lokalizacja** wybierz region, w którym znajduje się usługa Azure Cognitive Search. Upewnij się, że używasz tego regionu ze względu na wydajność. Za pomocą tego regionu również unieważnia wychodzących opłat przepustowości w różnych regionach.

1. Na liście **warstwy cenowej** wybierz **S0,** aby uzyskać kolekcję funkcji usługi Cognitive Services, w tym funkcje wizji i języka, które popierają wbudowane umiejętności dostarczane przez usługę Azure Cognitive Search.

   W przypadku warstwy S0 stawki dla określonych obciążeń można znaleźć na [stronie cennik usług Cognitive Services.](https://azure.microsoft.com/pricing/details/cognitive-services/)
  
   + Na liście **Wybierz ofertę** upewnij się, że wybrano usługi **Cognitive Services.**
   + W obszarze Funkcje **językowe** stawki za **standard analizy tekstu** mają zastosowanie do indeksowania sztucznej inteligencji.
   + W obszarze **Funkcji Wizji** obowiązują stawki za system Przetwarzania **Obrazów S1.**

1. Wybierz **pozycję Utwórz,** aby aprowizować nowy zasób usług Cognitive Services.

1. Powrót do poprzedniej karty zawierającej Kreatora importu danych. Wybierz **pozycję Odśwież,** aby wyświetlić zasób usług Cognitive Services, a następnie wybierz zasób:

   ![Wybieranie zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Wybieranie zasobu usług Cognitive Services")

1. Rozwiń sekcję **Dodaj umiejętności poznawcze,** aby wybrać określone umiejętności poznawcze, które chcesz uruchomić na danych. Ukończ resztę kreatora.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Dołączanie istniejącego zestawu umiejętności do zasobu usług Cognitive Services

Jeśli masz istniejący zestaw umiejętności, możesz dołączyć go do nowego lub innego zasobu usług Cognitive Services.

1. Na stronie **Przegląd usługi** wybierz pozycję **Skillsets**:

   ![Karta Skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Karta Skillsets")

1. Wybierz nazwę zestawu umiejętności, a następnie wybierz istniejący zasób lub utwórz nowy. Wybierz **przycisk OK,** aby potwierdzić zmiany.

   ![Lista zasobów zestawów umiejętności](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista zasobów zestawów umiejętności")

   Należy pamiętać, że **opcja Bezpłatne (ograniczone wzbogacenia)** ogranicza do 20 dokumentów dziennie i że można użyć **utwórz nowy zasób usług Cognitive Services** w celu aprowizowania nowego zasobu podlegających rozliczaniu. Jeśli utworzysz nowy zasób, wybierz **pozycję Odśwież,** aby odświeżyć listę zasobów usług Cognitive Services, a następnie wybierz zasób.

## <a name="attach-cognitive-services-programmatically"></a>Dołączanie usług Cognitive Services programowo

Podczas definiowania umiejętności programowo, dodaj sekcję `cognitiveServices` do zestawu umiejętności. W tej sekcji dołącz klucz zasobu usług Cognitive Services, który chcesz skojarzyć z zestawem umiejętności. Należy pamiętać, że zasób musi znajdować się w tym samym regionie co zasób usługi Azure Cognitive Search. Uwzględnij `@odata.type`również i `#Microsoft.Azure.Search.CognitiveServicesByKey`ustaw go na .

W poniższym przykładzie przedstawiono ten wzorzec. Zwróć `cognitiveServices` uwagę na sekcję na końcu definicji.

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

Aby oszacować koszty związane z indeksowania wyszukiwania poznawczego, należy rozpocząć od pojęcia, jak przeciętny dokument wygląda, dzięki czemu można uruchomić kilka liczb. Na przykład można przybliżyć:

+ 1000 plików PDF.
+ Po sześć stron.
+ Jeden obraz na stronę (6000 obrazów).
+ 3000 znaków na stronę.

Załóżmy, że potok, który składa się z pękanie każdego pliku PDF, wyodrębnianie obrazu i tekstu, optyczne rozpoznawanie znaków (OCR) obrazów i rozpoznawanie jednostek organizacji.

Ceny podane w tym artykule są hipotetyczne. Są one używane do zilustrowania procesu szacowania. Koszty mogą być niższe. Aby zapoznać się z rzeczywistymi cenami transakcji, zobacz [Ceny usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. W przypadku pękania dokumentów z zawartością tekstu i obrazu wyodrębnianie tekstu jest obecnie bezpłatne. W przypadku 6000 obrazów załóżmy, że $1 za każde 1000 wyodrębnionych obrazów. To koszt $6.00 dla tego kroku.

2. W przypadku OCR 6000 obrazów w języku angielskim umiejętności poznawcze OCR używają najlepszego algorytmu (DescribeText). Zakładając koszt $2.50 za 1000 obrazów do analizy, trzeba zapłacić $15.00 za ten krok.

3. W przypadku wyodrębniania encji na stronie mają łącznie trzy rekordy tekstowe. Każdy rekord składa się z 1000 znaków. Trzy rekordy tekstowe na stronę pomnożone przez 6000 stron są równe 18 000 rekordów tekstowych. Zakładając, że $2.00 za 1000 rekordów tekstowych, ten krok będzie kosztować $36.00.

Łącząc to wszystko razem, trzeba by zapłacić około 57,00 dolarów do spożycia 1000 dokumentów PDF tego typu z opisanego skillset.

## <a name="next-steps"></a>Następne kroki
+ [Strona z cennikiem usługi Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapować wzbogacone pola](cognitive-search-output-field-mapping.md)
