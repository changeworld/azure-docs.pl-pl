---
title: Zabezpieczenia bramy usługi Azure Data Box | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, które chronią urządzenie wirtualne, usługę i dane usługi Azure Data Box Gateway, lokalnie i w chmurze.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900626"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Zabezpieczenia i ochrona danych usługi Azure Data Box Gateway

Bezpieczeństwo jest poważnym problemem, gdy przyjmujesz nową technologię, zwłaszcza jeśli technologia ta jest używana z poufnymi lub zastrzeżonymi danymi. Usługa Azure Data Box Gateway pomaga zapewnić, że tylko autoryzowane jednostki mogą wyświetlać, modyfikować lub usuwać dane.

W tym artykule opisano funkcje zabezpieczeń usługi Azure Data Box Gateway, które pomagają chronić każdy ze składników rozwiązania i przechowywane w nich dane.

Rozwiązanie Bramy pól danych składa się z czterech głównych składników, które współdziałają ze sobą:

- **Usługa Bramy pola danych, hostowana na platformie Azure**. Zasób zarządzania używany do tworzenia zamówienia urządzenia, konfigurowania urządzenia, a następnie śledzenia zamówienia do zakończenia.
- **Urządzenie Bramy pola danych**. Urządzenie wirtualne, które można aprowizować w funkcji hypervisor systemu, który podasz. To urządzenie wirtualne służy do importowania danych lokalnych na platformę Azure.
- **Klienci/hosty podłączone do urządzenia**. Klienci w infrastrukturze, którzy łączą się z urządzeniem Bramy pola danych i zawierają dane, które muszą być chronione.
- **Przechowywanie w chmurze**. Lokalizacja na platformie chmurowej platformy Azure, na której przechowywane są dane. Ta lokalizacja jest zazwyczaj kontem magazynu połączonym z utworzonym zasobem bramy pola danych.


## <a name="data-box-gateway-service-protection"></a>Ochrona usługi Brama pól danych

Usługa Bramy pól danych to usługa zarządzania hostowana na platformie Azure. Usługa służy do konfigurowania urządzenia i zarządzania nim.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Ochrona urządzeń bramy pól danych

Data Box Gateway urządzenie jest urządzeniem wirtualnym, które jest aprowizowana w funkcji hypervisor systemu lokalnego, który można podać. Urządzenie pomaga wysyłać dane na platformę Azure. Urządzenie:

- Potrzebuje klucza aktywacyjnego, aby uzyskać dostęp do usługi Data Box Edge/Data Box Gateway.
- Jest zawsze chroniony hasłem urządzenia.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Chroń urządzenie za pomocą klucza aktywacyjnego

Tylko autoryzowane urządzenie bramy pola danych może dołączyć do usługi Bramy pola danych utworzonej w ramach subskrypcji platformy Azure. Aby autoryzować urządzenie, musisz użyć klucza aktywacyjnego, aby aktywować urządzenie za pomocą usługi Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [Pobierz klucz aktywacyjny](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Chroń urządzenie za pomocą hasła

Hasła zapewniają dostęp tylko autoryzowanym użytkownikom do twoich danych. Urządzenia bramy pola danych uruchamiają się w stanie zablokowanym.

Możesz:

- Połącz się z lokalnym internetowym interfejsem użytkownika urządzenia za pośrednictwem przeglądarki, a następnie podaj hasło, aby zalogować się do urządzenia.
- Zdalnie połącz się z interfejsem programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zarządzanie zdalne jest domyślnie włączone. Następnie możesz podać hasło urządzenia, aby zalogować się do urządzenia. Aby uzyskać więcej informacji, zobacz [Łączenie zdalnie z urządzeniem Bramy skrzynki danych](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Użyj lokalnego interfejsu użytkownika sieci Web, aby [zmienić hasło](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Jeśli zmienisz hasło, pamiętaj, aby powiadomić wszystkich użytkowników dostępu zdalnego, aby nie mieli problemów z zalogowaniem się.


## <a name="protect-your-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń bramy pól danych, które chronią dane przesyłane i przechowywane.

### <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrona danych podczas lotu

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pośrednictwem kont magazynu

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Regularnie obracaj, a następnie [synchronizuj klucze konta magazynu,](data-box-gateway-manage-shares.md#sync-storage-keys) aby chronić swoje konto magazynu przed nieautoryzowanymi użytkownikami.

## <a name="manage-personal-information"></a>Zarządzanie informacjami osobowymi

Usługa Data Box Gateway zbiera informacje osobiste w następujących scenariuszach:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskiwać dostęp do udziału lub go usuwać, wykonaj czynności opisane w [oknie Zarządzanie udziałami w bramie pól danych](data-box-gateway-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z zasadami zachowania poufności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia bramy skrzynki danych](data-box-gateway-deploy-prep.md)
