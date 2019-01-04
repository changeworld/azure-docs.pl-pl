---
title: Skojarzenie zasobu usług Cognitive Services za pomocą zestawu umiejętności w usłudze Azure Search
description: Instrukcje dotyczące dołączania subskrypcji w jednym usług Cognitive Services do cognitive zestawu umiejętności w usłudze Azure Search
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 52efa685bba330879365f56e547881d62a52a185
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000159"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Skojarzenie zasobu usług Cognitive Services za pomocą zestawu umiejętności w usłudze Azure Search 

Usługa cognitive search wyodrębnia i uzupełnia danych umożliwiają wyszukiwanie w usłudze Azure Search. Nazywamy ekstrakcji i wzbogacania *umiejętności poznawcze*. Zestaw umiejętności wywoływane podczas indeksowania zawartości są zdefiniowane w *zestawu umiejętności*. Można użyć zestawu umiejętności [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) lub umiejętności niestandardowe. Aby uzyskać więcej informacji, zobacz [przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md).

W tym artykule dowiesz się, jak skojarzyć [usług Azure Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) zasobów za pomocą usługi cognitive zestawu umiejętności.

Zasób usług Cognitive Services, który należy wybrać będzie power wbudowanych umiejętności poznawcze. Ten zasób będzie również na potrzeby rozliczeń. Wszelkie wzbogacenia wykonywanych przy użyciu wbudowanych umiejętności poznawcze będą rozliczane względem zasobu usług Cognitive Services, który wybierzesz. Będą one rozliczane przy użyciu stawki stosowanej tak, jakby były wykonywane tego samego zadania za pomocą zasobu usług Cognitive Services. Zobacz [cennik usługi Cognitive Service](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> Od 21 grudnia 2018 r. można skojarzyć zasobu usług Cognitive Services za pomocą usługi Azure Search zestawu umiejętności. Dzięki temu za wykonywanie zestawu umiejętności. W tym dniu również naliczamy opłaty za wyodrębniania obrazu jako część etap łamania dokumentów. Wyodrębnianie tekstu z dokumentów w dalszym ciągu oferowana bez ponoszenia dodatkowych kosztów.
>
> Wykonanie wbudowanego umiejętności podlega opłacie [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/). Cennik wyodrębniania obrazu jest rozliczana według ceny za wersję zapoznawczą i został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Limity, jeśli wybrano opcję nie zasobu usług Cognitive Services
Począwszy od 1 lutego 2019 r, jeśli nie skojarzona subskrypcja usług Cognitive Services za pomocą Twojego zestawu umiejętności, można wzbogacić tylko niewielka liczba dokumentów dla bezpłatnych (20 dokumenty na dzień). 

## <a name="associate-a-cognitive-services-resource-with-a-new-skillset"></a>Skojarzenie zasobu usług Cognitive Services za pomocą nowego zestawu umiejętności

1. Jako część [importowania danych](search-import-data-portal.md) kreatora, po nawiązaniu połączenia ze źródłem danych, przejdź do **wyszukiwania kognitywnego Dodaj** opcjonalny krok. Jest to drugi krok w kreatorze.

1. Rozwiń **dołączyć usług Cognitive Services** sekcji. W tym kroku przedstawiono wszystkie zasoby usług Cognitive Services, znajdującym się w tych samych regionach, jak usługa Azure Search.

   ![Rozwinięta sekcja dołączyć usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozwinięte dołączyć usług Cognitive Services")

1. Wybierz istniejący zasób usług Cognitive Services lub **Tworzenie nowego zasobu usług Cognitive Services**. Jeśli wybierzesz **bezpłatna (ograniczony wzbogacenia)**, będzie można wzbogacić tylko niewielka liczba dokumentów dla bezpłatnych (20 dokumenty na dzień). Jeśli wybierzesz **Tworzenie nowego zasobu usług Cognitive Services**, spowoduje to otwarcie nowej karty umożliwiającego utworzenie zasobu. 

1. Jeśli został utworzony nowy zasób, wybierz opcję **Odśwież** Aby odświeżyć listę zasobów usług Cognitive Services, a następnie wybierz zasób. 

   ![Wybrano zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "wybrane zasobu usług Cognitive Service")

1. Rozwiń **Dodaj wzbogacenia** sekcji, aby wybrać określone umiejętności poznawcze, które chcesz uruchamiać swoje dane i kontynuować pozostałej części przepływu.

## <a name="associate-a-cognitive-services-resource-with-an-existing-skillset"></a>Skojarzenie zasobu usług Cognitive Services za pomocą posiadane umiejętności

1. Na **Omówienie usługi** wybierz opcję **dokładniejsze** kartę.

   ![Karta dokładniejsze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartę potencjałem")

1. Wybieranie zestawu umiejętności, które chcesz zmodyfikować. Ten krok spowoduje otwarcie bloku, w którym można edytować zestawu umiejętności.

1. Wybierz istniejący zasób usług Cognitive Services lub **Tworzenie nowego zasobu usług Cognitive Services**. Jeśli wybierzesz **bezpłatna (ograniczony wzbogacenia)**, będzie można wzbogacić tylko niewielka liczba dokumentów dla bezpłatnych (20 dokumenty na dzień). Jeśli wybierzesz **Tworzenie nowego zasobu usług Cognitive Services**, spowoduje to otwarcie nowej karty umożliwiającego utworzenie zasobu.

   <n/> 
   <img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Jeśli został utworzony nowy zasób, wybierz opcję **Odśwież** Aby odświeżyć listę zasobów usług Cognitive Services, a następnie wybierz zasób.

1. Wybierz **OK** aby potwierdzić zmiany.

## <a name="associate-a-cognitive-services-resource-programmatically"></a>Programowe Kojarzenie zasobu usług Cognitive Services

Podczas programowego definiowania zestawu umiejętności, Dodaj `cognitiveServices` sekcji. W sekcji obejmują klucz zasobu usług Cognitive Services, które chcesz skojarzyć z zestawu umiejętności. Również obejmować `@odata.type`i ustaw ją na `#Microsoft.Azure.Search.CognitiveServicesByKey`. Ten wzorzec można znaleźć w poniższym przykładzie.

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
        "key": "your key goes here"
    }
}
```
## <a name="example-estimate-the-cost-of-document-cracking-and-enrichment"></a>Przykład: Oszacuj koszt łamania dokumentów i wzbogacanie
Można oszacować, ile to kosztuje wzbogacanie typ dokumentu. Poniższym ćwiczeniu jest tylko przykładem, ale mogą być pomocne dla użytkownika.

Wyobraź sobie, że masz 1000 plików PDF. Oceniasz, że średnio każdego z tych dokumentów ma 6 stron. Każda strona ma 1 obrazu. Istnieją średnio około 3000 znaków na każdej stronie. 

Teraz przyjęto założenie, że chcesz wykonać następujące czynności w ramach wzbogacony potok:
1. W ramach łamania dokumentów Wyodrębnij zawartość i obrazy z dokumentu.
1. W ramach wzbogacania Użyj optyczne rozpoznawanie znaków (OCR) dla każdego z nich wyodrębniony stron, łączenie tekstu dla wszystkich stron, a następnie Wyodrębnij każdej organizacji w połączonym tekście wszystkie obrazy.

Spróbujmy szacowania, ile będzie kosztować obsługę tych 1000 dokumentów, krok po kroku:

1. Dla łamania dokumentów czy Wyodrębnij łączna liczba 6000 obrazów. Zakładając, że $1 dla każdego 1000 obrazów wyodrębnione, który będzie kosztować możesz 6.00 $.

2. Czy Wyodrębnij tekst z każdego z tych obrazów 6000. Umiejętności cognitive optyczne rozpoznawanie znaków w języku angielskim, korzysta z najlepszego algorytmu (DescribeText). Zakładając, że koszt 2,50 USD za 1000 obrazów do analizy, zapłacisz $15,00 dla tego kroku.

3. Do działania funkcji wydobywania podmiotów trzeba łącznie 3 rekordów tekstowych na każdej stronie. (Każdy rekord jest 1000 znaków). Trzy rekordów tekstowych na każdej stronie * 6000 strony = 18,000 rekordów tekstowych. $2.00 na 1000 rekordów tekstowych, zakładając, że ten krok będzie kosztować 36.00 $.

Łączenie wszystkiego razem, zapłacisz 57.00 $ pozyskiwać 1000 dokumentów PDF, tego rodzaju z opisem zestawu umiejętności. W tym ćwiczeniu firma Microsoft zakłada, że najbardziej kosztowne cenę transakcji. Jego mogło być niższe ze względu na tłumaczenie ceny. Zobacz [cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Kolejne kroki
+ [Usługa Azure Search, cennik](https://azure.microsoft.com/pricing/details/search/)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
