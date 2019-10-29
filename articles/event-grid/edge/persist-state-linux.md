---
title: Stan utrwalania w systemie Linux — Azure Event Grid IoT Edge | Microsoft Docs
description: Utrwalanie metadanych w systemie Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 6639b8f4d947720db38a6b366c47700a56154af3
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992277"
---
# <a name="persist-state-in-linux"></a>Stan utrwalania w systemie Linux

Tematy i subskrypcje utworzone w module Event Grid są domyślnie przechowywane w systemie plików kontenerów. Bez trwałości, jeśli moduł zostanie ponownie wdrożony, wszystkie utworzone metadane zostałyby utracone. Obecnie tylko metadane są utrwalane. Zdarzenia są przechowywane w pamięci. Jeśli Event Grid module zostanie ponownie wdrożony lub uruchomiony, wszystkie niedostarczone zdarzenia zostaną utracone.

W tym artykule przedstawiono procedurę wdrażania modułu Event Grid z trwałością we wdrożeniach systemu Linux.

> [!NOTE]
>Moduł Event Grid działa jako użytkownik z niskim poziomem uprawnień z identyfikatorem UID `2000` i `eventgriduser`nazw.

## <a name="persistence-via-volume-mount"></a>Trwałość za pośrednictwem instalacji woluminu

 [Woluminy platformy Docker](https://docs.docker.com/storage/volumes/) są używane do zachowywania danych między wdrożeniami. Możesz zezwolić platformie Docker na automatyczne tworzenie nazwanego woluminu w ramach wdrażania modułu Event Grid. Ta opcja jest najprostszą opcją. Nazwę woluminu, który ma zostać utworzony, można określić w sekcji **powiązania** w następujący sposób:

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
>Nie zmieniaj drugiej części wartości powiązania. Wskazuje konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Linux musi być **/App/Metadata**.

Na przykład następująca konfiguracja spowoduje utworzenie woluminu **egmetadataDbVol** , gdzie metadane zostaną utrwalone.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
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

Alternatywnie można utworzyć wolumin platformy Docker za pomocą poleceń klienta platformy Docker. 

## <a name="persistence-via-host-directory-mount"></a>Trwałość przy użyciu instalacji katalogu hosta

Zamiast woluminu platformy Docker można również zainstalować folder hosta.

1. Najpierw Utwórz użytkownika o nazwie **eventgriduser** i identyfikatorze **2000** na komputerze hosta, uruchamiając następujące polecenie:

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
1. Następnie utwórz właściciela **eventgriduser** tego folderu, uruchamiając następujące polecenie.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Na przykład:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Użyj **powiązań** , aby zainstalować katalog i ponownie wdrożyć moduł Event Grid z Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    Na przykład:

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
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
    >Nie zmieniaj drugiej części wartości powiązania. Wskazuje konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Linux musi być **/App/Metadata**.
