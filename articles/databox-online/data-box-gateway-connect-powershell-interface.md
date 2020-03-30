---
title: Łączenie się z urządzeniem bramy usługi Azure Data Box Gateway i zarządzanie nim
description: W tym artykule opisano sposób łączenia się z bramą pola danych, z którymi można zarządzać za pośrednictwem interfejsu programu Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260217"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Zarządzanie urządzeniem bramy usługi Azure Data Box za pośrednictwem programu Windows PowerShell

Rozwiązanie usługi Azure Data Box Gateway umożliwia wysyłanie danych przez sieć na platformę Azure. W tym artykule opisano niektóre zadania konfiguracji i zarządzania dla urządzenia bramy pola danych. Do zarządzania urządzeniem można użyć witryny Azure portal, lokalnego interfejsu użytkownika sieci Web lub interfejsu programu Windows PowerShell.

W tym artykule skupiono się na zadaniach wykonywanych przy użyciu interfejsu programu PowerShell.

Ten artykuł zawiera następujące procedury:

- Łączenie się z interfejsem programu PowerShell
- Tworzenie pakietu pomocy technicznej
- Przekazywanie certyfikatu
- Uruchamianie w środowisku innych niż DHCP
- Wyświetlanie informacji o urządzeniu

## <a name="connect-to-the-powershell-interface"></a>Łączenie się z interfejsem programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Przekazywanie certyfikatu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Uruchamianie w środowisku innych niż DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Wyświetlanie informacji o urządzeniu

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Następne kroki

- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.
