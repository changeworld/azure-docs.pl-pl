---
title: Często zadawane pytania dotyczące plików NetApp platformy Azure | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Files NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806389"
---
# <a name="faqs-about-azure-netapp-files"></a>Często zadawane pytania dotyczące plików NetApp platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczących usługi Azure Files NetApp. 

## <a name="networking-faqs"></a>Sieć — często zadawane pytania

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Ścieżka danych systemu plików NFS przechodzi przez Internet?  

Nie. Ścieżka danych systemu plików NFS nie przechodzi przez Internet. Usługa Azure Files NetApp to Azure macierzystej usługi, która jest wdrażana w sieci wirtualnej platformy Azure (VNet) gdzie usługa jest dostępna. Usługi Azure Files NetApp używa delegowanego podsieci i obsługuje interfejs sieciowy bezpośrednio w sieci wirtualnej. 

Zobacz [wskazówki dotyczące usługi Azure Files NetApp sieci, planowanie](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) Aby uzyskać szczegółowe informacje.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Czy można połączyć sieć wirtualną, która już została utworzona, w usłudze Azure NetApp Files?

Tak, możesz połączyć sieci wirtualne, który został utworzony w usłudze. 

Zobacz [wskazówki dotyczące usługi Azure Files NetApp sieci, planowanie](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) Aby uzyskać szczegółowe informacje.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Czy można zainstalować woluminu systemu plików NFS plików NetApp platformy Azure, używając nazwy DNS FQDN?

Tak, możesz, jeśli tworzysz wymagane wpisy DNS. Usługa Azure Files NetApp dostarcza IP usługi dla aprowizowanego woluminu. 

> [!NOTE] 
> Usługa Azure Files NetApp można wdrożyć dodatkowe adresy IP dla usługi, stosownie do potrzeb.  Wpisy DNS może być konieczne okresowo aktualizowana.

## <a name="security-faqs"></a>Często zadawane pytania zabezpieczeń

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Ruch sieciowy między maszyną Wirtualną Azure i magazynu mogą być szyfrowane?

Ruch w sieci (ruch z NFSv3 lub SMBv3 klienta do usługi Azure Files NetApp woluminów) nie są szyfrowane. Jednak ruch z Maszyną wirtualną platformy Azure (uruchomienie klienta systemu plików NFS i protokołu SMB) do usługi Azure Files NetApp jest tak bezpieczne, jak pozostałe rodzaje ruchu Azure VM-Maszynami wirtualnymi. Ten ruch jest lokalnej do sieci centrum danych platformy Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Magazyn można szyfrować magazynowane?

Wszystkie woluminy plików NetApp platformy Azure są szyfrowane przy użyciu standardu FIPS 140-2 standard. Wszystkie klucze są zarządzane przez usługę Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Jak klucze szyfrowania są zarządzane? 

Zarządzanie kluczami dla usługi Azure Files NetApp jest obsługiwane przez usługę.  Obecnie zarządzana przez użytkownika kluczy (Bring Your własne klucze) nie są obsługiwane.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Można skonfigurować reguły zasad eksportu systemu plików NFS, kontrolować dostęp do miejsca docelowego instalacji usługi Azure NetApp Files?


Tak, można skonfigurować maksymalnie pięć reguł w jednych zasad eksportu systemu plików NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Usługi Azure Files NetApp obsługuje sieciowe grupy zabezpieczeń?

Nie, obecnie nie można zastosować sieciowych grup zabezpieczeń z podsiecią delegowanego plików NetApp platformy Azure lub interfejsów sieciowych utworzonych przez usługę.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Czy można używać platformy Azure, zarządzania tożsamościami i Dostępem, za pomocą usługi Azure Files NetApp?

Tak, usługi Azure Files NetApp obsługuje funkcje RBAC przy użyciu platformy Azure, zarządzania tożsamościami i Dostępem.

## <a name="performance-faqs"></a>Wydajność — często zadawane pytania

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Co należy zrobić, aby zoptymalizować lub dostosowywanie wydajności usługi Azure Files NetApp?

Można wykonać następujące czynności na wymagania dotyczące wydajności: 
- Upewnij się, że maszyna wirtualna ma odpowiedni rozmiar.
- Włącz przyspieszona sieć dla maszyny Wirtualnej.
- Wybierz poziom żądanej usługi i rozmiar pojemności puli.
- Utwórz wolumin o rozmiarze żądany limit przydziału pojemności i wydajności.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Jak przekonwertować poziomów przepływności usługi Azure Files NetApp na operacje We/Wy?

MB/s można przekonwertować na operacje We/Wy przy użyciu następującej formuły:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Jak zmienić poziom usługi woluminu?

Zmiana poziomu usługi woluminu nie jest obecnie obsługiwane.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Jak monitorować wydajność usługi Azure Files NetApp?

Usługa Azure Files NetApp dostarcza metryki wydajności woluminu. Umożliwia także usługi Azure Monitor do monitorowania metryk użycia dla usługi Azure Files NetApp.  Zobacz [metryki dla usługi Azure Files NetApp](azure-netapp-files-metrics.md) listy metryki wydajności dla usługi Azure Files NetApp.

## <a name="nfs-faqs"></a>Systemu plików NFS — często zadawane pytania

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chcę mieć wolumin instalowane automatycznie podczas uruchamiania lub ponownie uruchomić Maszynę wirtualną platformy Azure.  Jak skonfigurować Mój hosta dla trwałego woluminy systemu plików NFS?

Dla woluminu systemu plików NFS, automatycznego instalowania na początek maszyny Wirtualnej lub ponowne uruchomienie komputera, należy dodać wpis do `/etc/fstab` pliku na hoście. 

Na przykład: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    Adres IP usługi Azure Files NetApp woluminu w bloku właściwości woluminu
- $FILEPATH  
    Ścieżka eksportu woluminu, usługi Azure Files NetApp
- $MOUNTPOINT  
    Katalog, tworzona na hoście systemu Linux, używana do instalowania systemu plików NFS eksportu

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Dlaczego polecenie DF na komputerze klienckim systemu plików NFS nie uwzględnia aprowizowanego rozmiaru woluminu?

Rozmiar woluminu, DF zgłaszany jest maksymalny rozmiar woluminu NetApp usługi pliki Azure może zwiększyć się. Rozmiar woluminu plików NetApp platformy Azure w poleceniu DF nie jest odbijającą limitu przydziału lub rozmiaru woluminu.  Usługi Azure Files NetApp rozmiaru woluminu lub limitu przydziału można uzyskać za pośrednictwem witryny Azure portal lub interfejsu API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jakiej wersji systemu plików NFS obsługuje usługi Azure Files NetApp?

Usługa Azure Files NetApp obsługuje obecnie NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Jak włączyć zgniatanie głównego

Główny zgniatanie nie jest obecnie obsługiwane.

## <a name="smb-faqs"></a>Protokół SMB — często zadawane pytania

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Usługa Azure NetApp Files obsługuje usługi Azure Active Directory?

Nie, obecnie nie jest obsługiwane.  Usługa Azure Files NetApp obsługuje Active Directory Domain Services (Przynieś swoje Own AD), którego można używać istniejących kontrolerów domeny Active Directory z usługą Azure Files NetApp. Kontrolery domeny mogą znajdować się na platformie Azure, jako maszyn wirtualnych lub w środowisku lokalnym za pośrednictwem usługi ExpressRoute.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Jest wymagane dla dostęp przez protokół SMB połączenie z usługi Active Directory? 

Tak, należy utworzyć połączenie usługi Active Directory przed wdrożeniem woluminie SMB. Określone kontrolery domeny musi być dostępny za pomocą delegowanego podsieci usługi Azure Files NetApp do pomyślnego nawiązania połączenia.  Zobacz [Utwórz wolumin SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) Aby uzyskać szczegółowe informacje. 

### <a name="how-many-active-directory-connections-are-supported"></a>Liczba połączeń z usługi Active Directory są obsługiwane?

Usługa Azure Files NetApp obsługuje obecnie jedno połączenie usługi Active Directory na subskrypcję. Ponadto połączenia usługi Active Directory zależy od jednego konta NetApp; nie jest udostępniony na kontach. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Jakie wersje Windows AD są obsługiwane?

Usługa Azure Files NetApp obsługuje system Windows Server 2008r2SP1 2016 wersję Active Directory Domain Services.

## <a name="capacity-management-faqs"></a>Zarządzanie pojemnością — często zadawane pytania

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Jak monitorować użycie pojemności puli i liczby plików NetApp Azure? 

Usługa Azure Files NetApp zapewnia metryki użycia pojemności puli i woluminów. Usługa Azure Monitor umożliwia również monitorowanie użycia dla usługi Azure Files NetApp. Zobacz [metryki dla usługi Azure Files NetApp](azure-netapp-files-metrics.md) Aby uzyskać szczegółowe informacje. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Może zarządzać plików NetApp platformy Azure za pomocą Eksploratora usługi Azure Storage?

Nie. Usługa Azure Files NetApp nie jest obsługiwana przez Eksploratora usługi Azure Storage.

## <a name="data-migration-and-protection-faqs"></a>Dane migracji i ochrona — często zadawane pytania

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Jak migrować dane do usługi Azure Files NetApp?
Usługa Azure Files NetApp oferuje woluminy systemu plików NFS i protokołu SMB.  Aby przeprowadzić migrację danych do usługi, można użyć dowolnego narzędzia kopiowania opartej na plikach. 

NetApp oferuje rozwiązanie oparte na rozwiązaniach SaaS, [synchronizacji chmury NetApp](https://cloud.netapp.com/cloud-sync-service).  To rozwiązanie umożliwia replikowanie systemu plików NFS lub udziały SMB danych Azure NetApp plików NFS, eksportowanie lub SMB. 

Umożliwia także szeroką gamę bezpłatne narzędzia do kopiowania danych. Dla systemu plików NFS, można użyć narzędzia obciążeń takich jak [rsync](https://rsync.samba.org/examples.html) do kopiowania i Synchronizuj źródło danych do usługi Azure Files NetApp woluminu. W przypadku protokołu SMB, można użyć obciążeń [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) w taki sam sposób.  Te narzędzia można również replikować uprawnienia do pliku lub folderu. 

Wymagania dotyczące migracji danych z lokalnych do usługi Azure Files NetApp są następujące: 

- Upewnij się, że pliki NetApp Azure jest dostępny w miejscu docelowym regionem świadczenia usługi Azure.
- Weryfikowanie łączności sieciowej między źródłem a usługą Azure Files NetApp docelowy adres IP woluminu. Transfer danych między lokalnie i usługa Azure NetApp Files jest obsługiwany za pośrednictwem usługi ExpressRoute.
- Utwórz element docelowy wolumin usługi Azure Files NetApp.
- Transfer danych źródłowych do woluminu docelowego przy użyciu narzędzia do kopiowania plików preferowany.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Jak utworzyć kopię woluminie plików NetApp platformy Azure w innym regionie platformy Azure?
    
Usługa Azure Files NetApp oferuje woluminy systemu plików NFS i protokołu SMB.  Wszystkie narzędzia do kopiowania na podstawie plików może służyć do replikacji danych między regionami platformy Azure. 

NetApp oferuje rozwiązanie SaaS na podstawie [synchronizacji chmury NetApp](https://cloud.netapp.com/cloud-sync-service).  To rozwiązanie umożliwia replikowanie systemu plików NFS lub udziały SMB danych Azure NetApp plików NFS, eksportowanie lub SMB. 

Umożliwia także szeroką gamę bezpłatne narzędzia do kopiowania danych. Dla systemu plików NFS, można użyć narzędzia obciążeń takich jak [rsync](https://rsync.samba.org/examples.html) do kopiowania i Synchronizuj źródło danych do usługi Azure Files NetApp woluminu. W przypadku protokołu SMB, można użyć obciążeń [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) w taki sam sposób.  Te narzędzia można również replikować uprawnienia do pliku lub folderu. 

Wymagania dotyczące replikowania woluminu plików NetApp Azure do innego regionu platformy Azure są w następujący sposób: 
- Upewnij się, że pliki NetApp Azure jest dostępny w miejscu docelowym regionem świadczenia usługi Azure.
- Weryfikowanie łączności sieciowej między sieciami wirtualnymi w każdym regionie. Globalnej komunikacji równorzędnej między sieciami wirtualnymi nie jest obecnie obsługiwana.  Można ustanowić połączenia między sieciami wirtualnymi przez łączenie z obwodem usługi ExpressRoute lub połączenie sieci VPN S2S. 
- Utwórz element docelowy wolumin usługi Azure Files NetApp.
- Transfer danych źródłowych do woluminu docelowego przy użyciu narzędzia do kopiowania plików preferowany.

### <a name="is-migration-with-azure-data-box-supported"></a>Jest migracja z usługi Azure Data Box jest obsługiwane?

Nie. Urządzenie Azure Data Box nie obsługuje obecnie usługi Azure Files NetApp. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Jest migracji za pomocą usługi Azure Import/Export obsługiwane?

Nie. Usługa Azure Import/Export nie obsługuje obecnie usługi Azure Files NetApp.

## <a name="next-steps"></a>Kolejne kroki  

- [Często zadawane pytania dotyczące programu Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Sieci wirtualne platformy Microsoft Azure — często zadawane pytania](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Jak utworzyć żądanie pomocy technicznej dla platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)