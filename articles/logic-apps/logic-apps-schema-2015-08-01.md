---
title: Aktualizacje schematu dla sierpnia-1-2015 (wersja zapoznawcza) — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zaktualizowano schemat wersja 2015-08-01-preview dla definicji aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: stepsic-microsoft-com
ms.author: stepsic
ms.reviewer: klam, estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: dd05543c2a727f010432ecb54c2dc3e77a245de4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122781"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizacje schematu dla usługi Azure Logic Apps — 1 sierpnia 2015 r. (wersja zapoznawcza)

Ten schemat i interfejsu API wersji dla usługi Azure Logic Apps zawiera najważniejsze ulepszenia, które Twórz aplikacje logiki, bardziej niezawodne i łatwiejsze w użyciu:

* **APIApp** typ akcji nosi teraz nazwę [ **APIConnection**](#api-connections).
* **Powtórz** akcji nosi teraz nazwę [ **Foreach**](#foreach).
* [ **Odbiornika HTTP** aplikacji interfejsu API](#http-listener) nie jest już wymagane.
* Wywoływanie podrzędnego przepływy pracy używa [nowego schematu](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Przenieś do połączenia interfejsu API

Zmiana największych jest, że nie trzeba wdrażać aplikacje interfejsu API do subskrypcji platformy Azure, dzięki czemu mogą używać interfejsów API. Poniżej przedstawiono sposób, że mogą używać interfejsów API:

* Zarządzanych interfejsów API
* Niestandardowe interfejsy API sieci Web

Każdy sposób odbywa się nieco inaczej, ponieważ zarządzanie nimi i modele obsługi są różne. Jedną z zalet tego modelu jest one już ograniczone do zasobów, które są wdrożone w grupie zasobów platformy Azure. 

### <a name="managed-apis"></a>Zarządzanych interfejsów API

Firma Microsoft zarządza niektóre interfejsy API w Twoim imieniu, takich jak usługi Office 365, Salesforce, Twitter i FTP. Możesz użyć niektórych zarządzanych interfejsów API jako — takich jak Bing, tłumaczenie, podczas gdy inni wymagają konfiguracji, nazywa się również *połączenia*.

Na przykład gdy używasz usługi Office 365, należy utworzyć połączenie, które zawiera token logowania usługi Office 365. Twój token są bezpiecznie przechowywane i odświeżane, dzięki czemu Twoja aplikacja logiki zawsze można wywołać interfejsu API usługi Office 365. Jeśli chcesz połączyć się z serwerem bazy danych SQL lub FTP, należy utworzyć połączenie, które ma parametry połączenia. 

W tej definicji, te działania są nazywane `APIConnection`. Oto przykład połączenia, który wywołuje usługi Office 365, aby wysłać wiadomość e-mail:

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

`host` Obiekt jest częścią danych wejściowych, jest unikatowy dla połączenia interfejsu API, która zawiera następujące segmenty: `api` i `connection`. `api` Obiekt określa środowisko uruchomieniowe znajduje się adres URL, dla których, zarządzać interfejsu API. Możesz zobaczyć wszystkie dostępne interfejsy API zarządzane przez przez wywołanie metody `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Korzystając z interfejsu API tego interfejsu API może być lub może nie zdefiniowano żadnego *parametry połączenia*. Tak Jeśli interfejs API nie zdefiniowano tych parametrów, połączenie nie jest wymagane. Jeśli interfejs API definiowania tych parametrów, należy utworzyć połączenie z określoną nazwą.  
Następnie odwoływać się tą nazwą w `connection` wewnątrz `host` obiektu. Aby utworzyć połączenie w grupie zasobów, należy wywołać tej metody:

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Następujące jednostki:

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

W szablonie usługi Azure Resource Manager można utworzyć pełnej aplikacji, tak długo, jak interakcyjnego logowania nie jest wymagane.
Jeśli logowania jest wymagane, możesz skonfigurować wszystko, co przy użyciu szablonu usługi Azure Resource Manager, ale nadal jest konieczne można znaleźć w portalu Azure w celu autoryzowania połączeń. 

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

Widać, w tym przykładzie, że połączenia są tylko zasoby, które znajdują się w grupie zasobów. Mogą odwoływać się do zarządzanych interfejsów API dostępne w Twojej subskrypcji.

### <a name="your-custom-web-apis"></a>Niestandardowe interfejsy API sieci Web

Jeśli używasz własnych interfejsów API, a nie zarządzanych przez firmę Microsoft z nich, Użyj wbudowanych **HTTP** akcji do wywołania. Idealne środowisko pracy należy udostępnić punktu końcowego struktury Swagger dla interfejsu API. Ten punkt końcowy umożliwia Projektant aplikacji logiki do renderowania danych wejściowych i danych wyjściowych dla interfejsu API. Bez struktury Swagger projektanta można wyświetlić tylko dane wejściowe i wyjściowe jako nieprzezroczysty obiektów JSON.

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

Jeśli hostujesz internetowego interfejsu API w usłudze Azure App Service, internetowy interfejs API automatycznie pojawia się na liście akcji dostępnych w projektancie. W przeciwnym razie trzeba wkleić w adresie URL bezpośrednio. Punktu końcowego struktury Swagger muszą nieuwierzytelniony można używać w Projektancie aplikacji logiki, mimo że można zabezpieczyć API za pomocą dowolnych metod, które obsługuje struktury Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Wywoływanie wdrożonej aplikacji interfejsu API za pomocą 2015-08-01-preview

Jeśli wcześniej wdrożono aplikację interfejsu API, można wywołać tej aplikacji za pomocą **HTTP** akcji.
Na przykład, jeśli używasz usługi Dropbox, aby wyświetlić listę plików usługi **2014-12-01-preview** definicji wersji schematu może być mniej więcej tak:

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

Teraz możesz teraz utworzyć równoważne Akcja HTTP, jak w poniższym przykładzie, pozostawiając sekcji parametrów dla definicji aplikacji logiki:

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

Zapoznaniu się z tymi właściwościami jeden po drugim:

| Właściwości akcji | Opis |
| --- | --- |
| `type` | `Http` Zamiast `APIapp` |
| `metadata.apiDefinitionUrl` | Aby użyć tej akcji w Projektancie aplikacji logiki, obejmują punkt końcowy metadanych, które są konstruowane na podstawie: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Skonstruowany na podstawie: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | zawsze `POST` |
| `inputs.body` | Taka sama jak parametry aplikacji interfejsu API |
| `inputs.authentication` | Taka sama jak uwierzytelniania aplikacji interfejsu API |

Ta metoda powinna działać dla wszystkich akcji w aplikacji interfejsu API. Należy jednak pamiętać, że tych poprzedniej aplikacji interfejsu API nie są już obsługiwane. Dlatego należy przenieść się do jednego z dwóch innych poprzedniej opcji, zarządzany interfejs API lub hostowanie niestandardowego interfejsu API sieci Web.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Zmieniono nazwę elementu "repeat" na "foreach"

W poprzedniej wersji schematu, Otrzymaliśmy wiele opinii klientów, **Powtórz** nazwy akcji był kłopotliwy i przechwyciła prawidłowo, który **Powtórz** była rzeczywiście zmiana sposobu pętli for-each. Tak, możemy zmienić nazwy `repeat` do `foreach`. Wcześniej należy napisać tej akcji, takich jak w tym przykładzie:

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

Możesz teraz zamiast napisać tej wersji:

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

Ponadto `repeatItem()` funkcji, do których odwołuje się element, który przetwarza podczas bieżącej iteracji pętli, została zmieniona `item()`. 

### <a name="reference-outputs-from-foreach"></a>Odwołania danych wyjściowych ze "foreach"

Dla uproszczenia, dane wyjściowe z `foreach` akcje nie są już są zapakowane w obiekt o nazwie `repeatItems`. Ponadto za pomocą tych zmian `repeatItem()`, `repeatBody()`, i `repeatOutputs()` funkcje są usuwane.

Tak, przy użyciu poprzedniego `repeat` przykład pobrać te dane wyjściowe:

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

## <a name="native-http-listener"></a>Natywne odbiornika HTTP

Możliwości odbiornika HTTP są teraz wbudowane. Więc nie potrzebujesz już możliwość wdrożenia aplikacji interfejsu API odbiornika HTTP. Zobacz [pełne szczegóły jak punkt końcowy aplikacji logiki wywoływane tutaj](../logic-apps/logic-apps-http-endpoint.md). 

Za pomocą tych zmian, firma Microsoft usunęła `@accessKeys()` funkcji, która zastąpiliśmy z `@listCallbackURL()` funkcji w celu uzyskania punktu końcowego, gdy jest to konieczne. Ponadto teraz należy zdefiniować co najmniej jeden wyzwalacz w aplikacji logiki. Jeśli chcesz `/run` przepływu pracy, musi mieć jedną z tych wyzwalaczy: `manual`, `apiConnectionWebhook`, lub `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Wywoływanie podrzędnego przepływów pracy

Wcześniej wywoływania podrzędnego przepływów pracy wymagane, przechodząc do przepływu pracy, uzyskiwanie tokenu dostępu i wklejając token w definicji aplikacji logiki, które chcesz wywołać ten podrzędny przepływ pracy. Przy użyciu nowego schematu aparat usługi Logic Apps automatycznie generuje sygnaturę dostępu Współdzielonego w czasie wykonywania dla podrzędny przepływ pracy, więc nie trzeba wkleić jakichkolwiek kluczy tajnych w definicji. Oto przykład:

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

Drugi poprawy jest udostępniamy możliwość podrzędne przepływy pracy pełny dostęp do żądania przychodzącego. Oznacza to, że można przekazać parametry w *zapytania* sekcji i w *nagłówki* obiektu i czy można zdefiniować w pełni całej treści.

Ponadto istnieją zmiany wymagane w celu podrzędny przepływ pracy. Gdy wcześniej można bezpośrednio wywoływać podrzędny przepływ pracy, teraz należy zdefiniować punkt końcowy wyzwalacza w przepływie pracy nadrzędnego do wywołania. Ogólnie rzecz biorąc, należy dodać wyzwalacza, który ma `manual` typu, a następnie użyj tego wyzwalacza w definicji nadrzędnej. Uwaga `host` właściwość specjalnie ma `triggerName` ponieważ należy zawsze określić, której wyzwalacz wywoływania.

## <a name="other-changes"></a>Inne zmiany

### <a name="new-queries-property"></a>Nowa właściwość "zapytania"

Wszystkie typy akcji obsługują teraz nowe dane wejściowe o nazwie `queries`. Te dane wejściowe mogą być obiektów ze strukturą, zamiast konieczności ręcznie utworzyć ciąg.

### <a name="renamed-parse-function-to-json"></a>Zmieniono nazwę parse() funkcji "json()"

Dodajemy więcej zawartości wpisze wkrótce, dzięki czemu możemy zmienić nazwy `parse()` funkcja `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Już wkrótce: interfejsy API integracji przedsiębiorstw

Nie mamy zarządzanych jeszcze wersji Enterprise integracji interfejsów API, takich jak AS2. W międzyczasie możesz użyć istniejących wdrożonej BizTalk interfejsów API za pomocą akcji HTTP. Aby uzyskać szczegółowe informacje, zobacz "Za pomocą już wdrożonej aplikacji interfejsu API" w [planu integracji](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
