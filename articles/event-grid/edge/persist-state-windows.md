---
title: Stan utrwalania w systemie Windows Azure Event Grid IoT Edge | Microsoft Docs
description: Stan utrwalania w systemie Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992264"
---
# <a name="persist-state-in-windows"></a>Stan utrwalania w systemie Windows

Tematy i subskrypcje utworzone w module Event Grid są domyślnie przechowywane w systemie plików kontenerów. Bez trwałości, jeśli moduł zostanie ponownie wdrożony, wszystkie utworzone metadane zostałyby utracone. Aby zachować dane między wdrożeniami, należy utrzymać dane poza systemem plików kontenera. Obecnie tylko metadane są utrwalane. Zdarzenia są przechowywane w pamięci. Jeśli Event Grid module zostanie ponownie wdrożony lub uruchomiony, wszystkie niedostarczone zdarzenia zostaną utracone.

Ten artykuł zawiera kroki niezbędne do wdrożenia modułu Event Grid z trwałością we wdrożeniach systemu Windows.

> [!NOTE]
>Moduł Event Grid jest uruchamiany jako **ContainerUser** użytkownika z niskim poziomem uprawnień w systemie Windows.

## <a name="persistence-via-volume-mount"></a>Trwałość za pośrednictwem instalacji woluminu

[Woluminy platformy Docker](https://docs.docker.com/storage/volumes/) są używane do zachowywania danych między wdrożeniami. Aby zainstalować wolumin, należy go utworzyć przy użyciu poleceń platformy Docker, udzielić uprawnień, aby umożliwić odczytywanie i zapisywanie w kontenerze, a następnie wdrożenie modułu. Nie ma potrzeby automatycznego tworzenia woluminu z niezbędnymi uprawnieniami w systemie Windows. Przed wdrożeniem należy go utworzyć.

1. Utwórz wolumin, uruchamiając następujące polecenie:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Na przykład:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Pobierz katalog hosta, na który jest mapowany wolumin, uruchamiając poniższe polecenie

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Na przykład:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Przykładowe dane wyjściowe:-

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
1. Dodaj grupę **Użytkownicy** do wartości wskazywanej przez **mountpoint** w następujący sposób:
    1. Uruchom Eksploratora plików.
    1. Przejdź do folderu wskazywanego przez **mountpoint**.
    1. Kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Właściwości**.
    1. Wybierz pozycję **zabezpieczenia**.
    1. W obszarze * nazwy grup lub użytkowników wybierz pozycję **Edytuj**.
    1. Wybierz pozycję **Dodaj**, wprowadź `Users`, wybierz pozycję **Sprawdź nazwy**, a następnie wybierz **przycisk OK**.
    1. W obszarze *uprawnienia dla użytkowników*wybierz pozycję **Modyfikuj**, a następnie wybierz pozycję **OK**.
1. Użyj **powiązań** , aby zainstalować ten wolumin i ponownie wdrożyć moduł Event Grid z Azure Portal

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
   >Nie zmieniaj drugiej części wartości powiązania. Wskazuje on konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Windows musi to być **C:\\app\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>Trwałość przy użyciu instalacji katalogu hosta

Alternatywnie możesz utworzyć katalog w systemie hosta i zainstalować ten katalog.

1. Utwórz katalog w systemie plików hosta, uruchamiając następujące polecenie.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Na przykład:

   ```sh
   mkdir C:\myhostdir
   ```
1. Użyj **powiązań** , aby zainstalować katalog i ponownie wdrożyć moduł Event Grid z Azure Portal.

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
    >Nie zmieniaj drugiej części wartości powiązania. Wskazuje on konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Windows musi to być **C:\\app\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
