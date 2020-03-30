---
title: Jak korzystać z redis-cli z usługą Azure Cache for Redis
description: Dowiedz się, jak używać *programu redis-cli.exe* jako narzędzia wiersza polecenia do interakcji z pamięcią podręczną azure dla programu Redis jako klienta.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: a48e69f19db88c7823365964c2fe9c0629a078bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412686"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Jak korzystać z narzędzia wiersza polecenia Redis z usługą Azure Cache for Redis

*redis-cli.exe* to popularne narzędzie wiersza polecenia do interakcji z pamięcią podręczną Azure dla redis jako klienta. To narzędzie jest również dostępne do użytku z pamięcią podręczną Azure dla redis.

Narzędzie jest dostępne dla platform Windows, pobierając [narzędzia wiersza polecenia Redis dla systemu Windows](https://github.com/MSOpenTech/redis/releases/). 

Jeśli chcesz uruchomić narzędzie wiersza polecenia na innej platformie, [https://redis.io/download](https://redis.io/download)pobierz usługę Azure Cache for Redis z programu .

## <a name="gather-cache-access-information"></a>Zbieranie informacji o dostępie do pamięci podręcznej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można zebrać informacje potrzebne do uzyskania dostępu do pamięci podręcznej przy użyciu trzech metod:

1. Narzędzie cli platformy Azure przy użyciu [kluczy listy az redis](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Program Azure PowerShell przy użyciu [funkcji Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Za pomocą witryny Azure Portal.

W tej sekcji pobierzesz klucze z witryny Azure portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Włącz dostęp dla redis-cli.exe

W przypadku usługi Azure Cache for Redis domyślnie włączono tylko port SSL (6380). Narzędzie `redis-cli.exe` wiersza polecenia nie obsługuje ssl. Masz dwie opcje konfiguracji, aby go używać:

1. [Włącz port nienawiązywać się do protokołu SSL (6379)](cache-configure.md#access-ports) - **Ta konfiguracja nie jest zalecana,** ponieważ w tej konfiguracji klucze dostępu są wysyłane za pośrednictwem protokołu TCP w postaci zwykłego tekstu. Ta zmiana może naruszyć dostęp do pamięci podręcznej. Jedynym scenariuszem, w którym można rozważyć tę konfigurację jest, gdy jesteś tylko dostęp do pamięci podręcznej testów.

2. Pobierz i zainstaluj [stunnel](https://www.stunnel.org/downloads.html).

    Uruchom **stunnel GUI Start,** aby uruchomić serwer.

    Kliknij prawym przyciskiem myszy ikonę paska zadań dla serwera stunnel i kliknij polecenie **Pokaż okno dziennika**.

    W menu stunnel Log Window kliknij polecenie**Konfiguracja edycji** **konfiguracji,** > aby otworzyć bieżący plik konfiguracyjny.

    Dodaj następujący wpis dla *redis-cli.exe* w sekcji **Definicje usługi.** Wstaw rzeczywistą nazwę `yourcachename`pamięci podręcznej zamiast pliku . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Zapisz i zamknij plik konfiguracyjny. 
  
    W menu okno dziennika ogłusu kliknij polecenie > **Konfiguracja ponownie załaduj konfigurację**. **Configuration**


## <a name="connect-using-the-redis-command-line-tool"></a>Połącz się za pomocą narzędzia wiersza polecenia Redis.

Podczas korzystania z stunnel uruchom *program redis-cli.exe*i przekaż tylko *port*i *klucz dostępu* (podstawowy lub pomocniczy), aby połączyć się z pamięcią podręczną.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel z redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Jeśli używasz pamięci podręcznej testowej z **niezabezpieczonym** portem `redis-cli.exe` innego niż SSL, uruchom i przekaż *nazwę hosta,* *port*i *klucz dostępu* (podstawowy lub pomocniczy), aby połączyć się z pamięcią podręczną testową.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel z redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o używaniu [konsoli Redis](cache-configure.md#redis-console) do wydawania poleceń.

