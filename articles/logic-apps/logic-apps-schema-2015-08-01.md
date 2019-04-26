---
title: Aktualizacje schematu dla sierpnia-1-2015 (wersja zapoznawcza) — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zaktualizowano schemat wersja 2015-08-01-preview dla definicji aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: 92f522c72f69218e55b1ee4cfff74511a30288b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553763"
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

`host` Obiekt jest częścią danych wejściowych, jest unikatowy dla połączenia interfejsu API, która zawiera następujące segmenty: `api` i `connection`. `api` Obiekt określa środowisko uruchomieniowe znajduje się adres URL, dla których, zarządzać interfejsu API. Możesz zobaczyć wszystkie dostępne zarządzane interfejsy API, przez wywołanie tej metody:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Korzystając z interfejsu API tego interfejsu API może być lub może nie zdefiniowano żadnego *parametry połączenia*. Tak Jeśli interfejs API nie zdefiniowano tych parametrów, połączenie nie jest wymagane. Jeśli interfejs API definiowania tych parametrów, należy utworzyć połączenie z określoną nazwą.  
Następnie odwoływać się tą nazwą w `connection` wewnątrz `host` obiektu. Aby utworzyć połączenie w grupie zasobów, należy wywołać tej metody:

```text
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

Podczas logowania interaktywnego nie jest wymagane, można utworzyć pełnej aplikacji przy użyciu szablonu usługi Resource Manager.
Jeśli logowania jest wymagana, można nadal używać szablonu usługi Resource Manager, ale trzeba autoryzowania połączeń za pośrednictwem witryny Azure portal. 

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

Jeśli korzystasz z własnymi interfejsami API, a nie z nich zarządzanych przez firmę Microsoft, należy użyć wbudowanego **HTTP** akcji do wywołania interfejsów API. W idealnym przypadku należy podać punktu końcowego struktury Swagger dla interfejsu API. Ten punkt końcowy pomaga w Projektancie aplikacji logiki, Pokaż dane wejściowe i wyjściowe Twój interfejs API. Bez punktu końcowego struktury Swagger projektanta można wyświetlić tylko dane wejściowe i wyjściowe jako nieprzezroczysty obiekty JSON.

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

Teraz, teraz możesz tworzyć podobne Akcja HTTP i pozostaw definicji aplikacji logiki `parameters` sekcji bez zmian, na przykład:

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
| `inputs.body` | Takie same jak parametry aplikacji interfejsu API |
| `inputs.authentication` | Takie same jak uwierzytelniania aplikacji interfejsu API |

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
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
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
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
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

## <a name="native-http-listener"></a>Natywne odbiornika HTTP

Funkcje odbiornika HTTP są teraz wbudowane, więc nie trzeba wdrożyć aplikację interfejsu API odbiornika HTTP. Aby uzyskać więcej informacji, Dowiedz się, jak [upewnij punktu końcowego aplikacji logiki jako wywoływane](../logic-apps/logic-apps-http-endpoint.md). 

Za pomocą tych zmian, Logic Apps zastępuje `@accessKeys()` funkcją `@listCallbackURL()` funkcji, która pobiera punktu końcowego, gdy jest to konieczne. Ponadto teraz należy zdefiniować co najmniej jeden wyzwalacz w aplikacji logiki. Jeśli chcesz `/run` przepływu pracy, należy użyć jednego z następujących typów wyzwalacza: `Manual`, `ApiConnectionWebhook`, lub `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Wywoływanie podrzędnego przepływów pracy

Wcześniej wywoływania podrzędnego przepływów pracy wymagane, przechodząc do przepływu pracy, uzyskiwanie tokenu dostępu i wklejając token w definicji aplikacji logiki, które chcesz wywołać ten podrzędny przepływ pracy. Za pomocą tego schematu aparat usługi Logic Apps automatycznie generuje sygnaturę dostępu Współdzielonego w czasie wykonywania dla podrzędny przepływ pracy, więc nie trzeba wkleić jakichkolwiek kluczy tajnych w definicji. Oto przykład:

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

Ponadto podrzędne przepływy pracy Uzyskaj pełny dostęp do żądania przychodzącego. Tak, można przekazać parametry `queries` sekcji i w `headers` obiektu. Można również w pełni zdefiniować całą `body` sekcji.

Na koniec przepływy pracy podrzędne mają wymagane zmiany. Można wcześniej, i bezpośrednio wywołać podrzędny przepływ pracy, teraz należy zdefiniować punkt końcowy wyzwalacza w przepływie pracy nadrzędnego do wywołania. Ogólnie rzecz biorąc, należy dodać wyzwalacza, który ma `Manual` typu, a następnie użyj tego wyzwalacza w definicji nadrzędnej. `host` Właściwość specjalnie ma `triggerName` ponieważ należy zawsze określić, że wyzwalacz jest wywoływany.

## <a name="other-changes"></a>Inne zmiany

### <a name="new-queries-property"></a>Nowa właściwość "zapytania"

Wszystkie typy akcji obsługują teraz nowe dane wejściowe o nazwie `queries`. Te dane wejściowe mogą być obiektów ze strukturą, zamiast konieczności ręcznie utworzyć ciąg.

### <a name="renamed-parse-function-to-json"></a>Zmieniono nazwę parse() funkcji "json()"

`parse()` Funkcja została zmieniona `json()` funkcji dla typów zawartości w przyszłości.

## <a name="enterprise-integration-apis"></a>Interfejsy API integracji przedsiębiorstw

Ten schemat jeszcze nie obsługuje wersji zarządzanych interfejsów API integracji przedsiębiorstwa, takich jak AS2. Można jednak użyć istniejących wdrożonej interfejsów API usługi BizTalk przy użyciu akcji HTTP. Aby uzyskać więcej informacji, zobacz "Za pomocą już wdrożonej aplikacji interfejsu API" w [planu integracji](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
