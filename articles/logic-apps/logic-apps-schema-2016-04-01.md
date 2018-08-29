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
ms.openlocfilehash: 43fd52dd04e679b9756c07e8c6e260323469026a
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126206"
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

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
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

W schemacie poprzedniej wersji, warunków i pętli zostały parametrów skojarzonych z jednej akcji. Ten schemat wind tego ograniczenia, więc warunków i pętli pojawiają się jako typy akcji. Dowiedz się więcej o [pętli i zakresy](../logic-apps/logic-apps-loops-and-scopes.md), lub przejrzeć ten prosty przykład dla warunku akcji:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Właściwość "runAfter"

`runAfter` Zastępuje właściwość `dependsOn`, zapewniające większą dokładność podczas określania kolejności wykonywania akcji na podstawie stanu z poprzednich akcji.

`dependsOn` Właściwość jest synonimem "Akcja uruchomiono i zakończyło się pomyślnie.", nie ma znaczenia, ile razy chce się wykonać czynności, w oparciu o tego, czy poprzedniej akcji zakończyło się powodzeniem, nie powiodło się lub pominięte. `runAfter` Właściwości można swobodnie tego jako obiekt, który określa wszystkie nazwy akcji, po których jest uruchamiany obiektu. Ta właściwość definiuje również tablica stany, które są dopuszczalne jako wyzwalacze. Na przykład, jeśli chce się uruchomić po krok zakończy się powodzeniem, a także po kroku B zakończy się powodzeniem lub niepowodzeniem, możesz utworzyć to `runAfter` właściwości:

```
{
    "...",
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
    Jednak firma Microsoft **zdecydowanie zaleca się** wybierzesz **Zapisz jako** aby upewnić się, że wszystkie odwołania połączeń są prawidłowe w uaktualnionej aplikacji logiki.

3. Na pasku narzędzi uaktualniania bloku wybierz **Zapisz jako**.

4. Wprowadź nazwę logiki i stanu. Aby wdrożyć Twojej uaktualnionej aplikacji logiki, wybierz **Utwórz**.

5. Upewnij się, że Twoja aplikacja logiki uaktualnionego działa zgodnie z oczekiwaniami.
   
   > [!NOTE]
   > Jeśli używasz wyzwalacz ręczny lub żądanie adresu URL wywołania zwrotnego zmienia się w nowej aplikacji logiki. Przetestuj nowy adres URL, aby upewnić się, że działa środowisko end-to-end. Aby zachować poprzednie adresy URL, można sklonować za pośrednictwem istniejących aplikacji logiki.

6. *Opcjonalnie* zastąpienie poprzedniej aplikacji logiki przy użyciu nowej wersji schematu, na pasku narzędzi wybierz **klonowania**obok pozycji **aktualizacja schematu**. Ten krok jest niezbędny, tylko wtedy, gdy chcesz zachować ten sam identyfikator zasobu lub żądania URL wyzwalacza aplikacji logiki.

### <a name="upgrade-tool-notes"></a>Informacje o narzędzie uaktualniania

#### <a name="mapping-conditions"></a>Warunków mapowania

W uaktualnionej definicji narzędzie sprawia, że najlepszy nakład pracy na grupowanie akcji gałęzi true i false jako zakres. W szczególności projektanta wzorzec `@equals(actions('a').status, 'Skipped')` powinny się wyświetlać jako `else` akcji. Jednak jeśli narzędzie wykryje nierozpoznawalną wzorców, narzędzie może utworzyć osobne warunki dla wartości true i false gałęzi. Możesz ponownie zamapować czynności po uaktualnieniu, jeśli to konieczne.

#### <a name="foreach-loop-with-condition"></a>pętli "foreach" z warunkiem

W nowym schemacie do replikowania wzorca można użyć akcji filtrowania `foreach` pętli z warunkiem każdego elementu, ale tę zmianę automatycznie powinno mieć miejsce podczas uaktualniania. Warunek przestaje być akcję filtrowania przed pętli foreach w celu zwrócenia tylko tablicę elementów spełniających warunek, a ta tablica jest przekazywana do działania foreach. Aby uzyskać przykład, zobacz [pętli i zakresy](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Tagi zasobów

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

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
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
