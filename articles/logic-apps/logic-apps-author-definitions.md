---
title: Tworzenie, edytowanie i rozszerzyć JSON logiki aplikacji definicje - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i dostosowywanie definicji aplikacji logiki w formacie JSON
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2018
ms.author: estfan; LADocs
ms.openlocfilehash: f37f600d001b110775d8ca0e78950e3b8743df82
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="create-edit-or-customize-json-for-logic-app-definitions"></a>Tworzenie, edytowanie i dostosować JSON definicji aplikacji logiki

Po utworzeniu enterprise rozwiązania do integracji z automatycznego przepływów pracy w [Azure Logic Apps](../logic-apps/logic-apps-overview.md), podstawowej definicji aplikacji logiki Użyj proste i deklaratywne JavaScript Object Notation (JSON) wraz z programem [ Przepływ pracy schema Definition Language Umieszczany](../logic-apps/logic-apps-workflow-definition-language.md) ich opisu i sprawdzania poprawności. Formaty te ułatwiają logiki definicji aplikacji przeczytane i zrozumiane bez wiedzy o te informacje o kod. Jeśli chcesz zautomatyzować tworzenie i wdrażanie aplikacji logiki, mogą zawierać definicji aplikacji logiki jako [zasobów Azure](../azure-resource-manager/resource-group-overview.md) wewnątrz [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment). Do tworzenia, zarządzania i wdrażania aplikacji logiki, można użyć [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), lub [interfejsów API usługi Azure Logic Apps REST](https://docs.microsoft.com/rest/api/logic/).

Aby pracować z definicjami aplikacji logiki w formacie JSON, Otwórz Edytor widoku kodu podczas pracy w portalu Azure lub w programie Visual Studio, lub skopiuj definicji w dowolnym edytorze, który ma. Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Niektóre możliwości usługi Azure Logic Apps, takie jak Definiowanie parametrów i skojarzyć wielu wyzwalaczy w definicji aplikacji logiki, są dostępne tylko w JSON, nie projektanta aplikacji logiki. Aby dla tych zadań, można pracować w widoku kodu lub w innym edytorze.

## <a name="edit-json---azure-portal"></a>Edytuj JSON - portalu Azure

1. Zaloguj się w <a href="https://portal.azure.com" target="_blank">Portalu Azure</a>.

2. Z menu po lewej stronie wybierz **wszystkie usługi**. W polu wyszukiwania Znajdź "aplikacje logiki", a następnie spośród wyników wybierz aplikację logiki.

3. W menu aplikacji logiki w obszarze **narzędzi programistycznych**, wybierz pozycję **widoku kodu aplikacji logiki**.

   Widok kodu edytora zostanie otwarty i zawiera definicję aplikacji logiki w formacie JSON.

## <a name="edit-json---visual-studio"></a>Edytuj JSON - programu Visual Studio

Przed rozpoczęciem pracy na definicję aplikacji logiki w programie Visual Studio, upewnij się, że znasz [zainstalowane wymagane narzędzia](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Aby utworzyć aplikację logiki z programem Visual Studio, przejrzyj [Szybki Start: automatyzacji zadań i procesów przy użyciu usługi Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

W programie Visual Studio możesz otworzyć aplikacji logiki, które zostały utworzone i wdrażane albo bezpośrednio z portalu Azure jako projektów usługi Azure Resource Manager w programie Visual Studio.

1. Otwórz rozwiązanie Visual Studio lub [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) projekt, który zawiera aplikację logiki.

2. Znajdowanie i otwieranie definicji aplikacji logiki, która domyślnie jest wyświetlana w [szablonu usługi Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment)nazwanego **LogicApp.json**. Można użyć i dostosować ten szablon do wdrożenia w różnych środowiskach.

3. Otwórz menu skrótów szablon i definicję aplikacji logiki. Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**.

   ![Aplikacja logiki otwarty w rozwiązaniu Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. W dolnej części projektanta, wybierz **widoku kodu**. 

   Widok kodu edytora zostanie otwarty i zawiera definicję aplikacji logiki w formacie JSON.

5. Aby powrócić do widoku projektanta w dolnej części edytora widoku kodu Wybierz **projekt**.

## <a name="parameters"></a>Parametry

Parametry umożliwia ponowne użycie wartości w całej aplikacji logiki i są odpowiednie do zastępowania wartości, które można zmienić często. Na przykład jeśli masz adres e-mail, która ma zostać użyta w wielu miejscach, należy zdefiniować tego adresu e-mail jako parametr. 

Parametry są także przydatne, gdy trzeba zastąpić parametry w różnych środowiskach, Dowiedz się więcej o [parametry w celu wdrożenia](#deployment-parameters) i [interfejsu API REST usługi Azure Logic Apps dokumentacji](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parametry są dostępne tylko w widoku kodu.

W [pierwszej aplikacji logiki przykład](../logic-apps/quickstart-create-first-logic-app-workflow.md), utworzyć przepływ pracy, który wysyła wiadomości e-mail, gdy pojawią się nowe ogłoszenia w kanału informacyjnego RSS witryny sieci Web. Adres URL źródła jest zapisane na stałe, dlatego w tym przykładzie pokazano, jak zastąpić wartość zapytania parametr, aby łatwiej można zmienić adres URL źródła.

1. W widoku kodu, Znajdź `parameters : {}` obiekt, a następnie dodaj `currentFeedUrl` obiektu:

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

   Aby przyłączyć się co najmniej dwa ciągi, umożliwia także `concat` funkcji. 
   Na przykład `"@concat('#',parameters('currentFeedUrl'))"` działa tak samo, co w poprzednim przykładzie.

3.  Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. 

Teraz możesz zmienić danych RSS witryny sieci Web przez przekazanie do innego adresu URL za pośrednictwem `currentFeedURL` obiektu.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Parametry wdrożenia dla różnych środowisk

Zwykle cykle wdrożenia mają środowiska programowania, tymczasową i produkcyjną. Na przykład może użyć tej samej definicji aplikacji logiki w tych środowiskach, ale Użyj różnych baz danych. Podobnie możesz użyć tej samej definicji w różnych regionach, wysokiej dostępności, ale mają każde wystąpienie aplikacji logiki do używania bazy danych w tym regionie. 

> [!NOTE] 
> W tym scenariuszu różni się od podejmowania parametrów w *środowiska uruchomieniowego* gdzie należy używać `trigger()` zamiast tego działania.

Poniżej przedstawiono podstawowe definicji:

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
W rzeczywistym `PUT` żądania dla aplikacji logiki możesz podać parametr `uri`. W każdym środowisku można podać inną wartość dla `connection` parametru. Ponieważ istnieje już wartość domyślną, ładunku aplikacji logiki wymaga tego parametru:

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

Aby dowiedzieć się więcej, zobacz [interfejsu API REST usługi Azure Logic Apps dokumentacji](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Funkcje ciągów procesu

Logic Apps ma funkcje służące do pracy z ciągami znaków. Na przykład załóżmy, że chcesz przekazać nazwę firmy z zamówienia do innego systemu. Jednak nie wiadomo o obsługę właściwe dla kodowania znaków. Można wykonać kodowania base64 na ciąg, aby uniknąć specjalne w adresie URL, można jednak zastąpić kilku znaków zamiast tego. Ponadto wystarczy podciąg nazwy firmy ponieważ pięć pierwszych znaków nie są używane. 

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
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Te kroki opisano, jak w tym przykładzie przetwarza tych parametrów, Praca z wewnątrz na zewnątrz:

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Pobierz [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) o nazwę firmy, dlatego możesz uzyskać całkowita liczba znaków.

2. Aby uzyskać krótszego ciągu, odejmuje wartość `5`.

3. Pobierz teraz [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Rozpocznij od indeksu `5`, a następnie przejdź do końca ciągu.

4. Konwertuj to podciąg do [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) ciąg.

5. Teraz [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) wszystkie `+` znaków i zawierają `-` znaków.

6. Na koniec [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) wszystkie `/` znaków i zawierają `_` znaków.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapy do wartości właściwości elementów listy, a następnie użyj mapy jako parametry

Aby uzyskać różne wyniki na podstawie wartości właściwości, można utworzyć mapę odpowiadającą każdej wartości właściwości wyniku, a następnie użyć tej mapy jako parametr. 

Na przykład ten przepływ pracy definiuje kilka kategorii jako parametry i mapowanie zgodny z tych kategorii z określonym adresem URL. Po pierwsze przepływ pracy pobiera listę artykułów. Następnie przepływ pracy używa mapy adres URL dopasowanie kategorii dla każdego artykułu.

*   [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) Funkcja sprawdza, czy kategoria zgodny znanej kategorii zdefiniowane.

*   Po otrzymaniu pasującą kategorię, przykładzie ściąga elementu z tablicy przy użyciu nawiasy kwadratowe: `parameters[...]`

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
        "science": "http://www.nasa.gov",
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
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
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

## <a name="get-data-with-date-functions"></a>Pobierz dane z funkcji daty

Można pobrać danych ze źródła danych, która nie obsługuje natywnie *wyzwalaczy*, można użyć daty funkcje do pracy z godziny i daty zamiast tego. Na przykład, wyrażenie znajduje, jak długo kroki tego przepływu pracy są tworzone, Praca z wewnątrz na zewnątrz:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Z `order` akcji, Wyodrębnij `startTime`. 
2. Pobierz bieżący czas z `utcNow()`.
3. Odejmowanie jednej sekundy:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Inne jednostki czasu, można używać tak samo, jak `minutes` lub `hours`. 

3. Teraz możesz porównać te dwie wartości. 

   Jeśli pierwsza wartość jest mniejsza niż wartość drugiej, a następnie więcej niż jednej sekundy są spełnione, ponieważ najpierw umieszczono kolejności.

Do formatowania daty, można użyć ciągu elementy formatujące. Na przykład, aby uzyskać RFC1123, należy użyć [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). Dowiedz się więcej o [Data formatowania](../logic-apps/logic-apps-workflow-definition-language.md).

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
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
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
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
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

* [Wykonanie kroków na podstawie warunku (warunkowe instrukcje)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonanie kroków na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania czynności równoległe (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonanie kroków na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Dowiedz się więcej o [schematu język definicji przepływu pracy dla usługi Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Dowiedz się więcej o [działania przepływu pracy i Wyzwalacze dla usługi Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)