---
title: Schemat aktualizuje sierpnia-1-2015 preview — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utworzenie definicji JSON dla usługi Azure Logic Apps z wersji schematu 2015-08-01-preview
author: stepsic-microsoft-com
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic; LADocs
ms.openlocfilehash: 736a7cf03c7fe1e9fe976c3bcc80393bff2bada5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299872"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizacje schematu dla usługi Azure Logic Apps - 1 sierpnia 2015 preview

Ten schemat i interfejsu API w wersji dla usługi Azure Logic Apps obejmuje klucza ulepszeniom aplikacje logiki bardziej niezawodne i łatwiejsze w:

* **APIApp** typ akcji ma teraz nazwę [ **APIConnection**](#api-connections).
* **Powtórz** akcji ma teraz nazwę [ **Foreach**](#foreach).
* [ **Odbiornik HTTP** aplikacji interfejsu API](#http-listener) nie jest już wymagane.
* Wywoływanie podrzędnego przepływy pracy używa [nowego schematu](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Przenieś do połączenia interfejsu API

Zmiana największych jest już nie trzeba wdrażać aplikacje interfejsu API w Twojej subskrypcji platformy Azure, aby mogli używać interfejsów API. Poniżej przedstawiono sposoby używania interfejsów API:

* Zarządzane interfejsy API
* Niestandardowych interfejsów API sieci Web

Każdego sposób różni się nieznacznie, ponieważ ich zarządzania i modele obsługi są różne. Jedną z zalet tego modelu jest jest już ograniczona do zasobów, które zostały wdrożone w grupie zasobów platformy Azure. 

### <a name="managed-apis"></a>Zarządzane interfejsy API

Firma Microsoft zarządza niektórych interfejsów API w Twoim imieniu, takie jak usługi Office 365, Salesforce, Twitter i FTP. Można użyć niektóre zarządzane interfejsy API jako-takich jak Bing tłumaczenia, a inne wymagają konfiguracji, skrót *połączenia*.

Na przykład gdy używasz usługi Office 365, możesz utworzyć połączenie, które zawiera token logowania usługi Office 365. Token są bezpiecznie przechowywane i odświeżenia, tak aby aplikację logiki zawsze można wywołać interfejsu API usługi Office 365. Jeśli chcesz się połączyć z serwerem SQL lub FTP, należy utworzyć połączenie, które ma parametry połączenia. 

W tej definicji, te akcje są nazywane `APIConnection`. Oto przykład połączenia, który wywołuje usługi Office 365, aby wysłać wiadomość e-mail:

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

`host` Obiektu jest częścią danych wejściowych jest unikatowy dla połączenia interfejsu API, która zawiera następujące części: `api` i `connection`. `api` Obiektu określa środowisko uruchomieniowe znajduje się adres URL, dla których, która zarządza interfejsu API. Widoczne wszystkie dostępne zarządzanych interfejsów API, wywołując `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Jeśli korzystasz z interfejsu API tego interfejsu API może lub może nie zdefiniowano żadnego *parametry połączenia*. Dlatego jeśli interfejs API nie definiowania tych parametrów, żadne połączenie nie jest wymagane. Jeśli interfejs API definiowania tych parametrów, należy utworzyć połączenie o określonej nazwie.  
Następnie odwołać tę nazwę w `connection` obiektów wewnątrz `host` obiektu. Aby utworzyć połączenie w grupie zasobów, należy wywołać tę metodę:

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

O następujących treści:

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

Pełna aplikacji można utworzyć w szablonie usługi Azure Resource Manager tak długo, jak interakcyjnego logowania nie jest wymagane.
Jeśli logowanie jest wymagana, można skonfigurować wszystko przy użyciu szablonu usługi Azure Resource Manager, ale nadal jest konieczne można znaleźć w portalu Azure w celu autoryzowania połączeń. 

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

Widać w tym przykładzie, że połączenia są tylko zasoby, które znajdują się w grupie zasobów. Odwołujących się do zarządzanych interfejsów API dostępne w Twojej subskrypcji.

### <a name="your-custom-web-apis"></a>Niestandardowych interfejsów API sieci Web

Jeśli używasz własnych interfejsów API, z nich nie zarządzany przez firmę Microsoft, użyj wbudowanej **HTTP** akcji do wywołania. Nadaje się doskonale środowisko powinny ujawniać punktu końcowego struktury Swagger dla interfejsu API. Ten punkt końcowy włącza projektanta aplikacji logiki do renderowania danych wejściowych i danych wyjściowych dla interfejsu API. Bez struktury Swagger Projektant można tylko wyświetlić wejściami i wyjściami jako nieprzezroczyste obiektów JSON.

Oto przykład przedstawiający nowy `metadata.apiDefinitionUrl` właściwości:

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

Jeśli na serwerze sieci Web interfejsu API w usłudze Azure App Service, interfejs API sieci Web automatycznie zostanie wyświetlony na liście Akcje dostępne w projektancie. Jeśli nie masz wkleić bezpośrednio w adresie URL. Musi być nieuwierzytelniony punktu końcowego struktury Swagger może być używany w Projektancie aplikacji logiki, mimo że można zabezpieczyć API z dowolnej metody, które obsługuje struktury Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Wywołaj wdrożonej aplikacji interfejsu API za pomocą 2015-08-01-preview

Jeśli wdrożono aplikację interfejsu API można wywołać tej aplikacji za pomocą **HTTP** akcji.
Na przykład, jeśli używasz usługi Dropbox Aby wyświetlić listę plików z **2014-12-01-preview** definicji wersji schematu może mieć wyglądać mniej więcej tak:

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

Teraz można teraz utworzyć równoważne akcji HTTP, jak w następującym przykładzie, pozostawiając definicję aplikacji logiki można znaleźć w sekcji parametrów:

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

Instruktaż te właściwości jeden po drugim:

| Właściwość Action | Opis |
| --- | --- |
| `type` | `Http` Zamiast `APIapp` |
| `metadata.apiDefinitionUrl` | Aby użyć tej akcji w Projektancie aplikacji logiki, obejmują punktu końcowego metadanych, który jest tworzony z: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Utworzone na podstawie: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Zawsze `POST` |
| `inputs.body` | Takie same jak parametry aplikacji interfejsu API |
| `inputs.authentication` | Taki sam jak uwierzytelniania aplikacji interfejsu API |

Ta metoda powinna działać dla wszystkich akcji w aplikacji interfejsu API. Należy jednak pamiętać, że tych poprzedniej aplikacji interfejsu API nie są już obsługiwane. Dlatego należy przenieść do jednej z dwóch innych poprzednie opcje zarządzanego interfejsu API lub hosting niestandardowego interfejsu API sieci Web.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Zmieniona "repeat" do "foreach"

W poprzedniej wersji schematu Otrzymaliśmy dużo opinie klientów który **Powtórz** nazwy akcji została mylące i poprawnie nie przechwytywania, który **Powtórz** jest rzeczywiście dla każdej pętli. Tak, możemy zmienić nazwy `repeat` do `foreach`. Czy wcześniej zapisać tej akcji, takich jak w tym przykładzie:

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

Teraz może zapisać tej wersji zamiast tego:

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

Ponadto `repeatItem()` funkcji, która odwołuje się do elementu, który przetwarza podczas bieżącej iteracji pętli, Nowa nazwa `item()`. 

### <a name="reference-outputs-from-foreach"></a>Odwołanie do danych wyjściowych ze "foreach"

Dla uproszczenia, dane wyjściowe z `foreach` akcje nie są ujęte w obiekt o nazwie `repeatItems`. Ponadto z tych zmian `repeatItem()`, `repeatBody()`, i `repeatOutputs()` funkcje zostały usunięte.

Tak, przy użyciu poprzedniej `repeat` przykład pobrać te dane wyjściowe:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Teraz możesz uzyskać te dane wyjściowe zamiast tego:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Wcześniej Aby uzyskać `body` z akcji podczas odwoływania się do tych danych wyjściowych:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
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
         "uri": "http://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Natywny odbiornika HTTP

Odbiornik HTTP funkcji są obecnie wbudowane w. Dlatego nie należy wdrożyć aplikację interfejsu API odbiornika HTTP. Zobacz [szczegółowe instrukcje punkt końcowy aplikacji logiki można wywołać w tym miejscu](../logic-apps/logic-apps-http-endpoint.md). 

Wprowadzone zmiany, firma Microsoft usunęła `@accessKeys()` funkcji, która możemy zastąpione `@listCallbackURL()` funkcja uzyskania punktu końcowego, gdy jest to konieczne. Ponadto teraz należy zdefiniować co najmniej jeden wyzwalacz w aplikacji logiki. Jeśli chcesz `/run` przepływu pracy, musi mieć jeden z tych wyzwalaczy: `manual`, `apiConnectionWebhook`, lub `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Wywołania podrzędne przepływy pracy

Wcześniej wywoływania podrzędnego przepływów pracy wymagane do przepływu pracy, pobierania tokenu dostępu i wklejania tokenu w definicji aplikacji logiki, którym chcesz się połączyć podrzędny przepływ pracy. Z nowym schematem aparat Logic Apps automatycznie generuje SAS w czasie wykonywania przepływu pracy podrzędny, dzięki czemu nie trzeba Wklej żadnych kluczy tajnych w definicji. Oto przykład:

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

Drugi poprawy jest udostępniamy możliwość sprawowania podrzędne przepływy pracy pełny dostęp do żądania przychodzącego. Oznacza to, że można przekazać parametr w *zapytania* sekcji i w *nagłówki* obiekt i który pełni można zdefiniować całej treści.

Ponadto istnieją zmiany wymagane w celu podrzędny przepływ pracy. Gdy wcześniej można bezpośrednio wywołać podrzędny przepływ pracy, teraz należy zdefiniować punkt końcowy wyzwalacza w przepływie pracy nadrzędnego do wywołania. Ogólnie rzecz biorąc, należy dodać wyzwalacz, który ma `manual` typu, a następnie użyj tego wyzwalacza w definicji nadrzędnej. Uwaga `host` właściwość specjalnie ma `triggerName` ponieważ należy zawsze określić, której wyzwalacz wywoływania.

## <a name="other-changes"></a>Inne zmiany

### <a name="new-queries-property"></a>Nowe właściwości "zapytania"

Wszystkie typy akcji obsługują teraz nowe dane wejściowe o nazwie `queries`. Tych danych wejściowych może być obiektu strukturalnego, zamiast konieczności ręcznie utworzyć ciąg.

### <a name="renamed-parse-function-to-json"></a>Zmieniono nazwę parse() funkcji "json()"

Dodajemy więcej zawartości typy wkrótce, dlatego firma Microsoft zmieniona `parse()` funkcja `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Wkrótce: interfejsy API integracji przedsiębiorstwa

Nie mamy zarządzanej wersji jeszcze API integracji przedsiębiorstwa, takich jak AS2. W tym samym czasie możesz użyć istniejących wdrożonej BizTalk interfejsów API za pomocą akcji HTTP. Aby uzyskać więcej informacji, zobacz "Używanie już raz wdrożonej aplikacji interfejsu API" w [plan integracji](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
