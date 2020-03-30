---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9d9790c9b3dbe3b130be999dd76092ae64f7b52c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182937"
---
|Nazwa parametru| Typ | Opis| Możliwe wartości|
|-|-|-|-|
| /ServerMode|Obowiązkowy|Określa, czy mają zostać zainstalowane oba serwery, konfiguracji i przetwarzania, czy tylko serwer przetwarzania|CS<br>PS|
|/InstallLocation|Obowiązkowy|Folder, w którym są instalowane składniki| Dowolny folder na komputerze|
|/MySQLCredsFilePath|Obowiązkowy|Ścieżka pliku, w której są przechowywane poświadczenia serwera MySQL|Plik powinien mieć format określony poniżej|
|/VaultCredsFilePath|Obowiązkowy|Ścieżka pliku poświadczeń magazynu|Prawidłowa ścieżka pliku|
|/EnvType|Obowiązkowy|Typ środowiska, które chcesz chronić |VMware<br>NonVMware|
|/PSIP|Obowiązkowy|Adres IP karty sieciowej do użytku podczas przesyłania danych replikacji| Dowolny prawidłowy adres IP|
|/CSIP|Obowiązkowy|Adres IP karty sieciowej, na której nasłuchuje serwer konfiguracji| Dowolny prawidłowy adres IP|
|/PassphraseFilePath|Obowiązkowy|Pełna ścieżka do lokalizacji pliku hasła|Prawidłowa ścieżka pliku|
|/BypassProxy|Optional (Opcjonalność)|Określa, że serwer konfiguracji łączy się z platformą Azure bez serwera proxy|Należy uzyskać tę wartość z Venu|
|/ProxySettingsFilePath|Optional (Opcjonalność)|Ustawienia serwera proxy (domyślny serwer proxy wymaga uwierzytelniania lub niestandardowy serwer proxy)|Plik powinien mieć format określony poniżej|
|DataTransferSecurePort|Optional (Opcjonalność)|Numer portu dla protokołu PSIP do użytku z danymi replikacji| Prawidłowy numer portu (wartość domyślna to 9433)|
|/SkipSpaceCheck|Optional (Opcjonalność)|Pomiń sprawdzanie miejsca dla dysku pamięci podręcznej| |
|/AcceptThirdpartyEULA|Obowiązkowy|Flaga implikuje akceptację umowy licencyjnej innego producenta| |
|/ShowThirdpartyEULA|Optional (Opcjonalność)|Wyświetla umowę licencyjną innej firmy. Jeśli zostanie podany w danych wejściowych, wszystkie inne parametry są ignorowane| |
