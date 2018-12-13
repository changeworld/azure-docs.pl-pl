---
title: Dołączanie usługi Cognitive Services do zestawu umiejętności — usługa Azure Search
description: Instrukcje dotyczące dołączania subskrypcji w jednym usług Cognitive Services do Cognitive zestawu umiejętności
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317240"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Skojarzenie zasobu usług Cognitive Services za pomocą zestawu umiejętności 

> [!NOTE]
> Od 21 grudnia 2018 r. można skojarzyć zasobu usług Cognitive Services za pomocą usługi Azure Search zestawu umiejętności. Pozwoli to nam będą naliczane opłaty za wykonywanie zestawu umiejętności. W tym dniu również Zaczniemy naliczać opłaty do wyodrębnienia obrazu jako część etap łamania dokumentów. Wyodrębnianie tekstu z dokumentów nadal będzie oferowane bez dodatkowych opłat.
>
> Wykonanie wbudowanego umiejętności będzie powodować obciążenie opłatami istniejące [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/) . Cennik wyodrębniania obrazu będzie powodować obciążenie opłatami ceny za wersję zapoznawczą i został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).


Usługa cognitive search wyodrębnia i uzupełnia danych umożliwiają wyszukiwanie w usłudze Azure Search. Nazywamy ekstrakcji i wzbogacania *umiejętności poznawcze*. Zestaw umiejętności wywoływane podczas indeksowania zawartości są zdefiniowane w *zestawu umiejętności*. Można użyć zestawu umiejętności [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) lub umiejętności niestandardowe (zobacz [przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md) Aby uzyskać więcej informacji).

W tym artykule dowiesz się, jak skojarzyć [usług Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) zasób usługi cognitive zestawu umiejętności.

Zasobu usług Cognitive Services, którą wybierzesz będzie obsługiwać wbudowanych umiejętności poznawcze. Ten zasób będzie również na potrzeby rozliczeń. Wszelkie wzbogacenia wykonywanych przy użyciu wbudowanych umiejętności kognitywnych będą rozliczane względem zasobu usług Cognitive Services, którą wybierzesz. Będą one rozliczane przy użyciu stawki stosowanej tak, jakby były wykonywane tego samego zadania za pomocą zasobu usług Cognitive Services. Zobacz [cennik usługi Cognitive Service](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Limity, jeśli wybrano opcję nie zasobu usług Cognitive Services
Począwszy od 1 lutego 2019 r, jeśli nie skojarzysz subskrypcji usług Cognitive Services za pomocą Twojego zestawu umiejętności, tylko można wzbogacić niewielka liczba dokumentów dla bezpłatnych (20 dokumenty na dzień). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Skojarzenie zasobu usług Cognitive Services za pomocą nowego zestawu umiejętności

1. Jako część *importowania danych* występował, po nawiązaniu połączenia ze źródłem danych, które spowoduje przejście do *wyszukiwania kognitywnego Dodaj* opcjonalny krok. 

1. Rozwiń *dołączyć usług Cognitive Services* sekcji. Spowoduje to wyświetlenie wszystkie zasoby usługi cognitive Services, znajdującym się w tych samych regionach jako usługi wyszukiwania. 
![Rozwinięte dołączanie usługi cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozwinięte dołączyć usług Cognitive Services")

1. Wybierz istniejący zasób usług Cognitive Services, lub *Utwórz nowy zasób usług Cognitive Services*. Jeśli wybierzesz *zasobów bezpłatna (ograniczony wzbogacenia)*, tylko wtedy będzie można wzbogacić niewielka liczba dokumentów dla bezpłatnych (20 dokumenty na dzień). Jeśli kliknięto przycisk na *Utwórz nowy zasób usług Cognitive Services*, Nowa karta zostanie otwarty, która pozwoli na tworzenie zasobu usług Cognitive Services. 

1. Jeśli został utworzony nowy zasób, kliknij przycisk *Odśwież* Aby odświeżyć listę zasobów usług Cognitive Services, a następnie wybierz zasób. 
![Wybrany zasób usługi Cognitive Service](./media/cognitive-search-attach-cognitive-services/attach2.png "wybrany zasób usługi cognitive Services")

1. Gdy to zrobisz, możesz rozwinąć *Dodaj wzbogacenia* sekcji, aby wybrać określone umiejętności poznawcze, o których chcesz uruchamiać swoje dane i kontynuować pozostałej części przepływu.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Skojarzenie zasobu usług Cognitive Services za pomocą posiadane umiejętności

1. Na stronie Przegląd usługi wybierz *dokładniejsze* kartę. ![Karta dokładniejsze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartę potencjałem")

1. *Kliknij przycisk* na zestawu umiejętności, które chcesz zmodyfikować. Spowoduje to otwarcie bloku, która umożliwi Ci do edycji zestawu umiejętności.

1. Wybierz istniejący zasób usług Cognitive Services lub *Utwórz nowy zasób usług Cognitive Services*. Jeśli wybierzesz *zasobów bezpłatna (ograniczony wzbogacenia)*, tylko wtedy będzie można wzbogacić niewielka liczba dokumentów dla bezpłatnych (20 dokumenty na dzień). Jeśli kliknięto przycisk na *Utwórz nowy zasób usług Cognitive Services*, Nowa karta zostanie otwarty, która pozwoli na tworzenie zasobu usług Cognitive Services. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Jeśli został utworzony nowy zasób, kliknij przycisk *Odśwież* Aby odświeżyć listę zasobów usług Cognitive Services, a następnie wybierz zasób.
1. Kliknij przycisk *OK* aby zatwierdzić zmiany

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Programowe Kojarzenie zasobu usług Cognitive Services

Podczas definiowania zestawu umiejętności programistyczne, Dodaj `cognitiveServices` sekcji. Sekcji powinna zawierać klucz zasobu usług Cognitive Services, czy chcesz skojarzyć z zestawu umiejętności, jak również @odata.type, która powinna być ustawiona na "#Microsoft.Azure.Search.CognitiveServicesByKey". Ten wzorzec jest wyświetlany w poniższym przykładzie.

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Przykład: Szacowanie kosztów montażu łamania dokumentów i wzbogacanie
Można oszacować, ile to kosztuje wzbogacanie danego typu dokumentu. Poniżej wykonywania jest tylko przykładem, ale mogą być pomocne dla użytkownika.

Wyobraź sobie, że mamy 1000 plików PDF i szacowane czy średnio każdy z tych dokumentów ma 6 strony każdego. Załóżmy, że każdy z nich ma jeden obraz na stronie na potrzeby tego ćwiczenia. Ponadto Załóżmy, że średnio jest około 3000 znaków na stronie. 

Teraz załóżmy, że chcemy wykonać następujące czynności w ramach wzbogacony potok:
1. W ramach łamania dokumentów Wyodrębnij zawartość i obrazy z dokumentu.
1. Jako część wzbogacania wyodrębnić OCR stron, łączenie tekstu dla wszystkich stron, a następnie Wyodrębnij każdej organizacji w połączonym tekście wszystkie obrazy.

Spróbujmy szacowania, ile będzie kosztować obsługę tych dokumentów, krok po kroku.

1000 dokumentów:

1. Dokumentowanie łamania, firma Microsoft może wyodrębnić łączna liczba 6000 obrazów. Zakładając, że $1 dla każdego 1000 obrazów wyodrębnione, który będzie kosztować rozwiązywaniu $6.00.

2. Firma Microsoft będzie wyodrębnić tekst z każdego z tych obrazów 6000. Umiejętności cognitive optyczne rozpoznawanie znaków w języku angielskim, korzysta z najlepszego algorytmu (DescribeText). Zakładając, że koszt 2,50 USD za 1000 obrazów do analizy, firma Microsoft będzie zapłacić $15,00 dla tego kroku.

3. Do działania funkcji wydobywania podmiotów firma Microsoft musi łącznie 3 rekordów tekstowych na stronie (każdy rekord to 1000 znaków). strona rekordów tekst 3 * 6000 strony = 18,000 rekordów tekstowych. Zakładając, że $2.00 / rekordów tekstowych 1000, ten krok będzie kosztować rozwiązywaniu $36.00.

Łączenie wszystkiego razem, firma Microsoft będzie zapłacić 57.00 $ pozyskiwać 1000 dokumentów pdf tego rodzaju przy użyciu zestawu umiejętności opisem.  W tym ćwiczeniu firma Microsoft zakłada, że najbardziej kosztowne cenę transakcji, jego mogło być niższe z powodu tłumaczenie ceny. Zobacz [cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Kolejne kroki
+ [Stronie cennika usługi wyszukiwania platformy Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
