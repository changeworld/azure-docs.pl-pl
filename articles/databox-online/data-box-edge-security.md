---
title: Zabezpieczenia krawędź pola danych platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, służących do ochrony urządzeń usługi Azure Data Box Edge, usług i danych lokalnie i w chmurze.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682104"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Usługa Azure krawędź pola danych zabezpieczeń i ochrony danych

Zabezpieczenia są głównym problemem podczas wdrażania nowej technologii, zwłaszcza, jeśli jest to technologia używana przy użyciu danych poufnych lub zastrzeżonych. Microsoft Azure Data Box nowoczesne rozwiązanie pomaga upewnić się, że tylko autoryzowani jednostki można wyświetlić, zmodyfikować lub usunąć swoje dane.

W tym artykule opisano funkcje zabezpieczeń krawędź pola danych, które pomagają w ochronie poszczególne składniki rozwiązania oraz danych przechowywanych na nich.

Rozwiązanie usługi Azure Data Box Edge składa się z czterech głównych składników, które współdziałają ze sobą:

- **Usługa krawędź pola danych hostowanych na platformie Azure** — zasób zarządzania, który umożliwia tworzenie kolejności urządzeń, konfigurowanie urządzenia i śledzić kolejności, aby ukończenie.
- **Urządzenie brzegowe pole danych** — urządzenie do transferu, który jest dostarczany w celu zaimportowania danych lokalnych na platformę Azure.
- **Klienci/hosty są połączone z urządzeniem** — klientów w infrastrukturze, połączyć się z urządzeniem krawędź pola danych, które zawierają dane, które muszą być chronione.
- **Magazyn w chmurze** — lokalizacja w chmurze platformy Azure, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj konto magazynu, połączone z zasobu krawędź pola danych, który został utworzony.

## <a name="data-box-edge-service-protection"></a>Ochrona usługi krawędź pola danych

Usługa krawędź pola danych jest usługą zarządzania hostowanych na platformie Microsoft Azure. Usługa jest używana do konfigurowania i zarządzania urządzeniem.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Ochrona urządzeń krawędź pola danych

Urządzenie brzegowe pole danych jest lokalnym urządzeniem, która pomaga przekształcać dane, przetwarza go lokalnie, a następnie wysyłając je na platformie Azure. Urządzenia:

- Wymaga klucza aktywacji dostępu do usługi krawędź pola danych.
- Jest chroniony na cały czas hasła urządzenia.
- To urządzenie w trybie blokady. Urządzenie BMC i systemu BIOS są chronione hasłem z ograniczonym dostępem użytkownika systemu BIOS.
- Bezpieczny rozruch został włączony.
- Uruchamia program Windows Defender Device Guard. Funkcja Device Guard umożliwia uruchamianie tylko zaufane aplikacje zdefiniowane w zasadach integralności kodu.

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzeń za pomocą klucza aktywacji

Tylko krawędź pola danych urządzenia autoryzowanego może dołączyć do usługi krawędź pola danych, który został utworzony w ramach subskrypcji platformy Azure. Aby autoryzować urządzenia, trzeba użyć klucza aktywacji do aktywacji urządzenia z usługą krawędź pola danych.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, przejdź do [uzyskiwanie klucza aktywacji](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzeń za pomocą hasła

Haseł upewnij się, że Twoje dane są dostępne tylko dla autoryzowanych użytkowników. Dane krawędzi pola urządzenia rozruchu w stanie zablokowanym.

Możesz:

- Nawiązać połączenie z lokalnego interfejsu użytkownika urządzenia za pośrednictwem przeglądarki sieci web, a następnie podaj hasło do logowania się do urządzenia.
- Zdalne łączenie się z interfejsu programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączona. Następnie możesz podać hasło urządzenia, aby zalogować się do urządzenia. Aby uzyskać więcej informacji, przejdź do [Connect zdalnie na urządzenia brzegowe pole danych](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Użyj lokalnego internetowego interfejsu użytkownika do [zmiany hasła](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Jeśli zmienisz hasło, pamiętaj powiadomić wszystkich użytkowników dostępu zdalnego tak, aby nie będą dotykać logowania awarii.

## <a name="protect-the-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń krawędź pola danych, które ochrony danych przesyłanych i przechowywanych danych.

### <a name="protect-data-at-rest"></a>Ochrona magazynowanych danych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą konta magazynu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Obróć i następnie [synchronizowanie kluczy konta magazynu](data-box-edge-manage-shares.md#sync-storage-keys) regularnie, aby pomóc w zapewnieniu, że konto magazynu nie jest używane przez nieautoryzowanych użytkowników.

## <a name="manage-personal-information"></a>Zarządzanie informacji osobistych

Usługa krawędź pola danych zbiera informacje osobiste w następujących przypadkach klucza:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp, lub usunąć udział, wykonaj kroki opisane w [Zarządzanie udziałami na krawędzi pola danych](data-box-edge-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie urządzenia krawędź pola danych](data-box-edge-deploy-prep.md).
