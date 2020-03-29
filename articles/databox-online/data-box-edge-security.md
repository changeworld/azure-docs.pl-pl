---
title: Zabezpieczenia usługi Azure Data Box Edge | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, które chronią urządzenie, usługę i dane usługi Azure Data Box Edge lokalnie i w chmurze.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970880"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge security and data protection (Zabezpieczenia i ochrona danych w usłudze Azure Data Box Edge)

Bezpieczeństwo jest poważnym problemem, gdy przyjmujesz nową technologię, zwłaszcza jeśli technologia ta jest używana z poufnymi lub zastrzeżonymi danymi. Usługa Azure Data Box Edge pomaga zapewnić, że tylko autoryzowane jednostki mogą wyświetlać, modyfikować lub usuwać dane.

W tym artykule opisano funkcje zabezpieczeń usługi Data Box Edge, które pomagają chronić każdy ze składników rozwiązania i przechowywane w nich dane.

Usługa Azure Data Box Edge składa się z czterech głównych składników, które współdziałają ze sobą:

- **Usługa Data Box Edge, hostowana na platformie Azure**. Zasób zarządzania używany do tworzenia zamówienia urządzenia, konfigurowania urządzenia, a następnie śledzenia zamówienia do zakończenia.
- **Urządzenie Data Box Edge**. Urządzenie transferu, które jest wysyłane do Ciebie, dzięki czemu można zaimportować dane lokalne na platformę Azure.
- **Klienci/hosty podłączone do urządzenia**. Klienci w infrastrukturze, które łączą się z urządzeniem Data Box Edge i zawierają dane, które muszą być chronione.
- **Przechowywanie w chmurze**. Lokalizacja na platformie chmurowej platformy Azure, na której przechowywane są dane. Ta lokalizacja jest zazwyczaj kontem magazynu połączonym z utworzonym zasobem Data Box Edge.

## <a name="data-box-edge-service-protection"></a>Ochrona usługi Data Box Edge

Usługa Data Box Edge to usługa zarządzania hostowana na platformie Azure. Usługa służy do konfigurowania urządzenia i zarządzania nim.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Ochrona urządzeń Data Box Edge

Urządzenie data box edge to urządzenie lokalne, które pomaga przekształcać dane, przetwarzając je lokalnie, a następnie wysyłając je na platformę Azure. Urządzenie:

- Potrzebuje klucza aktywacyjnego, aby uzyskać dostęp do usługi Data Box Edge.
- Jest zawsze chroniony hasłem urządzenia.
- Jest urządzeniem zablokowanym. Urządzenie BMC i BIOS są chronione hasłem. System BIOS jest chroniony przez ograniczony dostęp użytkownika.
- Ma włączony bezpieczny rozruch.
- Uruchamia osłonę urządzenia usługi Windows Defender. Device Guard umożliwia uruchamianie tylko zaufanych aplikacji zdefiniowanych w zasadach integralności kodu.

### <a name="protect-the-device-via-activation-key"></a>Chroń urządzenie za pomocą klucza aktywacyjnego

Tylko autoryzowane urządzenie data box edge może dołączyć do usługi Data Box Edge utworzonej w ramach subskrypcji platformy Azure. Aby autoryzować urządzenie, musisz użyć klucza aktywacyjnego, aby aktywować urządzenie za pomocą usługi Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [Pobierz klucz aktywacyjny](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Chroń urządzenie za pomocą hasła

Hasła zapewniają dostęp tylko autoryzowanym użytkownikom do twoich danych. Urządzenia Data Box Edge uruchamiają się w stanie zablokowanym.

Możesz:

- Połącz się z lokalnym internetowym interfejsem użytkownika urządzenia za pośrednictwem przeglądarki, a następnie podaj hasło, aby zalogować się do urządzenia.
- Zdalnie połącz się z interfejsem programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zarządzanie zdalne jest domyślnie włączone. Następnie możesz podać hasło urządzenia, aby zalogować się do urządzenia. Aby uzyskać więcej informacji, zobacz [Łączenie zdalnie z urządzeniem Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Użyj lokalnego interfejsu użytkownika sieci Web, aby [zmienić hasło](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Jeśli zmienisz hasło, pamiętaj, aby powiadomić wszystkich użytkowników dostępu zdalnego, aby nie mieli problemów z zalogowaniem się.

## <a name="protect-your-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń usługi Data Box Edge, które chronią dane przesyłane i przechowywane.

### <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- 256-bitowe szyfrowanie bitlocker XTS-AES służy do ochrony danych lokalnych.


### <a name="protect-data-in-flight"></a>Ochrona danych podczas lotu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pośrednictwem kont magazynu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Regularnie obracaj, a następnie [synchronizuj klucze konta magazynu,](data-box-edge-manage-shares.md#sync-storage-keys) aby chronić swoje konto magazynu przed nieautoryzowanymi użytkownikami.

## <a name="manage-personal-information"></a>Zarządzanie informacjami osobowymi

Usługa Data Box Edge zbiera informacje osobiste w następujących scenariuszach:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskiwać dostęp do udziału lub go usuwać, wykonaj czynności opisane w [polu Zarządzanie udziałami na krawędzi pola danych](data-box-edge-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z zasadami zachowania poufności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia Data Box Edge](data-box-edge-deploy-prep.md)
