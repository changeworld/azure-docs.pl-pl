---
title: Zainstaluj krawędzi IoT Azure IoT Core | Dokumentacja firmy Microsoft
description: Zainstaluj środowisko uruchomieniowe Azure IoT Edge na urządzeniu Windows IoT Core
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ae5644a62b794dc8d6ace52f21a452fa70027d39
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029564"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Zainstaluj środowisko uruchomieniowe IoT Edge w systemie Windows IoT Core - preview

Usługa Azure IoT krawędzi i [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) działają razem, Włącz krawędzi przetwarzania danych na urządzeniach nawet małe. Środowisko uruchomieniowe krawędzi IoT Azure może działać nawet na niewielki rozmiar urządzenia pojedynczy komputer tablicy (SBC), które są bardzo często spotykane w branży IoT. 

W tym artykule przedstawiono inicjowania obsługi środowiska uruchomieniowego na tablicę programowanie z systemem Windows IoT Core. 

**Obecnie Windows IoT Core obsługuje Azure IoT krawędzi tylko na podstawie x64 procesory Intel.**

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

1. Konfiguruj płyty z **17134 kompilacji (RS4)** IoT Core obrazu. 
1. Włącz urządzenie, następnie [logowania zdalnie za pomocą programu PowerShell][lnk-powershell].
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
   >To środowisko uruchomieniowe kontenera pochodzi z serwera kompilacji projektu Moby i jest przeznaczona wyłącznie do celów ewaluacyjnych. Go ma nie przetestowane, zatwierdzone lub obsługiwane przez Docker.

## <a name="finish-installing"></a>Ukończenie instalacji

Instalowanie demona IoT krawędzi zabezpieczeń i skonfiguruj ją przy użyciu instrukcji w [w tym artykule][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz na urządzenie z systemem środowiska uruchomieniowego krawędzi IoT Dowiedz się jak [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md#download-the-edge-daemon-package-and-install
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers