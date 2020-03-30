---
title: Stan utrwalania w systemie Windows — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Stan utrwalania w systemie Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086630"
---
# <a name="persist-state-in-windows"></a>Stan utrwalania w systemie Windows

Tematy i subskrypcje utworzone w module Siatka zdarzeń są domyślnie przechowywane w systemie plików kontenera. Bez trwałości, jeśli moduł zostanie ponownie wydechowany, wszystkie utworzone metadane zostaną utracone. Aby zachować dane między wdrożeniami i ponownymi uruchomień, należy utrwalić dane poza systemem plików kontenera. 

Domyślnie tylko metadane są zachowywane, a zdarzenia są nadal przechowywane w pamięci w celu zwiększenia wydajności. Postępuj zgodnie z sekcją persist zdarzenia, aby włączyć trwałość zdarzeń, jak również.

Ten artykuł zawiera kroki potrzebne do wdrożenia modułu usługi Event Grid z trwałością we wdrożeniach systemu Windows.

> [!NOTE]
>Moduł Siatki zdarzeń działa jako **kontener** użytkownika o niskich uprawnieniach w systemie Windows.

## <a name="persistence-via-volume-mount"></a>Trwałość za pomocą uchwytu objętościowego

[Woluminy platformy Docker](https://docs.docker.com/storage/volumes/) są używane do zachowywania danych we wdrożeniach. Aby zainstalować wolumin, należy utworzyć go przy użyciu poleceń platformy docker, nadać uprawnienia, aby kontener mógł odczytywać, zapisywać do niego, a następnie wdrażać moduł.

1. Utwórz wolumin, uruchamiając następujące polecenie:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Na przykład:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Pobierz katalog hosta, do który mapuje wolumin, uruchamiając polecenie poniżej

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Na przykład:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Przykładowe wyjście:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Dodaj grupę **Użytkownicy** do wartości wskazanej przez **punkt mountpoint** w następujący sposób:
    1. Uruchom Eksploratora plików.
    1. Przejdź do folderu wskazywionego przez **punkt Mountpoint**.
    1. Kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Właściwości**.
    1. Wybierz pozycję **Zabezpieczenia**.
    1. W obszarze *Grupuj lub nazwy użytkowników wybierz pozycję **Edytuj**.
    1. Wybierz **pozycję** `Users`Dodaj , wprowadź , wybierz **pozycję Sprawdź nazwy**i wybierz **ok**.
    1. W obszarze *Uprawnienia dla użytkowników*wybierz pozycję **Modyfikuj**i **wybierz**ok .
1. Użyj **bindów,** aby zainstalować ten wolumin i ponownie wdrożyć moduł siatki zdarzeń z witryny Azure portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Nie należy zmieniać drugiej części wartości wiązania. Wskazuje na określoną lokalizację w module. Dla modułu Event Grid w systemie Windows musi to być **C:\\app\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Trwałość za pośrednictwem instalacji katalogu hosta

Zamiast montować wolumin, można utworzyć katalog w systemie hosta i zainstalować ten katalog.

1. Utwórz katalog w systemie plików hosta, uruchamiając następujące polecenie.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Na przykład:

   ```sh
   mkdir C:\myhostdir
   ```
1. Użyj **bindów,** aby zainstalować katalog i ponownie wdrożyć moduł siatki zdarzeń z witryny Azure portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Nie należy zmieniać drugiej części wartości wiązania. Wskazuje na określoną lokalizację w module. Dla modułu Siatki zdarzeń w systemie Windows musi to być **C:\\app\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Utrwalanie zdarzeń

Aby włączyć trwałość zdarzeń, należy najpierw włączyć trwałość zdarzeń za pomocą instalacji woluminu lub hosta katalogu mount przy użyciu powyższych sekcji.

Ważne rzeczy, na które należy zwrócić uwagę na powtarzające się zdarzenia:

* Utrwalanie zdarzeń jest włączone na podstawie subskrypcji zdarzeń i jest włączona po zamontowaniu woluminu lub katalogu.
* Trwałość zdarzeń jest skonfigurowana w subskrypcji zdarzeń w czasie tworzenia i nie można go zmodyfikować po utworzeniu subskrypcji zdarzeń. Aby przełączyć trwałość zdarzeń, należy usunąć i ponownie utworzyć subskrypcję zdarzeń.
* Utrzymujące się zdarzenia jest prawie zawsze wolniejsze niż w operacjach pamięci, jednak różnica prędkości jest w dużym stopniu zależna od charakterystyki dysku. Kompromis między szybkością a niezawodnością jest nieodłącznym elementem wszystkich systemów przesyłania wiadomości, ale staje się zauważalny tylko na dużą skalę.

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