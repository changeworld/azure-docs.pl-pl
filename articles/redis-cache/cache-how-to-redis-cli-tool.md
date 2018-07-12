---
title: Jak używać usługi redis — interfejs wiersza polecenia za pomocą usługi Azure Redis Cache | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi redis — interfejs wiersza polecenia za pomocą usługi Azure Redis Cache.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299095"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Jak używać narzędzia wiersza polecenia usługi Redis z usługą Azure Redis Cache

*Usługa redis-cli.exe* to popularne narzędzie wiersza polecenia do interakcji z usługą Redis Cache jako klienta. To narzędzie jest również dostępny do użycia z usługą Azure Redis Cache.

Narzędzie jest dostępne dla platform Windows, pobierając [Redis narzędzia wiersza polecenia dla Windows](https://github.com/MSOpenTech/redis/releases/). 

Jeśli chcesz uruchomić narzędzie wiersza polecenia na innej platformie, Pobierz pamięci podręcznej Redis z [ http://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Zbieranie informacji o dostęp do pamięci podręcznej

Można zbierać informacje wymagane do dostępu do pamięci podręcznej za pomocą trzech metod:

1. Przy użyciu interfejsu wiersza polecenia platformy Azure [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Platformy Azure za pomocą programu PowerShell [Get AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Przy użyciu witryny Azure portal.

W tej sekcji powoduje pobranie kluczy z witryny Azure portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Zapewnianie dostępu do pamięci podręcznej redis-cli.exe

Za pomocą usługi Azure Redis Cache tylko SSL port (6380) jest domyślnie włączona. `redis-cli.exe` Narzędzia wiersza polecenia nie obsługuje protokołu SSL. Dostępne są dwie opcje konfiguracji z niego korzystać:

1. [Włącz bez użycia protokołu SSL port (6379 został)](cache-configure.md#access-ports) - **ta konfiguracja nie jest zalecane** ponieważ w tej konfiguracji klucze dostępu są wysyłane za pośrednictwem protokołu TCP w postaci zwykłego tekstu. Ta zmiana może naruszyć bezpieczeństwo dostępu do pamięci podręcznej. Jedyny scenariusz, w którym należy rozważyć tej konfiguracji jest, gdy tylko uzyskujesz dostęp do pamięci podręcznej testu.

2. Pobierz i zainstaluj [stunnel](https://www.stunnel.org/downloads.html).

    Uruchom **Start graficznego interfejsu użytkownika programu stunnel** można uruchomić serwera.

    Kliknij prawym przyciskiem myszy ikonę na pasku zadań na serwer programu stunnel, a następnie kliknij przycisk **Pokaż okno Dziennik**.

    Polecenie menu Okno Dziennik programu stunnel **konfiguracji** > **Edycja konfiguracji** można otworzyć bieżącego pliku konfiguracji.

    Dodaj następujący wpis dotyczący *redis-cli.exe* w obszarze **usługi definicje** sekcji. Wstaw swoją nazwą rzeczywistego pamięci podręcznej, zamiast `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Zapisz i zamknij plik konfiguracji. 
  
    Polecenie menu Okno Dziennik programu stunnel **konfiguracji** > **ponowne załadowanie konfiguracji**.


## <a name="connect-using-the-redis-command-line-tool"></a>Połącz przy użyciu narzędzia wiersza polecenia usługi Redis.

Korzystając z programu stunnel, uruchamianie *redis-cli.exe*i przekaż tylko Twojej *portu*, i *klucz dostępu* (podstawowe lub pomocnicze), aby nawiązać połączenie z pamięci podręcznej.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel przy użyciu usługi redis — interfejs wiersza polecenia](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Jeśli używasz testów pamięć podręczną z **niezabezpieczonych** port bez protokołu SSL, uruchom `redis-cli.exe` i przekazać swoje *nazwy hosta*, *portu*, i *klucz dostępu*(podstawowe lub pomocnicze), połączyć się z pamięci podręcznej testu.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel przy użyciu usługi redis — interfejs wiersza polecenia](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o korzystaniu z [konsolę pamięci podręcznej Redis](cache-configure.md#redis-console) do wydawania poleceń.

