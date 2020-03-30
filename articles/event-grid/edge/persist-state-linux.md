---
title: Stan utrwalania w systemie Linux — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Utrwalanie metadanych w systemie Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086657"
---
# <a name="persist-state-in-linux"></a>Stan utrwalania w systemie Linux

Tematy i subskrypcje utworzone w module Siatka zdarzeń są domyślnie przechowywane w systemie plików kontenera. Bez trwałości, jeśli moduł zostanie ponownie wydechowany, wszystkie utworzone metadane zostaną utracone. Aby zachować dane między wdrożeniami i ponownymi uruchomień, należy utrwalić dane poza systemem plików kontenera.

Domyślnie tylko metadane są zachowywane, a zdarzenia są nadal przechowywane w pamięci w celu zwiększenia wydajności. Postępuj zgodnie z sekcją persist zdarzenia, aby włączyć trwałość zdarzeń, jak również.

W tym artykule przedstawiono kroki, aby wdrożyć moduł usługi Event Grid z trwałością we wdrożeniach systemu Linux.

> [!NOTE]
>Moduł Siatka zdarzeń działa jako użytkownik o `2000` niskich `eventgriduser`uprawnieniach z identyfikatorem UID i nazwą .

## <a name="persistence-via-volume-mount"></a>Trwałość za pomocą uchwytu objętościowego

 [Woluminy platformy Docker](https://docs.docker.com/storage/volumes/) są używane do zachowania danych we wdrożeniach. Dokowanie umożliwia automatyczne tworzenie woluminu nazwanego w ramach wdrażania modułu Siatki zdarzeń. Ta opcja jest najprostszą opcją. Nazwę woluminu, który ma zostać utworzony w sekcji **Powiązania,** można określić w następujący sposób:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Nie należy zmieniać drugiej części wartości wiązania. Wskazuje na określoną lokalizację w module. Dla modułu Event Grid w systemie Linux musi to być **/app/metadataDb**.

Na przykład następująca konfiguracja spowoduje utworzenie woluminu **egmetadataDbVol,** gdzie metadane będą zachowywane.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Zamiast montować wolumin, można utworzyć katalog w systemie hosta i zainstalować ten katalog.

## <a name="persistence-via-host-directory-mount"></a>Trwałość za pośrednictwem instalacji katalogu hosta

Zamiast woluminu docker, masz również możliwość zainstalowania folderu hosta.

1. Najpierw utwórz użytkownika o nazwie **eventgriduser** i ID **2000** na komputerze-hoście, uruchamiając następujące polecenie:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Utwórz katalog w systemie plików hosta, uruchamiając następujące polecenie.

   ```sh
   md <your-directory-name-here>
   ```

    Na przykład uruchomienie następującego polecenia spowoduje utworzenie katalogu o nazwie **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Następnie należy wprowadzić **eventgriduser** właściciela tego folderu, uruchamiając następujące polecenie.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Na przykład:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Użyj **bindów,** aby zainstalować katalog i ponownie wdrożyć moduł siatki zdarzeń z witryny Azure portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Na przykład:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Nie należy zmieniać drugiej części wartości wiązania. Wskazuje na określoną lokalizację w module. Dla modułu Siatki zdarzeń w systemie Linux musi to być **/app/metadataDb** i **/app/eventsDb**


## <a name="persist-events"></a>Utrwalanie zdarzeń

Aby włączyć trwałość zdarzeń, należy najpierw włączyć trwałość metadanych za pomocą instalacji woluminu lub hosta katalogu mount przy użyciu powyższych sekcji.

Ważne rzeczy, na które należy zwrócić uwagę na powtarzające się zdarzenia:

* Utrwalanie zdarzeń jest włączone na podstawie subskrypcji zdarzeń i jest włączona po zamontowaniu woluminu lub katalogu.
* Trwałość zdarzeń jest skonfigurowana w subskrypcji zdarzeń w czasie tworzenia i nie można go zmodyfikować po utworzeniu subskrypcji zdarzeń. Aby przełączyć trwałość zdarzeń, należy usunąć i ponownie utworzyć subskrypcję zdarzeń.
* Utrzymujące się zdarzenia jest prawie zawsze wolniejsze niż w operacjach pamięci, jednak różnica prędkości jest w dużym stopniu zależna od charakterystyki dysku. Kompromis między szybkością a niezawodnością jest nieodłącznym elementem wszystkich systemów obsługi wiadomości, ale na ogół staje się zauważalny tylko na dużą skalę.

Aby włączyć trwałość zdarzeń w `persistencePolicy` subskrypcji `true`zdarzeń, ustaw na:

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
