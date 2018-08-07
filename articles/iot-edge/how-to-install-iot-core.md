---
title: Instalowanie usługi Azure IoT Edge w systemie IoT Core | Dokumentacja firmy Microsoft
description: Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge na urządzeniu z systemem Windows IoT Core
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576002"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Zainstaluj środowisko uruchomieniowe usługi IoT Edge w systemie Windows IoT Core — wersja zapoznawcza

Usługa Azure IoT Edge i [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) działają razem w celu włączenia krawędzi przetwarzania danych na urządzeniach nawet niewielkie. Nawet w przypadku niewielkich urządzeń jednej tablicy komputera (Programowej), które są bardzo często spotykane w branży IoT uruchomić środowiska uruchomieniowego usługi Azure IoT Edge. 

Ten artykuł przeprowadzi inicjowania obsługi środowiska uruchomieniowego na potrzeby robotyki systemem Windows IoT Core. 

**Obecnie usługa Windows IoT Core obsługuje usługi Azure IoT Edge tylko na Intel x64 64 procesorów.**

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

1. Konfigurowanie tablicy przy użyciu **17134 kompilacji (RS4)** obraz IoT Core. 
1. Włącz urządzenie, następnie [Zaloguj się zdalnie przy użyciu programu PowerShell][lnk-powershell].
1. W konsoli programu PowerShell należy zainstalować środowisko uruchomieniowe kontenera: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Ten kontener środowiska uruchomieniowego pochodzi z serwera kompilacji projektu Moby i jest przeznaczona wyłącznie do celów oceny. Jego nie przetestowane, zatwierdzone lub obsługiwane przez platformę Docker.

## <a name="finish-installing"></a>Zakończ instalowanie

Zainstaluj demona zabezpieczeń IoT Edge i skonfiguruj go zgodnie z instrukcjami przedstawionymi w [w tym artykule][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Kolejne kroki

Teraz masz urządzenie z systemem środowiska uruchomieniowego usługi IoT Edge, Dowiedz się, jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
