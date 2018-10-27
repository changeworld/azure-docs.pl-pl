---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e743128932f6a136820819c21e0386eb678c4203
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165052"
---
<!--author=jgerend last changed: 03/16/16-->

## <a name="preparing-for-updates"></a>Przygotowywanie do aktualizacji
Konieczne będzie przed skanowanie i stosowanie aktualizacji należy wykonać następujące czynności:

1. Migawkę w chmurze danych urządzenia.
2. Upewnij się, że Twoje stałe adresy IP kontrolera podlegają routingowi i mogą łączyć się z Internetem. Te stałe adresy IP będą używane do obsługi aktualizacji na urządzeniu. Można to sprawdzić, uruchamiając następujące polecenie cmdlet na każdym kontrolerze z interfejsu programu Windows PowerShell urządzenia:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **Przykładowe dane wyjściowe dla połączenie testowe, gdy stałe adresy IP można połączyć się z Internetem**

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

Po ręcznej kontroli wstępnej została pomyślnie ukończona, możesz przejść do skanowania i zainstalować aktualizacje.

