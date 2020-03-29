---
title: Aktualizacje schematu dla wersji zapoznawczej sierpień-1-2015
description: Zaktualizowano wersję schematu 2015-08-01-preview dla definicji aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792842"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizacje schematu dla aplikacji logiki azure — wersja zapoznawcza z 1 sierpnia 2015 r.

Ten schemat i wersja interfejsu API dla usługi Azure Logic Apps zawiera kluczowe ulepszenia, które sprawiają, że aplikacje logiki są bardziej niezawodne i łatwiejsze w użyciu:

* Typ akcji **APIApp** nosi teraz nazwę [**APIConnection**](#api-connections).
* Akcja **Powtórz** ma teraz nazwę [**Foreach**](#foreach).
* Aplikacja interfejsu API [ **odbiornika HTTP** ](#http-listener) nie jest już wymagana.
* Wywoływanie podrzędnych przepływów pracy używa [nowego schematu](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Przechodzenie do połączeń interfejsu API

Największą zmianą jest to, że nie trzeba już wdrażać aplikacji interfejsu API w ramach subskrypcji platformy Azure, dzięki czemu można używać interfejsów API. Oto sposoby używania interfejsów API:

* Zarządzane interfejsy API
* Niestandardowe interfejsy API sieci Web

Każdy sposób jest traktowany nieco inaczej, ponieważ ich modele zarządzania i hostingu są różne. Jedną z zalet tego modelu jest nie jesteś już ograniczona do zasobów, które są wdrażane w grupie zasobów platformy Azure. 

### <a name="managed-apis"></a>Zarządzane interfejsy API

Firma Microsoft zarządza niektórymi interfejsami API w Twoim imieniu, takimi jak Office 365, Salesforce, Twitter i FTP. Można użyć niektórych zarządzanych interfejsów API w stanie takim, w jakim jest, takich jak Tłumacz Bing, podczas gdy inne wymagają konfiguracji, zwanej również *połączeniem*.

Na przykład podczas korzystania z usługi Office 365 należy utworzyć połączenie, które zawiera token logowania usługi Office 365. Token jest bezpiecznie przechowywany i odświeżany, dzięki czemu aplikacja logiki zawsze może wywołać interfejs API usługi Office 365. Jeśli chcesz połączyć się z serwerem SQL lub FTP, należy utworzyć połączenie, które ma parametry połączenia. 

W tej definicji te `APIConnection`akcje są nazywane . Oto przykład połączenia, które wywołuje w usłudze Office 365 wysyłanie wiadomości e-mail:

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

Obiekt `host` jest częścią danych wejściowych, która jest unikatowa dla połączeń `api` `connection`interfejsu API i zawiera następujące części: i . Obiekt `api` określa adres URL środowiska wykonawczego, w którym znajduje się zarządzany interfejs API. Wszystkie dostępne zarządzane interfejsy API można wyświetlić, wywołując tę metodę:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Podczas korzystania z interfejsu API ten interfejs API może lub nie został zdefiniowany przez *żadne parametry połączenia*. Tak więc jeśli interfejs API nie definiuje tych parametrów, nie jest wymagane połączenie. Jeśli interfejs API definiuje te parametry, należy utworzyć połączenie o określonej nazwie.  
Następnie odwołać się `connection` do tej `host` nazwy w obiekcie wewnątrz obiektu. Aby utworzyć połączenie w grupie zasobów, należy wywołać tę metodę:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Z następującym ciałem:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Wdrażanie zarządzanych interfejsów API w szablonie usługi Azure Resource Manager

Jeśli logowanie interaktywne nie jest wymagane, możesz utworzyć pełną aplikację przy użyciu szablonu Menedżera zasobów.
Jeśli logowanie jest wymagane, nadal można użyć szablonu Menedżera zasobów, ale musisz autoryzować połączenia za pośrednictwem witryny Azure portal. 

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

W tym przykładzie widać, że połączenia są tylko zasobami, które znajdują się w grupie zasobów. Odwołują się do zarządzanych interfejsów API dostępnych w ramach subskrypcji.

### <a name="your-custom-web-apis"></a>Niestandardowe interfejsy API sieci Web

Jeśli używasz własnych interfejsów API, a nie zarządzanych przez firmę Microsoft, użyj wbudowanej akcji **HTTP,** aby wywołać interfejsy API. W idealnym przypadku należy podać punkt końcowy Swagger dla interfejsu API. Ten punkt końcowy pomaga logic app designer pokazać dane wejściowe i wyjściowe interfejsu API. Bez punktu końcowego Swagger projektanta można wyświetlić tylko dane wejściowe i wyjściowe jako nieprzezroczyste obiekty JSON.

Oto przykład przedstawiający `metadata.apiDefinitionUrl` nową właściwość:

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

Jeśli hostujesz interfejs API sieci Web w usłudze Azure App Service, interfejs API sieci Web jest automatycznie wyświetlany na liście akcji dostępnych w projektancie. Jeśli nie, musisz wkleić bezpośrednio adres URL. Punkt końcowy Swagger musi być nieuwierzyfowany do zastosowania w logice App Designer, chociaż można zabezpieczyć samego interfejsu API za pomocą metod, które obsługuje Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Wywoływanie wdrożonych aplikacji interfejsu API z podglądem 2015-08-01

Jeśli wcześniej wdrożono aplikację interfejsu API, możesz wywołać tę aplikację za pomocą akcji **HTTP.**
Jeśli na przykład używasz Dropbox do wyświetlania plików, definicja wersji schematu **podglądu 2014-12-01** może mieć coś takiego:

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

Teraz możesz teraz utworzyć podobną akcję HTTP i pozostawić `parameters` sekcję definicji aplikacji logiki bez zmian, na przykład:

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

Przechodzenie przez te właściwości jeden po drugim:

| Właściwość akcji | Opis |
| --- | --- |
| `type` | `Http`Zamiast`APIapp` |
| `metadata.apiDefinitionUrl` | Aby użyć tej akcji w logice App Designer, obejmują punkt końcowy metadanych, który jest zbudowany z:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Wykonane z:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Zawsze`POST` |
| `inputs.body` | Tak samo jak parametry aplikacji interfejsu API |
| `inputs.authentication` | Tak samo jak uwierzytelnianie aplikacji interfejsu API |

Takie podejście powinno działać dla wszystkich akcji aplikacji interfejsu API. Należy jednak pamiętać, że te poprzednie aplikacje interfejsu API nie są już obsługiwane. Dlatego należy przejść do jednej z dwóch innych poprzednich opcji, zarządzanego interfejsu API lub hostowania niestandardowego interfejsu API sieci Web.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Zmieniono nazwę na "repeat" na "foreach"

W poprzedniej wersji schematu otrzymaliśmy wiele opinii klientów, że nazwa akcji **Powtórz** była myląca i nie poprawnie przechwyciła, że **repeat** był naprawdę dla każdej pętli. Tak, zmieniliśmy `repeat` `foreach`nazwę na . Wcześniej napisałeś tę akcję w tym przykładzie:

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

Teraz chcesz napisać tę wersję zamiast:

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

Ponadto `repeatItem()` funkcja, która odwołuje się do elementu, który przetwarza pętlę podczas bieżącej iteracji, jest teraz zmieniana `item()`. 

### <a name="reference-outputs-from-foreach"></a>Wyjścia referencyjne z 'foreach'

W celu uproszczenia `foreach` dane wyjściowe z akcji `repeatItems`nie są już zawijane w obiekt o nazwie . Ponadto, dzięki tym `repeatItem()`zmianom, , `repeatBody()`i `repeatOutputs()` funkcje są usuwane.

Tak więc, `repeat` korzystając z poprzedniego przykładu, można uzyskać następujące dane wyjściowe:

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

Teraz zamiast tego otrzymujesz następujące wyjścia:

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

Wcześniej, aby uzyskać `body` z akcji podczas odwoływania się do tych wyjść:

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

Teraz możesz użyć tej wersji:

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

Funkcje odbiornika HTTP są teraz wbudowane, więc nie trzeba wdrażać aplikacji interfejsu API odbiornika HTTP. Aby uzyskać więcej informacji, dowiedz się, jak [sprawić, aby punkt końcowy aplikacji logiki był wywoływany](../logic-apps/logic-apps-http-endpoint.md). 

Dzięki tym zmianom aplikacje `@accessKeys()` logiki `@listCallbackURL()` zastępuje funkcję z funkcją, która pobiera punkt końcowy, gdy jest to konieczne. Ponadto teraz należy zdefiniować co najmniej jeden wyzwalacz w aplikacji logiki. Aby przepływ `/run` pracy został uruchomiony, należy użyć jednego z `Manual` `ApiConnectionWebhook`następujących typów wyzwalaczy: , , lub`HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Wywoływanie podrzędnych przepływów pracy

Wcześniej wywoływanie podrzędnych przepływów pracy wymagało przechodzenia do przepływu pracy, uzyskiwania tokenu dostępu i wklejania tokenu w definicji aplikacji logiki, w którym chcesz wywołać ten podrzędny przepływ pracy. Za pomocą tego schematu aparat aplikacji logiki automatycznie generuje sygnatury dostępu Współdzielonego w czasie wykonywania dla przepływu pracy podrzędnej, dzięki czemu nie trzeba wklejać żadnych wpisów tajnych do definicji. Oto przykład:

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

Ponadto przepływy pracy podrzędnych uzyskać pełny dostęp do żądania przychodzącego. Tak, można przekazać parametry `queries` w sekcji `headers` i w obiekcie. Można również w pełni `body` zdefiniować całą sekcję.

Na koniec przepływy pracy podrzędne mają te wymagane zmiany. Chociaż można wcześniej i bezpośrednio wywołać podrzędny przepływ pracy, należy teraz zdefiniować punkt końcowy wyzwalacza w przepływie pracy dla obiektu nadrzędnego do wywołania. Ogólnie rzecz biorąc należy dodać `Manual` wyzwalacz, który ma typ, a następnie użyć tego wyzwalacza w definicji nadrzędnej. Właściwość `host` w szczególności `triggerName` ma, ponieważ należy zawsze określić wyzwalacz, który wywołujesz.

## <a name="other-changes"></a>Inne zmiany

### <a name="new-queries-property"></a>Nowa właściwość "zapytania"

Wszystkie typy akcji obsługują `queries`teraz nowe dane wejściowe o nazwie . To dane wejściowe mogą być obiektem strukturalnym, a nie koniecznością ręcznego składania ciągu.

### <a name="renamed-parse-function-to-json"></a>Zmieniono nazwę na "parse()" na 'json()'

Nazwa `parse()` funkcji jest teraz `json()` zmieniona na funkcję dla przyszłych typów zawartości.

## <a name="enterprise-integration-apis"></a>Interfejsy API integracji przedsiębiorstwa

Ten schemat nie obsługuje jeszcze wersji zarządzanych dla interfejsów API integracji przedsiębiorstwa, takich jak AS2. Można jednak użyć istniejących wdrożonych interfejsów API systemu BizTalk za pomocą akcji HTTP. Aby uzyskać więcej informacji, zobacz "Korzystanie z już wdrożonych aplikacji interfejsu API" w [planie integracji](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
