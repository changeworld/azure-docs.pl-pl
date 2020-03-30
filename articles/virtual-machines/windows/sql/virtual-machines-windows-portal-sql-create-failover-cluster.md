---
title: SQL Server FCI — maszyny wirtualne platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak utworzyć wystąpienie klastra trybu failover programu SQL Server na maszynach wirtualnych platformy Azure.
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
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249804"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurowanie wystąpienia klastra trybu failover programu SQL Server na maszynach wirtualnych platformy Azure

W tym artykule wyjaśniono, jak utworzyć wystąpienie klastra trybu failover programu SQL Server (FCI) na maszynach wirtualnych platformy Azure w modelu usługi Azure Resource Manager. To rozwiązanie używa [systemu Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako opartej na oprogramowaniu wirtualnej sieci SAN, która synchronizuje magazyn (dyski danych) między węzłami (maszyny wirtualne platformy Azure) w klastrze systemu Windows. Bezpośrednie miejsca do magazynowania to nowość w systemie Windows Server 2016.

Na poniższym diagramie przedstawiono kompletne rozwiązanie na maszynach wirtualnych platformy Azure:

![Kompletne rozwiązanie](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Ten diagram przedstawia:

- Dwie maszyny wirtualne platformy Azure w klastrze trybu failover systemu Windows Server. Gdy maszyna wirtualna znajduje się w klastrze trybu failover, jest również nazywana *węzłem* lub *węzłem*klastra.
- Każda maszyna wirtualna ma dwa lub więcej dysków danych.
- Bezpośrednie miejsca do magazynowania synchronizuje dane na dyskach z danymi i przedstawia zsynchronizowany magazyn jako pulę magazynu.
- Pula magazynu przedstawia udostępniony wolumin klastra (CSV) do klastra trybu failover.
- Rola klastra FCI programu SQL Server używa pliku CSV dla dysków danych.
- Moduł równoważenia obciążenia platformy Azure do przechowywania adresu IP dla SQL Server FCI.
- Zestaw dostępności platformy Azure zawiera wszystkie zasoby.

>[!NOTE]
>Wszystkie zasoby platformy Azure na diagramie znajdują się w tej samej grupie zasobów.

Aby uzyskać szczegółowe informacje na temat bezpośredniego miejsca do magazynowania, zobacz [Bezpośrednie miejsca do magazynowania w systemie Windows Server 2016 Datacenter edition](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Miejsca do magazynowania Bezpośrednie obsługuje dwa typy architektur: konwergentne i hiperkonwergentne. Architektura w tym dokumencie jest hiperkonwergentna. Infrastruktura hiperkonwergentna umieszcza magazyn na tych samych serwerach, na których jest obsługiwana aplikacja klastrowana. W tej architekturze magazyn znajduje się w każdym węźle FCI programu SQL Server.

## <a name="licensing-and-pricing"></a>Licencjonowanie i ustalanie cen

Na maszynach wirtualnych platformy Azure można licencjonować program SQL Server przy użyciu obrazów maszyn wirtualnych typu pay-as-you-go (PAYG) lub bring-your-own-license (BYOL). Typ wybranego obrazu ma wpływ na sposób naliczana opłata.

Dzięki licencjonowaniu płatności zgodnie z rzeczywistymi przyuśmiawszym wystąpienie klastra trybu failover (FCI) programu SQL Server na maszynach wirtualnych platformy Azure wiąże się z opłatami za wszystkie węzły FCI, w tym węzły pasywne. Aby uzyskać więcej informacji, zobacz [Ceny maszyn wirtualnych programu SQL Server Enterprise .](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)

Jeśli masz umowę Enterprise Agreement z pakietem Software Assurance, możesz użyć jednego bezpłatnego pasywnego węzła FCI dla każdego aktywnego węzła. Aby skorzystać z tej korzyści na platformie Azure, należy użyć obrazów maszyn wirtualnych BYOL i użyć tej samej licencji na węzłach aktywnych i pasywnych FCI. Aby uzyskać więcej informacji, zobacz [Umowa Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Aby porównać licencjonowanie płatności zgodnie z rzeczywistym obciążeniem i licencją BYOL dla programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [Wprowadzenie do maszyn wirtualnych SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Aby uzyskać pełne informacje dotyczące licencjonowania programu SQL Server, zobacz [Cennik](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Przykładowy szablon platformy Azure

Możesz utworzyć to całe rozwiązanie na platformie Azure na podstawie szablonu. Przykład szablonu jest dostępny w [szablonach szybki start](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)platformy Azure usługi GitHub. W tym przykładzie nie jest zaprojektowany lub przetestowany dla określonego obciążenia. Szablon można uruchomić, aby utworzyć PROGRAM SQL Server FCI z bezpośrednim magazynem miejsca do magazynowania połączonym z domeną. Możesz ocenić szablon i zmodyfikować go do swoich celów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem jest kilka rzeczy, które musisz wiedzieć i mieć na miejscu.

### <a name="what-to-know"></a>Co warto wiedzieć
Powinieneś mieć operacyjną wiedzę na temat tych technologii:

- [Technologie klastra systemu Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Wystąpienia klastra trybu failover programu SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Jedną z rzeczy, o których należy pamiętać, jest to, że w klastrze trybu failover gościa usługi Azure IaaS dla gości zalecamy pojedynczą kartę sieciową na serwer (węzeł klastra) i pojedynczą podsieć. Sieć platformy Azure ma nadmiarowość fizyczną, co sprawia, że dodatkowe karty sieciowe i podsieci są niepotrzebne w klastrze gościa maszyny Wirtualnej platformy Azure IaaS. Raport sprawdzania poprawności klastra ostrzega, że węzły są dostępne tylko w jednej sieci. Można zignorować to ostrzeżenie w klastrach trybu failover gościa usługi Azure IaaS.

Należy również mieć ogólne zrozumienie tych technologii:

- [Rozwiązania hiperkonwergentne korzystające z bezpośrednich miejsc do magazynowania w systemie Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Grupy zasobów platformy Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Obecnie wystąpienia klastra trybu failover programu SQL Server na maszynach wirtualnych platformy Azure są obsługiwane tylko w [trybie zarządzania w lekkim trybie zarządzania](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) [rozszerzeniem agenta IaaS programu SQL Server.](virtual-machines-windows-sql-server-agent-extension.md) Aby zmienić tryb pełnego rozszerzenia na odciążony, usuń zasób **maszyny wirtualnej SQL** dla odpowiednich maszyn wirtualnych, a następnie zarejestruj je u dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym. Podczas usuwania zasobu **maszyny wirtualnej SQL** za pomocą portalu Azure **wyczyść pole wyboru obok poprawnej maszyny wirtualnej**. Pełne rozszerzenie obsługuje takie funkcje, jak automatyczne tworzenie kopii zapasowych, łatanie i zaawansowane zarządzanie portalami. Te funkcje nie będą działać dla maszyn wirtualnych SQL po ponownym zainstalowaniu agenta w trybie zarządzania lightweight.

### <a name="what-to-have"></a>Co mieć

Przed wykonaniem czynności w tym artykule, należy już mieć:

- Subskrypcja platformy Microsoft Azure.
- Domena systemu Windows na maszynach wirtualnych platformy Azure.
- Konto, które ma uprawnienia do tworzenia obiektów zarówno na maszynach wirtualnych platformy Azure, jak i w usłudze Active Directory.
- Sieć wirtualna platformy Azure i podsieć z wystarczającą ilością miejsca adresu IP dla tych składników:
   - Obie maszyny wirtualne.
   - Adres IP klastra trybu failover.
   - Adres IP dla każdego FCI.
- Dns skonfigurowany w sieci platformy Azure, wskazując kontrolery domeny.

Z tych wymagań wstępnych w miejscu, można rozpocząć tworzenie klastra trybu failover. Pierwszym krokiem jest utworzenie maszyn wirtualnych.

## <a name="step-1-create-the-virtual-machines"></a>Krok 1: Tworzenie maszyn wirtualnych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą subskrypcji.

1. [Utwórz zestaw dostępności platformy Azure](../tutorial-availability-sets.md).

   Dostępność zestaw grup maszyn wirtualnych w domenach błędów i domeny aktualizacji. Zapewnia, że aplikacja nie ma wpływu na pojedyncze punkty awarii, takie jak przełącznik sieciowy lub jednostka zasilająca stojaka serwerów.

   Jeśli grupa zasobów nie została utworzona dla maszyn wirtualnych, zrób to podczas tworzenia zestawu dostępności platformy Azure. Jeśli używasz witryny Azure portal do tworzenia zestawu dostępności, wykonaj następujące kroki:

   1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób,** aby otworzyć witrynę Azure Marketplace. Wyszukaj **zestaw dostępności**.
   1. Wybierz **pozycję Zestaw dostępności**.
   1. Wybierz **pozycję Utwórz**.
   1. W obszarze **Utwórz zestaw dostępności**podaj następujące wartości:
      - **Nazwa**: Nazwa zestawu dostępności.
      - **Subskrypcja**: Twoja subskrypcja platformy Azure.
      - **Grupa zasobów**: Jeśli chcesz użyć istniejącej grupy, kliknij pozycję **Wybierz istniejącą,** a następnie wybierz grupę z listy. W przeciwnym razie wybierz pozycję **Utwórz nowy** i wprowadź nazwę grupy.
      - **Lokalizacja:** Ustaw lokalizację, w której planujesz utworzyć maszyny wirtualne.
      - **Domeny błędów:** Użyj wartości domyślnej (**3**).
      - **Aktualizuj domeny**: Użyj wartości domyślnej (**5**).
   1. Wybierz **pozycję Utwórz,** aby utworzyć zestaw dostępności.

1. Utwórz maszyny wirtualne w zestawie dostępności.

   Aprowizuj dwie maszyny wirtualne programu SQL Server w zestawie dostępności platformy Azure. Aby uzyskać instrukcje, zobacz [Aprowizuj maszynę wirtualną programu SQL Server w witrynie Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

   Umieść obie maszyny wirtualne:

   - W tej samej grupie zasobów platformy Azure jako zestaw dostępności.
   - W tej samej sieci co kontroler domeny.
   - W podsieci, która ma wystarczającą ilość miejsca adresu IP dla maszyn wirtualnych i wszystkich FCI, które mogą być ostatecznie używane w klastrze.
   - W zestawie dostępności platformy Azure.

      >[!IMPORTANT]
      >Po utworzeniu maszyny wirtualnej nie można ustawić ani zmienić zestawu dostępności.

   Wybierz obraz z witryny Azure Marketplace. Można użyć obrazu portalu Azure Marketplace zawierającego systemy Windows Server i SQL Server lub użyć obrazu, który zawiera tylko system Windows Server. Aby uzyskać szczegółowe informacje, zobacz [Omówienie programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   Oficjalne obrazy programu SQL Server w galerii Azure obejmują zainstalowane wystąpienie programu SQL Server, oprogramowanie instalacyjne programu SQL Server i wymagany klucz.

   Wybierz odpowiedni obraz na podstawie sposobu płacenia za licencję programu SQL Server:

   - **Licencjonowanie płatności za użycie**. Koszt na sekundę tych obrazów obejmuje licencjonowanie programu SQL Server:
      - **Centrum danych SQL Server 2016 Enterprise w systemie Windows Server 2016**
      - **Centrum danych SQL Server 2016 Standard w systemie Windows Server 2016**
      - **Deweloper programu SQL Server 2016 w centrum danych systemu Windows Server 2016**

   - **Licencja "Bring-your-own- " (BYOL)**

      - **(BYOL) Centrum danych SQL Server 2016 Enterprise w systemie Windows Server 2016**
      - **(BYOL) Centrum danych SQL Server 2016 Standard w systemie Windows Server 2016**

   >[!IMPORTANT]
   >Po utworzeniu maszyny wirtualnej usuń wstępnie zainstalowane autonomiczne wystąpienie programu SQL Server. Po skonfigurowaniu klastra trybu failover i bezpośrednie miejsce do magazynowania użyjesz wstępnie zainstalowanego nośnika programu SQL Server.

   Alternatywnie można użyć obrazów portalu Azure Marketplace, które zawierają tylko system operacyjny. Wybierz obraz **centrum danych systemu Windows Server 2016** i zainstaluj program SQL Server FCI po skonfigurowaniu klastra trybu failover i bezpośrednie miejsca do magazynowania. Ten obraz nie zawiera nośnika instalacyjnego programu SQL Server. Umieść nośnik instalacyjny programu SQL Server w lokalizacji, w której można go uruchomić dla każdego serwera.

1. Po platformie Azure tworzy maszyny wirtualne, połączyć się z każdym z nich przy użyciu protokołu RDP.

   Podczas pierwszego łączenia się z maszyną wirtualną przy użyciu protokołu RDP monit z pytaniem, czy chcesz zezwolić na wykrycie komputera w sieci. Wybierz **pozycję Tak**.

1. Jeśli używasz jednego z obrazów maszyny wirtualnej opartej na programie SQL Server, usuń wystąpienie programu SQL Server.

   1. W **obszarze Programy i funkcje**kliknij prawym przyciskiem myszy pozycję Microsoft SQL Server **2016 (64-bit)** i wybierz polecenie **Odinstaluj/zmień**.
   1. Wybierz pozycję **Usuń**.
   1. Wybierz wystąpienie domyślne.
   1. Usuń wszystkie funkcje w obszarze **Usługi aparatu baz danych**. Nie usuwaj **funkcji udostępnionych**. Zobaczysz coś takiego jak poniższy zrzut ekranu:

      ![Wybierz obiekty](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Wybierz **pozycję Dalej**, a następnie wybierz pozycję **Usuń**.

1. <a name="ports"></a>Otwórz porty zapory.

   Na każdej maszynie wirtualnej otwórz te porty w Zaporze systemu Windows:

   | Przeznaczenie | Port TCP | Uwagi
   | ------ | ------ | ------
   | SQL Server | 1433 | Normalny port dla domyślnych wystąpień programu SQL Server. Jeśli użyto obrazu z galerii, ten port zostanie automatycznie otwarty.
   | Sonda kondycji | 59999 | Dowolny otwarty port TCP. W późniejszym kroku skonfiguruj [sondę kondycji](#probe) modułu równoważenia obciążenia i klaster do użycia tego portu.  

1. Dodaj magazyn do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [dodawanie magazynu](../disks-types.md).

   Obie maszyny wirtualne potrzebują co najmniej dwóch dysków danych.

   Dołączanie dysków nieprzetworzonych, a nie dysków w formacie NTFS.
      >[!NOTE]
      >Po dołączeniu dysków w formacie NTFS można włączyć bezpośrednie miejsca do magazynowania tylko bez sprawdzania uprawnień dyskowych.  

   Dołącz co najmniej dwa najwyższej jakości karty SSD do każdej maszyny wirtualnej. Zalecamy co najmniej dyski P30 (1 TB).

   Ustaw buforowanie hosta na **Tylko do odczytu**.

   Pojemność magazynu używana w środowiskach produkcyjnych zależy od obciążenia. Wartości opisane w tym artykule są do demonstracji i testowania.

1. [Dodaj maszyny wirtualne do istniejącej domeny.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

Po utworzeniu i skonfigurowaniu maszyn wirtualnych można skonfigurować klaster trybu failover.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Krok 2: Konfigurowanie klastra trybu failover systemu Windows Server z bezpośrednimi miejscami do magazynowania

Następnym krokiem jest skonfigurowanie klastra trybu failover z bezpośrednimi miejscami do magazynowania. W tym kroku wykonasz następujące podkroki:

1. Dodaj funkcję klastrowania trybu failover systemu Windows Server.
1. Sprawdź poprawność klastra.
1. Tworzenie klastra trybu failover.
1. Tworzenie monitora chmury.
1. Dodaj magazyn.

### <a name="add-windows-server-failover-clustering"></a>Dodawanie klastrowania trybu failover systemu Windows Server

1. Połącz się z pierwszą maszyną wirtualną z usługą RDP przy użyciu konta domeny, które jest członkiem administratorów lokalnych i ma uprawnienia do tworzenia obiektów w usłudze Active Directory. Użyj tego konta dla pozostałej części konfiguracji.

1. [Dodaj klastrowanie trybu failover do każdej maszyny wirtualnej](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Aby zainstalować klastrowanie trybu failover z interfejsu użytkownika, wykonaj następujące kroki na obu maszynach wirtualnych:
   1. W **Menedżerze serwera**wybierz pozycję **Zarządzaj**, a następnie wybierz pozycję **Dodaj role i funkcje**.
   1. W **Kreatorze dodawania ról i funkcji**wybierz pozycję **Dalej,** aż przejdziesz do **pozycji Wybierz funkcje**.
   1. W **obszarze Wybierz obiekty**wybierz pozycję **Klastrowanie trybu failover**. Uwzględnij wszystkie wymagane funkcje i narzędzia do zarządzania. Wybierz **pozycję Dodaj operacje**.
   1. Wybierz **pozycję Dalej**, a następnie wybierz pozycję **Zakończ,** aby zainstalować obiekty.

   Aby zainstalować klastrowanie trybu failover przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Aby uzyskać więcej informacji na temat kolejnych kroków, zobacz instrukcje w obszarze Krok 3 [rozwiązania hiperkonwergentnego przy użyciu funkcji Bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Sprawdzanie poprawności klastra

Sprawdź poprawność klastra w interfejsie użytkownika lub przy użyciu programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, należy wykonać następujące kroki na jednej z maszyn wirtualnych:

1. W obszarze **Menedżer serwera**wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastrów trybu failover**.
1. W obszarze **Menedżer klastrów trybu failover**wybierz pozycję **Akcja**, a następnie wybierz **pozycję Sprawdź poprawność konfiguracji**.
1. Wybierz **pozycję Dalej**.
1. W obszarze **Wybierz serwery lub klaster**wprowadź nazwy obu maszyn wirtualnych.
1. W obszarze **Opcje testowania**wybierz pozycję **Uruchom tylko testy, które wybrałem**. Wybierz **pozycję Dalej**.
1. W obszarze **Wybór testu**wybierz wszystkie testy z wyjątkiem **magazynu**, jak pokazano poniżej:

   ![Wybieranie testów sprawdzania poprawności klastra](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Wybierz **pozycję Dalej**.
1. W obszarze **Potwierdzenie**wybierz pozycję **Dalej**.

Kreator sprawdzania poprawności konfiguracji uruchamia testy sprawdzania poprawności.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po sprawdzeniu poprawności klastra utwórz klaster trybu failover.

### <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

Aby utworzyć klaster trybu failover, należy:
- Nazwy maszyn wirtualnych, które staną się węzłami klastra.
- Nazwa klastra trybu failover
- Adres IP klastra trybu failover. Można użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej platformy Azure i podsieci jako węzły klastra.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Systemy od Windows Server 2008 do Windows Server 2016

Następujący skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2008 do windows server 2016. Zaktualizuj skrypt o nazwy węzłów (nazwy maszyn wirtualnych) i dostępny adres IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Następujący skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2019. Aby uzyskać więcej informacji, zobacz [Klaster trybu failover: Obiekt sieci klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Zaktualizuj skrypt o nazwy węzłów (nazwy maszyn wirtualnych) i dostępny adres IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Tworzenie monitora w chmurze

Usługa Cloud Witness to nowy typ monitora kworum klastra, który jest przechowywany w obiekcie blob magazynu platformy Azure. Eliminuje to potrzebę oddzielnej maszyny Wirtualnej, która obsługuje udział świadka.

1. [Utwórz monitor chmury dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Utwórz kontener obiektów blob.

1. Zapisz klucze dostępu i adres URL kontenera.

1. Konfigurowanie monitora kworum klastra trybu failover. Zobacz [Konfigurowanie monitora kworum w interfejsie użytkownika](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Dodawanie magazynu

Dyski bezpośrednie miejsca do magazynowania muszą być puste. Nie mogą zawierać partycji ani innych danych. Aby wyczyścić dyski, wykonaj [czynności opisane w tym przewodniku](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Włącz bezpośrednie miejsca w magazynie](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Następujący skrypt programu PowerShell umożliwia bezpośrednie miejsca do magazynowania:  

   ```powershell
   Enable-ClusterS2D
   ```

   W **Menedżerze klastrów trybu failover**można teraz wyświetlić pulę magazynu.

1. [Utwórz wolumin](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Bezpośrednie miejsca do magazynowania automatycznie tworzy pulę magazynu po jej włączeniu. Teraz możesz utworzyć wolumin. Polecenie cmdlet programu `New-Volume` PowerShell automatyzuje proces tworzenia woluminu. Ten proces obejmuje formatowanie, dodawanie woluminu do klastra i tworzenie udostępnionego woluminu klastra (CSV). W tym przykładzie tworzy 800-gigabajt (GB) CSV:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po zakończeniu tego polecenia wolumin o pojemności 800 GB jest instalowany jako zasób klastra. Głośność jest `C:\ClusterStorage\Volume1\`na poziomie .

   Ten zrzut ekranu przedstawia udostępniony wolumin klastra z bezpośrednimi miejscami do magazynowania:

   ![Udostępniony wolumin klastra](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: Testowanie pracy awaryjnej klastra trybu failover

W **Menedżerze klastrów trybu failover**sprawdź, czy można przenieść zasób magazynu do innego węzła klastra. Jeśli można połączyć się z klastrem trybu failover przy użyciu **Menedżera klastra trybu failover** i przenieść magazyn z jednego węzła do drugiego, można przystąpić do konfigurowania FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Krok 4: Tworzenie programu SQL Server FCI

Po skonfigurowaniu klastra trybu failover i wszystkich składników klastra, w tym magazynu, można utworzyć SQL Server FCI.

1. Połącz się z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżerze klastrów trybu failover**upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby przenieś wszystkie zasoby do tej maszyny wirtualnej.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure `C:\SQLServer_<version number>_Full`Marketplace, nośnik znajduje się pod adresem . Wybierz **pozycję Ustawienia**.

1. W **Centrum instalacji programu SQL Server**wybierz pozycję **Instalacja**.

1. Wybierz **pozycję Nowa instalacja klastra trybu failover programu SQL Server**. Postępuj zgodnie z instrukcjami kreatora, aby zainstalować SQL Server FCI.

   Katalogi danych FCI muszą znajdować się w klastrowanym magazynie. W przypadku funkcji Bezpośrednie miejsca do magazynowania nie jest to dysk udostępniony, ale punkt instalacji woluminu na każdym serwerze. Bezpośrednie miejsca do magazynowania synchronizuje wolumin między obydwoma węzłami. Wolumin jest przedstawiany klastrowi jako udostępniony wolumin klastra. Użyj punktu instalacji CSV dla katalogów danych.

   ![Katalogi danych](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po wykonaniu instrukcji w kreatorze Instalator zainstaluje SQL Server FCI w pierwszym węźle.

1. Po zainstalowaniu przez Instalatora interfejsu FCI w pierwszym węźle połącz się z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **Centrum instalacji programu SQL Server**. Wybierz **opcję Instalacja**.

1. Wybierz **pozycję Dodaj węzeł do klastra trybu failover programu SQL Server**. Postępuj zgodnie z instrukcjami kreatora, aby zainstalować program SQL Server i dodać serwer do fci.

   >[!NOTE]
   >Jeśli użyto obrazu galerii portalu portalu portalu Azure Marketplace zawierającego program SQL Server, narzędzia programu SQL Server zostały dołączone do obrazu. Jeśli nie użyto jednego z tych obrazów, zainstaluj narzędzia programu SQL Server oddzielnie. Zobacz [Pobieranie programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Krok 5: Tworzenie modułu równoważenia obciążenia platformy Azure

Na maszynach wirtualnych platformy Azure klastry używają modułu równoważenia obciążenia do przechowywania adresu IP, który musi znajdować się w jednym węźle klastra naraz. W tym rozwiązaniu moduł równoważenia obciążenia przechowuje adres IP dla PROGRAMU SQL Server FCI.

Aby uzyskać więcej informacji, zobacz [Tworzenie i konfigurowanie modułu równoważenia obciążenia platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Tworzenie modułu równoważenia obciążenia w witrynie Azure portal

Aby utworzyć moduł równoważenia obciążenia:

1. W witrynie Azure portal przejdź do grupy zasobów zawierającej maszyny wirtualne.

1. Wybierz pozycję **Dodaj**. Wyszukaj w portalu Azure Marketplace **moduł równoważenia obciążenia**. Wybierz **opcję Równoważenie obciążenia**.

1. Wybierz **pozycję Utwórz**.

1. Skonfiguruj moduł równoważenia obciążenia za pomocą:

   - **Subskrypcja**: Twoja subskrypcja platformy Azure.
   - **Grupa zasobów:** Grupa zasobów zawierająca maszyny wirtualne.
   - **Nazwa**: Nazwa identyfikująca moduł równoważenia obciążenia.
   - **Region:** lokalizacja platformy Azure zawierająca maszyny wirtualne.
   - **Typ**: Publiczny lub prywatny. Prywatny moduł równoważenia obciążenia jest dostępny z poziomu sieci wirtualnej. Większość aplikacji platformy Azure można użyć prywatnego modułu równoważenia obciążenia. Jeśli aplikacja potrzebuje dostępu do programu SQL Server bezpośrednio przez Internet, użyj publicznego modułu równoważenia obciążenia.
   - **Jednostka SKU**: Standardowa.
   - **Sieć wirtualna:** Ta sama sieć co maszyny wirtualne.
   - **Przypisanie adresu IP**: Statyczne. 
   - **Prywatny adres IP:** Adres IP przypisany do zasobu sieciowego klastra FCI programu SQL Server.

 Poniższy zrzut ekranu przedstawia interfejs użytkownika **równoważenia obciążenia Tworzenie:**

   ![Konfigurowanie modułu równoważenia obciążenia](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurowanie puli wewnętrznej bazy danych modułu równoważenia obciążenia

1. Wróć do grupy zasobów platformy Azure, która zawiera maszyny wirtualne i znajdź nowy moduł równoważenia obciążenia. Może być konieczne odświeżenie widoku w grupie zasobów. Wybierz moduł równoważenia obciążenia.

1. Wybierz **pozycję Pule wewnętrznej bazy danych**, a następnie wybierz pozycję **Dodaj**.

1. Skojarz pulę wewnętrznej bazy danych z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **Konfiguracje adresów IP sieci docelowej**wybierz **maszynę wirtualną** i wybierz maszyny wirtualne, które będą uczestniczyć w węzłach klastra. Pamiętaj, aby uwzględnić wszystkie maszyny wirtualne, które będą obsługiwać FCI.

1. Wybierz **przycisk OK,** aby utworzyć pulę wewnętrznej bazy danych.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurowanie sondy kondycji modułu równoważenia obciążenia

1. Na bloku modułu równoważenia obciążenia wybierz opcję **Sondy kondycji**.

1. Wybierz pozycję **Dodaj**.

1. Na **Add sondy** <a name="probe"> </a>kondycji bloku, ustaw parametry sondy kondycji.

   - **Nazwa**: Nazwa sondy kondycji.
   - **Protokół:** TCP.
   - **Port:** Ustaw port utworzony w zaporze dla sondy kondycji w [tym kroku](#ports). W tym artykule w przykładzie `59999`użyto portu TCP .
   - **Interwał:** 5 sekund.
   - **Próg niezdrowej**: 2 kolejne błędy.

1. Kliknij przycisk **OK**.

### <a name="set-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

1. W bloku modułu równoważenia obciążenia wybierz pozycję **Reguły równoważenia obciążenia**.

1. Wybierz pozycję **Dodaj**.

1. Ustaw parametry reguły równoważenia obciążenia:

   - **Nazwa**: Nazwa reguł równoważenia obciążenia.
   - **Adres IP frontu:** Adres IP zasobu sieciowego klastra FCI programu SQL Server.
   - **Port:** Port TCP PROGRAMU SQL Server FCI. Domyślny port wystąpienia to 1433.
   - **Port wewnętrznej bazy:** Używa tego samego portu co wartość **port po** włączeniu **przestawnego adresu IP (bezpośredni powrót serwera)**.
   - **Pula wewnętrznej bazy**danych: nazwa puli wewnętrznej bazy danych skonfigurowana wcześniej.
   - **Sonda kondycji:** sonda kondycji skonfigurowana wcześniej.
   - **Trwałość sesji**: Brak.
   - **Limit czasu bezczynnego (minuty)**: 4.
   - **Przestawny adres IP (bezpośredni powrót serwera)**: Włączone.

1. Kliknij przycisk **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Krok 6: Konfigurowanie klastra dla sondy

Ustaw parametr portu sondy klastra w programie PowerShell.

Aby ustawić parametr portu sondy klastra, zaktualizuj zmienne w następującym skrypcie wartościami ze środowiska. Usuń nawiasy kątowe (`<` i `>`) ze skryptu.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Na poniższej liście opisano wartości, które należy zaktualizować:

   - `<Cluster Network Name>`: Nazwa klastra trybu failover systemu Windows Server dla sieci. W obszarze > Menedżer**klastrów** **trybu failover**kliknij prawym przyciskiem myszy sieć i wybierz polecenie **Właściwości**. Prawidłowa wartość znajduje się w obszarze **Nazwa** na karcie **Ogólne.**

   - `<SQL Server FCI IP Address Resource Name>`: Nazwa zasobu adresu IP PROGRAMU SQL Server FCI. W obszarze**Role** >  **Menedżera klastrów trybu failover**w roli SQL Server FCI w obszarze **Nazwa serwera**kliknij prawym przyciskiem myszy **zasób**adres IP i wybierz polecenie Właściwości . Prawidłowa wartość znajduje się w obszarze **Nazwa** na karcie **Ogólne.** 

   - `<ILBIP>`: Adres IP równoważenia obciążenia. Ten adres jest skonfigurowany w witrynie Azure portal jako adres front-end równoważenia obciążenia. Jest to również adres IP FCI programu SQL Server. Można go znaleźć w **Menedżerze klastra trybu failover** `<SQL Server FCI IP Address Resource Name>`na tej samej stronie właściwości, na której znajduje się .  

   - `<nnnnn>`: Port sondy skonfigurowany w sondzie kondycji modułu równoważenia obciążenia. Każdy nieużyny port TCP jest prawidłowy.

>[!IMPORTANT]
>Maska podsieci dla parametru klastra musi `255.255.255.255`być adresem emisji IP TCP: .

Po ustawieniu sondy klastra można wyświetlić wszystkie parametry klastra w programie PowerShell. Uruchom ten skrypt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: Test FCI pracy awaryjnej

Przetestuj tryb failover interfejsu FCI w celu sprawdzenia poprawności funkcji klastra. Wykonaj następujące kroki:

1. Połącz się z jednym z węzłów klastra FCI programu SQL Server przy użyciu protokołu RDP.

1. Otwórz **Menedżera klastra trybu failover**. Wybierz **pozycję Role**. Zwróć uwagę, który węzeł jest właścicielem roli SQL Server FCI.

1. Kliknij prawym przyciskiem myszy rolę SQL Server FCI.

1. Wybierz **pozycję Przenieś**, a następnie wybierz pozycję Najlepszy możliwy **węzeł**.

**Menedżer klastra trybu failover** pokazuje rolę, a jego zasoby przechodzą w tryb offline. Zasoby następnie przenieść i przejść do trybu online w innym węźle.

### <a name="test-connectivity"></a>Testowanie łączności

Aby przetestować łączność, zaloguj się do innej maszyny wirtualnej w tej samej sieci wirtualnej. Otwórz **program SQL Server Management Studio** i połącz się z nazwą FCI programu SQL Server.

>[!NOTE]
>W razie potrzeby można [pobrać program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Ograniczenia

Maszyny wirtualne platformy Azure obsługują koordynatora transakcji rozproszonych firmy Microsoft (MSDTC) w systemie Windows Server 2019 z magazynem na klastrowanych woluminach udostępnionych (CSV) i [standardowym modułem równoważenia obciążenia.](../../../load-balancer/load-balancer-standard-overview.md)

Na maszynach wirtualnych platformy Azure usługa MSDTC nie jest obsługiwana w systemie Windows Server 2016 lub starszym, ponieważ:

- Klastrowanego zasobu MSDTC nie można skonfigurować do używania magazynu udostępnionego. W systemie Windows Server 2016 jeśli utworzysz zasób usługi MSDTC, nie będzie on widoczny dla wszystkich udostępnionego magazynu dostępnego do użycia, nawet jeśli magazyn jest dostępny. Ten problem został rozwiązany w systemie Windows Server 2019.
- Podstawowy moduł równoważenia obciążenia nie obsługuje portów RPC.

## <a name="see-also"></a>Zobacz też

[Konfigurowanie bezpośrednie miejsca do magazynowania za pomocą pulpitu zdalnego (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Rozwiązanie hiperkonwergentne z bezpośrednimi miejscami do magazynowania](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Bezpośrednie miejsca do magazynowania — omówienie](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Obsługa programu SQL Server dla bezpośrednie miejsca do magazynowania](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
