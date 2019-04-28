---
title: Schemat aktualizuje czerwca-1-2016 — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zaktualizowano schemat wersja 2016-06-01 definicji aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 6df29543df2b7b2609582f7e8dd9a0629182760c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995796"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizacje schematu dla usługi Azure Logic Apps — 1 czerwca 2016 r.

[Zaktualizowano schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) i wersja interfejsu API dla usługi Azure Logic Apps zawiera najważniejsze ulepszenia, które Twórz aplikacje logiki, bardziej niezawodne i łatwiejsze w użyciu:

* [Zakresy](#scopes) pozwalają grupie lub zagnieżdżanie akcje jako zbiór akcji.
* [Warunków i pętli](#conditions-loops) są teraz akcje pierwszej klasy.
* Bardziej precyzyjnego zamawiania do uruchamiania działań za pomocą `runAfter` właściwości zastąpienia `dependsOn`

Do uaktualnienia aplikacji logiki ze schematu w wersji zapoznawczej 1 sierpnia 2015 r. schematu 1 czerwca 2016 [zajrzyj do sekcji uaktualnienia](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Zakresy

Ten schemat obejmuje zakresy, które umożliwiają grupy razem lub zagnieżdżonych operacje wewnątrz siebie nawzajem. Na przykład warunek może zawierać inny warunek. Dowiedz się więcej o [zakresu składni](../logic-apps/logic-apps-loops-and-scopes.md), lub przejrzyj w tym przykładzie zakres podstawowe:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Zmiany warunków i pętli

W schemacie poprzedniej wersji, warunków i pętli zostały parametrów skojarzonych z jednej akcji. Ten schemat wind tego ograniczenia, więc warunków i pętli są teraz dostępne jako typy akcji. Dowiedz się więcej o [pętli i zakresy](../logic-apps/logic-apps-loops-and-scopes.md), [warunki](../logic-apps/logic-apps-control-flow-conditional-statement.md), lub przejrzeć tym podstawowym przykładzie, który zawiera informacje o akcji warunek:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Właściwość "runAfter"

`runAfter` Zastępuje właściwość `dependsOn`, zapewniające większą dokładność podczas określania kolejności wykonywania akcji na podstawie stanu z poprzednich akcji. `dependsOn` Właściwość wskazano, czy "Akcja uruchomiono i zakończyło się pomyślnie", na podstawie poprzedniej akcji zakończyło się pomyślnie, nie powiodło się, czy jako pominięte — nie liczbę razy, aby uruchomić akcję. `runAfter` Właściwość zapewnia elastyczność jako obiekt, który określa wszystkie akcje nazwy po uruchamia obiektu. Ta właściwość definiuje również tablica stany, które są dopuszczalne jako wyzwalacze. Na przykład, jeśli chcesz, aby dana akcja do uruchomienia po wykonaniu akcji A zakończy się pomyślnie, i również po akcji B zakończy się pomyślnie lub nie powiedzie się, skonfiguruj to `runAfter` właściwości:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uaktualnienie schematu

Uaktualnienie do [najnowszych schematu](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), należy tylko podjąć kilka kroków. Proces uaktualniania obejmuje, uruchamianie skryptu uaktualniania Zapisywanie jako nową aplikację logiki, a jeśli chcesz, prawdopodobnie zastąpienie poprzedniej aplikacji logiki.

1. W witrynie Azure portal Otwórz aplikację logiki.

2. Przejdź do **Przegląd**. Na pasku narzędzi aplikacji logiki wybierz **aktualizacja schematu**.
   
   ![Wybierz pozycję Aktualizuj schemat][1]
   
   Uaktualnionej definicji jest zwracany, który możesz skopiować i wkleić w definicji zasobu, jeśli to konieczne. 

   > [!IMPORTANT]
   > *Upewnij się, że* wybierzesz **Zapisz jako** więc wszystkie odwołania połączeń flagi card_authenticate_ w uaktualnionej aplikacji logiki.

3. Na pasku narzędzi uaktualniania bloku wybierz **Zapisz jako**.

4. Wprowadź nazwę logiki i stanu. Aby wdrożyć Twojej uaktualnionej aplikacji logiki, wybierz **Utwórz**.

5. Upewnij się, że Twoja aplikacja logiki uaktualnionego działa zgodnie z oczekiwaniami.
   
   > [!NOTE]
   > Jeśli używasz wyzwalacz ręczny lub żądanie adresu URL wywołania zwrotnego zmienia się w nowej aplikacji logiki. Przetestuj nowy adres URL, aby upewnić się, że działa środowisko end-to-end. Aby zachować poprzednie adresy URL, można sklonować za pośrednictwem istniejących aplikacji logiki.

6. *Opcjonalnie* zastąpienie poprzedniej aplikacji logiki przy użyciu nowej wersji schematu, na pasku narzędzi wybierz **klonowania**obok pozycji **aktualizacja schematu**. Ten krok jest niezbędny, tylko wtedy, gdy chcesz zachować ten sam identyfikator zasobu lub żądania URL wyzwalacza aplikacji logiki.

## <a name="upgrade-tool-notes"></a>Informacje o narzędzie uaktualniania

### <a name="mapping-conditions"></a>Warunków mapowania

W uaktualnionej definicji narzędzie sprawia, że najlepszy nakład pracy na grupowanie akcji gałęzi true i false jako zakres. W szczególności projektanta wzorzec `@equals(actions('a').status, 'Skipped')` pojawia się jako `else` akcji. Jednak jeśli narzędzie wykryje nierozpoznawalną wzorców, narzędzie może utworzyć osobne warunki dla wartości true i false gałęzi. Możesz ponownie zamapować czynności po uaktualnieniu, jeśli to konieczne.

#### <a name="foreach-loop-with-condition"></a>pętli "foreach" z warunkiem

W nowym schemacie, można użyć akcji filtrowania do replikowania wzorzec, który używa **dla każdego** pętli za pomocą jednego warunku każdego elementu. Jednak zmiana automatycznie odbywa się podczas uaktualniania. Warunek przestaje być akcji filtrowania, który pojawia się przed **dla każdego** pętli, tylko tablicę elementów spełniających warunek i macierzy w celu przekazywania **dla każdego** akcji. Aby uzyskać przykład, zobacz [pętli i zakresy](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Tagi zasobów

Po uaktualnieniu, tagi zasobów zostaną usunięte, więc należy je zresetować dla uaktualnionego przepływu pracy.

## <a name="other-changes"></a>Inne zmiany

### <a name="renamed-manual-trigger-to-request-trigger"></a>Zmieniono nazwę wyzwalacza "manual" wyzwalacza "Request (żądanie"

`manual` Typu wyzwalacza zostało przestarzały i zmieniona na `request` z typem `http`. Ta zmiana tworzy więcej spójności dla rodzaju wzorzec wyzwalacz jest używany do tworzenia.

### <a name="new-filter-action"></a>Nowa akcja "filter"

Aby filtrować dużą tablicę do mniejszy zestaw elementów, nowe `filter` typ akceptuje tablicy, jak i warunek, ocenia stan dla każdego elementu i zwraca tablicę z elementami spełniających warunek.

### <a name="restrictions-for-foreach-and-until-actions"></a>Ograniczenia dla "foreach" i "until" akcji

`foreach` i `until` pętli są ograniczone do pojedynczej akcji.

### <a name="new-trackedproperties-for-actions"></a>Nowy elemencie "trackedProperties" dla akcji

Akcje mogą teraz zawierać dodatkowe właściwości o nazwie `trackedProperties`, który jest elementem równorzędnym do `runAfter` i `type` właściwości. Ten obiekt określa niektórych danych wejściowych akcji ani danych wyjściowych, które mają zostać uwzględnione w danych telemetrycznych diagnostycznych platformy Azure, emitowane jako część przepływu pracy. Na przykład:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki
* [Tworzenie definicji przepływu pracy dla usługi logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Tworzenie szablonów wdrażania dla usługi logic apps](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
