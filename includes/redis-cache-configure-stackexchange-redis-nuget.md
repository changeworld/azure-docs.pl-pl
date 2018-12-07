---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 570ed080e208a890bec867b665c4344c502e4d71
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53020217"
---
Aplikacje platformy .NET mogą używać klienta pamięci podręcznej **StackExchange.Redis**, którego można skonfigurować w programie Visual Studio przy użyciu pakietu NuGet upraszczającego konfigurację aplikacji klienta pamięci podręcznej. 

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) w witrynie github i [dokumentacji dotyczącej klienta pamięci podręcznej Redis w pamięci podręcznej StackExchange.Azure](http://github.com/StackExchange/StackExchange.Redis#documentation).
> 
> 

Aby skonfigurować aplikację klienta w programie Visual Studio przy użyciu pakietu NuGet StackExchange.Redis, kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Zarządzaj pakietami NuGet**. 

![Zarządzanie pakietami NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Wpisz **StackExchange.Redis** lub **StackExchange.Redis.StrongName** w polu tekstowym wyszukiwania, wybierz z wyników żądaną wersję i kliknij przycisk **Zainstaluj**.

> [!NOTE]
> Jeśli wolisz użyć wersji biblioteki klienckiej **StackExchange.Redis** o silnej nazwie, wybierz pozycję **StackExchange.Redis.StrongName**; w innym wypadku wybierz pozycję **StackExchange.Redis**.
> 
> 

![Pakiet NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu umożliwiające aplikacji klienta uzyskiwać dostęp do usługi Azure Cache platformy Azure dla pamięci podręcznej Redis za pomocą klienta pamięci podręcznej Redis w pamięci podręcznej StackExchange.Azure.

> [!NOTE]
> Jeśli wcześniej skonfigurowano projekt pod kątem pakietu StackExchange.Redis, można sprawdzić dostępność aktualizacji do pakietu z poziomu **Menedżera pakietów NuGet**. Aby wyszukać i zainstalować zaktualizowane wersje pakietu StackExchange.Redis NuGet, kliknij przycisk **aktualizacje** w **Menedżera pakietów NuGet** okna. Jeśli dostępna jest aktualizacja pakietu NuGet StackExchange.Redis, można zaktualizować projekt tak, aby korzystał ze zaktualizowanej wersji.
> 
> 

Można także zainstalować pakiet NuGet StackExchange.Redis, klikając pozycję **Menedżer pakietów NuGet**, **Konsola menedżera pakietów** w menu **Narzędzia**, a następnie uruchamiając następujące polecenie w oknie **Konsola menedżera pakietów**.
    
```
Install-Package StackExchange.Redis
```
