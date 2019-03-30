---
title: Nawiązywanie połączenia i zarządzać urządzeniem krawędź pola danych platformy Azure firmy Microsoft za pośrednictwem interfejsu programu Windows PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak nawiązać połączenie, a następnie Zarządzaj krawędź pola danych za pośrednictwem interfejsu programu Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/29/2019
ms.author: alkohli
ms.openlocfilehash: a3096729b2430adf0fd884fc03e3b051b17f5b51
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660464"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Zarządzanie urządzeniem usługi Azure Data Box Edge za pośrednictwem programu Windows PowerShell

Rozwiązanie krawędź pola danych na platformie Azure pozwala na przetwarzanie danych i wyślij go za pośrednictwem sieci na platformie Azure. W tym artykule opisano niektóre z zadań konfiguracji i zarządzania dla urządzenia usługi Edge pola danych. Można użyć witryny Azure portal, lokalnego internetowego interfejsu użytkownika lub interfejsu programu Windows PowerShell do zarządzania urządzeniem.

Ten artykuł koncentruje się na zadaniach, które można zrobić, za pomocą interfejsu programu PowerShell.

W tym artykule opisano następujące procedury:

- Nawiązać połączenie z interfejsu programu PowerShell
- Rozpocząć sesję pomocy technicznej
- Tworzenie pakietu pomocy technicznej
- Przekazywanie certyfikatu
- Resetowanie urządzenia
- Wyświetl informacje na urządzeniu
- Pobierz dzienniki obliczeń
- Monitorowanie i rozwiązywanie problemów z modułami obliczeń

## <a name="connect-to-the-powershell-interface"></a>Nawiązać połączenie z interfejsu programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Przekazywanie certyfikatu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Można również przekazywać certyfikaty usługi IoT Edge w celu włączenia bezpiecznego połączenia między urządzeniem usługi IoT Edge i podrzędnym urządzenia, które może z nim połączyć. Występują trzy certyfikaty usługi IoT Edge (*PEM* format), musisz zainstalować:

- Certyfikat głównego urzędu certyfikacji lub właściciela urzędu certyfikacji
- Certyfikat dostępu Warunkowego do urządzeń
- Klucza certyfikatu urządzenia

Poniższy przykład przedstawia sposób użycia tego polecenia cmdlet, aby zainstalować certyfikaty z usługi IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Aby uzyskać więcej informacji na temat certyfikatów, przejdź do [certyfikaty usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) lub [instalowanie certyfikatów na bramie](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Wyświetl informacje na urządzeniu
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Resetowanie urządzenia

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Pobierz dzienniki obliczeń

Skonfigurowanie ról obliczeniowych na urządzeniu z systemem dzienniki obliczeniowych można także uzyskać za pośrednictwem interfejsu programu PowerShell.

1. [Nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj `Get-AzureDataBoxEdgeComputeRoleLogs` można pobrać dzienników obliczeniowe dla danego urządzenia.

    Poniższy przykład przedstawia sposób użycia tego polecenia cmdlet:

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    Poniżej przedstawiono opis parametry używane dla polecenia cmdlet:
    - `Path`: Podaj ścieżkę sieciową do udziału, w którym chcesz utworzyć pakiet dziennika obliczeń.
    - `Credential`: Podaj nazwę użytkownika i hasło do udziału sieciowego.
    - `RoleInstanceName`: Podaj ten ciąg `IotRole` dla tego parametru.
    - `FullLogCollection`: Tego parametru gwarantuje, że pakiet dziennika będzie zawierać wszystkie dzienniki obliczeń. Domyślnie pakiet dziennik zawiera tylko podzbiór dzienników.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorowanie i rozwiązywanie problemów z modułami obliczeń

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>Kolejne kroki

- Wdróż rozwiązanie [Azure Data Box Edge](data-box-edge-deploy-prep.md) w witrynie Azure Portal.
