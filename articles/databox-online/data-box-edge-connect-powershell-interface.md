---
title: Nawiązywanie połączenia i zarządzać urządzeniem krawędź pola danych platformy Azure firmy Microsoft za pośrednictwem interfejsu programu Windows PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak nawiązać połączenie, a następnie Zarządzaj krawędź pola danych za pośrednictwem interfejsu programu Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b3effdbace2be582bfe85d0402088f8aa0d96fe7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555090"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell-preview"></a>Zarządzanie urządzeniem usługi Azure Data Box Edge za pośrednictwem programu Windows PowerShell (wersja zapoznawcza)

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

> [!IMPORTANT]
> Usługa Azure Edge pole danych jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Nawiązać połączenie z interfejsu programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Rozpocząć sesję pomocy technicznej

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Przekazywanie certyfikatu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

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


## <a name="next-steps"></a>Kolejne kroki

- Wdróż rozwiązanie [Azure Data Box Edge](data-box-edge-deploy-prep.md) w witrynie Azure Portal.
