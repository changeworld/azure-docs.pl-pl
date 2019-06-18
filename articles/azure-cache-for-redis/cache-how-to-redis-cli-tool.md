---
title: Jak używać pamięci podręcznej redis — interfejs wiersza polecenia za pomocą usługi Azure Cache dla pamięci podręcznej Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą usługi redis-cli pamięć podręczna systemu Azure dla usługi Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: yegu
ms.openlocfilehash: 318d02f5da816ae8fe2fe199b9c87b3748d5d1fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133019"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Jak używać narzędzia wiersza polecenia usługi Redis z usługą Azure Cache dla pamięci podręcznej Redis

*Usługa redis-cli.exe* to popularne narzędzie wiersza polecenia do interakcji z usługą Azure Cache dla usługi Redis jako klienta. To narzędzie jest również dostępna do użycia z pamięci podręcznej Azure redis Cache.

Narzędzie jest dostępne dla platform Windows, pobierając [Redis narzędzia wiersza polecenia dla Windows](https://github.com/MSOpenTech/redis/releases/). 

Jeśli chcesz uruchomić narzędzie wiersza polecenia na innej platformie, Pobierz pamięć podręczna systemu Azure dla usługi Redis z [ https://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Zbieranie informacji o dostęp do pamięci podręcznej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można zbierać informacje wymagane do dostępu do pamięci podręcznej za pomocą trzech metod:

1. Przy użyciu interfejsu wiersza polecenia platformy Azure [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Platformy Azure za pomocą programu PowerShell [Get AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Przy użyciu witryny Azure portal.

W tej sekcji powoduje pobranie kluczy z witryny Azure portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Zapewnianie dostępu do pamięci podręcznej redis-cli.exe

Z pamięcią podręczną Azure redis cache, tylko SSL port (6380) jest domyślnie włączona. `redis-cli.exe` Narzędzia wiersza polecenia nie obsługuje protokołu SSL. Dostępne są dwie opcje konfiguracji z niego korzystać:

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

