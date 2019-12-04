---
title: Aktualizacje schematu dla wersji zapoznawczej sierpnia 1-2015
description: Zaktualizowano wersję schematu 2015-08-01-Preview dla definicji aplikacji logiki w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792842"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizacje schematu dla Azure Logic Apps — 1 sierpnia 2015 wersja zapoznawcza

Ten schemat i wersja interfejsu API dla Azure Logic Apps obejmują kluczowe udoskonalenia, dzięki którym Aplikacje logiki są bardziej niezawodne i łatwiejsze w użyciu:

* Typ akcji **APIApp** ma teraz nazwę [**APIConnection**](#api-connections).
* Akcja **powtarzania** jest teraz nazywana [**foreach**](#foreach).
* [Aplikacja interfejsu API **odbiornika http** ](#http-listener) nie jest już wymagana.
* Wywoływanie podrzędnych przepływów pracy używa [nowego schematu](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Przenieś do połączeń interfejsu API

Największą zmianą jest to, że nie trzeba już wdrażać API Apps w ramach subskrypcji platformy Azure, aby można było używać interfejsów API. Poniżej przedstawiono sposoby używania interfejsów API:

* Zarządzane interfejsy API
* Niestandardowe interfejsy API sieci Web

Każdy sposób jest obsługiwany nieco inaczej, ponieważ ich modele zarządzania i hostingu są różne. Jedną z zalet tego modelu jest już ograniczenie zasobów wdrożonych w grupie zasobów platformy Azure. 

### <a name="managed-apis"></a>Zarządzane interfejsy API

Firma Microsoft zarządza niektórymi interfejsami API w Twoim imieniu, takimi jak Office 365, Salesforce, Twitter i FTP. Niektóre zarządzane interfejsy API można używać w taki sam sposób, jak w przypadku funkcji tłumaczenia Bing, a inne wymagają konfiguracji, nazywanej również *połączeniem*.

Na przykład w przypadku korzystania z pakietu Office 365 należy utworzyć połączenie zawierające token logowania pakietu Office 365. Token jest bezpiecznie przechowywany i odświeżany, dzięki czemu aplikacja logiki zawsze może wywoływać interfejs API pakietu Office 365. Jeśli chcesz nawiązać połączenie z serwerem SQL lub FTP, musisz utworzyć połączenie z parametrami połączenia. 

W tej definicji te działania są nazywane `APIConnection`. Oto przykład połączenia, które wywołuje pakiet Office 365 w celu wysłania wiadomości e-mail:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

Obiekt `host` jest częścią danych wejściowych, które są unikatowe dla połączeń interfejsu API i zawiera te części: `api` i `connection`. Obiekt `api` określa adres URL środowiska uruchomieniowego dla miejsca, w którym jest hostowany zarządzany interfejs API. Aby wyświetlić wszystkie dostępne zarządzane interfejsy API, należy wywołać tę metodę:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

W przypadku korzystania z interfejsu API, ten interfejs API może lub nie zdefiniował żadnych *parametrów połączenia*. Dlatego, jeśli interfejs API nie definiuje tych parametrów, połączenie nie jest wymagane. Jeśli interfejs API definiuje te parametry, należy utworzyć połączenie z określoną nazwą.  
Następnie należy odwołać się do tej nazwy w obiekcie `connection` wewnątrz obiektu `host`. Aby utworzyć połączenie w grupie zasobów, Wywołaj tę metodę:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Z następującą treścią:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Wdrażanie zarządzanych interfejsów API w szablonie Azure Resource Manager

Gdy logowanie interaktywne nie jest wymagane, można utworzyć pełną aplikację przy użyciu szablonu Menedżer zasobów.
Jeśli jest wymagane logowanie, nadal możesz użyć szablonu Menedżer zasobów, ale musisz autoryzować połączenia za pomocą Azure Portal. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

W tym przykładzie można zobaczyć, że połączenia są tylko zasobami, które znajdują się na żywo w grupie zasobów. Odwołują się do zarządzanych interfejsów API dostępnych w ramach subskrypcji.

### <a name="your-custom-web-apis"></a>Niestandardowe interfejsy API sieci Web

Jeśli używasz własnych interfejsów API, a nie zarządzanych przez firmę Microsoft, użyj wbudowanej akcji **http** do wywoływania interfejsów API. Najlepszym rozwiązaniem jest zapewnienie punktu końcowego struktury Swagger dla interfejsu API. Ten punkt końcowy ułatwia projektantowi aplikacji logiki wyświetlanie danych wejściowych i wyjściowych interfejsu API. Bez punktu końcowego struktury Swagger Projektant może wyświetlać tylko dane wejściowe i wyjściowe jako nieprzezroczyste obiekty JSON.

Oto przykład przedstawiający nową właściwość `metadata.apiDefinitionUrl`:

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Jeśli interfejs API sieci Web jest hostowany na Azure App Service, internetowy interfejs API zostanie automatycznie wyświetlony na liście akcji dostępnych w projektancie. Jeśli nie, musisz bezpośrednio wkleić adres URL. Punkt końcowy struktury Swagger musi być nieuwierzytelniony, aby można go było używać w Projektancie aplikacji logiki, chociaż można zabezpieczyć interfejs API za pomocą dowolnych metod obsługiwanych przez strukturę Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Wywołaj wdrożone aplikacje interfejsu API z 2015-08-01 — wersja zapoznawcza

Jeśli wcześniej wdrożono aplikację interfejsu API, można wywołać tę aplikację za pomocą akcji **http** .
Na przykład jeśli używasz usługi Dropbox do wyświetlania plików, definicja wersji schematu **2014-12-01-Preview** może wyglądać następująco:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Teraz możesz teraz utworzyć podobną akcję HTTP i pozostawić sekcję `parameters` definicji aplikacji logiki bez zmian, na przykład:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Przechodzenie między tymi właściwościami jeden-do-jednego:

| Akcja — Właściwość | Opis |
| --- | --- |
| `type` | `Http` zamiast `APIapp` |
| `metadata.apiDefinitionUrl` | Aby użyć tej akcji w Projektancie aplikacji logiki, Dołącz punkt końcowy metadanych, który jest zbudowany z: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Skonstruowane z: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Zawsze `POST` |
| `inputs.body` | Analogicznie jak parametry aplikacji interfejsu API |
| `inputs.authentication` | Analogicznie jak uwierzytelnianie aplikacji interfejsu API |

Takie podejście powinno współpracować ze wszystkimi akcjami aplikacji interfejsu API. Należy jednak pamiętać, że te poprzednie API Apps nie są już obsługiwane. W związku z tym należy przejść do jednej z dwóch pozostałych opcji, zarządzanego interfejsu API lub hostowania niestandardowego interfejsu API sieci Web.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Zmieniono nazwę "Repeat" na "foreach"

W przypadku poprzedniej wersji schematu firma Microsoft otrzymała wiele opinii od klientów, że Nazwa akcji **powtarzania** była myląca i nieprawidłowo przechwytuje, że **powtarzające** się to dla każdej pętli. Dlatego zmieniono nazwę `repeat` na `foreach`. Wcześniej napiszesz tę akcję, taką jak w tym przykładzie:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Teraz napiszesz tę wersję zamiast:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Ponadto zmieniono nazwę `item()`funkcji `repeatItem()`, która odwołuje się do elementu, który jest przetwarzany przez pętlę podczas bieżącej iteracji. 

### <a name="reference-outputs-from-foreach"></a>Odwołania do danych wyjściowych z instrukcji "foreach"

Dla uproszczenia dane wyjściowe z akcji `foreach` nie są już zawijane w obiekcie o nazwie `repeatItems`. Ponadto po wprowadzeniu tych zmian funkcje `repeatItem()`, `repeatBody()`i `repeatOutputs()` są usuwane.

Dlatego przy użyciu poprzedniego `repeat` przykładu uzyskasz następujące dane wyjściowe:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Teraz uzyskasz następujące dane wyjściowe:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Wcześniej, aby uzyskać `body` z akcji podczas odwoływania się do tych danych wyjściowych:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Teraz można użyć tej wersji:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Natywny odbiornik HTTP

Funkcje odbiornika HTTP są teraz wbudowane, więc nie trzeba wdrażać aplikacji interfejsu API odbiornika HTTP. Aby uzyskać więcej informacji, Dowiedz się, jak nawiązać [żądanie punktu końcowego aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). 

Po wprowadzeniu tych zmian Logic Apps zastępuje funkcję `@accessKeys()` funkcją `@listCallbackURL()`, która pobiera punkt końcowy w razie potrzeby. Ponadto należy zdefiniować co najmniej jeden wyzwalacz w aplikacji logiki. Jeśli chcesz `/run` przepływ pracy, musisz użyć jednego z tych typów wyzwalaczy: `Manual`, `ApiConnectionWebhook`lub `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Wywołaj podrzędne przepływy pracy

Wcześniej wywołując podrzędne przepływy pracy wymagające przechodzenia do przepływu pracy, uzyskiwania tokenu dostępu i wklejania tokenu w definicji aplikacji logiki, w której chcesz wywołać ten podrzędny przepływ pracy. W tym schemacie aparat Logic Apps automatycznie generuje sygnaturę dostępu współdzielonego w czasie wykonywania dla podrzędnego przepływu pracy, dzięki czemu nie trzeba wklejać żadnych wpisów tajnych do definicji. Oto przykład:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Ponadto podrzędne przepływy pracy uzyskują pełny dostęp do żądania przychodzącego. Dlatego można przekazać parametry w sekcji `queries` i w obiekcie `headers`. Możesz również w pełni zdefiniować całą sekcję `body`.

Na koniec podrzędne przepływy pracy mają te wymagane zmiany. Chociaż można wcześniej i bezpośrednio wywołać podrzędny przepływ pracy, musisz teraz zdefiniować punkt końcowy wyzwalacza w przepływie pracy dla elementu nadrzędnego, który ma zostać wywołany. Ogólnie rzecz biorąc, należy dodać wyzwalacz, który ma typ `Manual`, a następnie użyć tego wyzwalacza w definicji nadrzędnej. Właściwość `host` ma szczególnie `triggerName`, ponieważ należy zawsze określić wyzwalacz, który jest wywoływany.

## <a name="other-changes"></a>Inne zmiany

### <a name="new-queries-property"></a>Nowa właściwość "zapytania"

Wszystkie typy akcji obsługują teraz nowe dane wejściowe o nazwie `queries`. Ta wartość wejściowa może być obiektem strukturalnym, a nie koniecznością podawania ciągu.

### <a name="renamed-parse-function-to-json"></a>Zmieniono nazwę funkcji "Parse ()" na "JSON ()"

Funkcja `parse()` ma teraz nazwę funkcji `json()` dla przyszłych typów zawartości.

## <a name="enterprise-integration-apis"></a>Interfejsy API Integracja dla przedsiębiorstw

Ten schemat nie obsługuje jeszcze zarządzanych wersji interfejsów API Integracja dla przedsiębiorstw, takich jak AS2. Można jednak używać istniejących wdrożonych interfejsów API BizTalk za pośrednictwem akcji HTTP. Aby uzyskać więcej informacji, zobacz sekcję "Używanie już wdrożonych aplikacji interfejsu API" w [przewodniku integracji](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
