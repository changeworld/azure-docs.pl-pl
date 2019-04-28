---
title: Zabezpieczenia krawędź pola danych platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, które chronią usługi Azure Data Box Edge urządzenia, usług i danych lokalnych i w chmurze.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756238"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Usługa Azure krawędź pola danych zabezpieczeń i ochrony danych

Bezpieczeństwo jest głównym problemem, gdy jest przyjęcie nowych technologii, zwłaszcza, jeśli jest to technologia używana przy użyciu danych poufnych lub zastrzeżonych. Azure krawędź pola danych pomaga należy upewnić się tylko autoryzowane jednostki można wyświetlić, zmodyfikować lub usunąć swoje dane.

W tym artykule opisano funkcje zabezpieczeń krawędź pola danych, które pomagają w ochronie wszystkich składników rozwiązania i przechowywane w nich dane.

Usługa Azure Edge pole danych składa się z czterech głównych składników, które współdziałają ze sobą:

- **Usługa krawędź pola danych, hostowanych na platformie Azure**. Zasób zarządzania, który umożliwia tworzenie kolejności urządzeń, skonfigurowanie urządzenia i śledzić kolejności do zakończenia.
- **Urządzenie brzegowe pole danych**. Urządzenie transferu, które jest dostarczane do Ciebie, dzięki czemu można zaimportować danych lokalnych na platformę Azure.
- **Klienci/hosty są połączone z urządzeniem**. Klienci w infrastrukturze, połączyć się z urządzeniem krawędź pola danych, które zawierają dane, które muszą być chronione.
- **Magazyn w chmurze**. Lokalizacja platformy w chmurze, w którym są przechowywane dane. Ta lokalizacja jest zazwyczaj konta magazynu, połączone do zasobu krawędź pola danych, którą tworzysz.

## <a name="data-box-edge-service-protection"></a>Ochrona usługi krawędź pola danych

Usługa krawędź pola danych jest usługą zarządzania, który jest hostowany na platformie Azure. Usługa jest używana do konfigurowania i zarządzania urządzeniem.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Ochrona urządzeń krawędź pola danych

Urządzenie brzegowe pole danych jest lokalnym urządzeniem, która pomaga przekształcać dane, przetwarza go lokalnie, a następnie wysyłając je na platformie Azure. Urządzenia:

- Wymaga klucza aktywacji dostępu do usługi krawędź pola danych.
- Jest chroniony na cały czas hasła urządzenia.
- To urządzenie w trybie blokady. Urządzenie BMC i systemu BIOS jest chroniony hasłem. System BIOS jest chroniony przez ograniczony dostęp użytkownika.
- Bezpieczny rozruch został włączony.
- Uruchamia program Windows Defender Device Guard. Funkcja Device Guard można uruchomić tylko zaufane aplikacje zdefiniowane w zasadach integralności kodu.

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzeń za pomocą klucza aktywacji

Tylko krawędź pola danych urządzenia autoryzowanego może dołączyć do usługi krawędź pola danych, utworzonego w ramach subskrypcji platformy Azure. Aby autoryzować urządzenia, musisz aktywacji urządzenia przy użyciu usługi krawędź pola danych za pomocą klucza aktywacji.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [uzyskiwanie klucza aktywacji](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzeń za pomocą hasła

Haseł upewnij się, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do danych. Dane krawędzi pola urządzenia rozruchu w stanie zablokowanym.

Możesz:

- Nawiązać połączenie z lokalnego interfejsu użytkownika urządzenia za pośrednictwem przeglądarki sieci web, a następnie podaj hasło do logowania się na urządzeniu.
- Zdalne łączenie się z interfejsu programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączona. Następnie możesz podać hasło urządzenia, aby zalogować się do urządzenia. Aby uzyskać więcej informacji, zobacz [Connect zdalnie na urządzenia brzegowe pole danych](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Użyj lokalnego internetowego interfejsu użytkownika do [zmiany hasła](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Jeśli zmienisz hasło, pamiętaj powiadomić wszystkich użytkowników dostępu zdalnego, aby nie musieli problemy z logowaniem.

## <a name="protect-your-data"></a>Ochrona Twoich danych

W tej sekcji opisano funkcje zabezpieczeń krawędź pola danych, które ochrony danych podczas przesyłania i przechowywanych.

### <a name="protect-data-at-rest"></a>Ochrona magazynowanych danych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą konta magazynu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Obróć i następnie [synchronizowanie kluczy konta magazynu](data-box-edge-manage-shares.md#sync-storage-keys) regularnie, aby chronić swoje konto magazynu przed nieautoryzowanymi użytkownikami.

## <a name="manage-personal-information"></a>Zarządzanie informacji osobistych

Usługa krawędź pola danych zbiera informacje osobiste w następujących scenariuszach:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp, lub usunąć udział, wykonaj kroki opisane w [Zarządzanie udziałami na krawędzi pola danych](data-box-edge-manage-shares.md).

Aby uzyskać więcej informacji, przejrzyj zasady zachowania poufności informacji firmy Microsoft na [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie urządzenia krawędź pola danych](data-box-edge-deploy-prep.md)
