---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/25/2018
ms.author: v-junlch
ms.openlocfilehash: 8ebf5ddfa118e0aeadeab0c00a981871a4b5708e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830298"
---
Aplikacje platformy .NET mogą używać klienta pamięci podręcznej **StackExchange.Redis**, którego można skonfigurować w programie Visual Studio przy użyciu pakietu NuGet upraszczającego konfigurację aplikacji klienta pamięci podręcznej. 

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) w witrynie github i [dokumentacji dotyczącej klienta pamięci podręcznej Redis w pamięci podręcznej StackExchange.Azure](http://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Aby skonfigurować aplikację klienta w programie Visual Studio przy użyciu pakietu NuGet StackExchange.Redis, kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Zarządzaj pakietami NuGet**. 

![Zarządzanie pakietami NuGet](./media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Wpisz **StackExchange.Redis** lub **StackExchange.Redis.StrongName** w polu tekstowym wyszukiwania, wybierz z wyników żądaną wersję i kliknij przycisk **Zainstaluj**.

> [!NOTE]
> Jeśli wolisz użyć wersji biblioteki klienckiej **StackExchange.Redis** o silnej nazwie, wybierz pozycję **StackExchange.Redis.StrongName**; w innym wypadku wybierz pozycję **StackExchange.Redis**.
>
>

![Pakiet NuGet StackExchange.Redis](./media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu umożliwiające aplikacji klienta uzyskiwać dostęp do usługi Azure Cache dla pamięci podręcznej Redis przy użyciu pamięci podręcznej StackExchange.Azure klienta pamięci podręcznej Redis.

> [!NOTE]
> Jeśli wcześniej skonfigurowano projekt pod kątem pakietu StackExchange.Redis, można sprawdzić dostępność aktualizacji do pakietu z poziomu **Menedżera pakietów NuGet**. Aby wyszukać i zainstalować zaktualizowane wersje pakietu StackExchange.Redis NuGet, kliknij przycisk **aktualizacje** w **Menedżera pakietów NuGet** okna. Jeśli dostępna jest aktualizacja pakietu NuGet StackExchange.Redis, można zaktualizować projekt tak, aby korzystał ze zaktualizowanej wersji.
>
>

Można także zainstalować pakiet NuGet StackExchange.Redis, klikając pozycję **Menedżer pakietów NuGet**, **Konsola menedżera pakietów** w menu **Narzędzia**, a następnie uruchamiając następujące polecenie w oknie **Konsola menedżera pakietów**.

```
Install-Package StackExchange.Redis
```

<!-- ms.date: 12/25/2018 -->