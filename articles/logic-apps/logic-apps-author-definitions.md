---
title: Tworzenie, edytowanie lub rozszerzanie definicji przepływu pracy JSON aplikacji logiki
description: Jak pisać, edytować i rozszerzać definicje przepływu pracy JSON aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 0f5f01c757bf651beddaa76fc3eb8046b21b31eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979395"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Tworzenie, edytowanie lub rozszerzanie definicji przepływu pracy aplikacji logiki w usłudze Azure Logic Apps

Podczas tworzenia rozwiązań integracji przedsiębiorstwa z zautomatyzowanych przepływów pracy w [usłudze Azure Logic Apps,](../logic-apps/logic-apps-overview.md)podstawowe definicje aplikacji logiki używać prostych i deklaratywne JavaScript Object Notation (JSON) wraz ze [schematem języka definicji przepływu pracy (WDL)](../logic-apps/logic-apps-workflow-definition-language.md) ich opis i sprawdzanie poprawności. Te formaty sprawiają, że definicje aplikacji logiki łatwiejsze do odczytania i zrozumienia, nie wiedząc wiele o kodzie.
Aby zautomatyzować tworzenie i wdrażanie aplikacji logiki, można dołączyć definicje aplikacji logiki jako [zasoby platformy Azure](../azure-resource-manager/management/overview.md) w [szablonach usługi Azure Resource Manager.](../azure-resource-manager/templates/overview.md)
Aby tworzyć aplikacje logiki, zarządzać nimi i wdrażać je, można następnie użyć interfejsów [API platformy Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.logicapp) [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)lub [interfejsów API REST aplikacji azure logic apps.](https://docs.microsoft.com/rest/api/logic/)

Aby pracować z definicjami aplikacji logiki w języku JSON, otwórz edytor widoku kodu podczas pracy w witrynie Azure portal lub w programie Visual Studio lub skopiuj definicję do dowolnego edytora.
Jeśli jesteś nowy w aplikacjach logiki, sprawdź, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Niektóre funkcje usługi Azure Logic Apps, takie jak definiowanie parametrów i wielu wyzwalaczy w definicjach aplikacji logiki, są dostępne tylko w usłudze JSON, a nie w Projektancie aplikacji logiki.
> Dlatego w przypadku tych zadań należy pracować w widoku kodu lub innym edytorze.

## <a name="edit-json---azure-portal"></a>Edytuj JSON — witryna Azure portal

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">Portalu Azure</a>.

2. Z lewego menu wybierz pozycję **Wszystkie usługi**.
W polu wyszukiwania znajdź "aplikacje logiki", a następnie z wyników wybierz aplikację logiki.

3. W menu aplikacji logiki w obszarze **Narzędzia programistyczne**wybierz pozycję **Widok kodu aplikacji logiki**.

   Edytor widoku kodu otwiera i pokazuje definicję aplikacji logiki w formacie JSON.

## <a name="edit-json---visual-studio"></a>Edycja JSON - Visual Studio

Zanim będzie można pracować nad definicją aplikacji logiki w programie Visual Studio, upewnij się, że [zainstalowano wymagane narzędzia](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Aby utworzyć aplikację logiki za pomocą programu Visual Studio, zapoznaj się z [przewodnikiem Szybki start: Automatyzuj zadania i procesy za pomocą aplikacji Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

W programie Visual Studio można otwierać aplikacje logiki, które zostały utworzone i wdrożone bezpośrednio z witryny Azure portal lub jako projekty usługi Azure Resource Manager z programu Visual Studio.

1. Otwórz rozwiązanie programu Visual Studio lub projekt [grupy zasobów platformy Azure,](../azure-resource-manager/management/overview.md) który zawiera aplikację logiki.

2. Znajdź i otwórz definicję aplikacji logiki, która domyślnie pojawia się w [szablonie Menedżera zasobów](../azure-resource-manager/templates/overview.md)o nazwie **LogicApp.json**.
Można użyć i dostosować ten szablon do wdrożenia w różnych środowiskach.

3. Otwórz menu skrótów dla definicji i szablonu aplikacji logiki.
Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**.

   ![Otwieranie aplikacji logiki w rozwiązaniu programu Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

4. U dołu projektanta wybierz pozycję **Widok kodu**.

   Edytor widoku kodu otwiera i pokazuje definicję aplikacji logiki w formacie JSON.

5. Aby powrócić do widoku projektanta, u dołu edytora widoku kodu wybierz pozycję **Projektowanie**.

## <a name="parameters"></a>Parametry

Cykl życia wdrażania zwykle ma różne środowiska do programowania, testową, przejściową i produkcyjną. Jeśli masz wartości, które chcesz ponownie użyć w całej aplikacji logiki bez twardego kodowania lub które różnią się w zależności od potrzeb wdrożenia, można utworzyć [szablon usługi Azure Resource Manager](../azure-resource-manager/management/overview.md) dla definicji przepływu pracy, dzięki czemu można również zautomatyzować wdrażanie aplikacji logiki.

Wykonaj następujące ogólne kroki, aby *sparametryzować*lub zdefiniować i użyć parametrów dla tych wartości. Następnie można podać wartości w oddzielnym pliku parametrów, który przekazuje te wartości do szablonu. W ten sposób można łatwiej zmienić te wartości bez konieczności aktualizowania i ponownego rozmieszczenia aplikacji logiki. Aby uzyskać szczegółowe informacje, zobacz [Omówienie: Automatyzacja wdrażania aplikacji logiki za pomocą szablonów usługi Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. W szablonie zdefiniuj parametry szablonu i parametry definicji przepływu pracy, aby zaakceptować wartości, które mają być używane odpowiednio podczas wdrażania i środowiska wykonawczego.

   Parametry szablonu są definiowane w sekcji parametrów, która znajduje się poza definicją przepływu pracy, podczas gdy parametry definicji przepływu pracy są definiowane w sekcji parametrów, która znajduje się wewnątrz definicji przepływu pracy.

1. Zastąp wartości zakodowane na stałe wyrażeniami, które odwołują się do tych parametrów. Wyrażenia szablonu używają składni, która różni się od wyrażeń definicji przepływu pracy.

   Należy unikać komplikowania kodu, nie używając wyrażeń szablonu, które są oceniane podczas wdrażania, wewnątrz wyrażeń definicji przepływu pracy, które są oceniane w czasie wykonywania. Używaj tylko wyrażeń szablonów poza definicją przepływu pracy. Użyj tylko wyrażeń definicji przepływu pracy wewnątrz definicji przepływu pracy.

   Po określeniu wartości parametrów definicji przepływu pracy można odwoływać się do parametrów szablonu przy użyciu sekcji parametrów, która znajduje się poza definicją przepływu pracy, ale nadal wewnątrz definicji zasobu dla aplikacji logiki. W ten sposób można przekazać wartości parametrów szablonu do parametrów definicji przepływu pracy.

1. Przechowuj wartości parametrów w oddzielnym [pliku parametrów](../azure-resource-manager/templates/parameter-files.md) i dołącz ten plik do wdrożenia.

## <a name="process-strings-with-functions"></a>Proces ciągów z funkcjami

Logic Apps ma różne funkcje do pracy z ciągami.
Załóżmy na przykład, że chcesz przekazać nazwę firmy z zamówienia do innego systemu.
Jednak nie masz pewności co do prawidłowej obsługi kodowania znaków.
Można wykonać kodowanie base64 na tym ciągu, ale aby uniknąć ucieczki w adresie URL, można zastąpić kilka znaków zamiast. Ponadto potrzebujesz tylko podciągu dla nazwy firmy, ponieważ pierwsze pięć znaków nie są używane.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

W tych krokach opisano, jak w tym przykładzie przetwarza ten ciąg, pracując od wewnątrz do zewnątrz:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Pobierz [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) nazwę firmy, aby uzyskać całkowitą liczbę znaków.

2. Aby uzyskać krótszy ciąg, `5`odejmij .

3. Teraz dostać [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md).
Rozpocznij `5`od indeksu i przejdź do pozostałej części ciągu.

4. Konwertuj ten [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) podciąg na ciąg.

5. Teraz [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) wszystkie `+` postacie z `-` postaciami.

6. Na koniec [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) wszystkie `/` postacie z `_` postaciami.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapuj elementy listy na wartości właściwości, a następnie używaj map jako parametrów

Aby uzyskać różne wyniki na podstawie wartości właściwości, można utworzyć mapę, która pasuje do każdej wartości właściwości do wyniku, a następnie użyć tej mapy jako parametru.

Na przykład ten przepływ pracy definiuje niektóre kategorie jako parametry i mapę, która pasuje do tych kategorii z określonym adresem URL.
Po pierwsze, przepływ pracy pobiera listę artykułów. Następnie przepływ pracy używa mapy, aby znaleźć adres URL pasujący do kategorii dla każdego artykułu.

*   Funkcja [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) sprawdza, czy kategoria jest zgodna ze znaną zdefiniowaną kategorią.

*   Po uzyskaniu pasującej kategorii przykład pobiera element z mapy przy użyciu nawiasów kwadratowych:`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Uzyskaj dane za pomocą funkcji Data

Aby uzyskać dane ze źródła danych, które nie obsługuje natywnie *wyzwalaczy,* można użyć funkcji Data do pracy z godzinami i datami.
Na przykład to wyrażenie znajduje, jak długo te kroki przepływu pracy są podejmowane, pracy od wewnątrz do zewnątrz:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Z `order` akcji wyodrębnij `startTime`plik .
2. Uzyskaj bieżący `utcNow()`czas z .
3. Odejmij jedną sekundę:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Można użyć innych jednostek `minutes` czasu, takich jak lub `hours`.

3. Teraz możesz porównać te dwie wartości.

   Jeśli pierwsza wartość jest mniejsza niż druga wartość, od pierwszego zamówienia minęło więcej niż jedną sekundę.

Do formatowania dat można użyć formaterów ciągów. Na przykład, aby uzyskać RFC1123, użyj [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md).
Dowiedz się więcej o [formatowaniu daty](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie kroków na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchamianie kroków na podstawie różnych wartości (instrukcje switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchamianie i powtarzanie kroków (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Uruchamianie kroków na podstawie stanu akcji zgrupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Dowiedz się więcej o [schemacie języka definicji przepływu pracy dla aplikacji logiki azure](../logic-apps/logic-apps-workflow-definition-language.md)
* Dowiedz się więcej o [akcjach przepływu pracy i wyzwalaczach dla usługi Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
