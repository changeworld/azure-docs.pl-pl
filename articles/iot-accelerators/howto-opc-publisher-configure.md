---
title: Konfigurowanie wydawca OPC — Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować wydawca OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: d262c83b0ae886806b8c4bce2375ffc10ad22c75
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450758"
---
# <a name="configure-opc-publisher"></a>Konfigurowanie wydawcy OPC

Wydawca OPC w celu określenia można skonfigurować:

- Zmiany danych węzła OPC UA do publikowania.
- Zdarzenia OPC UA do opublikowania.
- Format danych telemetrycznych.

Można skonfigurować wydawca OPC za pomocą plików konfiguracji lub przy użyciu wywołania metody.

## <a name="use-configuration-files"></a>Korzystanie z plików konfiguracji

W tej sekcji opisano opcje dotyczące konfigurowania publikowanie węzła OPC UA za pomocą plików konfiguracji.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Aby skonfigurować publikowanie zmian danych przy użyciu pliku konfiguracji

Najprostszym sposobem skonfigurowania węzły OPC UA do publikowania jest plikiem konfiguracji. Format pliku konfiguracji jest udokumentowany w [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) w repozytorium.

Składni plików konfiguracji zmienił się wraz z upływem czasu. Wydawca OPC nadal odczytuje stare formaty, ale konwertuje je na najnowszy format, gdy będzie się powtarzał konfiguracji.

Poniższy przykład przedstawia format pliku konfiguracji:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Aby skonfigurować publikowanie zdarzeń przy użyciu pliku konfiguracji

Aby opublikować zdarzenia OPC UA, należy użyć tego samego pliku konfiguracji, jak w przypadku zmiany danych.

Poniższy przykład pokazuje, jak skonfigurować publikowanie zdarzeń generowanych przez [serwera SimpleEvents](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server). Serwer SimpleEvents znajdują się w [repozytorium organizację OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard) jest:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Używać wywołań metod

W tej sekcji opisano wywołania metody, których można użyć do konfigurowania wydawcy OPC.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurowanie przy użyciu wywołania metody OPC UA

Wydawca OPC obejmuje serwer OPC UA, którego dostęp można uzyskać na porcie 62222. Jeśli nazwa hosta jest **wydawcy**, a następnie jest identyfikator URI punktu końcowego: `opc.tcp://publisher:62222/UA/Publisher`.

Ten punkt końcowy uwidacznia następujących metod:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurowanie przy użyciu wywołania metody bezpośredniej usługi IoT Hub

Wydawca OPC implementuje następujące wywołania metody bezpośredniej usługi IoT Hub:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

Format ładunku JSON metody żądania i odpowiedzi są zdefiniowane w [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Jeśli wywołasz Nieznana metoda w module odpowiadały parametrami, stwierdzający, że metoda nie jest implementowana. Nieznana metoda można wywołać jako sposób na polecenie ping modułu.

### <a name="configure-username-and-password-for-authentication"></a>Skonfiguruj nazwę użytkownika i hasło do uwierzytelniania

Tryb uwierzytelniania, można ustawić za pomocą usługi IoT Hub bezpośrednie wywołania metody. Ładunek musi zawierać właściwość **OpcAuthenticationMode** , nazwę użytkownika i hasło:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Hasło jest szyfrowane przez klienta usługi IoT Hub obciążenia i przechowywane w konfiguracji wydawcy. Aby zmienić uwierzytelniania anonimowego, należy użyć metody z ładunkiem następujące:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Jeśli **OpcAuthenticationMode** właściwość nie jest w ładunku, ustawienia uwierzytelniania pozostają bez zmian w konfiguracji.

## <a name="configure-telemetry-publishing"></a>Konfigurowanie publikowania danych telemetrycznych

Gdy wydawca OPC odbiera powiadomienia o zmianie wartości w węźle opublikowanych, generuje sformatowany komunikat JSON, które są wysyłane do usługi IoT Hub.

Możesz skonfigurować zawartość tej wiadomości sformatowanego JSON przy użyciu pliku konfiguracji. Jeśli plik konfiguracji nie jest określony za pomocą `--tc` używana jest opcja i domyślną konfigurację, która jest zgodna z [akcelerator rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory).

Jeśli wydawca OPC jest skonfigurowany do komunikatów usługi batch, następnie są one wysyłane jako prawidłową tablicą JSON.

Telemetria pochodzi z następujących źródeł:

- Wydawca OPC konfiguracji węzła dla węzła
- **MonitoredItem** obiekt stosu OPC UA, dla którego wydawcy OPC Otrzymano powiadomienie.
- Argument przekazany do tego powiadomienia, który zawiera szczegółowe informacje dotyczące zmian wartości danych.

Dane telemetryczne są umieszczane w sformatowane komunikat JSON jest wybór ważne właściwości tych obiektów. Jeśli potrzebujesz więcej właściwości, należy zmienić podstawowy kod wydawcy OPC.

Składnia plik konfiguracji jest w następujący sposób:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is ommited (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Teraz wyjaśniono sposób konfigurowania wydawca OPC, sugerowane następnym krokiem jest Dowiedz się, jak [Uruchom wydawca OPC](howto-opc-publisher-run.md).
