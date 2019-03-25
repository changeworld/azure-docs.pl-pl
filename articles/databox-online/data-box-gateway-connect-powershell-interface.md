---
title: Nawiązywanie połączenia i zarządzać urządzeniem Microsoft Azure Data Box Gateway za pomocą interfejsu programu Windows PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak nawiązać połączenie, a następnie Zarządzaj bramy pola danych za pośrednictwem interfejsu programu Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403497"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Zarządzanie urządzeniem bramy pola danych platformy Azure za pomocą programu Windows PowerShell

Rozwiązanie bramy pola danych na platformie Azure umożliwia wysyłanie danych przez sieć do platformy Azure. W tym artykule opisano niektóre z zadań konfiguracji i zarządzania dla urządzenia bramy pola danych. Można użyć witryny Azure portal, lokalnego internetowego interfejsu użytkownika lub interfejsu programu Windows PowerShell do zarządzania urządzeniem.

Ten artykuł koncentruje się na zadaniach, które można zrobić, za pomocą interfejsu programu PowerShell.

W tym artykule opisano następujące procedury:

- Nawiązać połączenie z interfejsu programu PowerShell
- Rozpocząć sesję pomocy technicznej
- Tworzenie pakietu pomocy technicznej
- Przekazywanie certyfikatu
- Rozruch w środowisku protokołu DHCP
- Wyświetl informacje na urządzeniu

## <a name="connect-to-the-powershell-interface"></a>Nawiązać połączenie z interfejsu programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Przekazywanie certyfikatu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Rozruch w środowisku protokołu DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Wyświetl informacje na urządzeniu

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Kolejne kroki

- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.
