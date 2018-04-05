---
title: Program SQL Server FCI - maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia wystąpienia klastra trybu Failover programu SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.date: 13/22/2018
ms.author: mikeray
ms.openlocfilehash: 425310f50cebc920a71090d2017dca2a6c135991
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Skonfiguruj wystąpienie klastra pracy awaryjnej programu SQL Server na maszynach wirtualnych Azure

W tym artykule opisano sposób tworzenia trybu Failover klastra wystąpienia (FCI) SQL Server na maszynach wirtualnych Azure w modelu usługi Resource Manager. To rozwiązanie wymaga [systemu Windows Server 2016 Datacenter edition bezpośrednie miejsca do magazynowania \(S2D\) ](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako opartych na oprogramowaniu wirtualnej sieci SAN synchronizujący magazynu (dysków danych) między węzłami (maszynach wirtualnych platformy Azure) w klastrze systemu Windows. S2D stanowi nowość w systemie Windows Server 2016.

Na poniższym diagramie przedstawiono kompletne rozwiązanie na maszynach wirtualnych Azure:

![Grupy dostępności](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Na powyższym diagramie przedstawiono:

- Dwóch maszyn wirtualnych platformy Azure w klastrze pracy awaryjnej systemu Windows. Maszyna wirtualna jest w klastrze pracy awaryjnej jest również nazywany *węzła klastra*, lub *węzłów*.
- Każda maszyna wirtualna ma co najmniej dwa dyski danych.
- S2D synchronizuje dane na dysk z danymi i przedstawia magazynu zostały zsynchronizowane jako puli magazynów.
- Udostępniony wolumin klastra (CSV) do klastra trybu failover przedstawia informacje o puli magazynu.
- Rolę klastra programu SQL Server infrastruktury klasyfikacji plików używa CSV dla dysków z danymi.
- Azure usługi równoważenia obciążenia do przechowywania adresu IP dla infrastruktury klasyfikacji plików z serwera SQL.
- Zestaw dostępności Azure zawiera wszystkie zasoby.

   >[!NOTE]
   >Wszystkie zasoby platformy Azure są na diagramie znajdują się w tej samej grupie zasobów.

Aby uzyskać szczegółowe informacje o S2D, zobacz [systemu Windows Server 2016 Datacenter edition bezpośrednie miejsca do magazynowania \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D obsługuje dwa typy architektury - konwergentnej i hiperkonwergentnych. Architektura w tym dokumencie jest hiperkonwergentnych. Na tych samych serwerów obsługujących aplikacji klastrowanej infrastrukturze hiperkonwergentnych umieszczone magazynu. W ramach tej architektury magazynu jest w każdym węźle SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licencjonowania i cen

Na maszynach wirtualnych platformy Azure można licencji programu SQL Server przy użyciu płatności zgodnie z rzeczywistym użyciem (między) lub użycie własnej licencji obrazów maszyn wirtualnych (BYOL). Typ obrazu, który można wybrać ma wpływ na sposób naliczane są opłaty.

Z między licencjonowania, wystąpienia klastra trybu failover (FCI dla) programu SQL Server na maszynach wirtualnych Azure zostaną naliczone opłaty powiązane dla wszystkich węzłów infrastruktury klasyfikacji plików, łącznie z węzły pasywne. Aby uzyskać więcej informacji, zobacz [programu SQL Server Enterprise maszyn wirtualnych cennik](http://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Klienci z umowy Enterprise Agreement z Software Assurance mają prawa do używania jednej bezpłatnej Węzeł pasywny infrastruktury klasyfikacji plików dla każdego aktywnego węzła. Aby móc korzystać z tego korzyści w Azure, obrazy BYOL maszyny Wirtualnej, a następnie użyj tego samego licencji na aktywnym i pasywnym węzłów infrastruktury klasyfikacji plików. Aby uzyskać więcej informacji, zobacz [umowy Enterprise Agreement](http://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Aby porównać między i BYOL licencjonowania programu SQL Server na maszynach wirtualnych Azure zobacz [wprowadzenie do maszyn wirtualnych SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Aby uzyskać pełne informacje na temat licencjonowania programu SQL Server, zobacz [cennik](http://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Przykład szablonu Azure

Można utworzyć całego rozwiązania na platformie Azure w ramach szablonu. Przykład szablonu jest dostępny w witrynie GitHub [szablonów Szybki Start Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). W tym przykładzie nie jest przeznaczona lub sprawdzane pod kątem żadnych określonego obciążenia. Można uruchomić szablon, aby utworzyć FCI SQL Server z magazynem S2D podłączony do domeny. Ocena szablonu i zmodyfikuj go do własnych celów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Istnieje kilka rzeczy, które trzeba znać i kilka rzeczy, które należy w miejscu przed kontynuowaniem.

### <a name="what-to-know"></a>Co należy wiedzieć
Musisz mieć operacyjne zrozumienia następujące technologie:

- [Technologie klastra systemu Windows](http://technet.microsoft.com/library/hh831579.aspx)
-  [Wystąpienia klastra trybu Failover programu SQL Server](http://msdn.microsoft.com/library/ms189134.aspx).

Ponadto musisz mieć ogólną wiedzą następujące technologie:

- [Zbieżność Hyper rozwiązania przy użyciu bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Grup zasobów platformy Azure](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>Co trzeba

Przed wykonaniem instrukcji zawartych w tym artykule, należy:

- Subskrypcja Microsoft Azure.
- Domena systemu Windows na maszynach wirtualnych Azure.
- Konta z uprawnieniami do tworzenia obiektów w maszynie wirtualnej platformy Azure.
- Sieć wirtualna platformy Azure i podsieci z wystarczającą IP adresu miejsca dla następujących składników:
   - Obydwie maszyny wirtualne.
   - Adres IP klastra pracy awaryjnej.
   - Adres IP dla każdego infrastruktury klasyfikacji plików.
- DNS skonfigurowany w sieci Azure, wskazując na kontrolerach domeny.

Z tych wymagań wstępnych w miejscu można kontynuować tworzenie klastra trybu failover. Pierwszym krokiem jest utworzenie maszyn wirtualnych.

## <a name="step-1-create-virtual-machines"></a>Krok 1: Tworzenie maszyn wirtualnych

1. Zaloguj się do [portalu Azure](http://portal.azure.com) w ramach subskrypcji.

1. [Tworzenie zestawu dostępności Azure](../tutorial-availability-sets.md).

   Dostępność ustawienia grupy maszyn wirtualnych w domenach awarii lub uaktualnienia domen. Zestaw dostępności upewnia się, że aplikacja nie ma wpływu na pojedyncze punkty awarii, takie jak przełącznik sieci lub jednostki zasilania stojak serwerów.

   Jeśli nie utworzono grupy zasobów dla maszyn wirtualnych, należy to zrobić, podczas tworzenia zestawu dostępności Azure. Jeśli używasz portalu Azure do utworzenia zestawu dostępności, wykonaj następujące czynności:

   - W portalu Azure kliknij **+** do otwierania portalu Azure Marketplace. Wyszukaj **zestawu dostępności**.
   - Kliknij przycisk **zestawu dostępności**.
   - Kliknij przycisk **Utwórz**.
   - Na **tworzenia zestawu dostępności** bloku, ustaw następujące wartości:
      - **Nazwa**: Nazwa zestawu dostępności.
      - **Subskrypcja**: Azure Twojej subskrypcji.
      - **Grupa zasobów**: Jeśli chcesz użyć istniejącej grupy, kliknij przycisk **Użyj istniejącego** i wybierz grupę z listy rozwijanej. W przeciwnym razie wybierz **Utwórz nowy** i wpisz nazwę grupy.
      - **Lokalizacja**: Ustaw lokalizację, w których planuje się tworzenie maszyn wirtualnych.
      - **Odporność domen**: Użyj wartości domyślnej (3).
      - **Aktualizowanie domeny**: Użyj wartości domyślnej (5).
   - Kliknij przycisk **Utwórz** utworzyć dostępności zestaw.

1. Tworzenie maszyn wirtualnych w zestawie dostępności.

   Umieszczanie dwóch maszyn wirtualnych programu SQL Server w zestawie dostępności Azure. Aby uzyskać instrukcje, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w portalu Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Umieść obydwie maszyny wirtualne:

   - W tym samym Azure zestawu dostępności z grupy zasobów jest w.
   - W tej samej sieci jako kontroler domeny.
   - W podsieci z wystarczającą przestrzenią adresów IP dla maszyn wirtualnych i wszystkie wystąpienia, które użytkownik może użyć w tym klastrze.
   - W zestawie dostępności Azure.   

      >[!IMPORTANT]
      >Nie można ustawić lub zmienić dostępności ustawić po utworzeniu maszyny wirtualnej.

   Wybierz obraz z portalu Azure Marketplace. Korzystając z witryny Marketplace zawiera obraz z tym systemu Windows Server i SQL Server lub tylko systemu Windows Server. Aby uzyskać więcej informacji, zobacz [Omówienie programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   Oficjalna obrazów programu SQL Server w galerii Azure obejmują zainstalowane wystąpienie programu SQL Server, oraz oprogramowanie instalacyjne programu SQL Server i wymaganego klucza.

   Wybierz obraz prawym zgodnie z jak chcesz zapłacić za licencji programu SQL Server:

   - **Należy zwrócić na użycie licencjonowania**: koszt na sekundę tych obrazów obejmuje licencjonowania programu SQL Server:
      - **SQL Server 2016 przedsiębiorstwa w systemie Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard w systemie Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer w systemie Windows Server Datacenter 2016**

   - **Przełącz your właścicielem licencji (BYOL)**

      - **{BYOL} SQL Server 2016 przedsiębiorstwa w systemie Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard w systemie Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Po utworzeniu maszyny wirtualnej, należy usunąć autonomiczne wstępnie zainstalowane wystąpienie programu SQL Server. Użyje wstępnie zainstalowane nośnika programu SQL Server do utworzenia SQL Server FCI, po skonfigurowaniu klastra trybu failover i S2D.

   Alternatywnie można użyć portalu Azure Marketplace obrazy tylko w systemie operacyjnym. Wybierz **systemu Windows Server 2016 Datacenter** obrazu i zainstaluj SQL Server FCI, po skonfigurowaniu klastra trybu failover i S2D. Ten obraz zawiera nośnik instalacyjny programu SQL Server. Umieść nośnik instalacyjny w lokalizacji, na którym uruchamiasz instalację programu SQL Server dla każdego serwera.

1. Gdy Azure utworzy maszyn wirtualnych, połączyć się z każdej maszyny wirtualnej z protokołem RDP.

   Po pierwsze połączenia z maszyną wirtualną z protokołem RDP komputer zapyta, jeśli chcesz zezwolić tego komputera być wykrywalny w sieci. Kliknij przycisk **Yes** (Tak).

1. Jeśli korzystasz z jednego z obrazów maszyny wirtualnej na serwerze SQL, należy usunąć wystąpienia programu SQL Server.

   - W **programy i funkcje**, kliknij prawym przyciskiem myszy **Microsoft SQL Server 2016 (64-bitowy)** i kliknij przycisk **Odinstaluj/Zmień**.
   - Kliknij przycisk **Usuń**.
   - Wybierz wystąpienie domyślne.
   - Usuwanie wszystkich funkcji w obszarze **usługi aparatu bazy danych**. Nie usuwaj **wspólne funkcje**. Znajduje się poniżej:

      ![Usuwanie funkcji](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Kliknij przycisk **dalej**, a następnie kliknij przycisk **Usuń**.

1. <a name="ports"></a>Otwórz porty zapory.

   Na każdej maszynie wirtualnej należy otworzyć następujące porty w Zaporze systemu Windows.

   | Przeznaczenie | TCP Port | Uwagi
   | ------ | ------ | ------
   | Oprogramowanie SQL Server | 1433 | Normalne port dla domyślnego wystąpienia programu SQL Server. Jeśli używasz obrazu z galerii, ten port jest automatycznie otwierane.
   | Badania kondycji | 59999 | Wszelkie Otwórz TCP port. W kolejnym kroku, należy skonfigurować usługę równoważenia obciążenia [sondy kondycji](#probe) i klaster, aby użyć tego portu.  

1. Dodawanie magazynu do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [dodać magazyn](../premium-storage.md).

   Dane co najmniej dwa dyski należy obie maszyny wirtualne.

   Dołącz raw dyski - dysków sformatowanych nie systemu plików NTFS.
      >[!NOTE]
      >Po dołączeniu dyski sformatowane przy użyciu systemu plików NTFS, można włączyć tylko S2D z bez sprawdzania kwalifikujące dysku.  

   Co najmniej dwa magazyn w warstwie Premium (SSD dyski) należy dołączyć do każdej maszyny Wirtualnej. Zaleca się co najmniej P30 dysków (1 TB).

   Zestaw hosta buforowanie, aby **tylko do odczytu**.

   Pojemność magazynu używanego w środowiskach produkcyjnych zależy od obciążenia. Wartości opisane w tym artykule dotyczą pokazu i testowania.

1. [Dodawanie maszyn wirtualnych do istniejącej domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po utworzenia i skonfigurowania maszyny wirtualnej można skonfigurować klastra pracy awaryjnej.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Krok 2: Konfigurowanie klastra trybu Failover systemu Windows z S2D

Następnym krokiem jest skonfigurowanie klastra pracy awaryjnej z S2D. W tym kroku spowoduje wykonanie następujących podetapów:

1. Dodaj funkcję Klaster pracy awaryjnej systemu Windows
1. Sprawdzanie poprawności klastra
1. Tworzenie klastra trybu failover
1. Tworzenie monitora chmury
1. Dodawanie magazynu

### <a name="add-windows-failover-clustering-feature"></a>Dodaj funkcję Klaster pracy awaryjnej systemu Windows

1. Aby rozpocząć, połączyć się z pierwszej maszynie wirtualnej z protokołem RDP przy użyciu konta domeny, który jest członkiem grupy administratorów lokalnych oraz ma uprawnienia do tworzenia obiektów w usłudze Active Directory. W pozostałej części konfiguracji, należy użyć tego konta.

1. [Dodaj funkcję Klaster pracy awaryjnej w każdej maszynie wirtualnej](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Aby zainstalować funkcję Klaster pracy awaryjnej w interfejsie użytkownika, wykonaj następujące czynności na obu maszynach wirtualnych.
   - W **Menedżera serwera**, kliknij przycisk **Zarządzaj**, a następnie kliknij przycisk **Dodaj role i funkcje**.
   - W **Kreatora dodawania ról i funkcji**, kliknij przycisk **dalej** aż do **Wybieranie funkcji**.
   - W **Wybieranie funkcji**, kliknij przycisk **klaster pracy awaryjnej**. Obejmują wszystkie wymagane funkcje i narzędzia do zarządzania. Kliknij przycisk **dodawania funkcji**.
   - Kliknij przycisk **dalej** , a następnie kliknij przycisk **Zakończ** do zainstalowania funkcji.

   Aby zainstalować funkcję Klaster trybu Failover przy użyciu programu PowerShell, uruchom następujący skrypt w sesji programu PowerShell administratora na jednym z maszyn wirtualnych.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Odwołania, aby wykonać dalsze czynności postępuj zgodnie z instrukcjami w kroku 3 [zbieżność Hyper rozwiązania przy użyciu bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Sprawdzanie poprawności klastra

Ten przewodnik odwołuje się do instrukcji w obszarze [weryfikacji klastra](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Sprawdzanie poprawności klastra przy użyciu interfejsu użytkownika lub przy użyciu programu PowerShell.

Do sprawdzania poprawności klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności na jednym z maszyn wirtualnych.

1. W **Menedżera serwera**, kliknij przycisk **narzędzia**, następnie kliknij przycisk **Menedżera klastra trybu Failover**.
1. W **Menedżera klastra trybu Failover**, kliknij przycisk **akcji**, następnie kliknij przycisk **Sprawdź poprawność konfiguracji...** .
1. Kliknij przycisk **Dalej**.
1. Na **Wybieranie serwerów lub klastrów**, wpisz nazwę obydwie maszyny wirtualne.
1. Na **opcji testowania**, wybierz **uruchamianie tylko testów I wybrać**. Kliknij przycisk **Dalej**.
1. Na **Test wybór**, obejmują wszystkich testów oprócz **magazynu**. Znajduje się poniżej:

   ![Zweryfikuj testy](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Kliknij przycisk **Dalej**.
1. Na **potwierdzenie**, kliknij przycisk **dalej**.

**Kreator weryfikacji konfiguracji** uruchamia testy sprawdzania poprawności.

Do sprawdzania poprawności klastra przy użyciu programu PowerShell, uruchom następujący skrypt w sesji programu PowerShell administratora na jednym z maszyn wirtualnych.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po weryfikacji klastra, utworzyć klaster trybu failover.

### <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

Ten przewodnik odwołuje się do [Tworzenie klastra trybu failover](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Aby utworzyć klaster trybu failover, należy:
- Nazwy maszyn wirtualnych, które stają się w węzłach klastra.
- Nazwa klastra trybu failover
- Adres IP dla klastra trybu failover. Można użyć adresu IP, który nie jest używany jako węzły klastra w tej samej sieci wirtualnej platformy Azure i podsieć.

Następujące środowiska PowerShell tworzy klaster pracy awaryjnej. Aktualizacja skryptu za pomocą nazwy węzłów (nazwy maszyny wirtualnej) i dostępny adres IP z sieci Wirtualnej platformy Azure:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Tworzenie monitora chmury

Monitor chmury jest nowy typ monitora kworum klastra przechowywane w obiekcie Blob magazynu Azure. Spowoduje to usunięcie konieczności oddzielnego hosting udziału monitora maszyny wirtualnej.

1. [Tworzenie chmury monitora dla klastra trybu failover](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Tworzenie kontenera obiektów blob.

1. Zapisz klucze dostępu i adresu URL kontenera.

1. Skonfiguruj Monitor kworum klastra klastra pracy awaryjnej. Zobacz, [Konfiguruj monitora kworum w interfejsie użytkownika]. (http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) w interfejsie użytkownika.

### <a name="add-storage"></a>Dodawanie magazynu

Dyski dla S2D muszą być puste i bez partycji lub innych danych. Aby wyczyścić wykonaj dysków [kroków tego podręcznika](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Włącz sklepu odstępów bezpośrednio \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Następujące Środowisko PowerShell umożliwia bezpośrednie miejsca do magazynowania.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   W **Menedżera klastra trybu Failover**, możesz teraz przeglądać puli magazynu.

1. [Tworzenie woluminu](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Jedną z funkcji S2D jest on automatycznie tworzona jest pula magazynu po jej włączeniu. Teraz można przystąpić do tworzenia woluminu. Polecenia programu PowerShell `New-Volume` automatyzuje proces tworzenia woluminu, w tym formatowania, do klastra i utworzenie udostępnionego woluminu klastra (CSV). Poniższy przykład tworzy 800 gigabajt (GB) CSV.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po wykonaniu tego polecenia, jako zasób klastra został zainstalowany wolumin 800 GB. Wolumin jest na `C:\ClusterStorage\Volume1\`.

   Na poniższym diagramie przedstawiono udostępniony wolumin klastra z S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: Testowanie trybu failover klastra trybu failover

W Menedżerze klastra trybu Failover Sprawdź, czy można przenieść zasobów pamięci masowej do innego węzła klastra. Jeśli można połączyć do klastra trybu failover z **Menedżera klastra trybu Failover** i przenieść magazyn z jednego węzła do drugiego, możesz przystąpić do konfigurowania infrastruktury klasyfikacji plików.

## <a name="step-4-create-sql-server-fci"></a>Krok 4: Tworzenie programu SQL Server infrastruktury klasyfikacji plików

Po skonfigurowaniu klastra trybu failover i wszystkie składniki klastra, łącznie z magazynem, można utworzyć SQL Server FCI.

1. Połącz się na pierwszej maszynie wirtualnej z protokołem RDP.

1. W **Menedżera klastra trybu Failover**, upewnij się, że wszystkie zasoby podstawowe klastra znajdują się na pierwszej maszynie wirtualnej. Jeśli to konieczne, Przenieś wszystkie zasoby do tej maszyny wirtualnej.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów Azure Marketplace, nośnika znajduje się pod adresem `C:\SQLServer_<version number>_Full`. Kliknij przycisk **Instalatora**.

1. W **Centrum instalacji programu SQL Server**, kliknij przycisk **instalacji**.

1. Kliknij przycisk **instalacji klastra pracy awaryjnej nowy serwer SQL**. Postępuj zgodnie z instrukcjami w kreatorze, aby zainstalować Wystąpieniu serwera SQL.

   Infrastruktury klasyfikacji plików katalogi danych muszą znajdować się w magazynie klastra. Z S2D nie jest udostępniony dysk, ale punktu instalacji do woluminu, na każdym serwerze. S2D synchronizuje woluminu między obu węzłów. Wolumin jest prezentowana w klastrze jako udostępniony wolumin klastra. Użyj punktu instalacji woluminu CSV do katalogów danych.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po zakończeniu pracy Kreatora instalacji zostanie zainstalowany FCI serwera SQL na pierwszym węźle.

1. Po zainstalowaniu Instalator pomyślnie infrastruktury klasyfikacji plików na pierwszym węźle połączyć się z drugiego węzła z protokołem RDP.

1. Otwórz **Centrum instalacji programu SQL Server**. Kliknij przycisk **instalacji**.

1. Kliknij przycisk **Dodaj węzeł do klastra trybu failover programu SQL Server**. Postępuj zgodnie z instrukcjami kreatora, aby zainstalować program SQL server i dodać ten serwer do infrastruktury klasyfikacji plików.

   >[!NOTE]
   >Jeśli używasz obrazu galerii Azure Marketplace z programem SQL Server, narzędzia programu SQL Server zostały dołączone do obrazu. Jeśli nie używasz tego obrazu, narzędzia programu SQL Server należy zainstalować oddzielnie. Zobacz [pobierania programu SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Krok 5: Tworzenie usługi równoważenia obciążenia Azure

Na maszynach wirtualnych Azure klastrów, należy użyć modułu równoważenia obciążenia do przechowywania adresu IP, który musi być w jednym węźle klastra w czasie. W tym rozwiązaniu usługi równoważenia obciążenia zawiera adres IP dla infrastruktury klasyfikacji plików z serwera SQL.

[Tworzenie i konfigurowanie usługi równoważenia obciążenia Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Tworzenie usługi równoważenia obciążenia w portalu Azure

Aby utworzyć usługę równoważenia obciążenia:

1. W portalu Azure przejdź do grupy zasobów z maszynami wirtualnymi.

1. Kliknij przycisk **+ Dodaj**. Wyszukaj witryny Marketplace w celu **modułu równoważenia obciążenia**. Kliknij przycisk **modułu równoważenia obciążenia**.

1. Kliknij przycisk **Utwórz**.

1. Skonfiguruj z usługą równoważenia obciążenia:

   - **Nazwa**: nazwę identyfikującą usługi równoważenia obciążenia.
   - **Typ**: usługi równoważenia obciążenia mogą być publicznych lub prywatnych. Usługi równoważenia obciążenia prywatny możliwy z w ramach tej samej sieci Wirtualnej. Najbardziej Azure aplikacje mogą używać modułu równoważenia obciążenia prywatnych. Jeśli aplikacja potrzebuje dostępu do programu SQL Server bezpośrednio przez Internet, należy użyć publiczny moduł równoważenia obciążenia.
   - **Sieć wirtualna**: tej samej sieci maszyn wirtualnych.
   - **Podsieci**: tej samej podsieci co maszyn wirtualnych.
   - **Prywatny adres IP**: tego samego adresu IP przypisanego do zasobu sieciowego klastra programu SQL Server infrastruktury klasyfikacji plików.
   - **Subskrypcja**: Azure Twojej subskrypcji.
   - **Grupa zasobów**: Użyj tej samej grupy zasobów jako maszyn wirtualnych.
   - **Lokalizacja**: Użyj tej samej lokalizacji platformy Azure jako maszyn wirtualnych.
   Znajduje się poniżej:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza modułu równoważenia obciążenia

1. Wróć do grupy zasobów platformy Azure z maszynami wirtualnymi i zlokalizuj nowy moduł równoważenia obciążenia. Może być konieczne odświeżenie widoku w grupie zasobów. Kliknij usługę równoważenia obciążenia.

1. Kliknij przycisk **pul zaplecza** i kliknij przycisk **+ Dodaj** można dodać puli wewnętrznej bazy danych.

1. Skojarz puli wewnętrznej bazy danych z zestawu dostępności, która zawiera maszyn wirtualnych.

1. W obszarze **Target konfiguracje adresów IP sieci**, sprawdź **maszyny WIRTUALNEJ** i wybierz maszyny wirtualne, które będą uczestniczyć jako węzły klastra. Pamiętaj uwzględnić wszystkie maszyny wirtualne, które będą obsługiwać infrastruktury klasyfikacji plików. 

1. Kliknij przycisk **OK** do utworzenia puli wewnętrznej bazy danych.

### <a name="configure-a-load-balancer-health-probe"></a>Skonfiguruj kondycji sondę modułu równoważenia obciążenia

1. W bloku modułu równoważenia obciążenia, kliknij **sondy kondycji**.

1. Kliknij przycisk **+ Dodaj**.

1. Na **sondy kondycji Dodaj** bloku <a name="probe"> </a>ustawić parametry sondy kondycji:

   - **Nazwa**: Nazwa sondy kondycji.
   - **Protokół**: TCP.
   - **Port**: Ustaw na dostępny port TCP. Ten port wymaga port zapory otwarte. Użyj [tego samego portu](#ports) ustawić sondy kondycji na zaporze.
   - **Interwał**: 5 sekund.
   - **Próg złej kondycji**: 2 kolejnych błędów.

1. Kliknij przycisk OK.

### <a name="set-load-balancing-rules"></a>Ustaw reguły równoważenia obciążenia

1. W bloku modułu równoważenia obciążenia, kliknij **reguły równoważenia obciążenia**.

1. Kliknij przycisk **+ Dodaj**.

1. Ustawianie parametrów reguły równoważenia obciążenia:

   - **Nazwa**: nazwę reguły równoważenia obciążenia.
   - **Adres IP frontonu**: Użyj adresu IP dla sieci zasobu klastra programu SQL Server infrastruktury klasyfikacji plików.
   - **Port**: ustawione dla portu TCP programu SQL Server infrastruktury klasyfikacji plików. Domyślnym portem wystąpienie jest port 1433.
   - **Port zaplecza**: Ta wartość używa tego samego portu **portu** wartość po włączeniu **pływającego adresu IP (bezpośredni zwrot serwera)**.
   - **Puli zaplecza**: Użyj wcześniej skonfigurowane nazwy puli wewnętrznej bazy danych.
   - **Sondy kondycji**: Użyj sondy kondycji, który został wcześniej skonfigurowany.
   - **Trwałość sesji**: Brak.
   - **Czas bezczynności (w minutach)**: 4.
   - **Zmienny adres IP (bezpośredni zwrot serwera)**: włączone

1. Kliknij przycisk **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Krok 6: Konfigurowanie klastra dla sondy

Ustaw parametr port sondy klastra w programie PowerShell.

Aby ustawić parametr port sondy klastra, zaktualizuj zmienne w poniższy skrypt z wartościami z używanego środowiska. Usuń nawiasu ostrego `<>` ze skryptu. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

W poprzednim skryptu ustawić wartości dla danego środowiska. Poniższa lista zawiera opis wartości:

   - `<Cluster Network Name>`: Nazwa klastra trybu Failover systemu Windows sieci. W **Menedżera klastra trybu Failover** > **sieci**, kliknij prawym przyciskiem myszy w sieci i kliknij przycisk **właściwości**. Poprawną wartość podlega **nazwa** na **ogólne** kartę. 

   - `<SQL Server FCI IP Address Resource Name>`: Nazwa zasobu adresu IP infrastruktury klasyfikacji plików serwera SQL. W **Menedżera klastra trybu Failover** > **ról**, w ramach roli serwera SQL infrastruktury klasyfikacji plików w obszarze **nazwy serwera**, kliknij prawym przyciskiem myszy zasób adresu IP i kliknij przycisk **Właściwości**. Poprawną wartość podlega **nazwa** na **ogólne** kartę. 

   - `<ILBIP>`: ILB adres IP. Ten adres jest skonfigurowany w portalu Azure jako adres frontonu ILB. Jest to również adres IP infrastruktury klasyfikacji plików na serwerze SQL. Można znaleźć w **Menedżera klastra trybu Failover** na tej samej stronie właściwości, w którym znajduje się `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Jest to port sondy, skonfigurowanych w sondy kondycji modułu równoważenia obciążenia. Dowolny nieużywany port TCP jest prawidłowy. 

>[!IMPORTANT]
>Maska podsieci w parametrze klastrze musi być adresem emisji TCP IP: `255.255.255.255`.

Po ustawieniu sondowania klastra, można wyświetlić wszystkie parametry klastra w programie PowerShell. Uruchom następujący skrypt:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: Testowanie trybu failover z infrastruktury klasyfikacji plików

Testowanie trybu failover z infrastruktury klasyfikacji plików, aby zweryfikować funkcje klastra. Wykonaj następujące czynności:

1. Podłącz do jednego z węzłów klastra programu SQL Server infrastruktury klasyfikacji plików z protokołem RDP.

1. Otwórz **Menedżera klastra trybu Failover**. Kliknij przycisk **ról**. Powiadomienia, który węzeł jest właścicielem roli SQL Server FCI.

1. Kliknij prawym przyciskiem myszy rolę SQL Server FCI.

1. Kliknij przycisk **Przenieś** i kliknij przycisk **najlepszego możliwego węzła**.

**Menedżer klastra trybu failover** pokazuje roli i jej zasobach przejścia do trybu offline. Zasoby następnie przenieś i przejdzie w tryb online w innym węźle.

### <a name="test-connectivity"></a>Testowanie łączności

Aby przetestować połączenie, należy zalogować się do innej maszyny wirtualnej w tej samej sieci wirtualnej. Otwórz **programu SQL Server Management Studio** i połącz się nazwa SQL Server FCI.

>[!NOTE]
>Jeśli to konieczne, możesz [pobierania programu SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Ograniczenia
Na maszynach wirtualnych Azure Microsoft Distributed Transaction Coordinator (DTC) nie jest obsługiwana na wystąpienia ponieważ portu RPC nie jest obsługiwana przez moduł równoważenia obciążenia.

## <a name="see-also"></a>Zobacz też

[Instalacja S2D przy użyciu pulpitu zdalnego (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Zbieżność Hyper rozwiązania z bezpośrednie miejsca do magazynowania](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Omówienie bezpośrednie miejsca magazynu](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Obsługa programu SQL Server dla S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
