---
title: Łączenie się z urządzeniem Microsoft Azure Data Box Edge i zarządzanie nim za pośrednictwem interfejsu programu Windows PowerShell | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób łączenia się z urządzeniem Data Box Edge, z którymi można się połączyć za pośrednictwem interfejsu programu Windows PowerShell, a następnie z nią zarządzać.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265482"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Zarządzanie urządzeniem usługi Azure Data Box Edge za pośrednictwem programu Windows PowerShell

Rozwiązanie usługi Azure Data Box Edge umożliwia przetwarzanie danych i wysyłanie ich za pośrednictwem sieci na platformę Azure. W tym artykule opisano niektóre zadania konfiguracji i zarządzania urządzenia data box edge. Do zarządzania urządzeniem można użyć witryny Azure portal, lokalnego interfejsu użytkownika sieci Web lub interfejsu programu Windows PowerShell.

W tym artykule skupiono się na zadaniach wykonywanych przy użyciu interfejsu programu PowerShell.

Ten artykuł zawiera następujące procedury:

- Łączenie się z interfejsem programu PowerShell
- Tworzenie pakietu pomocy technicznej
- Przekazywanie certyfikatu
- Resetowanie urządzenia
- Wyświetlanie informacji o urządzeniu
- Pobierz dzienniki obliczeniowe
- Monitorowanie i rozwiązywanie problemów z modułami obliczeniowymi

## <a name="connect-to-the-powershell-interface"></a>Łączenie się z interfejsem programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Przekazywanie certyfikatu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Można również przekazać certyfikaty usługi IoT Edge, aby włączyć bezpieczne połączenie między urządzeniem Usługi IoT Edge a urządzeniami podrzędnymi, które mogą się z nim łączyć. Istnieją trzy certyfikaty usługi IoT Edge (format*pem),* które należy zainstalować:

- Główny certyfikat urzędu certyfikacji lub właściciel urzędu certyfikacji
- Certyfikat urzędu certyfikacji urządzenia
- Certyfikat klucza urządzenia

W poniższym przykładzie przedstawiono użycie tego polecenia cmdlet do zainstalowania certyfikatów usługi IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Po uruchomieniu tego polecenia cmdlet zostanie wyświetlony monit o podanie hasła do udziału sieciowego.

Aby uzyskać więcej informacji na temat certyfikatów, przejdź do [certyfikatów usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) lub [zainstaluj certyfikaty na bramie](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Wyświetlanie informacji o urządzeniu
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Resetowanie urządzenia

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Pobierz dzienniki obliczeniowe

Jeśli rola obliczeniowa jest skonfigurowana na urządzeniu, można również uzyskać dzienniki obliczeniowe za pośrednictwem interfejsu programu PowerShell.

1. [Połącz się z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj, `Get-AzureDataBoxEdgeComputeRoleLogs` aby uzyskać dzienniki obliczeniowe dla urządzenia.

    W poniższym przykładzie przedstawiono użycie tego polecenia cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Oto opis parametrów używanych dla polecenia cmdlet:
    - `Path`: Podaj ścieżkę sieciową do udziału, w którym chcesz utworzyć pakiet dziennika obliczeniowego.
    - `Credential`: Podaj nazwę użytkownika udziału sieciowego. Po uruchomieniu tego polecenia cmdlet należy podać hasło udziału.
    - `FullLogCollection`: Ten parametr gwarantuje, że pakiet dziennika będzie zawierać wszystkie dzienniki obliczeniowe. Domyślnie pakiet dziennika zawiera tylko podzbiór dzienników.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorowanie i rozwiązywanie problemów z modułami obliczeniowymi

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Zamykanie sesji zdalnej

Aby zakończyć zdalną sesję programu PowerShell, zamknij okno programu PowerShell.

## <a name="next-steps"></a>Następne kroki

- Wdróż rozwiązanie [Azure Data Box Edge](data-box-edge-deploy-prep.md) w witrynie Azure Portal.
