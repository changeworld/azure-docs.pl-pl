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
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202694"
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