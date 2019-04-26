---
title: Tworzenie, edytowanie i rozszerzanie JSON dla logiki definicji aplikacji — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i rozszerzanie JSON dla logiki definicji aplikacji w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: daeb900abc3f24a408fc1b5f6e989e5181f2a463
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427053"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Tworzenie, edytowanie i rozszerzanie JSON dla logiki definicji aplikacji w usłudze Azure Logic Apps

Po utworzeniu enterprise rozwiązania do integracji z zautomatyzowane przepływy pracy w [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), podstawowej definicji aplikacji logiki Użyj prostego i deklaratywne JavaScript Object Notation (JSON) wraz z [ Schemat definicji języka Umieszczany przepływów pracy](../logic-apps/logic-apps-workflow-definition-language.md) ich opis i sprawdzania poprawności. Te formaty ułatwiają logiki definicji aplikacji do odczytania i zrozumienia, nie wiedząc o tym, te informacje o kodzie. Jeśli chcesz zautomatyzować tworzenie i wdrażanie aplikacji logiki może zawierać definicji aplikacji logiki jako [zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) wewnątrz [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment). Umożliwia tworzenie i wdrażanie aplikacji logiki, zarządzanie można następnie użyć [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), lub [interfejsów API REST usługi Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Aby pracować z definicji aplikacji logiki w formacie JSON, Otwórz Edytor widoku kodu podczas pracy w witrynie Azure portal lub w programie Visual Studio lub skopiuj definicję do dowolnego edytora, który ma. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [jak utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Niektóre funkcje usługi Azure Logic Apps, takich jak Definiowanie parametrów i wielu wyzwalaczy w definicji aplikacji logiki, są dostępne tylko w formacie JSON, nie Projektant aplikacji logiki.
> Aby dla tych zadań można pracować w widoku kodu lub innym edytorze.

## <a name="edit-json---azure-portal"></a>Edytuj plik JSON — witryna Azure portal

1. Zaloguj się w witrynie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. Z menu po lewej stronie wybierz **wszystkich usług**. W polu wyszukiwania Znajdź "logic apps", a następnie z listy wyników wybierz swoją aplikację logiki.

3. W menu aplikacji logiki w ramach **narzędzia programistyczne**, wybierz opcję **widok kodu aplikacji logiki**.

   Widok kodu edytora otwiera i pokazuje definicji aplikacji logiki w formacie JSON.

## <a name="edit-json---visual-studio"></a>Edytuj plik JSON — z programu Visual Studio

Przed rozpoczęciem pracy w definicji aplikacji logiki w programie Visual Studio, upewnij się, że masz [zainstalowane wymagane narzędzia](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Aby utworzyć aplikację logiki przy użyciu programu Visual Studio, zapoznaj się z [Szybki Start: Automatyzowanie zadań i procesów przy użyciu usługi Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

W programie Visual Studio możesz otworzyć aplikacje logiki, które zostały utworzone i wdrożone lub bezpośrednio w witrynie Azure portal jako projektów usługi Azure Resource Manager w programie Visual Studio.

1. Otwórz rozwiązanie programu Visual Studio lub [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) projektu, który zawiera aplikację logiki.

2. Znajdowanie i otwieranie definicji aplikacji logiki, która domyślnie jest wyświetlana w [szablonu usługi Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment)o nazwie **LogicApp.json**. Możesz użyć i Dostosuj ten szablon do wdrożenia w różnych środowiskach.

3. Otwórz menu skrótów dla swojej definicji aplikacji logiki i szablonu. Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**.

   ![Aplikacja logiki otwarte w rozwiązaniu Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. W dolnej części projektanta wybierz **widok kodu**. 

   Widok kodu edytora otwiera i pokazuje definicji aplikacji logiki w formacie JSON.

5. Aby powrócić do projektanta widoku w dolnej części Widok kodu edytora, wybierz **projektowania**.

## <a name="parameters"></a>Parametry

Parametry umożliwiają ponowne używanie wartości w całej aplikacji logiki i są odpowiednie dla zastępowanie wartości, które mogą ulec zmianie, często. Na przykład jeśli masz adres e-mail, którego chcesz używać w wielu miejscach, należy zdefiniować tego adresu e-mail jako parametr.

Parametry są również przydatne, gdy trzeba zastąpić parametry w różnych środowiskach, Dowiedz się więcej o [parametry w celu wdrożenia](#deployment-parameters) i [interfejsu API REST dla usługi Azure Logic Apps dokumentacji](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parametry są dostępne tylko w widoku kodu.

W [pierwszą aplikację logiki przykład](../logic-apps/quickstart-create-first-logic-app-workflow.md), został utworzony przepływ pracy, który wysyła wiadomości e-mail, gdy pojawią się nowych wpisów w źródle danych RSS witryny sieci Web. Adres URL źródła danych jest stałe, więc w tym przykładzie pokazano, jak zastąpić wartość zapytania z parametrem tak, aby łatwiej zmienić adres URL źródła danych firmy.

1. W widoku kodu, Znajdź `parameters : {}` obiektu, a następnie dodaj `currentFeedUrl` obiektu:

   ``` json
   "currentFeedUrl" : {
      "type" : "string",
      "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. W `When_a_feed-item_is_published` akcji, Znajdź `queries` sekcji i Zastąp wartości zapytania z `"feedUrl": "#@{parameters('currentFeedUrl')}"`.

   **Przed**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },
   ```

   **Po**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },
   ```

   Aby przyłączyć się dwa lub więcej ciągów, można również użyć `concat` funkcji. 
   Na przykład `"@concat('#',parameters('currentFeedUrl'))"` działa tak samo jak w poprzednim przykładzie.

3.  Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

Teraz możesz zmienić witryny sieci Web źródła danych RSS, przekazując innego adresu URL za pośrednictwem `currentFeedURL` obiektu.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Parametry wdrożenia w różnych środowiskach

Cykle życia wdrożenia mają zwykle, środowisk na potrzeby programowania, przemieszczania i produkcji. Na przykład może używać tej samej definicji aplikacji logiki w tych środowiskach, ale korzystanie z różnych baz danych. Podobnie można użyć tej samej definicji w różnych regionach, wysokiej dostępności, ale ma każde wystąpienie aplikacji logiki do używania bazy danych w tym regionie.

> [!NOTE]
> W tym scenariuszu, który różni się od przełączania parametrów w *środowiska uruchomieniowego* gdzie należy używać `trigger()` zamiast tego funkcji.

Poniżej przedstawiono podstawową definicję:

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
W rzeczywistym `PUT` żądania w przypadku usługi logic apps, możesz podać parametr `uri`. W każdym środowisku, można podać inną wartość dla `connection` parametru. Wartość domyślna już nie istnieje, ten parametr wymaga ładunku aplikacji logiki:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
```

Aby dowiedzieć się więcej, zobacz [interfejsu API REST dla usługi Azure Logic Apps dokumentacji](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Ciągi procesu przy użyciu funkcji

Usługa Logic Apps zawiera różne funkcje do pracy z ciągami. Na przykład załóżmy, że chcesz przekazać nazwę firmy zamówienia w innym systemie. Jednak nie wiadomo o właściwe obsługę kodowania znaków. Można wykonać kodowanie base64 na te parametry, aby uniknąć sekwencje ucieczki w adresie URL, można jednak zastąpić kilku znaków zamiast tego. Ponadto wystarczy tylko podciąg dla nazwy firmy ponieważ pięć pierwszych znaków nie są używane.

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

Te kroki opisują, jak w tym przykładzie przetwarza ten ciąg pracy od środka na zewnątrz:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Pobierz [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) dla nazwy firmy, dlatego otrzymujesz całkowita liczba znaków.

2. Aby uzyskać krótszy ciąg, Odejmij `5`.

3. Teraz [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Rozpocznij od indeksu `5`, a następnie przejdź do końca ciągu.

4. Ta podciąg, który należy przekonwertować [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) ciągu.

5. Teraz [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) wszystkich `+` znaków z `-` znaków.

6. Na koniec [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) wszystkich `/` znaków z `_` znaków.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapowanie elementów listy wartości właściwości, a następnie użyć mapy jako parametrów

Aby uzyskać różne wyniki na podstawie wartości właściwości, możesz utworzyć mapę odpowiadającą każdej wartości właściwości do wyniku, a następnie użyć tej mapy jako parametr.

Na przykład ten przepływ pracy określa niektóre kategorie jako parametry i mapę, która pasuje do tych kategorii określonych adresów URL. Po pierwsze przepływ pracy pobiera listę artykułów. Następnie przepływ pracy używa mapy, aby znaleźć adres URL dopasowywania kategorii dla każdego artykułu.

*   [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) Funkcja sprawdza, czy pasuje do znanego kategorii zdefiniowanych kategorii.

*   Po otrzymaniu odpowiadającej kategorii, przykład pobiera element z mapy przy użyciu nawiasami kwadratowymi: `parameters[...]`

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

## <a name="get-data-with-date-functions"></a>Pobieranie danych za pomocą funkcji daty

Można pobrać danych ze źródła danych, która nie obsługuje natywnie *wyzwalaczy*, możesz użyć daty funkcje do pracy z godziny i daty w zamian. Na przykład, to wyrażenie znajduje, jak długo kroki opisane w tym przepływie pracy są tworzone, pracy z poziomu wewnętrznej na zewnątrz:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Z `order` akcji, wyodrębnianie `startTime`.
2. Pobierz bieżący czas z `utcNow()`.
3. Odejmij sekundy:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Można użyć innych jednostkach czasu, takie jak `minutes` lub `hours`.

3. Teraz możesz porównać te dwie wartości. 

   Jeśli pierwsza wartość jest mniejsza od drugiej wartości, a następnie więcej niż jedna sekunda minęło od zamówienia najpierw zakończyło się.

Aby sformatować daty, można użyć ciągów formatujących. Na przykład, aby uzyskać RFC1123, użyj [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). Dowiedz się więcej o [daty, formatowanie](../logic-apps/logic-apps-workflow-definition-language.md).

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

## <a name="next-steps"></a>Kolejne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i wykonaj ponownie kroki (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania równoległymi krokami (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Dowiedz się więcej o [schemat języka definicji przepływu pracy dla usługi Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Dowiedz się więcej o [Wyzwalacze dla usługi Azure Logic Apps i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md)
