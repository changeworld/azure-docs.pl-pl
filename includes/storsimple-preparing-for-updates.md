---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183424"
---
## <a name="preparing-for-updates"></a>Przygotowanie do aktualizacji
Przed skanowaniem i zastosowaniem aktualizacji należy wykonać następujące kroki:

1. Zrób migawkę danych urządzenia w chmurze.
2. Upewnij się, że kontroler stałych adresów IP są rutowalne i może łączyć się z Internetem. Te stałe ustawienia IP będą używane do obsługi aktualizacji urządzenia. Można to przetestować, uruchamiając następujące polecenie cmdlet na każdym kontrolerze z interfejsu programu Windows PowerShell urządzenia:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Przykładowe dane wyjściowe dla połączenia testowego, gdy stałe adresy IP mogą łączyć się z Internetem**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Po pomyślnym zakończeniu tych ręcznych kontroli wstępnych można przystąpić do skanowania i instalowania aktualizacji.

