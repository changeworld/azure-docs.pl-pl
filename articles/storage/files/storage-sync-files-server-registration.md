---
title: Zarządzanie serwerami zarejestrowanych za pomocą usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rejestrować i wyrejestrowywać systemu Windows Server za pomocą usługi Azure File Sync magazynu synchronizacji.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9f1195927acee143a34ec6c74f3ad301194fbc84
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759495"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Zarządzanie serwerami zarejestrowanych za pomocą usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Robi to poprzez przekształcenie serwerów Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

Następujący artykuł przedstawia sposób zarejestrować i zarządzanie serwerem za pomocą usługi synchronizacji magazynu. Zobacz [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md) informacji na temat wdrażania usługi Azure File Sync end-to-end.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Zarejestrować/wyrejestrować serwer za pomocą usługi synchronizacji magazynu
Zarejestrowanie serwera za pomocą usługi Azure File Sync ustanawia relację zaufania między systemem Windows Server i platformą Azure. Ta relacja może następnie służyć do tworzenia *punkty końcowe serwera* na serwerze, które reprezentują określonych folderów, które powinna zostać zsynchronizowana z udziału plików platformy Azure (znany także jako *punkt końcowy w chmurze*). 

### <a name="prerequisites"></a>Wymagania wstępne
Aby zarejestrować serwer przy użyciu usługi synchronizacji magazynu, należy najpierw przygotować serwera przy użyciu niezbędnych wymagań wstępnych:

* Serwer musi działać obsługiwana wersja systemu Windows Server. Aby uzyskać więcej informacji, zobacz [współdziałania i wymagania dotyczące systemu Azure File Sync](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Upewnij się, że wdrożono usługę synchronizacji magazynu. Aby uzyskać więcej informacji na temat wdrażania usługi synchronizacji magazynu, zobacz [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).
* Upewnij się, że serwer jest połączony z Internetem i czy Azure jest dostępna.
* Wyłącz Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer dla administratorów za pomocą interfejsu użytkownika Menedżera serwera.
    
    ![Interfejs użytkownika Menedżera serwera przy użyciu konfiguracji zwiększonych zabezpieczeń wyróżniony](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Upewnij się, że modułu Azure PowerShell jest zainstalowany na serwerze. Jeśli serwer jest członkiem klastra trybu Failover, każdy węzeł w klastrze wymaga modułu Az. Szczegółowe informacje na temat sposobu instalowania modułu Az znajduje się na [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Zalecamy używanie najnowszą wersję modułu programu Az PowerShell zarejestrować/wyrejestrować serwera. Jeśli pakiet Az została wcześniej zainstalowana na tym serwerze (i wersja programu PowerShell na tym serwerze jest 5.* lub nowszej), możesz użyć `Update-Module` polecenia cmdlet tego pakietu aktualizacji. 
* Jeśli korzystanie z serwera proxy sieci w danym środowisku, skonfiguruj ustawienia serwera proxy na serwerze dla agenta synchronizacji wykorzystanie.
    1. Określenia użytkownika serwera proxy IP adres i numer portu
    2. Edytuj te dwa pliki:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Dodaj wiersze na rysunku 1 (poniżej w tej sekcji) w obszarze /System.ServiceModel w dwóch plikach powyżej, zmieniając 127.0.0.1:8888 poprawny adres IP (Zastąp 127.0.0.1) i poprawnego numeru portu (Zastąp 8888):
    4. Ustaw ustawienia serwera proxy WinHTTP za pomocą wiersza polecenia:
        * Pokaż serwera proxy: netsh winhttp Pokaż serwera proxy
        * Ustaw serwer proxy: netsh winhttp Ustaw 127.0.0.1:8888 serwera proxy
        * Resetuj serwer proxy: netsh winhttp resetowania serwera proxy
        * Jeśli jest skonfigurowana, po zainstalowaniu agenta, uruchom ponownie naszego agenta synchronizacji: polecenie net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Rejestrowanie serwera za pomocą usługi synchronizacji magazynu
Zanim serwer może służyć jako *punkt końcowy serwera* w usłudze Azure File Sync *grupy synchronizacji*, musi być zarejestrowana w *Usługa synchronizacji magazynu*. Serwer można zarejestrować tylko przy użyciu jednej usługi synchronizacji magazynu w danym momencie.

#### <a name="install-the-azure-file-sync-agent"></a>Pobieranie agenta usługi Azure File Sync
1. [Pobieranie agenta usługi Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Uruchom Instalatora agenta usługi Azure File Sync.
    
    ![Pierwszego okienka Instalatora agenta usługi Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Pamiętaj włączyć aktualizacje agenta usługi Azure File Sync za pomocą usługi Microsoft Update. Jest ważne, ponieważ krytycznych poprawek i ulepszone funkcje umożliwiające pakiet serwera są dostarczane za pośrednictwem usługi Microsoft Update.

    ![Upewnij się, że Microsoft Update jest włączona w okienku usługi Microsoft Update Instalatora agenta usługi Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Jeśli serwer nie został wcześniej zarejestrowany, rejestracja serwera UI wyskakującym zostanie wyświetlona natychmiast po zakończeniu instalacji.

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu Failover, agenta usługi Azure File Sync musi być zainstalowany na każdym węźle w klastrze.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Rejestrowanie serwera przy użyciu rejestracji serwera interfejsu użytkownika
> [!Important]  
> Cloud Solution Provider (CSP) subskrypcji nie można użyć rejestracji serwera interfejsu użytkownika. Zamiast tego należy użyć programu PowerShell (poniżej w tej sekcji).

1. Jeśli rejestracja serwera interfejsu użytkownika nie zostały uruchomione natychmiast po zakończeniu instalacji agenta usługi Azure File Sync, może być uruchamiany ręcznie, wykonując `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Kliknij przycisk *logowania* na dostęp do Twojej subskrypcji platformy Azure. 

    ![Otwieranie okna dialogowego rejestracji serwera interfejsu użytkownika](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Wybierz poprawną subskrypcję, grupy zasobów i usługi synchronizacji magazynu z poziomu okna dialogowego.

    ![Informacje o usłudze synchronizacji magazynu](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. W wersji zapoznawczej co więcej logowania jest wymagane do ukończenia procesu. 

    ![Zaloguj się w oknie dialogowym](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu Failover, każdy serwer musi przeprowadzić rejestracji serwera. Po wyświetleniu zarejestrowane serwery w witrynie Azure Portal usługi Azure File Sync automatycznie rozpoznaje każdy węzeł jako członka tego samego klastra trybu Failover i grupuje je ze sobą odpowiednio.

#### <a name="register-the-server-with-powershell"></a>Rejestrowanie serwera przy użyciu programu PowerShell
Można również wykonać rejestrowania serwera za pomocą programu PowerShell. Jest to obsługiwana jedynie rejestracji serwera w przypadku subskrypcji Cloud Solution Provider (CSP):

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Wyrejestruj serwer za pomocą usługi synchronizacji magazynu
Istnieje kilka kroków, które są wymagane do Wyrejestruj serwer za pomocą usługi synchronizacji magazynu. Przyjrzyjmy się, jak poprawnie wyrejestrowywanie serwera.

> [!Warning]  
> Nie należy próbować Rozwiązywanie problemów z usługą synchronizacji, obsługi warstw w chmurze lub dowolnego aspektu usługi Azure File Sync, Wyrejestrowywanie i rejestrowanie serwera lub usunięcie i ponowne utworzenie punkty końcowe serwera, chyba że jawnie zalecił inżynier z firmy Microsoft. Wyrejestrowywanie serwera i usuwanie punkty końcowe serwera jest operacją destrukcyjną, a pliki warstwowe na woluminach, które punkty końcowe serwera zostanie nie "ponownie połączony" do ich lokalizacji w udziale plików platformy Azure po zarejestrowanym serwerze i punkty końcowe serwera odtworzone, co spowoduje błędy synchronizacji. Należy również zauważyć, pliki warstwowe, które znajdują się poza obszar nazw punktu końcowego serwera może być trwale utracone. Pliki warstwowe mogą istnieć na serwerze punktów końcowych Obsługa warstw w chmurze nawet wtedy, gdy nigdy nie było włączone.

#### <a name="optional-recall-all-tiered-data"></a>(Opcjonalnie) Odwołaj wszystkie dane warstwowe
Jeśli chcesz, aby pliki, które obecnie są organizowane w warstwy ma być dostępny po usunięciu usługi Azure File Sync (czyli to produkcyjnych, nie testowania, środowiska), należy odwołać wszystkie pliki na każdym woluminie zawierający punkty końcowe serwera. Wyłączanie obsługi warstw na wszystkie punkty końcowe serwera w chmurze, a następnie uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Jeśli lokalnym woluminie obsługującym punkt końcowy serwera nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe `Invoke-StorageSyncFileRecall` polecenie cmdlet zakończy się niepowodzeniem.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Usuń serwer ze wszystkich grup synchronizacji
Przed wyrejestrowaniem serwer, na usługę synchronizacji magazynu, należy usunąć wszystkie punkty końcowe serwera na tym serwerze. Można to zrobić za pomocą witryny Azure portal:

1. Przejdź do usługi synchronizacji magazynu, gdy serwer jest zarejestrowany.
2. Usuń wszystkie punkty końcowe serwera dla tego serwera w każdej grupie synchronizacji w usłudze synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni serwer punktu końcowego w okienku grup synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupą synchronizacji](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Ponadto można to zrobić za pomocą prostego skryptu programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzAccount
Login-AzStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Wyrejestruj serwer
Teraz, gdy wszystkie dane zostały przypomnieć i serwer został usunięty ze wszystkich grup synchronizacji, serwer można wyrejestrować. 

1. W witrynie Azure portal przejdź do *zarejestrowane serwery* sekcji Usługa synchronizacji magazynu.
2. Kliknij prawym przyciskiem myszy na serwerze, które chcesz wyrejestrować, a następnie kliknij przycisk "Wyrejestrowywania serwera".

    ![Wyrejestruj serwer](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zapewnianie usługi Azure File Sync jest dobrym sąsiadem w centrum danych 
Ponieważ usługi Azure File Sync będą rzadko tylko usługi działające w Twoim centrum danych, można ograniczyć użycie sieci i magazynu usługi Azure File Sync.

> [!Important]  
> Ustawienie zbyt niskich limity wpływają na wydajność synchronizacji usługi Azure File Sync i odwołania.

### <a name="set-azure-file-sync-network-limits"></a>Ustaw limity sieci usługa Azure File Sync
Możliwość ograniczania użycia sieci usługi Azure File Sync za pomocą `StorageSyncNetworkLimit` polecenia cmdlet.

> [!Note]  
> Limity sieci nie są stosowane podczas uzyskiwania dostępu do plików warstwowych lub polecenia cmdlet Invoke-StorageSyncFileRecall jest używany.

Na przykład można utworzyć nowy limit przepustowości, aby upewnić się, że usługi Azure File Sync nie korzysta z więcej niż 10 MB/s, między 9: 00 i 17: 00 (godz. 17:00) podczas tygodnia pracy: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Możesz zobaczyć swój limit przy użyciu następującego polecenia cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Aby usunąć limity sieci, należy użyć `Remove-StorageSyncNetworkLimit`. Na przykład następujące polecenie usuwa wszystkie limity sieci:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Użyj funkcji QoS magazynu systemu Windows Server 
Gdy usługi Azure File Sync jest hostowany w maszynie wirtualnej na hoście wirtualizacji systemu Windows Server, można użyć funkcji QoS magazynu (QoS magazynu) sposób użycia operacji We/Wy magazynu. Można ustawić jako maksymalną (lub limit, takie jak sposób wymuszania limitu StorageSyncNetwork powyżej) lub jako co najmniej (lub rezerwacji) zasad QoS magazynu. Ustawienie minimalnej zamiast maksymalnie umożliwia usługi Azure File Sync przejścia do użycia przepustowości dostępnego magazynu, jeśli inne obciążenia nie jest używany. Aby uzyskać więcej informacji, zobacz [jakości usług magazynowania](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Zobacz także
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
- [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md)
