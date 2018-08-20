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
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046815"
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