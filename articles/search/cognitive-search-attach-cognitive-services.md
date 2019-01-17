---
title: Dołącz zasób usług Cognitive Services za pomocą zestawu umiejętności — usługa Azure Search
description: Instrukcje dotyczące dołączania subskrypcji w jednym usług Cognitive Services do cognitive wzbogacony potok w usłudze Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5bffeacaa07f90a11c374061eb6c0d36fc8f86a9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351462"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Dołącz zasób usług Cognitive Services za pomocą zestawu umiejętności w usłudze Azure Search 

Algorytmy sztucznej Inteligencji zapewniają [potoków wyszukiwania kognitywnego](cognitive-search-concept-intro.md) do przetwarzania danych bez struktury są oparte na [ **zasobów usług Cognitive Services**](https://azure.microsoft.com/services/cognitive-services/). Zasoby, takie jak [ **komputerowej** ](https://azure.microsoft.com/services/cognitive-services/computer-vision/) umożliwiają analizowanie obrazów i optyczne rozpoznawanie znaków (OCR) do wyodrębniania tekstu i struktury poza pliki obrazów podczas [ **tekstu Analiza** ](https://azure.microsoft.com/services/cognitive-services/text-analytics/) umożliwia przetwarzanie, takich jak jednostki rozpoznawanie i klucz frazy, kilka języka naturalnego.

Bezpłatnie wzbogacanie ograniczonej liczby dokumentów lub dołączyć płatnych zasobu usług Cognitive Services dla obciążeń typu większych i częściej. W tym artykule, Dowiedz się, jak skojarzyć zasobu usług Cognitive Services za pomocą usługi cognitive zestawu umiejętności, aby wzbogacić dane podczas [indeksowanie usługi Azure Search](search-what-is-an-index.md).

Jeśli Potok składa się wyłącznie z [umiejętności niestandardowe](cognitive-search-create-custom-skill-example.md), nie trzeba dołączać zasobu usług Cognitive Services.

> [!NOTE]
> Od 21 grudnia 2018 r. można skojarzyć zasobu usług Cognitive Services za pomocą usługi Azure Search zestawu umiejętności. Dzięki temu za wykonywanie zestawu umiejętności. W tym dniu również naliczamy opłaty za wyodrębniania obrazu jako część etap łamania dokumentów. Wyodrębnianie tekstu z dokumentów w dalszym ciągu oferowana bez ponoszenia dodatkowych kosztów.
>
> Wykonanie [wbudowanych umiejętności poznawcze](cognitive-search-predefined-skills.md) podlega opłacie [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services), w taki sam szybkości tak, jakby zadanie było wykonywane bezpośrednio. Obraz wyodrębniania jest płatne zdarzenie usługi Azure Search, obecnie oferowana w cenie wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400) lub [sposób działania rozliczeń](search-sku-tier.md#how-billing-works).


## <a name="use-free-resources"></a>Bezpłatne zasoby

Opcja przetwarzania ograniczone, bezpłatna umożliwia wykonać ćwiczenia wyszukiwania kognitywnego samouczek i przewodnik Szybki Start. 

> [!Important]
> Począwszy od 1 lutego 2019 **bezpłatna (ograniczony wzbogacenia)** będzie ograniczona do 20 dokumentów na dzień. 

1. Otwórz **importowania danych** kreatora.

   ![Polecenie importu danych](media/search-get-started-portal/import-data-cmd2.png "polecenie importu danych")

1. Wybierz źródło danych i w dalszym ciągu **Dodaj wyszukiwanie kognitywne (opcjonalnie)**. Aby uzyskać przewodnik krok po kroku tego kreatora, zobacz [importu, indeksu i odpytywanie za pomocą narzędzia portalu](search-get-started-portal.md).

1. Rozwiń **dołączyć usług Cognitive Services** i wybierz **bezpłatna (ograniczony wzbogacenia)**.

   ![Rozwinięta sekcja dołączyć usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozwinięte dołączyć usług Cognitive Services")

Przejdź do kolejnego etapu **Dodaj wzbogacenia**. Aby uzyskać opis umiejętności dostępne w portalu, zobacz ["krok 2: Dodaj umiejętności cognitive"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) w przewodniku Szybki Start usługa cognitive search.

## <a name="use-billable-resources"></a>Korzystać z płatnych zasobów

W przypadku obciążeń numerowanie dokumentów ponad 20 dni należy płatnych zasobu usług Cognitive Services.

1. W **importowania danych** kreatora w **dołączyć usług Cognitive Services**, wybierz istniejący zasób, lub kliknij przycisk **Tworzenie nowego zasobu usług Cognitive Services**.

1. Aby uzyskać **Tworzenie nowego zasobu usług Cognitive Services**, zostanie otwarta nowa karta, dzięki czemu można utworzyć zasobu. Nadaj zasobu unikatową nazwę.

1. Wybierz tę samą lokalizację usługi Azure Search. Obecnie umiejętności poznawcze indeksowania jest obsługiwane w tych regionach:

  * Środkowo-zachodnie stany USA
  * Środkowo-południowe stany USA
  * Wschodnie stany USA
  * Wschodnie stany USA 2
  * Zachodnie stany USA 2
  * Kanada Środkowa
  * Europa Zachodnia
  * Południowe Zjednoczone Królestwo
  * Europa Północna
  * Brazylia Południowa
  * Azja Południowo-Wschodnia
  * Indie Środkowe
  * Australia Wschodnia

1. Wybierz warstwę cenową w jednym **S0**. Ta warstwa zapewnia funkcje przetwarzania i język, obsługujące wstępnie zdefiniowane umiejętności programowania w usłudze wyszukiwania poznawczego.

    ![Utwórz nowy zasób usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Utwórz nowy zasób usług Cognitive Services")

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
