---
title: SQL Server FCI z udziałem plików premium — maszyny wirtualne platformy Azure
description: W tym artykule wyjaśniono, jak utworzyć wystąpienie klastra trybu failover programu SQL Server przy użyciu udziału plików w wersji premium na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303226"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurowanie wystąpienia klastra trybu failover programu SQL Server przy pomocą udziału plików premium na maszynach wirtualnych platformy Azure

W tym artykule wyjaśniono, jak utworzyć wystąpienie klastra trybu failover programu SQL Server (FCI) na maszynach wirtualnych platformy Azure przy użyciu [udziału plików premium.](../../../storage/files/storage-how-to-create-premium-fileshare.md)

Udziały plików premium to wspierane przez dysk SSD, stale o niskim opóźnieniu udziały plików, które są w pełni obsługiwane do użytku z wystąpieniami klastra trybu failover dla programu SQL Server 2012 lub nowszego w systemie Windows Server 2012 lub nowszym. Udziały plików premium zapewniają większą elastyczność, co pozwala na ich rozmiar i skalowanie bez żadnych przestojów.


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem jest kilka rzeczy, które musisz wiedzieć i mieć na miejscu.

Powinieneś mieć operacyjną wiedzę na temat tych technologii:

- [Technologie klastra systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Wystąpienia klastra trybu failover programu SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Jedną z rzeczy, o których należy pamiętać, jest to, że w klastrze trybu failover maszyny Wirtualnej usługi Azure IaaS zaleca się pojedynczą kartę sieciową na serwer (węzeł klastra) i pojedynczą podsieć. Sieć platformy Azure ma nadmiarowość fizyczną, która sprawia, że dodatkowe karty sieciowe i podsieci są niepotrzebne w klastrze gościa maszyn wirtualnych platformy Azure IaaS. Raport sprawdzania poprawności klastra ostrzega, że węzły są dostępne tylko w jednej sieci. To ostrzeżenie można zignorować w klastrach trybu failover usługi Azure IaaS VM.

Należy również mieć ogólne zrozumienie tych technologii:

- [Udział plików w usłudze Azure premium](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupy zasobów platformy Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Obecnie wystąpienia klastra trybu failover programu SQL Server na maszynach wirtualnych platformy Azure są obsługiwane tylko w [trybie zarządzania w lekkim trybie zarządzania](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) [rozszerzeniem agenta IaaS programu SQL Server.](virtual-machines-windows-sql-server-agent-extension.md) Aby zmienić tryb pełnego rozszerzenia na odciążony, usuń zasób **maszyny wirtualnej SQL** dla odpowiednich maszyn wirtualnych, a następnie zarejestruj je u dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym. Podczas usuwania zasobu **maszyny wirtualnej SQL** za pomocą portalu Azure **wyczyść pole wyboru obok poprawnej maszyny wirtualnej**. Pełne rozszerzenie obsługuje takie funkcje, jak automatyczne tworzenie kopii zapasowych, łatanie i zaawansowane zarządzanie portalami. Te funkcje nie będą działać dla maszyn wirtualnych SQL po ponownym zainstalowaniu agenta w trybie zarządzania lightweight.

Udziały plików premium zapewniają usługi We/Wy i całe pojemności, które spełnią potrzeby wielu obciążeń. W przypadku obciążeń intensywnie korzystających z we/wy należy wziąć pod uwagę [wystąpienia klastra trybu failover programu SQL Server z bezpośrednimi miejscami do magazynowania,](virtual-machines-windows-portal-sql-create-failover-cluster.md)oparte na zarządzanych dyskach premium lub dyskach ultra.  

Sprawdź działanie we/wy we/wy w swoim środowisku i sprawdź, czy udziały plików w premii zapewnią potrzebne usługi We/Wy usługi We/Wy przed rozpoczęciem wdrażania lub migracji. Liczniki dysków Monitora wydajności systemu Windows umożliwia monitorowanie całkowitej liczby we/wy obsługi i obsługi (transferów dysków/sekundę) i przepływności (bajtów dysku/sekundę) wymaganych dla plików SQL Server Data, Log i Temp DB.

Wiele obciążeń ma pęknięty we/wy, więc jest to dobry pomysł, aby sprawdzić podczas okresów intensywnego użytkowania i zanotować zarówno maksymalne we/wy i średnie we/wy. Udziały plików premium zapewniają usługi We/Wy na podstawie rozmiaru udziału. Udziały plików premium zapewniają również bezpłatne pękanie, które pozwala na rozerwanie we/wy, aby potroić kwotę bazową do jednej godziny.

Aby uzyskać więcej informacji na temat wydajności udziału plików w warstwie Premium, zobacz [Warstwy wydajności udziału plików](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licencjonowanie i ustalanie cen

Na maszynach wirtualnych platformy Azure można licencjonować program SQL Server przy użyciu obrazów maszyn wirtualnych typu pay-as-you-go (PAYG) lub bring-your-own-license (BYOL). Typ wybranego obrazu ma wpływ na sposób naliczana opłata.

Dzięki licencjonowaniu płatności zgodnie z rzeczywistymi przyuśmiawszym wystąpienie klastra trybu failover (FCI) programu SQL Server na maszynach wirtualnych platformy Azure wiąże się z opłatami za wszystkie węzły FCI, w tym węzły pasywne. Aby uzyskać więcej informacji, zobacz [Ceny maszyn wirtualnych programu SQL Server Enterprise .](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)

Jeśli masz umowę Enterprise Agreement z pakietem Software Assurance, możesz użyć jednego bezpłatnego pasywnego węzła FCI dla każdego aktywnego węzła. Aby skorzystać z tej korzyści na platformie Azure, należy użyć obrazów maszyn wirtualnych BYOL i użyć tej samej licencji na węzłach aktywnych i pasywnych FCI. Aby uzyskać więcej informacji, zobacz [Umowa Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Aby porównać licencjonowanie płatności zgodnie z rzeczywistym obciążeniem i licencją BYOL dla programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [Wprowadzenie do maszyn wirtualnych SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Aby uzyskać pełne informacje dotyczące licencjonowania programu SQL Server, zobacz [Cennik](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Strumień pliku

Strumień plików nie jest obsługiwany dla klastra trybu failover z udziałem plików premium. Aby użyć strumienia plików, należy wdrożyć klaster przy użyciu [funkcji Bezpośrednie miejsca do magazynowania](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem czynności w tym artykule, należy już mieć:

- Subskrypcja platformy Microsoft Azure.
- Domena systemu Windows na maszynach wirtualnych platformy Azure.
- Konto użytkownika domeny, które ma uprawnienia do tworzenia obiektów zarówno na maszynach wirtualnych platformy Azure, jak i w usłudze Active Directory.
- Konto użytkownika domeny do uruchomienia usługi SQL Server i że można zalogować się do maszyny wirtualnej podczas instalowania udziału plików.  
- Sieć wirtualna platformy Azure i podsieć z wystarczającą ilością miejsca adresu IP dla tych składników:
   - Dwie maszyny wirtualne.
   - Adres IP klastra trybu failover.
   - Adres IP dla każdego FCI.
- Dns skonfigurowany w sieci platformy Azure, wskazując kontrolery domeny.
- [Udział plików w wersji premium,](../../../storage/files/storage-how-to-create-premium-fileshare.md) który ma być używany jako dysk klastrowany, na podstawie przydziału magazynu bazy danych dla plików danych.
- Jeśli korzystasz z systemu Windows Server 2012 R2 i starszych, będziesz potrzebować innego udziału plików, aby użyć go jako monitora udziału plików, ponieważ świadkowie chmury są obsługiwani dla systemu Windows 2016 i nowszych. Można użyć innego udziału plików platformy Azure lub można użyć udziału plików na osobnej maszynie wirtualnej. Jeśli zamierzasz użyć innego udziału plików platformy Azure, możesz zainstalować go z tym samym procesem, co w przypadku udziału plików premium używanego dla dysku klastrowanego. 

Z tych wymagań wstępnych w miejscu, można rozpocząć tworzenie klastra trybu failover. Pierwszym krokiem jest utworzenie maszyn wirtualnych.

## <a name="step-1-create-the-virtual-machines"></a>Krok 1: Tworzenie maszyn wirtualnych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą subskrypcji.

1. [Utwórz zestaw dostępności platformy Azure](../tutorial-availability-sets.md).

   Dostępność zestaw grup maszyn wirtualnych w domenach błędów i domeny aktualizacji. Zapewnia, że aplikacja nie ma wpływu na pojedyncze punkty awarii, takie jak przełącznik sieciowy lub jednostka zasilania stojaka serwerów.

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

   >[!IMPORTANT]
   > Po utworzeniu maszyny wirtualnej usuń wstępnie zainstalowane autonomiczne wystąpienie programu SQL Server. Po skonfigurowaniu klastra trybu failover i udziału plików premium jako magazynu użyjesz wstępnie zainstalowanego nośnika programu SQL Server.

   Alternatywnie można użyć obrazów portalu Azure Marketplace, które zawierają tylko system operacyjny. Wybierz obraz **centrum danych systemu Windows Server 2016** i zainstaluj program SQL Server FCI po skonfigurowaniu klastra trybu failover i udziału plików premium jako magazynu. Ten obraz nie zawiera nośnika instalacyjnego programu SQL Server. Umieść nośnik instalacyjny programu SQL Server w lokalizacji, w której można go uruchomić dla każdego serwera.

1. Po platformie Azure tworzy maszyny wirtualne, połączyć się z każdym z nich przy użyciu protokołu RDP.

   Podczas pierwszego łączenia się z maszyną wirtualną przy użyciu protokołu RDP monit z pytaniem, czy chcesz zezwolić na wykrycie komputera w sieci. Wybierz **pozycję Tak**.

1. Jeśli używasz jednego z obrazów maszyny wirtualnej opartej na programie SQL Server, usuń wystąpienie programu SQL Server.

   1. W **obszarze Programy i funkcje**kliknij prawym przyciskiem myszy pozycję Microsoft SQL Server **201_ (64-bit)** i wybierz polecenie **Odinstaluj/zmień**.
   1. Wybierz pozycję **Usuń**.
   1. Wybierz wystąpienie domyślne.
   1. Usuń wszystkie funkcje w obszarze **Usługi aparatu baz danych**. Nie usuwaj **funkcji udostępnionych**. Zobaczysz coś takiego jak poniższy zrzut ekranu:

        ![Wybierz obiekty](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Wybierz **pozycję Dalej**, a następnie wybierz pozycję **Usuń**.

1. <span id="ports"> </span> Otwórz porty zapory.  

   Na każdej maszynie wirtualnej otwórz te porty w Zaporze systemu Windows:

   | Przeznaczenie | Port TCP | Uwagi
   | ------ | ------ | ------
   | SQL Server | 1433 | Normalny port dla domyślnych wystąpień programu SQL Server. Jeśli użyto obrazu z galerii, ten port zostanie automatycznie otwarty.
   | Sonda kondycji | 59999 | Dowolny otwarty port TCP. W późniejszym kroku skonfiguruj [sondę kondycji](#probe) modułu równoważenia obciążenia i klaster do użycia tego portu.
   | Udział plików | 445 | Port używany przez usługę udostępniania plików.

1. [Dodaj maszyny wirtualne do istniejącej domeny.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

Po utworzeniu i skonfigurowaniu maszyn wirtualnych można skonfigurować udział plików premium.

## <a name="step-2-mount-the-premium-file-share"></a>Krok 2: Zamontuj udział plików premium

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do konta magazynu.
1. Przejdź do **opcji Udziały plików** w obszarze **Usługa plików** i wybierz udział plików premium, którego chcesz użyć w magazynie SQL.
1. Wybierz **pozycję Połącz,** aby przywołać ciąg połączenia dla udziału plików.
1. Wybierz literę dysku, której chcesz użyć z listy rozwijanej, a następnie skopiuj oba bloki kodu do Notatnika.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Kopiowanie obu poleceń programu PowerShell z portalu connect udziału plików":::

1. Użyj protokołu RDP, aby połączyć się z maszyną wirtualną programu SQL Server z kontem, którego program SQL Server FCI będzie używany dla konta usługi.
1. Otwórz administracyjną konsolę poleceń programu PowerShell.
1. Uruchom polecenia zapisane wcześniej podczas pracy w portalu.
1. Przejdź do udziału za pomocą Eksploratora plików lub okna dialogowego **Uruchom** (klawisz logo systemu Windows + r). Użyj ścieżki `\\storageaccountname.file.core.windows.net\filesharename`sieciowej . Na przykład: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Utwórz co najmniej jeden folder na nowo połączonym udziale plików, aby umieścić pliki SQL Data.
1. Powtórz te kroki na każdej maszynie wirtualnej programu SQL Server, która będzie uczestniczyć w klastrze.

  > [!IMPORTANT]
  > - Rozważ użycie oddzielnego udziału plików do tworzenia kopii zapasowych plików, aby zapisać we/wy i pojemność miejsca dla tego udziału dla plików danych i dzienników. Do tworzenia kopii zapasowych można użyć udziału plików premium lub standardowego.
  > - Jeśli korzystasz z systemu Windows 2012 R2 i starszych, wykonaj te same czynności, aby zainstalować udział plików, który będzie używany jako monitor udziału plików. 

## <a name="step-3-configure-the-failover-cluster"></a>Krok 3: Konfigurowanie klastra trybu failover

Następnym krokiem jest skonfigurowanie klastra trybu failover. W tym kroku wykonaj następujące podkroki:

1. Dodaj funkcję klastrowania trybu failover systemu Windows Server.
1. Sprawdź poprawność klastra.
1. Tworzenie klastra trybu failover.
1. Utwórz monitor chmury (dla systemu Windows Server 2016 i nowszych) lub monitor udziału plików (dla systemu Windows Server 2012 R2 i starszych).


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

### <a name="validate-the-cluster"></a>Sprawdzanie poprawności klastra

Sprawdź poprawność klastra w interfejsie użytkownika lub przy użyciu programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, należy wykonać następujące kroki na jednej z maszyn wirtualnych:

1. W obszarze **Menedżer serwera**wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastrów trybu failover**.
1. W obszarze **Menedżer klastrów trybu failover**wybierz pozycję **Akcja**, a następnie wybierz **pozycję Sprawdź poprawność konfiguracji**.
1. Wybierz **pozycję Dalej**.
1. W obszarze **Wybierz serwery lub klaster**wprowadź nazwy obu maszyn wirtualnych.
1. W obszarze **Opcje testowania**wybierz pozycję **Uruchom tylko testy, które wybrałem**. Wybierz **pozycję Dalej**.
1. W obszarze **Wybór testu**wybierz wszystkie testy z wyjątkiem miejsca **do magazynowania** i **miejsca do magazynowania bezpośrednie**, jak pokazano poniżej:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Wybieranie testów sprawdzania poprawności klastra":::

1. Wybierz **pozycję Dalej**.
1. W obszarze **Potwierdzenie**wybierz pozycję **Dalej**.

**Kreator sprawdzania poprawności konfiguracji** uruchamia testy sprawdzania poprawności.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Po sprawdzeniu poprawności klastra utwórz klaster trybu failover.

### <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

Aby utworzyć klaster trybu failover, należy:
- Nazwy maszyn wirtualnych, które staną się węzłami klastra.
- Nazwa klastra trybu failover
- Adres IP klastra trybu failover. Można użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej platformy Azure i podsieci jako węzły klastra.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Systemy od Windows Server 2012 do Windows Server 2016

Następujący skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2012 do windows server 2016. Zaktualizuj skrypt o nazwy węzłów (nazwy maszyn wirtualnych) i dostępny adres IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Następujący skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2019. Aby uzyskać więcej informacji, zobacz [Klaster trybu failover: Obiekt sieci klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Zaktualizuj skrypt o nazwy węzłów (nazwy maszyn wirtualnych) i dostępny adres IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Tworzenie monitora w chmurze (Wygrana 2016 +)

Jeśli korzystasz z systemu Windows Server 2016 i większych, musisz utworzyć monitor w chmurze. Usługa Cloud Witness to nowy typ monitora kworum klastra, który jest przechowywany w obiekcie blob magazynu platformy Azure. Eliminuje to potrzebę oddzielnej maszyny Wirtualnej, która obsługuje udział świadka lub przy użyciu oddzielnego udziału plików.

1. [Utwórz monitor chmury dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Utwórz kontener obiektów blob.

1. Zapisz klucze dostępu i adres URL kontenera.

### <a name="configure-quorum"></a>Konfigurowanie kworum 

W systemie Windows Server 2016 i większych należy skonfigurować klaster tak, aby używał właśnie utworzonego monitora w chmurze. Wykonaj wszystkie kroki [Konfigurowanie monitora kworum w interfejsie użytkownika](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

W przypadku systemu Windows Server 2012 R2 i starszych wykonaj te same czynności w temacie [Konfigurowanie monitora kworum w interfejsie użytkownika,](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) ale na stronie **Widok wybierania kworum** wybierz opcję **Konfiguruj monitor udziału plików.** Określ udział plików, który został przydzielony jako monitor udziału plików, niezależnie od tego, czy jest on skonfigurowany na osobnej maszynie wirtualnej, czy zainstalowany z platformy Azure. 


## <a name="step-4-test-cluster-failover"></a>Krok 4: Testowanie klastra w pracy awaryjnej

Przetestuj przewijamy próbę awaryjną klastra. W **Menedżerze klastrów trybu failover**kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Więcej akcji** > Przenieś**węzeł Wyboru****zasobu klastra** > podstawowego, a następnie wybierz inny węzeł klastra. Przenieś podstawowy zasób klastra do każdego węzła klastra, a następnie przenieś go z powrotem do węzła podstawowego. Jeśli można pomyślnie przenieść klaster do każdego węzła, można przystąpić do zainstalowania programu SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Testowanie pracy awaryjnej klastra przez przeniesienie zasobu podstawowego do innych węzłów":::

## <a name="step-5-create-the-sql-server-fci"></a>Krok 5: Tworzenie programu SQL Server FCI

Po skonfigurowaniu klastra trybu failover można utworzyć SQL Server FCI.

1. Połącz się z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżerze klastrów trybu failover**upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby przenieś wszystkie zasoby na tę maszynę wirtualną.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure `C:\SQLServer_<version number>_Full`Marketplace, nośnik znajduje się pod adresem . Wybierz **pozycję Ustawienia**.

1. W **Centrum instalacji programu SQL Server**wybierz pozycję **Instalacja**.

1. Wybierz **pozycję Nowa instalacja klastra trybu failover programu SQL Server**. Postępuj zgodnie z instrukcjami kreatora, aby zainstalować SQL Server FCI.

   Katalogi danych FCI muszą znajdować się w udziale plików premium. Wprowadź pełną ścieżkę udziału w tym `\\storageaccountname.file.core.windows.net\filesharename\foldername`formularzu: . Pojawi się ostrzeżenie informujące o położeniu serwera plików jako katalogu danych. To ostrzeżenie jest oczekiwane. Upewnij się, że konto użytkownika, które RDP do maszyny Wirtualnej z podczas utrwalonych udziału plików jest to samo konto, które usługa SQL Server używa, aby uniknąć możliwych błędów.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Używanie udziału plików jako katalogów danych SQL":::

1. Po wykonaniu kroków w kreatorze Instalator zainstaluje SQL Server FCI w pierwszym węźle.

1. Po zainstalowaniu przez Instalatora interfejsu FCI w pierwszym węźle połącz się z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **Centrum instalacji programu SQL Server**. Wybierz **opcję Instalacja**.

1. Wybierz **pozycję Dodaj węzeł do klastra trybu failover programu SQL Server**. Postępuj zgodnie z instrukcjami kreatora, aby zainstalować program SQL Server i dodać serwer do fci.

   >[!NOTE]
   >Jeśli użyto obrazu galerii portalu portalu azure marketplace z programem SQL Server, narzędzia programu SQL Server zostały dołączone do obrazu. Jeśli nie użyto jednego z tych obrazów, zainstaluj narzędzia programu SQL Server oddzielnie. Zobacz [Pobieranie programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Krok 6: Tworzenie modułu równoważenia obciążenia platformy Azure

Na maszynach wirtualnych platformy Azure klastry używają modułu równoważenia obciążenia do przechowywania adresu IP, który musi znajdować się w jednym węźle klastra naraz. W tym rozwiązaniu moduł równoważenia obciążenia przechowuje adres IP dla PROGRAMU SQL Server FCI.

Aby uzyskać więcej informacji, zobacz [Tworzenie i konfigurowanie modułu równoważenia obciążenia platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Tworzenie modułu równoważenia obciążenia w witrynie Azure portal

Aby utworzyć moduł równoważenia obciążenia:

1. W witrynie Azure portal przejdź do grupy zasobów zawierającej maszyny wirtualne.

1. Wybierz pozycję **Dodaj**. Wyszukaj w portalu Azure Marketplace **moduł równoważenia obciążenia**. Wybierz **opcję Równoważenie obciążenia**.

1. Wybierz **pozycję Utwórz**.

1. Skonfiguruj moduł równoważenia obciążenia przy użyciu następujących wartości:

   - **Subskrypcja**: Twoja subskrypcja platformy Azure.
   - **Grupa zasobów:** Grupa zasobów zawierająca maszyny wirtualne.
   - **Nazwa**: Nazwa identyfikująca moduł równoważenia obciążenia.
   - **Region:** lokalizacja platformy Azure zawierająca maszyny wirtualne.
   - **Typ**: Publiczny lub prywatny. Prywatny moduł równoważenia obciążenia jest dostępny z poziomu sieci wirtualnej. Większość aplikacji platformy Azure można użyć prywatnego modułu równoważenia obciążenia. Jeśli aplikacja potrzebuje dostępu do programu SQL Server bezpośrednio przez Internet, użyj publicznego modułu równoważenia obciążenia.
   - **Jednostka SKU**: Standardowa.
   - **Sieć wirtualna:** Ta sama sieć co maszyny wirtualne.
   - **Przypisanie adresu IP**: Statyczne. 
   - **Prywatny adres IP:** Adres IP przypisany do zasobu sieciowego klastra FCI programu SQL Server.

   Na poniższej ilustracji przedstawiono interfejs użytkownika **modułu równoważenia obciążenia Tworzenie:**

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

1. Na **Add sondy** <span id="probe"> </span> kondycji bloku, ustawić następujące parametry sondy kondycji.

   - **Nazwa**: Nazwa sondy kondycji.
   - **Protokół:** TCP.
   - **Port:** Port utworzony w zaporze dla sondy kondycji w [tym kroku](#ports). W tym artykule w przykładzie `59999`użyto portu TCP .
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Krok 7: Konfigurowanie klastra dla sondy

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

## <a name="step-8-test-fci-failover"></a>Krok 8: Test FCI pracy awaryjnej

Przetestuj tryb failover interfejsu FCI w celu sprawdzenia poprawności funkcji klastra. Wykonaj następujące kroki:

1. Połącz się z jednym z węzłów klastra FCI programu SQL Server przy użyciu protokołu RDP.

1. Otwórz **Menedżera klastra trybu failover**. Wybierz **pozycję Role**. Zwróć uwagę, który węzeł jest właścicielem roli SQL Server FCI.

1. Kliknij prawym przyciskiem myszy rolę SQL Server FCI.

1. Wybierz **pozycję Przenieś**, a następnie wybierz pozycję Najlepszy możliwy **węzeł**.

**Menedżer klastra trybu failover** pokazuje rolę, a jego zasoby przechodzą w tryb offline. Zasoby następnie przenieść i wrócić do trybu online w innym węźle.

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

- [Technologie klastra systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Wystąpienia klastra trybu failover programu SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
