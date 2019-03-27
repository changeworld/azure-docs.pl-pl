---
title: Program SQL Server infrastruktury klasyfikacji plików — usługa Azure Virtual Machines | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia wystąpienia klastra trybu Failover programu SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 3bb829e7cc99ee0d6e2d02f7ed3880d6c0226123
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486322"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurowanie wystąpienia klastra trybu Failover programu SQL Server na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób tworzenia programu SQL Server trybu Failover klastra wystąpienia (FCI) na maszynach wirtualnych platformy Azure w modelu usługi Resource Manager. To rozwiązanie używa [systemu Windows Server 2016 Datacenter edition bezpośrednimi miejscami do magazynowania \(S2D\) ](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako opartych na oprogramowaniu wirtualna sieć SAN synchronizujący magazynu (dyski danych) między węzły (maszyny wirtualne platformy Azure) w Klaster Windows. Funkcja S2D to nowość w systemie Windows Server 2016.

Na poniższym diagramie przedstawiono kompletne rozwiązanie na maszynach wirtualnych platformy Azure:

![Grupy dostępności](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Na powyższym diagramie przedstawiono:

- Dwóch maszyn wirtualnych w klastrze pracy awaryjnej Windows. Maszyna wirtualna jest w klastrze trybu failover jest również nazywany *węzła klastra*, lub *węzłów*.
- Każda maszyna wirtualna ma co najmniej dwóch dysków z danymi.
- S2D synchronizuje dane na dysku danych i prezentuje zsynchronizowane magazynu jako puli magazynów.
- Pula magazynów przedstawia udostępnionego woluminu klastra (CSV) do klastra trybu failover.
- Rolę klastra programu SQL Server infrastruktury klasyfikacji plików używa CSV dla dysków z danymi.
- Usługa Azure load balancer do przechowania adresu IP dla infrastruktury klasyfikacji plików z serwera SQL.
- Zestawu dostępności platformy Azure zawiera wszystkie zasoby.

   >[!NOTE]
   >Wszystkie zasoby platformy Azure znajdują się w diagramie znajdują się w tej samej grupie zasobów.

Aby uzyskać szczegółowe informacje o S2D, zobacz [systemu Windows Server 2016 Datacenter edition bezpośrednimi miejscami do magazynowania \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D obsługuje dwa typy architektur — zbieżne i hiper zbieżności. Architektura, w tym dokumencie jest hiper zbieżności. To infrastruktura hiper zbieżności umieszcza magazynu na serwerach tego samego hosta aplikacji klastrowanej. W ramach tej architektury magazynu jest w każdym węźle infrastruktury klasyfikacji plików programu SQL Server.

## <a name="licensing-and-pricing"></a>Licencjonowanie i ceny

W usłudze Azure Virtual Machines można licencji programu SQL Server przy użyciu płatność zgodnie z rzeczywistym użyciem (PAYG) lub model dostarczania własnej licencji obrazów maszyn wirtualnych (BYOL). Typ obrazu, wybór ma wpływ na sposób są naliczane.

Za pomocą PAYG licencjonowania, wystąpienia klastra trybu failover (FCI) programu SQL Server na maszynach wirtualnych Azure wiąże się z opłat dla wszystkich węzłów infrastruktury klasyfikacji plików, w tym węzły pasywne. Aby uzyskać więcej informacji, zobacz [cennik maszyn wirtualnych usługi SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Klienci z umowy Enterprise Agreement z pakietem Software Assurance mają prawa do używania jednego bezpłatnego węzła pasywnego infrastruktury klasyfikacji plików dla każdego aktywnego węzła. Aby móc korzystać z tej korzyści w przypadku platformy Azure, używać obrazów maszyn wirtualnych w ramach opcji BYOL, a następnie użyć tej samej licencji na aktywnym i pasywnym węzły trybu. Aby uzyskać więcej informacji, zobacz [umowy Enterprise Agreement](https://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Aby porównać PAYG i BYOL licencji na program SQL Server na maszynach wirtualnych Azure zobacz [wprowadzenie do maszyn wirtualnych SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Aby uzyskać pełne informacje na temat licencjonowania programu SQL Server, zobacz [ceny](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Przykładowy szablon platformy Azure

Całe rozwiązanie na platformie Azure można utworzyć z szablonu. Przykład szablonu jest dostępny w witrynie GitHub [szablony szybkiego startu platformy Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). W tym przykładzie nie została zaprojektowana lub jest sprawdzane pod kątem dowolnego określonego obciążenia. Możesz uruchomić szablonu do utworzenia infrastruktury klasyfikacji plików programu SQL Server z magazynem S2D podłączony do domeny. Możesz ocenić szablon i zmodyfikuj go do własnych celów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Istnieje kilka rzeczy, które należy znać i kilka rzeczy, które należy w miejscu przed kontynuowaniem.

### <a name="what-to-know"></a>Co należy wiedzieć
Musisz mieć operacyjnej znajomości następujące technologie:

- [Technologie klastrowania Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Wystąpienia klastra trybu Failover programu SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Jedną istotną różnicą jest, że w klastrze trybu failover gościa maszyny Wirtualnej IaaS platformy Azure, firma Microsoft zaleca jednej karty Sieciowej na serwerze (węzeł klastra) oraz pojedynczej podsieci. Sieci platformy Azure ma fizyczny nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci niepotrzebne w klastrze gościa maszyny Wirtualnej IaaS platformy Azure. Mimo że raport z weryfikacji klastra wyświetli ostrzeżenie węzły tylko są dostępne w ramach jednej sieci, to ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny Wirtualnej IaaS platformy Azure. 

Ponadto powinien mieć ogólna wiedza o następujące technologie:

- [Hiperkonwergentne rozwiązanie z zastosowaniem bezpośrednich miejsc do magazynowania w systemie Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Grupy zasobów platformy Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> W tej chwili [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md) nie jest obsługiwana dla infrastruktury klasyfikacji plików programu SQL Server na platformie Azure. Firma Microsoft zaleca, odinstaluj rozszerzenie z maszyny wirtualne, które uczestniczą w trybu. To rozszerzenie obsługuje funkcje, takie jak automatyczne kopie zapasowe i stosowanie poprawek i niektórych funkcji portalu do języka SQL. Te funkcje nie będą działać dla maszyn wirtualnych SQL, po odinstalowaniu agenta.

### <a name="what-to-have"></a>Co trzeba

Przed wykonaniem instrukcji zawartych w tym artykule, musisz już mieć:

- Subskrypcja Microsoft Azure.
- Domena Windows na maszynach wirtualnych platformy Azure.
- Konta z uprawnieniami do tworzenia obiektów na maszynie wirtualnej platformy Azure.
- Sieć wirtualna platformy Azure i podsieć IP wystarczające przestrzeń adresowa następujące składniki:
   - Obie maszyny wirtualne.
   - Adres IP klastra trybu failover.
   - Adres IP dla każdej infrastruktury klasyfikacji plików.
- DNS skonfigurowany w sieci Azure, wskazując na kontrolerach domeny.

Te warunki wstępne są spełnione możesz kontynuować tworzenie klastra trybu failover. Pierwszym krokiem jest utworzenie maszyn wirtualnych.

## <a name="step-1-create-virtual-machines"></a>Krok 1: Tworzenie maszyn wirtualnych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) z Twoją subskrypcją.

1. [Tworzenie zestawu dostępności platformy Azure](../tutorial-availability-sets.md).

   Dostępność zestawu maszyn wirtualnych grup w domenach błędów i domenach aktualizacji. Zestaw dostępności gwarantuje, że aplikacja nie ma wpływu pojedynczych punktów awarii, takie jak przełącznik sieciowy lub zasilacz stojaka serwerów.

   Jeśli nie utworzono grupę zasobów dla maszyn wirtualnych, należy to zrobić, po utworzeniu zestawu dostępności platformy Azure. Jeśli używasz witryny Azure portal można utworzyć zestawu dostępności, wykonaj następujące czynności:

   - W witrynie Azure portal kliknij pozycję **+** można otworzyć portalu Azure Marketplace. Wyszukaj **zestawu dostępności**.
   - Kliknij przycisk **zestawu dostępności**.
   - Kliknij pozycję **Utwórz**.
   - Na **Tworzenie zestawu dostępności** bloku, ustaw następujące wartości:
      - **Nazwa**: Nazwa zestawu dostępności.
      - **Subskrypcja**: Swoją subskrypcję platformy Azure.
      - **Grupa zasobów**: Jeśli chcesz użyć istniejącej grupy, kliknij przycisk **Użyj istniejącej** i wybierz grupę z listy rozwijanej. W przeciwnym razie wybierz **Utwórz nowy** i wpisz nazwę grupy.
      - **Lokalizacja**: Ustaw lokalizację, w którym planujesz utworzenie maszyn wirtualnych.
      - **Domeny błędów**: Użyj wartości domyślnej (3).
      - **Domeny aktualizacji**: Użyj wartości domyślnej (5).
   - Kliknij przycisk **Utwórz** utworzyć dostępności zestaw.

1. Tworzenie maszyn wirtualnych w zestawie dostępności.

   Aprowizuj dwie maszyny wirtualne programu SQL Server w zestawie dostępności platformy Azure. Aby uzyskać instrukcje, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w witrynie Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

   Umieść obie maszyny wirtualne:

   - W tym samym grupy zasobów, zestaw dostępności usługi jest w.
   - W tej samej sieci jako kontroler domeny.
   - W podsieci z wystarczającą przestrzenią adresów IP dla maszyn wirtualnych i wszystkich występowanie, które może ostatecznie zostanie użyty w tym klastrze.
   - W zestawie dostępności platformy Azure.   

      >[!IMPORTANT]
      >Nie można ustawić lub zmienić zestawu dostępności po utworzeniu maszyny wirtualnej.

   Wybierz obraz z witryny Azure Marketplace. Możesz użyć rynku obrazów, korzystając z niego obejmuje systemu Windows Server i programu SQL Server lub po prostu systemu Windows Server. Aby uzyskać więcej informacji, zobacz [Omówienie programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   Oficjalne obrazy programu SQL Server w galerii systemu Azure to zainstalowane wystąpienie programu SQL Server, oraz oprogramowanie instalacyjne programu SQL Server i wymaganego klucza.

   Wybierz właściwy obraz zgodnie z jak chcesz zapłacić za licencję programu SQL Server:

   - **Płatność za użycie licencji**: Koszt na sekundę tych obrazów zawiera licencję programu SQL Server:
      - **SQL Server 2016 Enterprise dla systemu Windows Server Datacenter 2016**
      - **Program SQL Server 2016 Standard w systemie Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer w systemie Windows Server Datacenter 2016**

   - **Bring-your-own-license (BYOL)**

      - **{BYOL} SQL Server 2016 Enterprise dla systemu Windows Server Datacenter 2016**
      - **{BYOL} Program SQL Server 2016 Standard w systemie Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Po utworzeniu maszyny wirtualnej, należy usunąć wstępnie zainstalowane autonomiczne wystąpienie programu SQL Server. Użyjesz wstępnie zainstalowanych multimediów programu SQL Server do utworzenia infrastruktury klasyfikacji plików z serwera SQL, po skonfigurowaniu klastra trybu failover i S2D.

   Alternatywnie można użyć obrazów portalu Azure Marketplace tylko w systemie operacyjnym. Wybierz **systemu Windows Server 2016 Datacenter** obrazu i instalowanie trybu programu SQL Server, po skonfigurowaniu klastra trybu failover i S2D. Ten obraz nie zawiera nośnik instalacyjny programu SQL Server. W lokalizacji, na którym uruchamiasz instalację programu SQL Server dla każdego serwera, należy umieścić nośnika instalacyjnego.

1. Po platforma Azure tworzy maszyny wirtualne, łączyć się do każdej maszyny wirtualnej przy użyciu protokołu RDP.

   Podczas pierwszego połączenia z maszyną wirtualną za pomocą protokołu RDP, komputer z pytaniem, jeśli chcesz zezwolić na ten komputer jako wykrywalne w sieci. Kliknij przycisk **Yes** (Tak).

1. Jeśli używasz jednego z obrazów maszyn wirtualnych opartych na programie SQL Server, należy usunąć wystąpienia programu SQL Server.

   - W **programy i funkcje**, kliknij prawym przyciskiem myszy **Microsoft SQL Server 2016 (64-bitowy)** i kliknij przycisk **Odinstaluj/Zmień**.
   - Kliknij pozycję **Usuń**.
   - Wybierz domyślne wystąpienie.
   - Usuń wszystkie funkcje w obszarze **usługi aparatu bazy danych**. Nie usuwaj **funkcje wspólne**. Zobacz poniższy obraz:

      ![Usuwanie funkcji](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Kliknij przycisk **dalej**, a następnie kliknij przycisk **Usuń**.

1. <a name="ports"></a>Otwórz porty zapory.

   Na każdej maszynie wirtualnej należy otworzyć następujące porty w Zaporze Windows.

   | Przeznaczenie | TCP Port | Uwagi
   | ------ | ------ | ------
   | Oprogramowanie SQL Server | 1433 | Normalne port dla domyślnego wystąpienia programu SQL Server. Jeśli używasz obrazu z galerii, ten port jest automatycznie otwierany.
   | Sonda kondycji | 59999 | Dowolny Otwórz TCP port. Na późniejszym etapie, konfigurowanie równoważenia obciążenia [sondy kondycji](#probe) i klaster pod kątem używania tego portu.  

1. Dodaj magazyn do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [dodać magazyn](../disks-types.md).

   Obie maszyny wirtualne należy co najmniej dwa dyski danych.

   Dołącz niesformatowane — nie NTFS sformatowane dysków.
      >[!NOTE]
      >Jeśli dołączysz dyski sformatowane przy użyciu systemu plików NTFS, S2D można włączyć tylko przy użyciu sprawdzania uprawnień do nie dysków.  

   Do każdej maszyny Wirtualnej, należy dołączyć co najmniej dwóch dysków SSD w warstwie premium. Zalecamy co najmniej P30 dysków (1 TB).

   Host zestaw z pamięci podręcznej w celu **tylko do odczytu**.

   Pojemność magazynu, którego używasz w środowiskach produkcyjnych, zależy od obciążenia. Wartości opisanych w tym artykule dotyczą demonstracji i testowania.

1. [Dodaj maszyny wirtualne do wcześniej istniejącej domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po utworzeniu i skonfigurowaniu maszyn wirtualnych można skonfigurować klaster trybu failover.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Krok 2: Konfigurowanie klastra pracy awaryjnej Windows przy użyciu S2D

Następnym krokiem jest, aby skonfigurować klaster trybu failover przy użyciu S2D. W tym kroku będzie wykonywać następujące podrzędne:

1. Dodaj funkcję Klaster pracy awaryjnej Windows
1. Sprawdź poprawność klastra
1. Tworzenie klastra trybu failover
1. Tworzenie monitora w chmurze
1. Dodawanie magazynu

### <a name="add-windows-failover-clustering-feature"></a>Dodaj funkcję Klaster pracy awaryjnej Windows

1. Aby rozpocząć, połączyć się z pierwszej maszyny wirtualnej przy użyciu protokołu RDP przy użyciu konta domeny, które jest członkiem Administratorzy lokalni i ma uprawnienia do tworzenia obiektów w usłudze Active Directory. W pozostałej części konfiguracji, należy użyć tego konta.

1. [Dodaj funkcję Klaster trybu Failover do każdej maszyny wirtualnej](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Aby zainstalować funkcję Klaster trybu Failover z interfejsu użytkownika, wykonaj następujące czynności na obu maszynach wirtualnych.
   - W **Menedżera serwera**, kliknij przycisk **Zarządzaj**, a następnie kliknij przycisk **Dodaj role i funkcje**.
   - W **Kreatora dodawania ról i funkcji**, kliknij przycisk **dalej** aż dojdziesz do **Wybieranie funkcji**.
   - W **Wybieranie funkcji**, kliknij przycisk **klastra trybu Failover**. Obejmują wszystkie wymagane funkcje i narzędzia do zarządzania. Kliknij przycisk **Dodaj funkcje**.
   - Kliknij przycisk **dalej** a następnie kliknij przycisk **Zakończ** do zainstalowania funkcji.

   Aby zainstalować funkcję Klaster trybu Failover przy użyciu programu PowerShell, uruchom następujący skrypt w sesji programu PowerShell administratora na jednej z maszyn wirtualnych.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Odwołanie, kolejne kroki postępuj zgodnie z instrukcjami w kroku 3 [hiperkonwergentne rozwiązanie z zastosowaniem bezpośrednich miejsc do magazynowania w systemie Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Sprawdź poprawność klastra

Ten przewodnik odwołuje się do instrukcji w obszarze [weryfikacji klastra](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Sprawdzanie poprawności klastra w interfejsie użytkownika lub za pomocą programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności z jednej z maszyn wirtualnych.

1. W **Menedżera serwera**, kliknij przycisk **narzędzia**, następnie kliknij przycisk **Menedżera klastra trybu Failover**.
1. W **Menedżera klastra trybu Failover**, kliknij przycisk **akcji**, następnie kliknij przycisk **Sprawdź poprawność konfiguracji...** .
1. Kliknij przycisk **Dalej**.
1. Na **Wybieranie serwerów lub klastrów**, wpisz nazwę obie maszyny wirtualne.
1. Na **opcji testowania**, wybierz **uruchomić testy tylko mogę wybrać**. Kliknij przycisk **Dalej**.
1. Na **Test selection**, obejmują wszystkich testów oprócz **magazynu**. Zobacz poniższy obraz:

   ![Zweryfikuj testy](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Kliknij przycisk **Dalej**.
1. Na **potwierdzenie**, kliknij przycisk **dalej**.

**Kreator weryfikacji konfiguracji** uruchamiane są testy weryfikacyjne.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt w sesji programu PowerShell administratora na jednej z maszyn wirtualnych.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po sprawdzeniu klastra, należy utworzyć klaster trybu failover.

### <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

Ten przewodnik odwołuje się do [utworzyć klaster trybu failover](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Aby utworzyć klaster trybu failover, potrzebne są:
- Nazwy maszyn wirtualnych, które stają się węzłami klastra.
- Nazwa klastra trybu failover
- Adres IP dla klastra trybu failover. Można użyć adresu IP, który nie jest używany jako węzły klastra w tej samej sieci wirtualnej platformy Azure i podsieć.

Następujące polecenie programu PowerShell umożliwia utworzenie klastra trybu failover. Zaktualizuj skrypt o nazwach węzłów (nazwy maszyn wirtualnych) i dostępnego adresu IP z sieci Wirtualnej platformy Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Tworzenie monitora w chmurze

Monitor w chmurze to nowy typ monitora kworum klastra, przechowywane w usłudze Azure Blob Storage. Eliminuje to konieczność osobne maszyny wirtualnej, którym udział monitora.

1. [Tworzenie monitora w chmurze dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Utwórz kontener obiektów blob.

1. Zapisać klucze dostępu i adresem URL kontenera.

1. Konfigurowanie monitora kworum klastra trybu failover. Zobacz, [konfigurowania monitora kworum w interfejsie użytkownika](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) w interfejsie użytkownika.

### <a name="add-storage"></a>Dodawanie magazynu

Dyski S2D muszą być puste i bez partycji ani innych danych. Aby wyczyścić postępuj zgodnie z dysków [kroki opisane w tym przewodniku](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Włącz Store bezpośrednimi miejscami do magazynowania \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Następujące polecenie programu PowerShell umożliwia bezpośrednie miejsca do magazynowania magazynu.  

   ```powershell
   Enable-ClusterS2D
   ```

   W **Menedżera klastra trybu Failover**, możesz teraz wyświetlić puli magazynu.

1. [Tworzenie woluminu](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Jedna z funkcji S2D jest on automatycznie tworzy pulę magazynu po jej włączeniu. Teraz możesz przystąpić do tworzenia woluminu. Polecenia cmdlet programu PowerShell `New-Volume` automatyzuje proces tworzenia woluminu, w tym formatowania, dodać do klastra i utworzenie udostępnionego woluminu klastra (CSV). Poniższy przykład tworzy 800 gigabajt (GB) pliku CSV.

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po wykonaniu tego polecenia, wolumin 800 GB jest instalowany jako zasób klastra. Wolumin ma pod `C:\ClusterStorage\Volume1\`.

   Na poniższym diagramie przedstawiono udostępnionego woluminu klastra za pomocą S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: Testowanie trybu failover klastra trybu failover

W Menedżerze klastra trybu Failover Sprawdź, czy można przenieść zasobu magazynu do innego węzła klastra. Jeśli chcesz nawiązać połączenie klaster trybu failover z **Menedżera klastra trybu Failover** i przenieść magazyn z jednego węzła do drugiego, możesz przystąpić do konfigurowania trybu.

## <a name="step-4-create-sql-server-fci"></a>Krok 4: Tworzenie serwera SQL infrastruktury klasyfikacji plików

Po skonfigurowaniu klastra trybu failover i wszystkie składniki klastra, w tym usługi storage, można utworzyć trybu serwera SQL.

1. Połącz na pierwszej maszynie wirtualnej za pomocą protokołu RDP.

1. W **Menedżera klastra trybu Failover**, upewnij się, że wszystkie zasoby podstawowe klastra znajdują się na pierwszej maszynie wirtualnej. Jeśli to konieczne, Przenieś wszystkie zasoby, z tą maszyną wirtualną.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure Marketplace, nośnika znajduje się w `C:\SQLServer_<version number>_Full`. Kliknij przycisk **Instalatora**.

1. W **Centrum instalacji SQL Server**, kliknij przycisk **instalacji**.

1. Kliknij przycisk **instalacji klastra pracy awaryjnej nowy program SQL Server**. Postępuj zgodnie z instrukcjami w kreatorze, aby zainstalować trybu serwera SQL.

   Katalogi danych infrastruktury klasyfikacji plików muszą znajdować się w magazynie klastra. Za pomocą S2D nie jest udostępniony dysk, ale punkt instalacji woluminu na każdym serwerze. S2D synchronizuje woluminu między obu węzłów. Wolumin są prezentowane w klastrze jako udostępniony wolumin klastra. W przypadku katalogów danych, należy użyć punktu instalacji woluminu CSV.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po zakończeniu działania kreatora, Instalator zainstaluje FCI serwera SQL na pierwszym węźle.

1. Po pomyślnym instalacja trybu na pierwszym węźle, łączyć się do drugiego węzła przy użyciu protokołu RDP.

1. Otwórz **Centrum instalacji SQL Server**. Kliknij przycisk **instalacji**.

1. Kliknij przycisk **Dodaj węzeł do klastra trybu failover programu SQL Server**. Postępuj zgodnie z instrukcjami w Kreatorze instalacji programu SQL server i dodać ten serwer do trybu.

   >[!NOTE]
   >Jeśli używasz obrazu galerii witryny Azure Marketplace z programem SQL Server, narzędzia programu SQL Server zostały zawarte w obrazie. Jeśli nie używasz tego obrazu, narzędzia programu SQL Server należy zainstalować oddzielnie. Zobacz [pobieranie programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Krok 5. Tworzenie modułu równoważenia obciążenia na platformie Azure

Na maszynach wirtualnych Azure klastry używają modułu równoważenia obciążenia do przechowywania adresu IP, który musi być w jednym węźle klastra naraz. W przypadku tego rozwiązania modułu równoważenia obciążenia zawiera adres IP dla infrastruktury klasyfikacji plików z serwera SQL.

[Tworzenie i konfigurowanie usługi Azure load balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Utwórz moduł równoważenia obciążenia w witrynie Azure portal

Aby utworzyć moduł równoważenia obciążenia:

1. W witrynie Azure portal przejdź do grupy zasobów z maszynami wirtualnymi.

1. Kliknij pozycję **+ Dodaj**. Wyszukaj w witrynie Marketplace **moduł równoważenia obciążenia**. Kliknij przycisk **moduł równoważenia obciążenia**.

1. Kliknij pozycję **Utwórz**.

1. Konfigurowanie równoważenia obciążenia za pomocą:

   - **Nazwa**: Nazwa, która identyfikuje modułu równoważenia obciążenia.
   - **Typ**: Moduł równoważenia obciążenia może być publicznym lub prywatnym. Moduł równoważenia obciążenia prywatny możliwy z w ramach tej samej sieci Wirtualnej. Najbardziej Azure aplikacje mogą używać modułu równoważenia obciążenia prywatny. Jeśli aplikacja wymaga dostępu do programu SQL Server bezpośrednio przez Internet, należy użyć publicznego modułu równoważenia obciążenia.
   - **Virtual Network**: Tej samej sieci maszyn wirtualnych.
   - **Podsieć**: Tej samej podsieci maszyn wirtualnych.
   - **Prywatny adres IP**: Ten sam adres IP przypisany do zasobu sieciowego klastra programu SQL Server infrastruktury klasyfikacji plików.
   - **Subskrypcja**: Swoją subskrypcję platformy Azure.
   - **Grupa zasobów**: Użyj tej samej grupie zasobów jako maszyn wirtualnych.
   - **Lokalizacja**: Użyj tej samej lokalizacji platformy Azure jako maszyn wirtualnych.
   Zobacz poniższy obraz:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza modułu równoważenia obciążenia

1. Wróć do grupy zasobów platformy Azure z maszynami wirtualnymi i zlokalizuj nowy moduł równoważenia obciążenia. Może być konieczne odświeżenie widoku w grupie zasobów. Kliknij usługę równoważenia obciążenia.

1. Kliknij przycisk **pule zaplecza** i kliknij przycisk **+ Dodaj** na dodawanie puli zaplecza.

1. Skojarz puli wewnętrznej bazy danych z zestawu dostępności, która zawiera maszyny wirtualne.

1. W obszarze **docelowe konfiguracje protokołu IP sieci**, sprawdź **maszyny WIRTUALNEJ** i wybierz maszyny wirtualne, które będą uczestniczyć jako węzły klastra. Pamiętaj uwzględnić wszystkie maszyny wirtualne, które będą obsługiwać trybu. 

1. Kliknij przycisk **OK** do utworzenia puli zaplecza.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurowanie sondy kondycji modułu równoważenia obciążenia

1. W bloku usługi równoważenia obciążenia, kliknij przycisk **sondy kondycji**.

1. Kliknij pozycję **+ Dodaj**.

1. Na **Dodaj sondę kondycji** bloku <a name="probe"> </a>Ustaw parametry sondy kondycji:

   - **Nazwa**: Nazwa sondy kondycji.
   - **Protokół**: TCP.
   - **Port**: Ustaw dostępny port TCP. Ten port wymaga portu zapory open. Użyj [tego samego portu](#ports) ustawione dla sondy kondycji na zaporze.
   - **Interwał**: 5 sekund.
   - **Próg złej kondycji**: 2 kolejnych niepowodzeń.

1. Kliknij przycisk OK.

### <a name="set-load-balancing-rules"></a>Ustaw reguły równoważenia obciążenia

1. W bloku usługi równoważenia obciążenia, kliknij przycisk **reguły równoważenia obciążenia**.

1. Kliknij pozycję **+ Dodaj**.

1. Ustawianie parametrów reguły równoważenia obciążenia:

   - **Nazwa**: Nazwa reguły równoważenia obciążenia.
   - **Adres IP frontonu**: Użyj adresu IP dla sieci zasobu klastra programu SQL Server infrastruktury klasyfikacji plików.
   - **Port**: Ustaw dla portu TCP programu SQL Server z osobna. Wystąpienia domyślnego portu to 1433.
   - **Port zaplecza**: Ta wartość używa tego samego portu **portu** wartość po włączeniu **pływającego adresu IP (bezpośredni zwrot serwera)**.
   - **Pula zaplecza**: Użyj nazwy puli zaplecza, które zostały wcześniej skonfigurowane.
   - **Sonda kondycji**: Za pomocą sondy kondycji, które zostały wcześniej skonfigurowane.
   - **Trwałość sesji**: Brak.
   - **Limit czasu (w minutach) bezczynności**: 4.
   - **Pływający adres IP (bezpośredni zwrot serwera)**: Enabled (Włączony)

1. Kliknij przycisk **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Krok 6: Konfigurowanie klastra na potrzeby sondowania

Ustaw parametr port sondy klastra w programie PowerShell.

Aby ustawić parametr port sondy klastra, zaktualizuj zmienne w poniższy skrypt z wartościami z używanego środowiska. Usuń nawiasy kątowe `<>` ze skryptu. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

W poprzednim skrypcie Ustaw wartości dla danego środowiska. Poniższa lista zawiera opis wartości:

   - `<Cluster Network Name>`: Nazwa klastra pracy awaryjnej systemu Windows Server w sieci. W **Menedżera klastra trybu Failover** > **sieci**, kliknij prawym przyciskiem myszy w sieci i kliknij przycisk **właściwości**. Prawidłowa wartość musi być w obszarze **nazwa** na **ogólne** kartę. 

   - `<SQL Server FCI IP Address Resource Name>`: Nazwa zasobu adresu IP infrastruktury klasyfikacji plików serwera SQL. W **Menedżera klastra trybu Failover** > **role**, w ramach roli programu SQL Server infrastruktury klasyfikacji plików w obszarze **nazwy serwera**, kliknij prawym przyciskiem myszy zasób adresu IP i kliknij przycisk **Właściwości**. Prawidłowa wartość musi być w obszarze **nazwa** na **ogólne** kartę. 

   - `<ILBIP>`: Adres IP wewnętrznego modułu równoważenia obciążenia. Ten adres jest skonfigurowany w witrynie Azure portal jako frontonu adres wewnętrznego modułu równoważenia obciążenia. Jest to również adres IP infrastruktury klasyfikacji plików serwera SQL. Znaleźć go w **Menedżera klastra trybu Failover** na tej samej stronie właściwości, gdzie się `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Jest to port sondy, skonfigurowanego w sondy kondycji modułu równoważenia obciążenia. Dowolny nieużywany port TCP jest prawidłowy. 

>[!IMPORTANT]
>Maska podsieci dla parametru klastra musi być adresem emisji TCP IP: `255.255.255.255`.

Po ustawieniu sondy klastra, możesz zobaczyć wszystkie parametry klastra w programie PowerShell. Uruchom następujący skrypt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: Testowanie trybu failover z infrastruktury klasyfikacji plików

Testowanie trybu failover z infrastruktury klasyfikacji plików, aby zweryfikować funkcje klastra. Wykonaj następujące czynności:

1. Połącz się z jednym z węzłów klastra programu SQL Server infrastruktury klasyfikacji plików za pomocą protokołu RDP.

1. Otwórz **Menedżera klastra trybu Failover**. Kliknij przycisk **role**. Zwróć uwagę, który węzeł jest właścicielem roli infrastruktury klasyfikacji plików programu SQL Server.

1. Kliknij prawym przyciskiem myszy rolę infrastruktury klasyfikacji plików programu SQL Server.

1. Kliknij przycisk **przenieść** i kliknij przycisk **najlepszego możliwego węzła**.

**Menedżer klastra trybu failover** pokazuje rolę i jego zasobów przejdą w tryb offline. Zasoby, następnie przenieść i przejdzie w tryb online w innym węźle.

### <a name="test-connectivity"></a>Testowanie łączności

Aby przetestować łączność, zaloguj się do innej maszyny wirtualnej w tej samej sieci wirtualnej. Otwórz **SQL Server Management Studio** i nawiąż połączenie z nazwy infrastruktury klasyfikacji plików programu SQL Server.

>[!NOTE]
>Jeśli to konieczne, możesz to zrobić [pobieranie programu SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Ograniczenia

Maszyny wirtualne platformy Azure obsługuje transakcji Koordynator MSDTC (Microsoft Distributed) 2019 serwera systemu Windows z magazynem w udostępnione woluminy klastra (CSV) i [standardowego modułu równoważenia obciążenia](../../../load-balancer/load-balancer-standard-overview.md).

Na maszynach wirtualnych platformy Azure usługi MSDTC nie jest obsługiwana w systemie Windows Server 2016 i wcześniej ponieważ:

- Aby korzystać z udostępnionego magazynu, nie można skonfigurować zasobów klastra MSDTC. Z systemu Windows Server 2016 Jeśli utworzysz zasób usługi MSDTC nie będą widoczne każdy udostępniony Magazyn dostępny do użycia, nawet jeśli magazyn jest dostępne. Ten problem został rozwiązany w 2019 r Server systemu Windows.
- Podstawowego modułu równoważenia obciążenia nie obsługuje portów usługi RPC.

## <a name="see-also"></a>Zobacz też

[Konfigurowanie S2D przy użyciu pulpitu zdalnego (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hiperkonwergentne rozwiązanie z funkcji miejsca do magazynowania bezpośredniego](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Omówienie bezpośrednich miejsc magazynu](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Obsługa programu SQL Server dla S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
