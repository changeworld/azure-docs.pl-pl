---
title: Zabezpieczenia usługi Azure Data Box bramy | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, które chronią urządzenia wirtualnego bramy pola danych platformy Azure, usług i danych lokalnych i w chmurze.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754356"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Usługa Azure brama pola danych zabezpieczeń i ochrony danych

Bezpieczeństwo jest głównym problemem, gdy jest przyjęcie nowych technologii, zwłaszcza, jeśli jest to technologia używana przy użyciu danych poufnych lub zastrzeżonych. Azure brama pola danych pomaga należy upewnić się tylko autoryzowane jednostki można wyświetlić, zmodyfikować lub usunąć swoje dane.

W tym artykule opisano funkcje zabezpieczeń bramy pola danych platformy Azure, które chronić poszczególne składniki rozwiązania oraz danych przechowywanych w nich.

Rozwiązanie bramy pola danych składa się z czterech głównych składników, które współdziałają ze sobą:

- **Usługa bramy pola danych, hostowanych na platformie Azure**. Zasób zarządzania, który umożliwia tworzenie kolejności urządzeń, skonfigurowanie urządzenia i śledzić kolejności do zakończenia.
- **Urządzenie bramy pola danych**. Urządzenie wirtualne aprowizować w ramach funkcji hypervisor systemu, które należy podać. To urządzenie wirtualne umożliwia importowanie danych lokalnych do platformy Azure.
- **Klienci/hosty są połączone z urządzeniem**. Klienci w infrastrukturze, połączyć się z urządzeniem bramy pola danych, które zawierają dane, które muszą być chronione.
- **Magazyn w chmurze**. Lokalizacja platformy w chmurze, w którym są przechowywane dane. Ta lokalizacja jest zazwyczaj konto magazynu, połączone z utworzonego zasobu bramy pola danych.


## <a name="data-box-gateway-service-protection"></a>Ochrona usługi bramy pola danych

Usługa bramy pole danych jest usługą zarządzania, który jest hostowany na platformie Azure. Usługa jest używana do konfigurowania i zarządzania urządzeniem.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Ochrona urządzenia bramy pola danych

Urządzenie bramy pole danych jest aprowizowanej w funkcji hypervisor systemu lokalnego, który podasz urządzenia wirtualnego. Urządzenie pomaga wysyłania danych do platformy Azure. Urządzenia:

- Wymaga klucza aktywacji dostępu do usługi bramy okno usługi Edge i dane pole danych.
- Jest chroniony na cały czas hasła urządzenia.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzeń za pomocą klucza aktywacji

Tylko bramy pola danych urządzenia autoryzowanego może dołączyć do usługi bramy pola danych, utworzonego w ramach subskrypcji platformy Azure. Aby autoryzować urządzenia, musisz aktywacji urządzenia przy użyciu usługi bramy pola danych za pomocą klucza aktywacji.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [uzyskiwanie klucza aktywacji](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzeń za pomocą hasła

Haseł upewnij się, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do danych. Dane bramy pola urządzenia rozruchu w stanie zablokowanym.

Możesz:

- Nawiązać połączenie z lokalnego interfejsu użytkownika urządzenia za pośrednictwem przeglądarki sieci web, a następnie podaj hasło do logowania się na urządzeniu.
- Zdalne łączenie się z interfejsu programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączona. Następnie możesz podać hasło urządzenia, aby zalogować się do urządzenia. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia zdalne urządzenie bramy pola danych](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Użyj lokalnego internetowego interfejsu użytkownika do [zmiany hasła](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Jeśli zmienisz hasło, pamiętaj powiadomić wszystkich użytkowników dostępu zdalnego, aby nie mają problemy z logowaniem.


## <a name="protect-your-data"></a>Ochrona Twoich danych

W tej sekcji opisano funkcje zabezpieczeń bramy pola danych, które ochrony danych podczas przesyłania i przechowywanych.

### <a name="protect-data-at-rest"></a>Ochrona magazynowanych danych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą konta magazynu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Obróć i następnie [synchronizowanie kluczy konta magazynu](data-box-gateway-manage-shares.md#sync-storage-keys) regularnie, aby chronić swoje konto magazynu przed nieautoryzowanymi użytkownikami.

## <a name="manage-personal-information"></a>Zarządzanie informacji osobistych

Usługa bramy pola danych zbiera informacje osobiste w następujących scenariuszach:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp, lub usunąć udział, wykonaj kroki opisane w [Zarządzanie udziałami w bramie pole danych](data-box-gateway-manage-shares.md).

Aby uzyskać więcej informacji, przejrzyj zasady zachowania poufności informacji firmy Microsoft na [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie urządzenia bramy pola danych](data-box-gateway-deploy-prep.md)
