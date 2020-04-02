---
title: Często zadawane pytania dotyczące plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące plików NetApp platformy Azure.
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
ms.date: 04/01/2020
ms.author: b-juche
ms.openlocfilehash: 59453dbb9617f5a2ddbdca08709747b11a688560
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547098"
---
# <a name="faqs-about-azure-netapp-files"></a>Często zadawane pytania dotyczące plików NetApp platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące plików NetApp usługi Azure. 

## <a name="networking-faqs"></a>Najczęściej zadawane pytania dotyczące sieci

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Czy ścieżka danych NFS przechodzi przez Internet?  

Nie. Ścieżka danych NFS nie przechodzi przez Internet. Usługa Azure NetApp Files to natywna usługa platformy Azure, która jest wdrażana w sieci wirtualnej platformy Azure,gdzie usługa jest dostępna. Usługa Azure NetApp Files używa delegowanej podsieci i aplikuje interfejs sieciowy bezpośrednio w sieci wirtualnej. 

Szczegółowe informacje można znaleźć [w wskazówkach dotyczących planowania sieci usługi Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Czy mogę połączyć sieć wirtualną, która została już utworzona z usługą Azure NetApp Files?

Tak, można połączyć sieci wirtualne utworzone z usługą. 

Szczegółowe informacje można znaleźć [w wskazówkach dotyczących planowania sieci usługi Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Czy można zainstalować wolumin NFS plików NetApp usługi Azure przy użyciu nazwy DNS FQDN?

Tak, można, jeśli utworzysz wymagane wpisy DNS. Usługa Azure NetApp Files dostarcza adres IP usługi dla aprowizowanego woluminu. 

> [!NOTE] 
> Usługi Azure NetApp Files można wdrożyć dodatkowe pliki IP dla usługi w razie potrzeby.  Wpisy DNS mogą wymagać okresowej aktualizacji.

## <a name="security-faqs"></a>Często zadawane pytania dotyczące zabezpieczeń

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Czy ruch sieciowy między maszyną wirtualną platformy Azure a magazynem może być szyfrowany?

Ruch danych (ruch z klienta NFSv3, NFSv4.1 lub SMBv3 do woluminów usługi Azure NetApp Files) nie jest szyfrowany. Jednak ruch z maszyny Wirtualnej platformy Azure (z uruchomionym klientem NFS lub SMB) do usługi Azure NetApp Files jest tak samo bezpieczny, jak każdy inny ruch azure-vm-to-VM. Ten ruch jest lokalny w sieci centrum danych platformy Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Czy pamięć masowa może być szyfrowana w spoczynku?

Wszystkie woluminy usługi Azure NetApp Files są szyfrowane przy użyciu standardu FIPS 140-2. Wszystkie klucze są zarządzane przez usługę Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>W jaki sposób zarządzane są klucze szyfrowania? 

Zarządzanie kluczami dla plików NetApp platformy Azure jest obsługiwane przez usługę. Dla każdego woluminu generowany jest unikatowy klucz szyfrowania danych XTS-AES-256. Hierarchia kluczy szyfrowania służy do szyfrowania i ochrony wszystkich kluczy woluminu. Te klucze szyfrowania nigdy nie są wyświetlane ani zgłaszane w formacie niezaszyfrowanym. Klucze szyfrowania są usuwane natychmiast po usunięciu woluminu.

Obsługa kluczy zarządzanych przez użytkownika (Bring Your Own Keys) przy użyciu dedykowanego modułu HSM platformy Azure jest dostępna na zasadzie kontrolowanej w regionach Wschodnich, West2 w stanach ZJEDNOCZONYCH i południowo-środkowych stanów USA.  Możesz poprosić **anffeedback@microsoft.com**o dostęp pod adresem . Ponieważ pojemność jest dostępna, wnioski zostaną zatwierdzone.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Czy mogę skonfigurować reguły eksportu systemu plików NFS, aby kontrolować dostęp do obiektu docelowego instalacji usługi Azure NetApp Files?


Tak, można skonfigurować maksymalnie pięć reguł w jednej polityce eksportu systemu plików NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Czy pliki NetApp platformy Azure obsługują sieciowe grupy zabezpieczeń?

Nie, obecnie nie można zastosować grup zabezpieczeń sieciowych do delegowanej podsieci plików NetApp platformy Azure ani interfejsów sieciowych utworzonych przez usługę.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Czy mogę używać usługi Azure IAM za pomocą plików NetApp usługi Azure?

Tak, usługa Azure NetApp Files obsługuje funkcje RBAC za pomocą usługi Azure IAM.

## <a name="performance-faqs"></a>Często zadawane pytania dotyczące wydajności

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Co należy zrobić, aby zoptymalizować lub dostosować wydajność usługi Azure NetApp Files?

Można podjąć następujące działania zgodnie z wymaganiami dotyczącymi wydajności: 
- Upewnij się, że maszyna wirtualna jest odpowiednio dobrany.
- Włącz przyspieszoną sieć dla maszyny Wirtualnej.
- Wybierz żądany poziom i rozmiar usługi dla puli pojemności.
- Utwórz wolumin o żądanym rozmiarze przydziału dla pojemności i wydajności.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Jak przekonwertować poziomy usług opartych na przepływocie plików NetApp usługi Azure na usługi We/Wy?

Mb/s można konwertować na usługi We/Wy przy użyciu następującej formuły:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Jak zmienić poziom usług woluminu?

Zmiana poziomu usług woluminu nie jest obecnie obsługiwana.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Jak monitorować wydajność usługi Azure NetApp Files?

Usługa Azure NetApp Files udostępnia metryki wydajności woluminu. Można również użyć usługi Azure Monitor do monitorowania metryk użycia dla plików NetApp platformy Azure.  Zobacz [metryki dla plików NetApp platformy Azure, aby](azure-netapp-files-metrics.md) uzyskać listę metryk wydajności dla plików NetApp platformy Azure.

## <a name="nfs-faqs"></a>Najczęściej zadawane pytania dotyczące nfs

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chcę mieć wolumin zainstalowany automatycznie po uruchomieniu lub ponownym uruchomieniu maszyny Wirtualnej platformy Azure.  Jak skonfigurować hosta dla trwałych woluminów NFS?

Aby wolumin systemu plików NFS był automatycznie montowy przy uruchamianiu lub ponownym uruchomieniu maszyny Wirtualnej, należy dodać wpis do `/etc/fstab` pliku na hoście. 

Szczegółowe informacje można znaleźć w sekcji [Instalowanie lub odinstalowyjaj wolumin maszyn wirtualnych z systemem Windows lub Linux.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Dlaczego polecenie DF na kliencie systemu plików NFS nie pokazuje rozmiaru woluminu aprowizacji?

Rozmiar woluminu zgłoszony w DF to maksymalny rozmiar woluminu usługi Azure NetApp Files, do jaki może wzrosnąć. Rozmiar woluminu plików NetApp platformy Azure w DF polecenia nie odzwierciedla przydziału lub rozmiaru woluminu.  Rozmiar woluminu lub przydział plików NetApp usługi Azure można uzyskać za pośrednictwem witryny Azure portal lub interfejsu API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jaką wersję systemu plików NFS obsługuje usługa Azure NetApp Files?

Usługa Azure NetApp Files obsługuje pliki NFSv3 i NFSv4.1. Wolumin [można utworzyć](azure-netapp-files-create-volumes.md) przy użyciu wersji systemu plików NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Jak włączyć zgniatanie korzeni?

Zgniatanie korzeni nie jest obecnie obsługiwane.

## <a name="smb-faqs"></a>Najczęściej zadawane pytania dotyczące SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Czy dla dostępu do SMB jest wymagane połączenie usługi Active Directory? 

Tak, przed wdrożeniem woluminu SMB należy utworzyć połączenie usługi Active Directory. Określone kontrolery domeny muszą być dostępne przez delegowaną podsieć plików NetApp platformy Azure dla pomyślnego połączenia.  Aby uzyskać szczegółowe [informacje, zobacz Tworzenie woluminu SMB.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 

### <a name="how-many-active-directory-connections-are-supported"></a>Ile połączeń usługi Active Directory jest obsługiwanych?

Usługa Azure NetApp Files nie obsługuje wielu połączeń usługi Active Directory (AD) w jednym *regionie,* nawet jeśli połączenia usługi AD znajdują się na różnych kontach NetApp. Jednak można mieć wiele połączeń AD w jednej *subskrypcji,* tak długo, jak połączenia usługi AD znajdują się w różnych regionach. Jeśli potrzebujesz wielu połączeń AD w jednym regionie, możesz użyć oddzielnych subskrypcji, aby to zrobić. 

Połączenie usługi AD jest skonfigurowane na konto NetApp; połączenie usługi AD jest widoczne tylko za pośrednictwem konta NetApp, na które jest utworzone.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Czy pliki NetApp platformy Azure obsługują usługę Azure Active Directory? 

Obsługiwane są zarówno [Usługi domenowe usługi w usłudze Azure Active Directory (AD),](https://docs.microsoft.com/azure/active-directory-domain-services/overview) jak i [Usługi domenowe Active Directory (AD DS).](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Kontrolerów domeny usługi Active Directory można używać za pomocą plików NetApp usługi Azure. Kontrolery domeny mogą przebywać na platformie Azure jako maszyny wirtualne lub lokalnie za pośrednictwem usługi ExpressRoute lub S2S VPN. Usługa Azure NetApp Files nie obsługuje obecnie funkcji aneks ad dla [usługi Azure Active Directory.](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)

Jeśli używasz usługi Azure NetApp Files z usługami domenowymi `OU=AADDC Computers` Usługi domenowe Active Directory platformy Azure, ścieżka jednostki organizacyjnej jest podczas konfigurowania usługi Active Directory dla konta NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Jakie wersje usługi Active Directory systemu Windows Server są obsługiwane?

Usługa Azure NetApp Files obsługuje wersje usług domenowych Active Directory w systemie Windows Server 2008r2SP1-2019.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Dlaczego dostępne miejsce na moim kliencie SMB nie pokazuje aprowizowanego rozmiaru?

Rozmiar woluminu zgłoszony przez klienta SMB jest maksymalny rozmiar woluminu usługi Azure NetApp Files może wzrosnąć do. Rozmiar woluminu usługi Azure NetApp Files, jak pokazano na kliencie SMB nie odzwierciedla przydziału lub rozmiaru woluminu. Rozmiar woluminu lub przydział plików NetApp usługi Azure można uzyskać za pośrednictwem witryny Azure portal lub interfejsu API.

### <a name="does-azure-netapp-files-support-kerberos-encryption"></a>Czy pliki NetApp platformy Azure obsługują szyfrowanie Kerberos?

Tak, domyślnie usługa Azure NetApp Files obsługuje szyfrowanie AES-128 i AES-256 dla ruchu między usługą a docelowymi kontrolerami domeny usługi Active Directory. Zobacz [Tworzenie woluminu SMB dla plików NetApp platformy Azure](azure-netapp-files-create-volumes-smb.md) dla wymagań. 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Często zadawane pytania dotyczące zarządzania pojemnością

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Jak monitorować użycie puli pojemności i ilości plików NetApp platformy Azure? 

Usługa Azure NetApp Files udostępnia metryki puli pojemności i użycia woluminu. Można również użyć usługi Azure Monitor do monitorowania użycia plików NetApp platformy Azure. Szczegółowe informacje można znaleźć w [metrykach dla plików NetApp platformy Azure.](azure-netapp-files-metrics.md) 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Czy mogę zarządzać plikami NetApp platformy Azure za pośrednictwem Eksploratora usługi Azure Storage?

Nie. Usługa Azure NetApp Files nie jest obsługiwana przez Eksploratora usługi Azure Storage.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Jak ustalić, czy katalog zbliża się do rozmiaru limitu?

Można użyć `stat` polecenia z klienta, aby zobaczyć, czy katalog zbliża się do maksymalnego limitu rozmiaru (320 MB).

W przypadku katalogu o rozmiarze 320 MB liczba bloków wynosi 655360, przy czym każdy rozmiar bloku wynosi 512 bajtów.  (Oznacza to, że 320x1024x1024/512.)  

Przykłady:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Często zadawane pytania dotyczące migracji i ochrony danych

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Jak przeprowadzić migrację danych do plików NetApp usługi Azure?
Usługa Azure NetApp Files udostępnia woluminy NFS i SMB.  Do migracji danych do usługi można użyć dowolnego narzędzia do kopiowania opartego na plikach. 

NetApp oferuje rozwiązanie oparte na SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Rozwiązanie umożliwia replikowanie danych NFS lub SMB do eksportu plików NFS usług Azure NetApp Files lub udziałów SMB. 

Do kopiowania danych można również użyć szerokiej gamy bezpłatnych narzędzi. W przypadku usług NFS można używać narzędzi obciążeń, takich jak [rsync,](https://rsync.samba.org/examples.html) do kopiowania i synchronizowania danych źródłowych w woluminie usługi Azure NetApp Files. W przypadku SMB można używać obciążeń [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) w ten sam sposób.  Narzędzia te mogą również replikować uprawnienia do plików lub folderów. 

Wymagania dotyczące migracji danych z lokalnego do usługi Azure NetApp Files są następujące: 

- Upewnij się, że pliki NetApp platformy Azure są dostępne w docelowym regionie platformy Azure.
- Sprawdź poprawność łączności sieciowej między źródłem a adresem IP docelowego woluminu usługi Azure NetApp Files. Transfer danych między lokalnie a usługą Azure NetApp Files jest obsługiwany za pomocą usługi ExpressRoute.
- Utwórz docelowy wolumin plików NetApp platformy Azure.
- Przenieś dane źródłowe do woluminu docelowego za pomocą preferowanego narzędzia do kopiowania plików.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Jak utworzyć kopię woluminu usługi Azure NetApp Files w innym regionie platformy Azure?
    
Usługa Azure NetApp Files udostępnia woluminy NFS i SMB.  Do replikowania danych między regionami platformy Azure można użyć dowolnego narzędzia do kopiowania plików. 

NetApp oferuje rozwiązanie oparte na SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Rozwiązanie umożliwia replikowanie danych NFS lub SMB do eksportu plików NFS usług Azure NetApp Files lub udziałów SMB. 

Do kopiowania danych można również użyć szerokiej gamy bezpłatnych narzędzi. W przypadku usług NFS można używać narzędzi obciążeń, takich jak [rsync,](https://rsync.samba.org/examples.html) do kopiowania i synchronizowania danych źródłowych w woluminie usługi Azure NetApp Files. W przypadku SMB można używać obciążeń [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) w ten sam sposób.  Narzędzia te mogą również replikować uprawnienia do plików lub folderów. 

Wymagania dotyczące replikowania woluminu plików netapp platformy Azure do innego regionu platformy Azure są następujące: 
- Upewnij się, że pliki NetApp platformy Azure są dostępne w docelowym regionie platformy Azure.
- Sprawdzanie poprawności łączności sieciowej między sieciami wirtualnymi w każdym regionie. Obecnie globalna komunikacja równorzędna między sieciami wirtualnymi nie jest obsługiwana.  Łączność między sieciami wirtualnymi można ustanowić, łącząc się z obwodem usługi ExpressRoute lub korzystając z połączenia sieci VPN S2S. 
- Utwórz docelowy wolumin plików NetApp platformy Azure.
- Przenieś dane źródłowe do woluminu docelowego za pomocą preferowanego narzędzia do kopiowania plików.

### <a name="is-migration-with-azure-data-box-supported"></a>Czy migracja za pomocą usługi Azure Data Box jest obsługiwana?

Nie. Usługa Azure Data Box nie obsługuje obecnie plików NetApp platformy Azure. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Czy migracja za pomocą usługi Azure Import/Export jest obsługiwana?

Nie. Usługa Azure Import/Export usługa nie obsługuje obecnie plików NetApp platformy Azure.

## <a name="next-steps"></a>Następne kroki  

- [Często zadawane pytania dotyczące usługi Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Często zadawane pytania dotyczące sieci wirtualnej platformy Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Jak utworzyć żądanie pomocy technicznej dla platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Często zadawane pytania dotyczące wydajności SMB dla plików NetApp usługi Azure](azure-netapp-files-smb-performance.md)
