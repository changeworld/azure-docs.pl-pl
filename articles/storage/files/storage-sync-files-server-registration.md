---
title: Zarządzanie serwerami zarejestrowanych za pomocą synchronizacji plików Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rejestrowanie i wyrejestrowywanie systemu Windows Server z usługą Azure pliku synchronizacji magazynu synchronizacji.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 9367b2bdb1bb77725356d2be41d5e44d900cb927
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Zarządzanie serwerami zarejestrowanych za pomocą synchronizacji plików Azure (wersja zapoznawcza)
Usługa Azure File Sync (wersja zapoznawcza) umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Jest to realizowane poprzez przekształcanie systemów Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

Artykule przedstawiono sposób rejestrowania i zarządzanie serwerem za pomocą usługi synchronizacji magazynu. Zobacz [Wdrażanie synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-deployment-guide.md) informacji na temat wdrażania synchronizacji plików Azure end-to-end.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Register/unregister serwer z magazynu usługi synchronizacji
Rejestrowanie serwera z funkcją synchronizacji plików Azure ustanawia relację zaufania między serwerem systemu Windows Azure. Ta relacja może następnie służyć do tworzenia *punkty końcowe serwera* na serwerze, które reprezentują określonych folderów, które powinny być zsynchronizowane z udziałem plików na platformę Azure (znanej także jako *punktu końcowego w chmurze*). 

### <a name="prerequisites"></a>Wymagania wstępne
Aby zarejestrować serwer z magazynu usługi synchronizacji, należy najpierw przygotować serwera z usługą wymagania wstępne:

* Na serwerze musi być uruchomiony obsługiwanych wersji systemu Windows Server. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje systemu Windows Server](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Upewnij się, że wdrożono usługę synchronizacji magazynu. Aby uzyskać więcej informacji na temat wdrażania usługi synchronizacji magazynu, zobacz [Wdrażanie synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-deployment-guide.md).
* Upewnij się, że serwer ma połączenie z Internetem i czy Azure jest dostępna.
* Wyłącz Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer dla administratorów przy użyciu interfejsu użytkownika Menedżera serwera.
    
    ![Interfejs użytkownika Menedżera serwera z Konfiguracja zwiększonych zabezpieczeń wyróżnione](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Upewnij się, że moduł AzureRM PowerShell jest zainstalowany na serwerze. Jeśli serwer jest członkiem klastra pracy awaryjnej, każdy węzeł w klastrze wymaga modułu AzureRM. Więcej informacji na temat instalowania modułu AzureRM można znaleźć w [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > Zalecamy używanie najnowszej wersji modułu programu AzureRM PowerShell zarejestrować/wyrejestrować serwera. Jeśli pakiet AzureRM został wcześniej zainstalowany na tym serwerze (i 5.* jest wersja programu PowerShell na tym serwerze lub większej), można użyć `Update-Module` polecenia cmdlet tego pakietu aktualizacji. 
* Jeśli korzysta się z serwerem proxy sieci w środowisku, należy skonfigurować ustawienia serwera proxy na serwerze korzystanie z agenta synchronizacji.
    1. Określić użytkownika serwera proxy IP adres i numer portu
    2. Edytuj te dwa pliki:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Dodaj wiersze na rysunku 1 (poniżej tej sekcji) w obszarze /System.ServiceModel w dwóch plikach powyżej zmiana 127.0.0.1:8888 poprawny adres IP (Zastąp 127.0.0.1) i prawidłowego numeru portu (Zastąp 8888):
    4. Ustaw ustawienia serwera proxy WinHTTP przy użyciu wiersza polecenia:
        * Pokaż serwera proxy: netsh winhttp Pokaż serwera proxy
        * Ustaw serwer proxy: netsh winhttp ustawić 127.0.0.1:8888 serwera proxy
        * Resetuj serwera proxy: netsh winhttp resetowania serwera proxy
        * Jeśli jest skonfigurowana, po zainstalowaniu agenta, uruchom ponownie naszych agent synchronizacji: polecenie net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Zarejestruj serwer z magazynu usługi synchronizacji
Zanim serwer może być używany jako *punkt końcowy serwera* synchronizacji plików Azure *grupy synchronizacji*, musi być zarejestrowana w *Usługa synchronizacji magazynu*. Serwer można zarejestrować tylko z jedną usługę synchronizacji magazynu naraz.

#### <a name="install-the-azure-file-sync-agent"></a>Zainstaluj agenta synchronizacji plików Azure
1. [Pobierz agenta synchronizacji plików Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Uruchom Instalatora agenta synchronizacji plików Azure.
    
    ![W okienku pierwszej synchronizacji plików Azure Instalatora agenta](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Pamiętaj włączyć aktualizacje agenta synchronizacji plików Azure przy użyciu usługi Microsoft Update. Jest ważne, ponieważ poprawki błędów krytycznych i ulepszone funkcje umożliwiające pakiet serwera są wysyłane za pośrednictwem usługi Microsoft Update.

    ![Upewnij się, że usługa Microsoft Update jest włączona w okienku usługi Microsoft Update Instalatora agenta synchronizacji plików Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Jeśli serwer nie został wcześniej zarejestrowany, rejestrowania serwera interfejsu użytkownika wyskakującym zostanie natychmiast po zakończeniu instalacji.

> [!Important]  
> Jeśli serwer jest członkiem klastra pracy awaryjnej, agent synchronizacji plików Azure musi można zainstalować na każdym węźle w klastrze.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Zarejestruj serwer przy użyciu rejestrowania serwera interfejsu użytkownika
> [!Important]  
> Cloud Solution Provider (CSP) subskrypcji nie można użyć interfejsu użytkownika rejestrowania serwera. Zamiast tego należy użyć programu PowerShell (poniżej tej sekcji).

1. Jeśli rejestracja serwera interfejsu użytkownika nie zostały uruchomione bezpośrednio po zakończeniu instalacji agenta synchronizacji plików Azure, można było go uruchomić ręcznie, wykonując `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Kliknij przycisk *logowania* można uzyskać dostępu do Twojej subskrypcji platformy Azure. 

    ![Otwieranie okna dialogowego rejestracji serwera interfejsu użytkownika](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Wybierz poprawną subskrypcję, grupy zasobów i usługi synchronizacji magazynu z okna dialogowego.

    ![Informacje o synchronizacji usługi magazynu](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. W wersji zapoznawczej co więcej logowania jest wymagany do ukończenia procesu. 

    ![Zaloguj się w oknie dialogowym](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Jeśli serwer jest członkiem klastra pracy awaryjnej, każdy serwer musi obsługiwać rejestrowania serwera. Po wyświetleniu zarejestrowane serwery w portalu Azure synchronizacji plików Azure automatycznie rozpoznaje każdego węzła jest członkiem tego samego klastra trybu Failover i grupuje je razem odpowiednio.

#### <a name="register-the-server-with-powershell"></a>Zarejestruj serwer przy użyciu programu PowerShell
Można także przeprowadzić rejestracji serwera za pomocą programu PowerShell. To jest jedynym sposobem obsługiwanych serwera rejestracji subskrypcji Cloud Solution Provider (CSP):

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Wyrejestruj serwer z magazynu usługi synchronizacji
Istnieje kilka czynności, które są wymagane do wyrejestrowania serwera z usługą synchronizacji magazynu. Spójrzmy how prawidłowo wyrejestrować serwera.

#### <a name="optional-recall-all-tiered-data"></a>(Opcjonalnie) Odwołaj wszystkie dane warstwowych
Po włączeniu dla serwera punktu końcowego w chmurze będzie warstw *warstwy* plików do sieci udziały plików platformy Azure. Dzięki temu lokalnymi udziały plików do działania jako pamięci podręcznej, zamiast pełną kopię zestawu danych, aby wydajnie korzystać z miejsca na serwerze plików. Jednak jeśli punktu końcowego serwera zostanie usunięty z plikami warstwowych nadal lokalnie na serwerze, pliki staną się można niedostępne. W związku z tym dalsze dostęp do pliku, najpierw odwołać wszystkie pliki warstwowy z plików Azure przed kontynuowaniem wyrejestrowania. 

Można to zrobić za pomocą polecenia cmdlet programu PowerShell w sposób przedstawiony poniżej:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Jeśli lokalnym woluminie obsługującym punkt końcowy serwera nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowych `Invoke-StorageSyncFileRecall` polecenia cmdlet zakończy się niepowodzeniem.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Usuń serwer z wszystkich grup synchronizacji
Przed wyrejestrowywaniem serwera na usługę synchronizacji magazynu, należy usunąć wszystkie serwera punkty końcowe na tym serwerze. Można to zrobić za pośrednictwem portalu Azure:

1. Przejdź do magazynu usługi synchronizacji, gdy serwer jest zarejestrowany.
2. Usuń wszystkie serwera punkty końcowe dla tego serwera w każdej grupie synchronizacji przez usługę synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni serwer punktu końcowego w okienku grupy synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Ponadto można to zrobić za pomocą prostego skryptu PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Wyrejestruj serwer
Wszystkie dane zostały przypomnieć, a serwer został usunięty ze wszystkich grup synchronizacji, serwer można wyrejestrować. 

1. W portalu Azure, przejdź do *zarejestrowanych serwerów* sekcji usługi synchronizacji magazynu.
2. Kliknij prawym przyciskiem myszy na serwerze, który chcesz wyrejestrować, a następnie kliknij przycisk "Wyrejestrowywania serwera".

    ![Wyrejestruj serwer](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zapewnienie synchronizacji plików Azure jest dobrym sąsiada w centrum danych. 
Ponieważ synchronizacji plików Azure będzie rzadko tylko usługa działająca w centrum danych, można ograniczyć użycie sieci i magazynu Azure synchronizacji plików.

> [!Important]  
> Ustawienie zbyt małej limity ma wpływ na wydajność synchronizacji synchronizacji plików Azure i odwołania.

### <a name="set-azure-file-sync-network-limits"></a>Ustaw limity sieci Azure synchronizacji plików
Możliwość ograniczania użycia sieci synchronizacji plików Azure przy użyciu `StorageSyncNetworkLimit` polecenia cmdlet. 

Na przykład można utworzyć nowy limit przepustnicy, aby upewnić się, czy synchronizacji plików Azure nie używa więcej niż 10 MB/s miedzy 9 a 17: 00 (h 17:00) w tygodniu pracy: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Twój limit można wyświetlić za pomocą następującego polecenia cmdlet:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Aby usunąć limity sieci, należy użyć `Remove-StorageSyncNetworkLimit`. Na przykład następujące polecenie spowoduje usunięcie wszystkich limity sieci:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Użyj funkcji QoS magazynowania systemu Windows Server 
Podczas synchronizacji plików Azure znajduje się na maszynie wirtualnej uruchomiona na hoście wirtualizacji systemu Windows Server, można użyć QoS magazynu (jakości usług magazynowania) uregulowanie zużycie we/wy magazynu. Zasad QoS magazynu można ustawić jako maksymalną (lub limit, takich jak jak StorageSyncNetwork limit jest wymuszana powyżej) lub jako minimum (lub rezerwacji). Ustawienie minimalnej zamiast maksymalnie umożliwia synchronizacji plików Azure do serii do użycia przepustowości dostępnej pamięci, jeśli inne obciążenia nie korzystają go. Aby uzyskać więcej informacji, zobacz [jakości usług magazynowania](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Zobacz także
- [Planowanie wdrożenia synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-planning.md)
- [Wdrażanie synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-deployment-guide.md) 
- [Rozwiązywanie problemów z synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-troubleshoot.md)
