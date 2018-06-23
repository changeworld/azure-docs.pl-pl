Aplikacje platformy .NET mogą używać klienta pamięci podręcznej **StackExchange.Redis**, którego można skonfigurować w programie Visual Studio przy użyciu pakietu NuGet upraszczającego konfigurację aplikacji klienta pamięci podręcznej. 

> [!NOTE]
> Więcej informacji znajduje się na stronie witryny GitHub [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) oraz w [dokumentacji dotyczącej klienta pamięci podręcznej StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).
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

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu umożliwiające aplikacji klienta uzyskanie dostępu do usługi Azure Redis Cache przy użyciu klienta pamięci podręcznej StackExchange.Redis.

> [!NOTE]
> Jeśli wcześniej skonfigurowano projekt pod kątem pakietu StackExchange.Redis, można sprawdzić dostępność aktualizacji do pakietu z poziomu **Menedżera pakietów NuGet**. Kliknij, aby wyszukać i zainstaluj zaktualizowane wersje pakietu NuGet w programie StackExchange.Redis **aktualizacje** w **Menedżera pakietów NuGet** okna. Jeśli dostępna jest aktualizacja pakietu NuGet StackExchange.Redis, można zaktualizować projekt tak, aby korzystał ze zaktualizowanej wersji.
> 
> 

Można także zainstalować pakiet NuGet StackExchange.Redis, klikając pozycję **Menedżer pakietów NuGet**, **Konsola menedżera pakietów** w menu **Narzędzia**, a następnie uruchamiając następujące polecenie w oknie **Konsola menedżera pakietów**.
    
```
Install-Package StackExchange.Redis
```
