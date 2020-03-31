---
title: Aktualizacje schematu czerwiec-1-2016
description: Zaktualizowano wersję schematu 2016-06-01 dla definicji aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: e2f65f1c52dc7dfb2e4e4bf66f5c7e82f4b802b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792880"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizacje schematu dla aplikacji logiki azure — 1 czerwca 2016 r.

[Zaktualizowany schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) i wersja interfejsu API dla usługi Azure Logic Apps zawiera kluczowe ulepszenia, które sprawiają, że aplikacje logiki są bardziej niezawodne i łatwiejsze w użyciu:

* [Zakresy](#scopes) umożliwiają grupowanie lub zagnieżdżanie akcji jako zbiór akcji.
* [Warunki i pętle](#conditions-loops) są teraz akcje pierwszej klasy.
* Bardziej precyzyjne zamawianie do `runAfter` prowadzenia działań z nieruchomością, wymiana`dependsOn`

Aby uaktualnić aplikacje logiki ze schematu podglądu z 1 sierpnia 2015 r. do schematu z 1 czerwca 2016 r., [zapoznaj się z sekcją uaktualniania](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Zakresy

Ten schemat zawiera zakresy, które umożliwiają grupowanie akcji razem lub zagnieżdżanie akcji wewnątrz siebie. Na przykład warunek może zawierać inny warunek. Dowiedz się więcej o [składni zakresu](../logic-apps/logic-apps-loops-and-scopes.md)lub przejrzyj ten przykład zakresu podstawowego:

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

W poprzednich wersjach schematu warunki i pętle były parametrami skojarzonymi z pojedynczą akcją. Ten schemat znosi to ograniczenie, więc warunki i pętle są teraz dostępne jako typy akcji. Dowiedz się więcej o [pętlach i zakresach](../logic-apps/logic-apps-loops-and-scopes.md), [warunkach](../logic-apps/logic-apps-control-flow-conditional-statement.md)lub przejrzyj ten podstawowy przykład, który pokazuje akcję warunku:

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

## <a name="runafter-property"></a>Właściwość 'runAfter'

Właściwość `runAfter` zastępuje `dependsOn`, zapewniając większą precyzję podczas określania kolejności uruchamiania dla akcji na podstawie stanu poprzednich akcji. Właściwość wskazała, `dependsOn` czy "akcja została pomyślnie", w zależności od tego, czy poprzednia akcja zakończyła się pomyślnie, nie powiodła się lub pominięta - nie liczba razy, gdy chcesz uruchomić akcję. Właściwość `runAfter` zapewnia elastyczność jako obiekt, który określa wszystkie nazwy akcji, po których obiekt jest uruchamiany. Ta właściwość definiuje również tablicę stanów, które są dopuszczalne jako wyzwalacze. Na przykład jeśli chcesz, aby akcja została uruchamiana po pomyślnym działania A, `runAfter` a także po pomyślnym lub niepowodzeniem akcji B, skonfiguruj tę właściwość:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uaktualnianie schematu

Aby uaktualnić do [najnowszego schematu,](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)wystarczy wykonać tylko kilka kroków. Proces uaktualniania obejmuje uruchamianie skryptu uaktualniania, zapisywanie jako nowej aplikacji logiki, a jeśli chcesz, ewentualnie zastąpienie poprzedniej aplikacji logiki.

1. W witrynie Azure portal otwórz aplikację logiki.

2. Przejdź do **przeglądu**. Na pasku narzędzi aplikacji logiki wybierz pozycję **Aktualizuj schemat**.
   
   ![Wybieranie opcji Aktualizuj schemat][1]
   
   Zwracana jest ulepszona definicja, którą można w razie potrzeby skopiować i wkleić do definicji zasobu. 

   > [!IMPORTANT]
   > *Upewnij się, że* wybierz pozycję **Zapisz jako,** aby wszystkie odwołania do połączeń pozostały prawidłowe w uaktualnionej aplikacji logiki.

3. Na pasku narzędzi bloku przybornika uaktualnienia wybierz pozycję **Zapisz jako**.

4. Wprowadź nazwę i stan logiki. Aby wdrożyć uaktualnioną aplikację logiki, wybierz pozycję **Utwórz**.

5. Upewnij się, że uaktualniona aplikacja logiki działa zgodnie z oczekiwaniami.
   
   > [!NOTE]
   > Jeśli używasz wyzwalacza ręcznego lub żądania, adres URL wywołania zwrotnego zmienia się w nowej aplikacji logiki. Przetestuj nowy adres URL, aby upewnić się, że kompleksowe środowisko działa. Aby zachować poprzednie adresy URL, można sklonować istniejącą aplikację logiki.

6. *Opcjonalnie* Aby zastąpić poprzednią aplikację logiki nową wersją schematu, na pasku narzędzi wybierz pozycję **Klonuj**, obok **pozycji Aktualizuj schemat**. Ten krok jest konieczne tylko wtedy, gdy chcesz zachować ten sam identyfikator zasobu lub adres URL wyzwalacza żądania aplikacji logiki.

## <a name="upgrade-tool-notes"></a>Notatki dotyczące narzędzia uaktualniania

### <a name="mapping-conditions"></a>Warunki mapowania

W uaktualnionej definicji narzędzie sprawia, że najlepsze wysiłki w grupowaniu akcji true i false gałęzi razem jako zakres. W szczególności wzorzec `@equals(actions('a').status, 'Skipped')` projektanta `else` pojawia się jako akcja. Jeśli jednak narzędzie wykryje nierozpoznawalne wzorce, narzędzie może utworzyć oddzielne warunki zarówno dla gałęzi true, jak i false. W razie potrzeby można ponownie mapować akcje po uaktualnieniu.

#### <a name="foreach-loop-with-condition"></a>Pętla "foreach" z warunkiem

W nowym schemacie można użyć akcji filtrowania do replikowania wzorca, który używa **For each** pętli z jednym warunkiem na element. Jednak zmiana nastąpi automatycznie po uaktualnieniu. Warunek staje się akcja filtrowania, który pojawia się przed **For each** pętli, zwracając tylko tablicę elementów, które pasują do warunku i przekazywania tej tablicy **dla każdej** akcji. Na przykład zobacz [Pętle i zakresy](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Tagi zasobów

Po uaktualnieniu tagi zasobów są usuwane, więc należy je zresetować dla uaktualnionego przepływu pracy.

## <a name="other-changes"></a>Inne zmiany

### <a name="renamed-manual-trigger-to-request-trigger"></a>Zmieniono nazwę wyzwalacza "ręczny" na wyzwalacz "żądanie"

Typ `manual` wyzwalacza został przestarzały `request` i `http`zmieniono jego nazwę na typ . Ta zmiana tworzy większą spójność dla rodzaju wzorca, który jest używany do tworzenia wyzwalacza.

### <a name="new-filter-action"></a>Nowa akcja "filtruj"

Aby filtrować dużą tablicę do mniejszego `filter` zestawu elementów, nowy typ akceptuje tablicę i warunek, ocenia warunek dla każdego elementu i zwraca tablicę z elementami spełniania warunku.

### <a name="restrictions-for-foreach-and-until-actions"></a>Ograniczenia dotyczące działań "foreach" i "do"

I `foreach` `until` pętli są ograniczone do jednej akcji.

### <a name="new-trackedproperties-for-actions"></a>Nowe "właściwości śledzone" dla działań

Akcje mogą teraz mieć `trackedProperties`dodatkową właściwość o `runAfter` `type` nazwie , która jest elementem równorzędnym i właściwości. Ten obiekt określa pewne dane wejściowe lub dane wyjściowe akcji, które mają zostać uwzględnione w telemetrii diagnostyki azure, emitowane jako część przepływu pracy. Przykład:

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

* [Tworzenie definicji przepływów pracy dla aplikacji logiki](../logic-apps/logic-apps-author-definitions.md)
* [Tworzenie wdrożenia aplikacji logiki](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
