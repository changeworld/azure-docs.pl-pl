---
title: Instalowanie usługi Azure IoT Edge w systemie IoT Core | Dokumentacja firmy Microsoft
description: Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge na urządzeniu z systemem Windows IoT Core
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 17afc095fa0500ca98559a9523282d75949faa60
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564830"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Zainstaluj środowisko uruchomieniowe usługi IoT Edge w systemie Windows IoT Core — wersja zapoznawcza

Usługa Azure IoT Edge i [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) działają razem w celu włączenia krawędzi przetwarzania danych na urządzeniach nawet niewielkie. Nawet w przypadku niewielkich urządzeń jednej tablicy komputera (Programowej), które są bardzo często spotykane w branży IoT uruchomić środowiska uruchomieniowego usługi Azure IoT Edge. 

Ten artykuł przeprowadzi inicjowania obsługi środowiska uruchomieniowego na potrzeby robotyki systemem Windows IoT Core. 

**Obecnie usługa Windows IoT Core obsługuje usługi Azure IoT Edge tylko na Intel x64 64 procesorów.**

## <a name="install-the-container-runtime"></a>Zainstaluj środowisko uruchomieniowe kontenera

1. Konfigurowanie tablicy przy użyciu **17134 kompilacji (RS4)** obraz IoT Core. 
1. Włącz urządzenie, następnie [Zaloguj się zdalnie przy użyciu programu PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).
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

Zainstaluj demona zabezpieczeń IoT Edge i skonfiguruj go zgodnie z instrukcjami przedstawionymi w [w tym artykule](how-to-install-iot-edge-windows-with-windows.md)

## <a name="next-steps"></a>Kolejne kroki

Teraz masz urządzenie z systemem środowiska uruchomieniowego usługi IoT Edge, Dowiedz się, jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).