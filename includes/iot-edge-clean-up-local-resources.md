---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866807"
---
### <a name="delete-local-resources"></a>Usuwanie zasobów lokalnych

Jeśli chcesz usunąć środowisko uruchomieniowe usługi IoT Edge i powiązane zasoby z urządzenia, użyj odpowiednich poleceń dla systemu operacyjnego urządzenia. 

#### <a name="windows"></a>Windows

Odinstalowywanie środowiska uruchomieniowego usługi IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Po usunięciu środowiska uruchomieniowego usługi IoT Edge utworzone przez nie kontenery zostaną zatrzymane, ale pozostaną na urządzeniu. Wyświetl wszystkie kontenery.

   ```powershell
   docker ps -a
   ```

Usuń kontenery środowiska uruchomieniowego utworzone na urządzeniu.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Usuń wszystkie dodatkowe kontenery, które zostały wymienione w danych wyjściowych `docker ps`, odwołując się do nazw kontenerów. 

#### <a name="linux"></a>Linux

Usuń środowisko uruchomieniowe usługi IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Po usunięciu środowiska uruchomieniowego usługi IoT Edge utworzone przez nie kontenery zostaną zatrzymane, ale pozostaną na urządzeniu. Wyświetl wszystkie kontenery.

   ```bash
   sudo docker ps -a
   ```

Usuń kontenery środowiska uruchomieniowego utworzone na urządzeniu.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Usuń wszystkie dodatkowe kontenery, które zostały wymienione w danych wyjściowych `docker ps`, odwołując się do nazw kontenerów. 

Usuń środowisko uruchomieniowe kontenera.

   ```bash
   sudo apt-get remove --purge moby
   ```