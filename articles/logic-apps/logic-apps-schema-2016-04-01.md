---
title: Schemat aktualizuje czerwca-1-2016 - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utworzenie definicji JSON dla usługi Azure Logic Apps z wersją schematu 2016-06-01
author: jeffhollan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 1cbb20a96f39ffa6f4c7375a38b3bf8046d0bd3a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299695"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizacje schematu dla usługi Azure Logic Apps — 1 czerwca 2016 r.

[Zaktualizowany schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) i wersja interfejsu API dla usługi Azure Logic Apps obejmuje klucza ulepszeniom aplikacje logiki bardziej niezawodne i łatwiejsze do użycia:

* [Zakresy](#scopes) można zagnieździć działania jako zbiór akcji lub grupy.
* [Warunkach i pętlach](#conditions-loops) są teraz akcje najwyższej jakości.
* Bardziej dokładne kolejność uruchamiania akcji z `runAfter` właściwości, zastępując `dependsOn`

Aby uaktualnić aplikacje logiki 1 sierpnia 2015 preview schematu do schematu 1 czerwca 2016 r. [wyewidencjonować sekcja uaktualnienie](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Zakresy

Ten schemat zawiera zakresy, które pozwalają grupy razem lub zagnieżdżania operacje wewnątrz siebie. Na przykład warunek może zawierać inny warunek. Dowiedz się więcej o [zakres składni](../logic-apps/logic-apps-loops-and-scopes.md), lub przejrzyj w tym przykładzie zakres podstawowe:

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

## <a name="conditions-and-loops-changes"></a>Zmiany w warunkach i pętlach

W schemacie poprzedniej wersji, warunkach i pętlach zostały parametrów skojarzonych z jednej akcji. Ten schemat podnosi to ograniczenie, więc warunkach i pętlach są teraz wyświetlane jako typów akcji. Dowiedz się więcej o [pętli i zakresy](../logic-apps/logic-apps-loops-and-scopes.md), lub przejrzyj to prosty przykład dla warunku akcji:

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

`runAfter` Zamienia właściwość `dependsOn`, zapewniających większą dokładność po określeniu kolejność wykonywania działań na podstawie stanu z poprzedniej akcji.

`dependsOn` Właściwość synonimem "Akcja uruchomienie i było pomyślne", nie ma znaczenia, ile razy, aby wykonać czynności, w oparciu o czy poprzedniej akcji zakończyło się powodzeniem, nie powiodło się lub pominięte. `runAfter` Właściwości zapewnia elastyczność, że jako obiekt, który określa wszystkie nazwy akcji, po którym jest uruchamiany obiektu. Ta właściwość definiuje również tablicę stany, które są akceptowane jako wyzwalacze. Na przykład, jeśli chcesz uruchomić po krok zakończy się pomyślnie, a także po kroku B pomyślnym lub niepomyślnym, możesz utworzyć to `runAfter` właściwości:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uaktualnienia schematu do

Aby uaktualnić do [najnowszych schematu](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), tylko należy wykonać kilka kroków. Proces uaktualniania zawiera uruchomienie skryptu uaktualnienia, zapisywanie jako nową aplikację logiki, a jeśli chcesz, prawdopodobnie zastąpienie poprzedniej aplikacji logiki.

1. Otwórz aplikację logiki w portalu Azure.

2. Przejdź do **omówienie**. Na pasku narzędzi aplikacji logiki, wybierz **Aktualizuj schemat**.
   
    ![Wybierz pozycję Aktualizuj schemat][1]
   
    Uaktualniony definicji jest zwracany, który możesz skopiować i wkleić do definicji zasobu, jeśli to konieczne. 
    Jednak firma Microsoft **zdecydowanie zaleca się** wybierzesz **Zapisz jako** aby upewnić się, że wszystkie odwołania połączenia są prawidłowe w aplikacji logiki uaktualniony.

3. Na pasku zadań uaktualniania bloku wybierz **Zapisz jako**.

4. Wprowadź nazwę logiki i stanu. Aby wdrożyć aplikację logiki uaktualniony, wybierz **Utwórz**.

5. Upewnij się, że aplikację logiki uaktualnionego działa zgodnie z oczekiwaniami.
   
   > [!NOTE]
   > Jeśli używasz wyzwalacz ręczny lub żądanie wywołania zwrotnego adresu URL zmiany w nowej aplikacji logiki. Przetestuj nowy adres URL, aby upewnić się, że działa środowisko end-to-end. Aby zachować poprzednie adresów URL, można sklonować za pośrednictwem istniejących aplikacji logiki.

6. *Opcjonalne* zastąpienie poprzedniej aplikacji logiki w nowej wersji schematu na pasku narzędzi wybierz **klonowania**obok pola **Aktualizuj schemat**. Ten krok jest niezbędny tylko wtedy, gdy chcesz zachować ten sam identyfikator zasobu lub adres URL wyzwalacza aplikacji logiki żądania.

### <a name="upgrade-tool-notes"></a>Narzędzie uaktualniania uwagi

#### <a name="mapping-conditions"></a>Mapowanie warunków

W definicji uaktualniony narzędzie sprawia, że optymalnego na grupowanie akcji gałęzi true i false jako zakres. W szczególności projektanta wzorzec `@equals(actions('a').status, 'Skipped')` powinny się wyświetlać jako `else` akcji. Jeśli jednak narzędzie wykryje nierozpoznawalną wzorców, narzędzie może utworzyć osobne warunki true i false gałęzi. W razie potrzeby można ponownie zamapować akcje po uaktualnieniu.

#### <a name="foreach-loop-with-condition"></a>Pętla "foreach" z warunkiem

W nowym schemacie, można użyć akcji filtrowania replikowanie wzorzec `foreach` pętli z warunkiem dla każdego elementu, ale ta zmiana automatycznie powinno się zdarzyć w przypadku uaktualnienia. Warunek staje się akcję filtru przed pętli foreach dla tylko tablicę elementów spełniających warunek i tablicy jest przekazywany do akcji foreach. Na przykład zobacz [pętli i zakresy](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Tagi zasobów

Po uaktualnieniu, tagi zasobów są usuwane, dlatego należy je zresetować uaktualnionego przepływu pracy.

## <a name="other-changes"></a>Inne zmiany

### <a name="renamed-manual-trigger-to-request-trigger"></a>Zmieniono nazwę wyzwalacza "manual" do wyzwalacza 'żądanie'

`manual` Typ wyzwalacza została zastąpiona i zmieniona na `request` z typem `http`. Ta zmiana tworzy więcej spójności dla rodzaju wzorzec wyzwalacza jest używany do tworzenia.

### <a name="new-filter-action"></a>Nowa akcja "filtr.

Aby filtrować dużą tablicę dół mniejszy zestaw elementów, nowe `filter` typu akceptuje tablicy i warunku, ocenia stan dla każdego elementu i zwraca tablicę z elementami spełniających warunek.

### <a name="restrictions-for-foreach-and-until-actions"></a>Ograniczenia dla "foreach" i "do" Akcje

`foreach` i `until` pętli są ograniczone do jednej akcji.

### <a name="new-trackedproperties-for-actions"></a>Nowy "trackedProperties" dla akcji

Akcje teraz może zawierać dodatkowe właściwości o nazwie `trackedProperties`, który jest elementem równorzędnym do `runAfter` i `type` właściwości. Ten obiekt określa niektórych akcji wejściowych i wyjściowych, które chcesz uwzględnić w Azure diagnostycznych danych telemetrycznych emitowane w ramach przepływu pracy. Na przykład:

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
* [Utwórz szablony wdrażania dla usługi logic apps](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
