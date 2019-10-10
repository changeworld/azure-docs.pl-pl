---
title: SQL Server FCI — Virtual Machines platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób tworzenia SQL Server wystąpienia klastra trybu failover na platformie Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: b30ccbcba0b2126d1fe1abce9ae67a55ce25f601
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170268"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurowanie SQL Server wystąpienia klastra trybu failover na platformie Azure Virtual Machines

W tym artykule opisano sposób tworzenia SQL Server wystąpienia klastra trybu failover (FCI) na maszynach wirtualnych platformy Azure w modelu Menedżer zasobów. To rozwiązanie używa [systemu Windows Server 2016 Datacenter edition Bezpośrednie miejsca do magazynowania \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako wirtualnej sieci San opartej na oprogramowaniu, która synchronizuje magazyn (dyski danych) między węzłami (maszyn wirtualnych platformy Azure) w klastrze systemu Windows. Funkcja S2D jest nowością w systemie Windows Server 2016.

Na poniższym diagramie przedstawiono kompletne rozwiązanie na maszynach wirtualnych platformy Azure:

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Na powyższym diagramie przedstawiono:

- Dwie maszyny wirtualne platformy Azure w klastrze trybu failover systemu Windows. Jeśli maszyna wirtualna znajduje się w klastrze trybu failover, jest również nazywana *węzłem klastra*lub *węzłami*.
- Każda maszyna wirtualna ma co najmniej dwa dyski danych.
- Funkcja S2D synchronizuje dane na dysku danych i przedstawia zsynchronizowany magazyn jako pulę magazynów.
- Pula magazynów przedstawia udostępniony wolumin klastra (CSV) w klastrze trybu failover.
- Rola klastra usługi SQL Server FCI używa woluminu CSV dla dysków danych.
- Moduł równoważenia obciążenia platformy Azure do przechowywania adresu IP SQL Server FCI.
- Zestaw dostępności platformy Azure zawiera wszystkie zasoby.

   >[!NOTE]
   >Wszystkie zasoby platformy Azure znajdują się na diagramie należącym do tej samej grupy zasobów.

Aby uzyskać szczegółowe informacje na temat funkcji S2D, zobacz [Windows Server 2016 Datacenter edition Bezpośrednie miejsca do magazynowania \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Funkcja S2D obsługuje dwa typy architektur — zbieżne i nadaje zbieżność. Architektura w tym dokumencie jest technologią Hyper-zbieżną. Infrastruktura z technologią Hyper-zbieżną umieszcza magazyn na tych samych serwerach, które obsługują klastrowaną aplikację. W tej architekturze magazyn znajduje się na każdym SQL Server węźle FCI.

## <a name="licensing-and-pricing"></a>Licencjonowanie i Cennik

Na platformie Azure Virtual Machines możesz licencjonować SQL Server przy użyciu funkcji płatność zgodnie z rzeczywistym użyciem lub przenieść własne obrazy maszyn wirtualnych (BYOL). Wybrany typ obrazu ma wpływ na sposób naliczania opłat.

Dzięki licencjonowaniu PAYG wystąpienie klastra trybu failover (FCI) SQL Server na platformie Azure Virtual Machines naliczane są opłaty za wszystkie węzły FCI, w tym węzły pasywne. Aby uzyskać więcej informacji, zobacz [SQL Server Enterprise Virtual Machines cennika](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Klienci z Umowa Enterpriseą z programem Software Assurance mają prawo do korzystania z jednego wolnego węzła pasywnej FCI dla każdego aktywnego węzła. Aby skorzystać z zalet tej korzyści na platformie Azure, użyj obrazów maszyn wirtualnych BYOL, a następnie użyj tej samej licencji na węzłach aktywnych i pasywnych FCI. Aby uzyskać więcej informacji, zobacz [Umowa Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Aby porównać licencję PAYG i BYOL na potrzeby SQL Server na platformie Azure Virtual Machines Zobacz Rozpoczynanie [pracy z maszynami wirtualnymi SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Aby uzyskać pełne informacje na temat licencjonowania SQL Server, zobacz [Cennik](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Przykładowy szablon platformy Azure

Możesz utworzyć całe rozwiązanie na platformie Azure na podstawie szablonu. Przykładowy szablon jest dostępny w [szablonach szybkiego startu platformy Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Ten przykład nie jest zaprojektowany ani testowany pod kątem określonego obciążenia. Można uruchomić szablon, aby utworzyć SQL Server FCI z magazynem funkcji S2D podłączonym do domeny. Możesz oszacować szablon i zmodyfikować go do swoich celów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Istnieje kilka rzeczy, które należy znać, i kilka rzeczy, które są potrzebne przed kontynuowaniem.

### <a name="what-to-know"></a>Co należy wiedzieć
Należy mieć praktyczne zrozumienie następujących technologii:

- [Technologie klastrów systemu Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server wystąpienia klastra trybu failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Istotną różnicą jest to, że w klastrze trybu failover gościa maszyny wirtualnej Azure IaaS zalecamy pojedyncze karty sieciowe na serwer (węzeł klastra) i jedną podsieć. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci są niepotrzebne w klastrze gościa maszyny wirtualnej IaaS platformy Azure. Mimo że raport z weryfikacji klastra wyświetli ostrzeżenie, że węzły są dostępne tylko w ramach jednej sieci, to ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej IaaS platformy Azure. 

Ponadto należy ogólnie zrozumieć następujące technologie:

- [Rozwiązanie z technologią Hyper-zbieżne przy użyciu Bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Grupy zasobów platformy Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> W tej chwili SQL Server wystąpienia klastra trybu failover w usłudze Azure Virtual Machines są obsługiwane tylko w trybie [uproszczonego](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) zarządzania [rozszerzenia agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Odinstaluj pełne rozszerzenie z maszyn wirtualnych, które uczestniczą w klastrze trybu failover, a następnie zarejestruj je przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie `lightweight`. Pełne rozszerzenie obsługuje takie funkcje, jak automatyczne tworzenie kopii zapasowych, stosowanie poprawek i zaawansowane zarządzanie portalem. Te funkcje nie będą działały w przypadku maszyn wirtualnych SQL po ponownym zainstalowaniu agenta w trybie uproszczonego zarządzania.

### <a name="what-to-have"></a>Co należy zrobić

Przed wykonaniem instrukcji przedstawionych w tym artykule należy wykonać następujące czynności:

- Subskrypcja Microsoft Azure.
- Domena systemu Windows na maszynach wirtualnych platformy Azure.
- Konto z uprawnieniami do tworzenia obiektów na maszynie wirtualnej platformy Azure.
- Sieć wirtualna platformy Azure i podsieć z wystarczającą przestrzenią adresową IP dla następujących składników:
   - Obie maszyny wirtualne.
   - Adres IP klastra trybu failover.
   - Adres IP dla każdego FCIu.
- System DNS skonfigurowany w sieci platformy Azure, wskazujący kontrolery domeny.

Po spełnieniu tych wymagań wstępnych można kontynuować tworzenie klastra trybu failover. Pierwszym krokiem jest utworzenie maszyn wirtualnych.

## <a name="step-1-create-virtual-machines"></a>Krok 1. Tworzenie maszyn wirtualnych

1. Zaloguj się do [Azure Portal](https://portal.azure.com) z subskrypcją.

1. [Utwórz zestaw dostępności platformy Azure](../tutorial-availability-sets.md).

   Zestaw dostępności grupuje maszyny wirtualne w domenach błędów i domenach aktualizacji. Zestaw dostępności pozwala upewnić się, że aplikacja nie ma wpływ na pojedyncze punkty awarii, takie jak przełącznik sieciowy lub jednostka mocy stojaka serwerów.

   Jeśli nie utworzono grupy zasobów dla maszyn wirtualnych, zrób to podczas tworzenia zestawu dostępności platformy Azure. Jeśli używasz Azure Portal do utworzenia zestawu dostępności, wykonaj następujące czynności:

   - W Azure Portal kliknij pozycję **+** , aby otworzyć witrynę Azure Marketplace. Wyszukaj **zestaw dostępności**.
   - Kliknij pozycję **zestaw dostępności**.
   - Kliknij przycisk **Utwórz**.
   - W bloku **Tworzenie zestawu dostępności** ustaw następujące wartości:
      - **Nazwa**: Nazwa zestawu dostępności.
      - **Subskrypcja**: Twoja subskrypcja platformy Azure.
      - **Grupa zasobów**: Jeśli chcesz użyć istniejącej grupy, kliknij pozycję **Użyj istniejącej** , a następnie wybierz grupę z listy rozwijanej. W przeciwnym razie wybierz pozycję **Utwórz nową** , a następnie wpisz nazwę grupy.
      - **Lokalizacja**: Ustaw lokalizację, w której planujesz utworzyć maszyny wirtualne.
      - **Domeny błędów**: Użyj ustawienia domyślnego (3).
      - **Aktualizowanie domen**: Użyj domyślnego (5).
   - Kliknij przycisk **Utwórz** , aby utworzyć zestaw dostępności.

1. Utwórz maszyny wirtualne w zestawie dostępności.

   Udostępnij dwie SQL Server maszyny wirtualne w zestawie dostępności platformy Azure. Aby uzyskać instrukcje, zobacz temat [udostępnianie maszyny wirtualnej SQL Server w Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Umieść obie maszyny wirtualne:

   - W tej samej grupie zasobów platformy Azure, w której znajduje się zestaw dostępności.
   - W tej samej sieci, w której znajduje się kontroler domeny.
   - W podsieci z wystarczającą przestrzenią adresów IP dla obu maszyn wirtualnych i wszystkich FCIs, które mogą być używane w tym klastrze.
   - W zestawie dostępności platformy Azure.   

      >[!IMPORTANT]
      >Nie można ustawić lub zmienić zestawu dostępności po utworzeniu maszyny wirtualnej.

   Wybierz obraz z portalu Azure Marketplace. Możesz użyć obrazu z portalu Marketplace zawierającego system Windows Server i SQL Server lub tylko system Windows Server. Aby uzyskać szczegółowe informacje, zobacz [omówienie SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)

   Oficjalne obrazy SQL Server w galerii platformy Azure obejmują zainstalowane wystąpienie SQL Server oraz oprogramowanie instalacyjne SQL Server oraz wymagany klucz.

   Wybierz odpowiedni obraz z uwzględnieniem sposobu płacenia SQL Server licencji:

   - **Płatność za Licencjonowanie za użycie**: koszt za każdy z tych obrazów zawiera SQL Server Licencjonowanie:
      - **SQL Server 2016 Enterprise w systemie Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard w systemie Windows Server Datacenter 2016**
      - **Deweloper SQL Server 2016 w systemie Windows Server Datacenter 2016**

   - **Przenieś własną licencję (BYOL)**

      - **BYOL SQL Server 2016 Enterprise w systemie Windows Server Datacenter 2016**
      - **BYOL SQL Server 2016 Standard w systemie Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Po utworzeniu maszyny wirtualnej Usuń wstępnie zainstalowane wystąpienie SQL Server autonomicznej. Przed skonfigurowaniem klastra trybu failover i programu S2D należy użyć wstępnie zainstalowanego nośnika SQL Server do utworzenia SQL Server FCI.

   Alternatywnie możesz użyć obrazów portalu Azure Marketplace z tylko systemem operacyjnym. Wybierz obraz **systemu Windows Server 2016 Datacenter** i zainstaluj SQL Server FCI po skonfigurowaniu klastra trybu failover i funkcji S2D. Ten obraz nie zawiera nośnika instalacyjnego SQL Server. Umieść nośnik instalacyjny w lokalizacji, w której można uruchomić instalację SQL Server dla każdego serwera.

1. Po utworzeniu maszyn wirtualnych przez platformę Azure Połącz się z każdą maszyną wirtualną za pomocą protokołu RDP.

   Gdy po raz pierwszy łączysz się z maszyną wirtualną przy użyciu protokołu RDP, komputer pyta, czy chcesz zezwolić na odnajdowanie tego komputera w sieci. Kliknij przycisk **Yes** (Tak).

1. Jeśli używasz jednego z obrazów maszyn wirtualnych opartych na SQL Server, Usuń wystąpienie SQL Server.

   - W obszarze **programy i funkcje**kliknij prawym przyciskiem myszy pozycję **Microsoft SQL Server 2016 (64-bitowe)** , a następnie kliknij polecenie **Odinstaluj/Zmień**.
   - Kliknij pozycję **Usuń**.
   - Wybierz wystąpienie domyślne.
   - Usuń wszystkie funkcje w obszarze **usługi aparatu bazy danych**. Nie usuwaj **funkcji udostępnionych**. Zapoznaj się z poniższym obrazem:

      ![Usuń funkcje](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Kliknij przycisk **dalej**, a następnie kliknij przycisk **Usuń**.

1. <a name="ports"></a>Otwórz porty zapory.

   Na każdej maszynie wirtualnej Otwórz następujące porty w zaporze systemu Windows.

   | Przeznaczenie | Port TCP | Uwagi
   | ------ | ------ | ------
   | Oprogramowanie SQL Server | 1433 | Normalny port dla domyślnych wystąpień SQL Server. Jeśli obraz został użyty z galerii, ten port zostanie automatycznie otwarty.
   | Sonda kondycji | 59999 | Dowolny otwarty port TCP. W późniejszym kroku należy skonfigurować [sondę kondycji](#probe) modułu równoważenia obciążenia oraz klaster, aby używać tego portu.  

1. Dodaj magazyn do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie magazynu](../disks-types.md).

   Obie maszyny wirtualne potrzebują co najmniej dwóch dysków z danymi.

   Dołączanie dysków nieprzetworzonych — nie sformatowanych w systemie plików NTFS.
      >[!NOTE]
      >W przypadku dołączania dysków sformatowanych w systemie plików NTFS można włączyć funkcję S2D tylko bez sprawdzania uprawnień dysku.  

   Dołącz co najmniej dwie dysków SSD w warstwie Premium do każdej maszyny wirtualnej. Zalecamy co najmniej P30 (1 TB) dysków.

   Ustaw buforowanie hosta na **tylko do odczytu**.

   Pojemność magazynu używana w środowiskach produkcyjnych zależy od obciążenia. Wartości opisane w tym artykule dotyczą demonstracji i testowania.

1. [Dodaj maszyny wirtualne do istniejącej domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po utworzeniu i skonfigurowaniu maszyn wirtualnych można skonfigurować klaster trybu failover.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Krok 2. Konfigurowanie klastra trybu failover systemu Windows przy użyciu funkcji S2D

Następnym krokiem jest skonfigurowanie klastra trybu failover za pomocą funkcji S2D. W tym kroku wykonasz następujące czynności:

1. Dodaj funkcję Klaster trybu failover systemu Windows
1. Weryfikowanie klastra
1. Tworzenie klastra trybu failover
1. Utwórz monitor w chmurze
1. Dodaj magazyn

### <a name="add-windows-failover-clustering-feature"></a>Dodaj funkcję Klaster trybu failover systemu Windows

1. Aby rozpocząć, Połącz się z pierwszą maszyną wirtualną za pomocą protokołu RDP przy użyciu konta domeny, które jest członkiem lokalnej grupy administratorów i ma uprawnienia do tworzenia obiektów w Active Directory. Użyj tego konta do pozostałej części konfiguracji.

1. [Dodaj funkcję Klaster trybu failover do każdej maszyny wirtualnej](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Aby zainstalować funkcję Klaster trybu failover z interfejsu użytkownika, wykonaj następujące czynności na obu maszynach wirtualnych.
   - W **Menedżer serwera**kliknij pozycję **Zarządzaj**, a następnie kliknij pozycję **Dodaj role i funkcje**.
   - W **Kreatorze dodawania ról i funkcji**kliknij przycisk **dalej** , aż **wybierzesz pozycję funkcje**.
   - W obszarze **Wybieranie funkcji**kliknij pozycję **klaster trybu failover**. Dołącz wszystkie wymagane funkcje i narzędzia do zarządzania. Kliknij pozycję **Dodaj funkcje**.
   - Kliknij przycisk **dalej** , a następnie kliknij przycisk **Zakończ** , aby zainstalować funkcje.

   Aby zainstalować funkcję Klaster trybu failover z programem PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Aby uzyskać informacje na ten temat, należy postępować zgodnie z instrukcjami podanymi w sekcji Krok 3 rozwiązania z technologią [Hyper-zbieżność przy użyciu bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Weryfikowanie klastra

Ten przewodnik odwołuje się do instrukcji w obszarze [Weryfikuj klaster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Sprawdź poprawność klastra w interfejsie użytkownika lub za pomocą programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności z jednej z maszyn wirtualnych.

1. W **Menedżer serwera**kliknij pozycję **Narzędzia**, a następnie kliknij pozycję **Menedżer klastra trybu failover**.
1. W **Menedżer klastra trybu failover**, kliknij przycisk **Akcja**, a następnie kliknij przycisk **Weryfikuj konfigurację...** .
1. Kliknij przycisk **Dalej**.
1. Na stronie **Wybieranie serwerów lub klastrów**wpisz nazwę obu maszyn wirtualnych.
1. W obszarze **opcje testowania**wybierz opcję **Uruchom tylko wybrane testy**. Kliknij przycisk **Dalej**.
1. W przypadku **wyboru testu**Uwzględnij wszystkie testy poza **magazynem**. Zapoznaj się z poniższym obrazem:

   ![Weryfikuj testy](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Kliknij przycisk **Dalej**.
1. W obszarze **potwierdzenie**kliknij przycisk **dalej**.

**Kreator weryfikacji konfiguracji** uruchamia testy weryfikacyjne.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po sprawdzeniu poprawności klastra utwórz klaster trybu failover.

### <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

Ten przewodnik odwołuje się do [tworzenia klastra trybu failover](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Aby utworzyć klaster trybu failover, potrzebne są:
- Nazwy maszyn wirtualnych, które staną się węzłami klastra.
- Nazwa klastra trybu failover
- Adres IP klastra trybu failover. Można użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej platformy Azure i podsieci co węzły klastra.

#### <a name="windows-server-2008-2016"></a>System Windows Server 2008-2016

Poniższy program PowerShell tworzy klaster trybu failover dla **systemu Windows Server 2008-2016**. Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Poniższy program PowerShell tworzy klaster trybu failover dla systemu Windows Server 2019.  Aby uzyskać więcej informacji, zapoznaj się z tematem [klaster trybu failover w blogu: obiekt sieci klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Utwórz monitor w chmurze

Monitor w chmurze to nowy typ monitora kworum klastra, który jest przechowywany w Azure Storage Blob. Eliminuje to potrzebę oddzielnej maszyny wirtualnej obsługującej udział monitora.

1. [Utwórz monitor chmury dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Utwórz kontener obiektów BLOB.

1. Zapisz klucze dostępu i adres URL kontenera.

1. Skonfiguruj monitor kworum klastra trybu failover. Zobacz temat [Konfigurowanie monitora kworum w interfejsie użytkownika](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) w oknie użytkownika.

### <a name="add-storage"></a>Dodaj magazyn

Dyski dla funkcji S2D muszą być puste i nie mogą zawierać partycji ani innych danych. Aby wyczyścić dyski [, wykonaj kroki opisane w tym przewodniku](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Włącz funkcję bezpośrednie miejsca do magazynowania @no__t — 1S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Poniższy program PowerShell umożliwia bezpośrednie miejsca do magazynowania.  

   ```powershell
   Enable-ClusterS2D
   ```

   W **Menedżer klastra trybu failover**można teraz zobaczyć pulę magazynów.

1. [Utwórz wolumin](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Jedną z funkcji opcji S2D jest automatyczne tworzenie puli magazynów po jej włączeniu. Teraz można przystąpić do tworzenia woluminu. Program PowerShell polecenia cmdlet `New-Volume` automatyzuje proces tworzenia woluminu, w tym formatowanie, dodawanie do klastra i tworzenie udostępnionego woluminu klastra (CSV). Poniższy przykład tworzy wolumin CSV 800 gigabajta (GB).

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po zakończeniu wykonywania tego polecenia wolumin 800 GB zostanie zainstalowany jako zasób klastra. Wolumin jest w `C:\ClusterStorage\Volume1\`.

   Na poniższym diagramie przedstawiono udostępniony wolumin klastra z funkcją S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3. Testowanie trybu failover klastra trybu failover

W Menedżer klastra trybu failover upewnij się, że można przenieść zasób magazynu do innego węzła klastra. Jeśli można nawiązać połączenie z klastrem trybu failover z **Menedżer klastra trybu failover** i przenieść magazyn z jednego węzła do drugiego, możesz skonfigurować FCI.

## <a name="step-4-create-sql-server-fci"></a>Krok 4. tworzenie SQL Server FCI

Po skonfigurowaniu klastra trybu failover i wszystkich składników klastra, w tym magazynu, można utworzyć SQL Server FCI.

1. Nawiąż połączenie z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżer klastra trybu failover**upewnij się, że wszystkie zasoby podstawowe klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby Przenieś wszystkie zasoby do tej maszyny wirtualnej.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure Marketplace, nośnik znajduje się w `C:\SQLServer_<version number>_Full`. Kliknij przycisk **Setup (Konfiguracja**).

1. W **centrum instalacji SQL Server**kliknij pozycję **Instalacja**.

1. Kliknij kolejno pozycje **nowy SQL Server Instalacja klastra trybu failover**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server FCI.

   Katalogi danych FCI muszą znajdować się w magazynie klastrowanym. W przypadku funkcji S2D nie jest to dysk udostępniony, ale punkt instalacji należy do woluminu na każdym serwerze. Funkcja S2D synchronizuje wolumin między obydwoma węzłami. Wolumin jest prezentowany w klastrze jako udostępniony wolumin klastra. Użyj punktu instalacji woluminu CSV dla katalogów danych.

   ![Datadirectors](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po zakończeniu pracy Kreatora Instalator zainstaluje SQL Server FCI na pierwszym węźle.

1. Po pomyślnym zainstalowaniu przez Instalatora FCI na pierwszym węźle Nawiąż połączenie z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **centrum instalacji SQL Server**. Kliknij pozycję **Instalacja**.

1. Kliknij przycisk **Dodaj węzeł do klastra trybu failover SQL Server**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować program SQL Server i dodać ten serwer do FCI.

   >[!NOTE]
   >Jeśli korzystasz z obrazu galerii portalu Azure Marketplace z SQL Server, narzędzia SQL Server zostały dołączone do obrazu. Jeśli nie korzystasz z tego obrazu, zainstaluj SQL Server narzędzia osobno. Zobacz [pobieranie SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Krok 5. Tworzenie modułu równoważenia obciążenia platformy Azure

W przypadku maszyn wirtualnych platformy Azure klastry używają usługi równoważenia obciążenia do przechowywania adresu IP, który musi znajdować się w jednym węźle klastra naraz. W tym rozwiązaniu moduł równoważenia obciążenia przechowuje adres IP SQL Server FCI.

[Utwórz i skonfiguruj moduł równoważenia obciążenia platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Utwórz moduł równoważenia obciążenia w Azure Portal

Aby utworzyć moduł równoważenia obciążenia:

1. W Azure Portal przejdź do grupy zasobów z maszynami wirtualnymi.

1. Kliknij pozycję **+ Dodaj**. Wyszukaj **Load Balancer**w portalu Marketplace. Kliknij **Load Balancer**.

1. Kliknij przycisk **Utwórz**.

1. Skonfiguruj moduł równoważenia obciążenia przy użyciu:

   - **Nazwa**: Nazwa identyfikująca moduł równoważenia obciążenia.
   - **Typ**: usługa równoważenia obciążenia może być publiczna lub prywatna. Dostęp do prywatnego modułu równoważenia obciążenia można uzyskać w ramach tej samej sieci wirtualnej. Większość aplikacji platformy Azure może korzystać z prywatnego modułu równoważenia obciążenia. Jeśli aplikacja wymaga dostępu do SQL Server bezpośrednio za pośrednictwem Internetu, użyj publicznego modułu równoważenia obciążenia.
   - **Virtual Network**: sieć, w której znajduje się maszyna wirtualna.
   - **Podsieć**: ta sama podsieć, w której znajduje się maszyna wirtualna.
   - **Prywatny adres IP**: ten sam adres IP, który został przypisany do zasobu sieci klastra SQL Server FCI.
   - **subskrypcja**: Twoja subskrypcja platformy Azure.
   - **Grupa zasobów**: Użyj tej samej grupy zasobów co maszyny wirtualne.
   - **Lokalizacja**: Użyj tej samej lokalizacji platformy Azure co maszyny wirtualne.
   Zapoznaj się z poniższym obrazem:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza modułu równoważenia obciążenia

1. Wróć do grupy zasobów platformy Azure z maszynami wirtualnymi i Znajdź nowy moduł równoważenia obciążenia. Może być konieczne odświeżenie widoku w grupie zasobów. Kliknij moduł równoważenia obciążenia.

1. Kliknij pozycję **Pule zaplecza** i kliknij pozycję **+ Dodaj** , aby dodać pulę zaplecza.

1. Skojarz pulę zaplecza z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **Konfiguracja IP sieci docelowej**Sprawdź **maszynę wirtualną** i wybierz maszyny wirtualne, które będą uczestniczyć w węzłach klastra. Pamiętaj, aby uwzględnić wszystkie maszyny wirtualne, które będą hostować FCI. 

1. Kliknij przycisk **OK** , aby utworzyć pulę zaplecza.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurowanie sondy kondycji modułu równoważenia obciążenia

1. W bloku moduł równoważenia obciążenia kliknij pozycję **sondy kondycji**.

1. Kliknij pozycję **+ Dodaj**.

1. W bloku **Dodawanie sondy kondycji** <a name="probe"> </a>ustaw parametry sondy kondycji:

   - **Name**: Nazwa sondy kondycji.
   - **Protokół**: TCP.
   - **Port**: Ustaw na port utworzony w zaporze dla sondy kondycji w [tym kroku](#ports). W tym artykule w przykładzie zastosowano port TCP `59999`.
   - **Interwał**: 5 sekund.
   - **Próg złej kondycji**: 2 kolejne błędy.

1. Kliknij przycisk OK.

### <a name="set-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

1. W bloku moduł równoważenia obciążenia kliknij pozycję **reguły równoważenia obciążenia**.

1. Kliknij pozycję **+ Dodaj**.

1. Ustaw parametry zasad równoważenia obciążenia:

   - **Name**: nazwa dla reguł równoważenia obciążenia.
   - **Adres IP frontonu**: Użyj adresu IP dla zasobu sieci klastra SQL Server FCI.
   - **Port**: Ustaw dla portu TCP SQL Server FCI. Domyślnym portem wystąpienia jest 1433.
   - **Port zaplecza**: Ta wartość używa tego samego portu co wartość **portu** w przypadku włączenia **swobodnego adresu IP (bezpośredni zwrot serwera)** .
   - **Pula zaplecza**: Użyj wcześniej skonfigurowanej nazwy puli zaplecza.
   - **Sonda kondycji**: Użyj wcześniej skonfigurowanej sondy kondycji.
   - **Trwałość sesji**: Brak.
   - **Limit czasu bezczynności (w minutach)** : 4.
   - **Zmienny adres IP (bezpośredni zwrot serwera)** : włączony

1. Kliknij przycisk **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Krok 6. Konfigurowanie klastra do sondowania

Ustaw parametr portu sondowania klastra w programie PowerShell.

Aby ustawić parametr portu sondowania klastra, zaktualizuj zmienne w następującym skrypcie przy użyciu wartości ze środowiska. Usuń nawiasy kątowe `<>` ze skryptu. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

W powyższym skrypcie Ustaw wartości dla danego środowiska. Na poniższej liście opisano wartości:

   - `<Cluster Network Name>`: Nazwa klastra trybu failover systemu Windows Server dla sieci. W **Menedżer klastra trybu failover** > **sieci**, kliknij prawym przyciskiem myszy sieć i kliknij polecenie **Właściwości**. Poprawna wartość jest pod **nazwą** na karcie **Ogólne** . 

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI nazwa zasobu adresu IP. W **Menedżer klastra trybu failover** **role** >  w obszarze SQL Server roli FCI w obszarze **Nazwa serwera**kliknij prawym przyciskiem myszy zasób adres IP, a następnie kliknij pozycję **Właściwości**. Poprawna wartość jest pod **nazwą** na karcie **Ogólne** . 

   - `<ILBIP>`: adres IP ILB. Ten adres jest skonfigurowany w Azure Portal jako adres frontonu ILB. Jest to również SQL Server FCI adres IP. Można go znaleźć w **Menedżer klastra trybu failover** na tej samej stronie właściwości, na której znajduje się `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: jest port sondy skonfigurowany w sondy kondycji modułu równoważenia obciążenia. Dowolny nieużywany port TCP jest prawidłowy. 

>[!IMPORTANT]
>Maska podsieci parametru klastra musi być adresem IP protokołu TCP: `255.255.255.255`.

Po ustawieniu sondy klastra można zobaczyć wszystkie parametry klastra w programie PowerShell. Uruchom następujący skrypt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7. Testowanie pracy w trybie failover FCI

Przetestuj tryb failover FCI, aby sprawdzić poprawność działania klastra. Wykonaj następujące czynności:

1. Połącz się z jednym z SQL Server węzłów klastra FCI z protokołem RDP.

1. Otwórz **Menedżer klastra trybu failover**. Kliknij pozycję **role**. Zwróć uwagę na to, który węzeł jest właścicielem SQL Server roli FCI.

1. Kliknij prawym przyciskiem myszy SQL Server rolę FCI.

1. Kliknij przycisk **Przenieś** , a następnie kliknij pozycję **najlepszy możliwy węzeł**.

**Menedżer klastra trybu failover** pokazuje rolę i jej zasobów przejdź do trybu offline. Zasoby są następnie przenoszone i dostępne w trybie online w innym węźle.

### <a name="test-connectivity"></a>Testowanie łączności

Aby przetestować łączność, zaloguj się do innej maszyny wirtualnej w tej samej sieci wirtualnej. Otwórz **SQL Server Management Studio** i nawiąż połączenie z SQL Server nazwą FCI.

>[!NOTE]
>W razie potrzeby można [pobrać SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Ograniczenia

Usługa Azure Virtual Machines obsługuje usługę Microsoft Distributed Transaction Coordinator (MSDTC) w systemie Windows Server 2019 z magazynem w udostępnionych woluminach klastra (CSV) i w [standardowym module równoważenia obciążenia](../../../load-balancer/load-balancer-standard-overview.md).

W przypadku maszyn wirtualnych platformy Azure usługa MSDTC nie jest obsługiwana w systemie Windows Server 2016 i jego starszych wersjach, ponieważ:

- Nie można skonfigurować klastrowanego zasobu usługi MSDTC do korzystania z magazynu udostępnionego. W przypadku korzystania z systemu Windows Server 2016 w przypadku utworzenia zasobu usługi MSDTC nie będą wyświetlane żadne udostępnione magazyny dostępne do użycia, nawet jeśli magazyn istnieje. Ten problem został rozwiązany w systemie Windows Server 2019.
- Podstawowa usługa równoważenia obciążenia nie obsługuje portów RPC.

## <a name="see-also"></a>Zobacz też

[Konfigurowanie funkcji S2D przy użyciu pulpitu zdalnego (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Rozwiązanie funkcji Hyper-zbieżne z funkcją bezpośrednie miejsca do magazynowania](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Bezpośrednie miejsce do magazynowania — omówienie](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Obsługa funkcji S2D w SQL Server](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
