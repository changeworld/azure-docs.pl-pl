---
title: Zabezpieczenia usługi Azure Data Box bramy | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, które chronią swoje urządzenie wirtualne bramy pola danych platformy Azure, usług i danych lokalnie i w chmurze.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685903"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Usługa Azure brama pola danych zabezpieczeń i ochrony danych

Zabezpieczenia są głównym problemem podczas wdrażania nowej technologii, zwłaszcza, jeśli jest to technologia używana przy użyciu danych poufnych lub zastrzeżonych. Rozwiązanie Microsoft Azure Data Box Gateway pomaga upewnić się, że tylko autoryzowani jednostki można wyświetlić, zmodyfikować lub usunąć swoje dane.

W tym artykule opisano funkcji zabezpieczeń bramy pola danych platformy Azure, które pomagają w ochronie poszczególne składniki rozwiązania oraz danych przechowywanych na nich.

Rozwiązanie bramy pola danych składa się z czterech głównych składników, które współdziałają ze sobą:

- **Usługa bramy pola danych hostowanych na platformie Azure** — zasób zarządzania, który umożliwia tworzenie kolejności urządzeń, konfigurowanie urządzenia i śledzić kolejności, aby ukończenie.
- **Urządzenie bramy pola danych** — urządzenie wirtualne, aprowizowana przez Ciebie w funkcji hypervisor systemu podane. To urządzenie wirtualne umożliwia importowanie danych lokalnych do platformy Azure.
- **Klienci/hosty są połączone z urządzeniem** — klientów w infrastrukturze, połączyć się z urządzeniem bramy pola danych, które zawierają dane, które muszą być chronione.
- **Magazyn w chmurze** — lokalizacja w chmurze platformy Azure, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj konto magazynu, połączone z utworzonego zasobu bramy pola danych.


## <a name="data-box-gateway-service-protection"></a>Ochrona usługi bramy pola danych

Usługa bramy pole danych jest usługą zarządzania hostowanych na platformie Microsoft Azure. Usługa jest używana do konfigurowania i zarządzania urządzeniem.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Ochrona urządzenia bramy pola danych

Urządzenie bramy pole danych jest urządzenie wirtualne aprowizowane w funkcji hypervisor systemu lokalnego, który należy podać. Urządzenie pomaga wysyłania danych do platformy Azure. Urządzenia:

- Wymaga klucza aktywacji dostępu do usługi bramy okno usługi Edge i dane pole danych.
- Jest chroniony na cały czas hasła urządzenia.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzeń za pomocą klucza aktywacji

Tylko bramy pola danych urządzenia autoryzowanego może dołączyć do usługi bramy pola danych, który został utworzony w ramach subskrypcji platformy Azure. Aby autoryzować urządzenia, trzeba użyć klucza aktywacji do aktywacji urządzenia przy użyciu usługi bramy pola danych.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, przejdź do [uzyskiwanie klucza aktywacji](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzeń za pomocą hasła

Haseł upewnij się, że Twoje dane są dostępne tylko dla autoryzowanych użytkowników. Dane bramy pola urządzenia rozruchu w stanie zablokowanym.

Możesz:

- Nawiązać połączenie z lokalnego interfejsu użytkownika urządzenia za pośrednictwem przeglądarki sieci web, a następnie podaj hasło do logowania się do urządzenia.
- Zdalne łączenie się z interfejsu programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączona. Następnie możesz podać hasło urządzenia, aby zalogować się do urządzenia. Aby uzyskać więcej informacji, przejdź do [nawiązywanie połączenia zdalne urządzenie bramy pola danych](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Użyj lokalnego internetowego interfejsu użytkownika do [zmiany hasła](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Jeśli zmienisz hasło, pamiętaj powiadomić wszystkich użytkowników dostępu zdalnego tak, aby nie będą dotykać logowania awarii.


## <a name="protect-the-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń bramy pola danych, które ochrony danych przesyłanych i przechowywanych danych.

### <a name="protect-data-at-rest"></a>Ochrona magazynowanych danych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą konta magazynu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Obróć i następnie [synchronizowanie kluczy konta magazynu](data-box-gateway-manage-shares.md#sync-storage-keys) regularnie, aby pomóc w zapewnieniu, że konto magazynu nie jest używane przez nieautoryzowanych użytkowników.

## <a name="manage-personal-information"></a>Zarządzanie informacji osobistych

Usługa bramy pola danych zbiera informacje osobiste w następujących przypadkach klucza:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp, lub usunąć udział, wykonaj kroki opisane w [Zarządzanie udziałami w bramie pole danych](data-box-gateway-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie urządzenia bramy pola danych](data-box-gateway-deploy-prep.md).
