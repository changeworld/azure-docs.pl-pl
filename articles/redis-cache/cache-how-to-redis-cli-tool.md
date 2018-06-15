---
title: Sposób użycia interfejsu wiersza polecenia redis z pamięci podręcznej Redis Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć interfejsu wiersza polecenia redis z pamięci podręcznej Redis Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182591"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Jak używać narzędzia wiersza polecenia Redis z pamięci podręcznej Redis Azure

*redis cli.exe* popularnych narzędzie wiersza polecenia do interakcji z pamięci podręcznej Redis jako klienta. To narzędzie jest również dostępny do użycia z pamięci podręcznej Redis Azure.

Narzędzie to jest dostępna dla platformy systemu Windows, pobierając [Redis narzędzia wiersza polecenia dla systemu Windows](https://github.com/MSOpenTech/redis/releases/). 

Jeśli chcesz uruchomić narzędzie wiersza polecenia na innej platformie, Pobierz pamięci podręcznej Redis z [ http://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Zbieranie informacji o dostępie do pamięci podręcznej

Możesz zbierać informacje potrzebne do pamięci podręcznej, za pomocą trzech metod:

1. Przy użyciu interfejsu wiersza polecenia platformy Azure [az redis listy kluczy](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure przy użyciu programu PowerShell [Get AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Przy użyciu portalu Azure.

W tej sekcji klucze będą pobierane z portalu Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Zapewnianie dostępu do pamięci podręcznej redis cli.exe

W pamięci podręcznej Redis Azure, tylko SSL portu (6380) jest domyślnie włączona. `redis-cli.exe` Narzędzia wiersza polecenia nie obsługuje protokołu SSL. Dostępne są dwie opcje konfiguracji, aby użyć go:

1. [Włącz (6379) portu bez protokołu SSL](cache-configure.md#access-ports) - **ta konfiguracja nie jest zalecana** ponieważ w tej konfiguracji klucze dostępu są wysyłane za pośrednictwem protokołu TCP w postaci zwykłego tekstu. Ta zmiana może naruszyć dostępu do pamięci podręcznej. Jedyny scenariusz, w którym należy rozważyć tej konfiguracji jest, jeśli tylko uzyskujesz dostęp do pamięci podręcznej testu.

2. Pobierz i zainstaluj [stunnel](https://www.stunnel.org/downloads.html).

    Uruchom **stunnel GUI Start** do uruchomienia serwera.

    Kliknij prawym przyciskiem myszy ikonę na pasku zadań na serwerze stunnel, a następnie kliknij przycisk **Pokaż okno Dziennik**.

    W menu stunnel okno Dziennik, kliknij polecenie **konfiguracji** > **Edycja konfiguracji** otworzyć bieżącego pliku konfiguracji.

    Dodaj następujący wpis dotyczący *redis cli.exe* w obszarze **usługi definicje** sekcji. Wstaw nazwę rzeczywiste pamięci podręcznej, zamiast `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Zapisz i zamknij plik konfiguracji. 
  
    W menu stunnel okno Dziennik, kliknij polecenie **konfiguracji** > **ponownego załadowania konfiguracji**.


## <a name="connect-using-the-redis-command-line-tool"></a>Połącz przy użyciu narzędzia wiersza polecenia Redis.

Korzystając z stunnel, uruchom *redis cli.exe*i przekazywać tylko z *portu*, i *klucz dostępu* (podstawowej lub pomocniczej), aby nawiązać połączenie pamięci podręcznej.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel za pomocą interfejsu wiersza polecenia redis](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Jeśli korzystasz z pamięci podręcznej testu **niezabezpieczone** portu bez protokołu SSL, uruchom `redis-cli.exe` i przekaż Twojej *nazwy hosta*, *portu*, i *klucz dostępu*(podstawowej lub pomocniczej) nawiązywania połączenia z pamięci podręcznej testu.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel za pomocą interfejsu wiersza polecenia redis](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o korzystaniu z [Redis konsoli](cache-configure.md#redis-console) do wydawania poleceń.

