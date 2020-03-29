---
title: Konfigurowanie wysokiej dostępności maszyn wirtualnych usługi Azure Resource Manager | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć grupę dostępności zawsze włączone przy tworzeniu maszyn wirtualnych platformy Azure w trybie usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d7c88e500886453fbfb53655748ccf7025ab7d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374263"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Automatyczne konfigurowanie grup dostępności zawsze włączone w maszynach wirtualnych platformy Azure: Menedżer zasobów

W tym samouczku pokazano, jak utworzyć grupę dostępności programu SQL Server, która używa maszyn wirtualnych usługi Azure Resource Manager. Samouczek używa bloków platformy Azure do skonfigurowania szablonu. Możesz przejrzeć ustawienia domyślne, wpisać wymagane ustawienia i zaktualizować ostrza w portalu podczas przechodzenia przez ten samouczek.

Kompletny samouczek tworzy grupę dostępności programu SQL Server na maszynach wirtualnych platformy Azure, które zawierają następujące elementy:

* Sieć wirtualna, która ma wiele podsieci, w tym podsieci frontendu i wewnętrznej bazy danych
* Dwa kontrolery domeny z domeną usługi Active Directory
* Dwie maszyny wirtualne, które uruchamiają program SQL Server i są wdrażane w podsieci wewnętrznej bazy danych i przyłączone do domeny usługi Active Directory
* Klaster trybu failover z trzema węzłami z modelem kworum kworum większościowego węzła
* Grupa dostępności zawierająca dwie repliki synchroniczności bazy danych dostępności

Poniższa ilustracja przedstawia kompletne rozwiązanie.

![Testowanie architektury laboratorium dla grup dostępności na platformie Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Wszystkie zasoby w tym rozwiązaniu należą do jednej grupy zasobów.

Przed rozpoczęciem tego samouczka upewnij się, że:

* Masz już konto platformy Azure. Jeśli go nie masz, [zarejestruj konto próbne.](https://azure.microsoft.com/pricing/free-trial/)
* Wiesz już, jak używać interfejsu użytkownika do aprowizowania maszyny wirtualnej programu SQL Server z galerii maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Masz już solidne zrozumienie grup dostępności. Aby uzyskać więcej informacji, zobacz [Zawsze włączone grupy dostępności (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Jeśli chcesz używać grup dostępności w programie SharePoint, zobacz również [Konfigurowanie grup dostępności programu SQL Server 2012 Always On dla programu SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

W tym samouczku użyj witryny Azure portal, aby:

* Wybierz szablon Zawsze włączone z portalu.
* Przejrzyj ustawienia szablonu i zaktualizuj kilka ustawień konfiguracji dla swojego środowiska.
* Monitoruj platformę Azure, ponieważ tworzy całe środowisko.
* Połącz się z kontrolerem domeny, a następnie z serwerem z systemem SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Aprowizowanie klastra z galerii
Platforma Azure udostępnia obraz galerii dla całego rozwiązania. Aby zlokalizować szablon:

1. Zaloguj się do witryny Azure portal przy użyciu konta.
2. W witrynie Azure Portal kliknij pozycję **Utwórz zasób,** aby otworzyć **nowe** okienko.
3. W okienku **Nowy** wyszukaj hasło **AlwaysOn**.
   ![Znajdź szablon AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. W wynikach wyszukiwania znajdź **klaster programu SQL Server AlwaysOn Cluster**.
   ![Szablon AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. W **obszarze Wybierz model wdrażania**wybierz pozycję Menedżer **zasobów**.

### <a name="basics"></a>Podstawy
Kliknij **pozycję Podstawowe** i skonfiguruj następujące ustawienia:

* **Nazwa użytkownika administratora** jest kontem użytkownika, które ma uprawnienia administratora domeny i jest członkiem roli serwera stałego programu SQL Server w obu wystąpieniach programu SQL Server. W tym samouczku użyj **DomainAdmin**.
* **Hasło** to hasło do konta administratora domeny. Użyj złożonego hasła. Potwierdź hasło.
* **Subskrypcja** to subskrypcja, którą platforma Azure rozlicza w celu uruchomienia wszystkich wdrożonych zasobów dla grupy dostępności. Jeśli twoje konto ma wiele subskrypcji, możesz określić inną subskrypcję.
* **Grupa zasobów** to nazwa grupy, do której należą wszystkie zasoby platformy Azure utworzone przez ten szablon. W tym samouczku użyj **SQL-HA-RG**. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* **Lokalizacja** jest region platformy Azure, gdzie samouczek tworzy zasoby. Wybierz region platformy Azure.

Poniższy zrzut ekranu to ukończony blok **Podstawy:**

![Podstawy](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kliknij przycisk **OK**.

### <a name="domain-and-network-settings"></a>Ustawienia domeny i sieci
Ten szablon galerii platformy Azure tworzy kontrolery domeny i domeny. Tworzy również sieć i dwie podsieci. Szablon nie może utworzyć serwerów w istniejącej domenie lub sieci wirtualnej. W następnym kroku skonfigurowano ustawienia domeny i sieci.

W bloku **Ustawienia domeny i sieci** przejrzyj wstępnie ustawione wartości ustawień domeny i sieci:

* **Nazwa domeny głównej lasu** jest nazwą domeny usługi Active Directory, która obsługuje klaster. W samouczku użyj **contoso.com**.
* **Nazwa sieci wirtualnej** to nazwa sieciowa sieci wirtualnej platformy Azure. W samouczku użyj **autohaVNET**.
* **Nazwa podsieci Kontrolera domeny** to nazwa części sieci wirtualnej, która obsługuje kontroler domeny. Użyj **podsieci-1**. Ta podsieć używa prefiksu adresu **10.0.0.0/24**.
* **Nazwa podsieci programu SQL Server** to nazwa części sieci wirtualnej obsługującej serwery z programem SQL Server i monitora udziału plików. Użyj **podsieci-2**. Ta podsieć używa prefiksu adresu **10.0.1.0/26**.

Aby dowiedzieć się więcej o sieciach wirtualnych na platformie Azure, zobacz [Omówienie sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md).  

**Ustawienia domeny i sieci** powinny wyglądać następująco:

![Ustawienia domeny i sieci](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

W razie potrzeby można zmienić te wartości. W tym samouczku użyj wstępnie ustawionych wartości.

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="availability-group-settings"></a>Ustawienia grupy dostępności
W **ustawieniach grupy Dostępność**przejrzyj wstępnie ustawione wartości dla grupy dostępności i odbiornika.

* **Nazwa grupy dostępności** jest klasterową nazwą zasobu dla grupy dostępności. W tym samouczku użyj **contoso-ag**.
* **Nazwa odbiornika grupy dostępności** jest używana przez klaster i wewnętrzny moduł równoważenia obciążenia. Klienci, którzy łączą się z programem SQL Server, mogą używać tej nazwy do łączenia się z odpowiednią repliką bazy danych. W tym samouczku użyj **contoso-listener**.
* **Port odbiornika grupy dostępności** określa port TCP odbiornika programu SQL Server. W tym samouczku użyj domyślnego portu **1433**.

W razie potrzeby można zmienić te wartości. W tym samouczku użyj wstępnie ustawionych wartości.  

![ustawienia grupy dostępności](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kliknij przycisk **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Rozmiar maszyny wirtualnej, ustawienia magazynu
W **przypadku rozmiaru maszyny wirtualnej ustawienia magazynu**wybierz rozmiar maszyny wirtualnej programu SQL Server i przejrzyj inne ustawienia.

* **Rozmiar maszyny wirtualnej programu SQL Server** jest rozmiarem dla obu maszyn wirtualnych, które uruchamiają program SQL Server. Wybierz odpowiedni rozmiar maszyny wirtualnej dla obciążenia. Jeśli budujesz to środowisko dla samouczka, użyj **DS2**. W przypadku obciążeń produkcyjnych wybierz rozmiar maszyny wirtualnej, który może obsługiwać obciążenie. Wiele obciążeń produkcyjnych wymaga **ds4** lub większych. Szablon tworzy dwie maszyny wirtualne tego rozmiaru i instaluje sql server na każdym z nich. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Platforma Azure instaluje wersję Enterprise Edition programu SQL Server. Koszt zależy od wersji i rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje na temat bieżących kosztów, zobacz [ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Rozmiar maszyny wirtualnej kontrolera domeny** jest rozmiar maszyny wirtualnej dla kontrolerów domeny. W tym samouczku użyj **D2**.
* **Rozmiar maszyny wirtualnej monitora udostępniania plików** jest rozmiarem maszyny wirtualnej dla monitora udziału plików. W tym samouczku użyj **A1**.
* **Konto magazynu SQL** to nazwa konta magazynu, które przechowuje dyski z danymi i systemami operacyjnymi programu SQL Server. W tym samouczku użyj **alwaysonsql01**.
* **Konto magazynu kontrolera domeny** to nazwa konta magazynu kontrolerów domeny. W tym samouczku użyj **alwaysondc01**.
* **Rozmiar dysku danych programu SQL Server** w TB jest rozmiarem dysku danych programu SQL Server w tb. Określ liczbę od 1 do 4. W tym samouczku użyj **1**.
* **Optymalizacja magazynu** ustawia określone ustawienia konfiguracji magazynu dla maszyn wirtualnych programu SQL Server na podstawie typu obciążenia. Wszystkie maszyny wirtualne programu SQL Server w tym scenariuszu używają magazynu w warstwie Premium z pamięcią podręczną hosta dysków platformy Azure ustawioną na tylko do odczytu. Ponadto można zoptymalizować ustawienia programu SQL Server dla obciążenia, wybierając jedno z tych trzech ustawień:

  * **Ogólne obciążenie** nie ustawia żadnych określonych ustawień konfiguracji.
  * **Przetwarzanie transakcyjne** ustawia flagę śledzenia 1117 i 1118.
  * **Magazynowanie danych** ustawia flagę śledzenia 1117 i 610.

W tym samouczku użyj **ogólnego obciążenia**.

![Ustawienia magazynu rozmiaru maszyny Wirtualnej](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

#### <a name="a-note-about-storage"></a>Uwaga dotycząca przechowywania danych
Dodatkowe optymalizacje zależą od rozmiaru dysków z danymi programu SQL Server. Dla każdego terabajta dysku danych platforma Azure dodaje dodatkowy magazyn w wersji premium o pojemności 1 TB. Gdy serwer wymaga 2 TB lub więcej, szablon tworzy pulę magazynu na każdej maszynie wirtualnej programu SQL Server. Pula magazynu jest formą wirtualizacji magazynu, w której wiele dysków jest skonfigurowanych w taki sposób, aby zapewnić większą pojemność, odporność i wydajność.  Szablon następnie tworzy miejsce do magazynowania w puli magazynu i przedstawia jeden dysk danych do systemu operacyjnego. Szablon oznacza ten dysk jako dysk danych dla programu SQL Server. Szablon dostraja pulę magazynu dla programu SQL Server przy użyciu następujących ustawień:

* Rozmiar paska jest ustawieniem przeplotu dysku wirtualnego. Obciążenia transakcyjne używają 64 KB. Obciążenia hurtowni danych używają 256 KB.
* Odporność jest prosta (brak odporności).

> [!NOTE]
> Usługa Azure w magazynie w wersji premium jest lokalnie nadmiarowa i przechowuje trzy kopie danych w jednym regionie, więc dodatkowa odporność w puli magazynu nie jest wymagana.
>
>

* Liczba kolumn jest równa liczbie dysków w puli magazynu.

Aby uzyskać dodatkowe informacje na temat miejsca do magazynowania i pul magazynowych, zobacz:

* [Miejsca do magazynowania — omówienie](https://technet.microsoft.com/library/hh831739.aspx)
* [Kopia zapasowa systemu Windows Server i pule magazynu](https://technet.microsoft.com/library/dn390929.aspx)

Aby uzyskać więcej informacji na temat najlepszych rozwiązań dotyczących konfiguracji programu SQL Server, zobacz [Najważniejsze wskazówki dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-performance.md)

### <a name="sql-server-settings"></a>Ustawienia programu SQL Server
W **przypadku ustawień programu SQL Server**przejrzyj i zmodyfikuj prefiks nazwy maszyny wirtualnej programu SQL Server, wersję programu SQL Server, konto usługi i hasło programu SQL Server oraz harmonogram konserwacji automatycznego wprowadzania poprawek SQL.

* **Prefiks nazwy programu SQL Server** jest używany do tworzenia nazwy dla każdej maszyny wirtualnej programu SQL Server. W tym samouczku użyj **programu sqlserver**. Szablon nazywa maszyny wirtualne programu SQL Server *sqlserver-0* i *sqlserver-1*.
* **Wersja programu SQL Server** jest wersją programu SQL Server. W tym samouczku użyj **programu SQL Server 2014**. Można również wybrać **sql server 2012** lub **SQL Server 2016**.
* **Nazwa użytkownika konta usługi SQL Server** jest nazwą konta domeny usługi SQL Server. W tym samouczku użyj **sqlservice**.
* **Hasło** to hasło do konta usługi PROGRAMU SQL Server.  Użyj złożonego hasła. Potwierdź hasło.
* **Harmonogram konserwacji automatycznego instalowania poprawek SQL** identyfikuje dzień tygodnia, w którym platforma Azure automatycznie poprawia serwery SQL. W tym samouczku wpisz **sunday**.
* **Godzina rozpoczęcia konserwacji automatycznego instalowania poprawek SQL** jest porą dnia dla regionu platformy Azure po rozpoczęciu automatycznego wprowadzania poprawek.

> [!NOTE]
> Okno poprawek dla każdej maszyny wirtualnej jest rozłożone o jedną godzinę. Tylko jedna maszyna wirtualna jest łatana w czasie, aby zapobiec zakłóceniom usług.
>
>

![Ustawienia programu SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="summary"></a>Podsumowanie
Na stronie podsumowania platforma Azure sprawdza poprawność ustawień. Możesz również pobrać szablon. Przejrzyj podsumowanie. Kliknij przycisk **OK**.

### <a name="buy"></a>Kup
Ten ostatni ostrze zawiera **warunki użytkowania**i **politykę prywatności.** Przejrzyj te informacje. Gdy platforma Azure będzie gotowa rozpocząć tworzenie maszyn wirtualnych i wszystkich innych wymaganych zasobów dla grupy dostępności, kliknij przycisk **Utwórz**.

Portal Azure tworzy grupę zasobów i wszystkie zasoby.

## <a name="monitor-deployment"></a>Monitorowanie wdrażania
Monitorowanie postępu wdrażania z witryny Azure portal. Ikona reprezentująca wdrożenie jest automatycznie przypięta do pulpitu nawigacyjnego portalu Azure.

![Pulpit nawigacyjny platformy Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server
Nowe wystąpienia programu SQL Server są uruchomione na maszynach wirtualnych, które mają adresy IP podłączone do Internetu. Pulpit zdalny (RDP) można zdalnie (RDP) bezpośrednio do każdej maszyny wirtualnej programu SQL Server.

Aby przejść z protokołu RDP do programu SQL Server, wykonaj następujące czynności:

1. Na pulpicie nawigacyjnym portalu azure sprawdź, czy wdrożenie zakończyło się pomyślnie.
2. Kliknij **pozycję Zasoby**.
3. W **bloku Zasoby** kliknij **sqlserver-0**, który jest nazwą komputera jednej z maszyn wirtualnych z systemem SQL Server.
4. Na bloku dla **sqlserver-0**kliknij przycisk **Połącz**. Przeglądarka zapyta, czy chcesz otworzyć lub zapisać obiekt połączenia zdalnego. Kliknij przycisk **Open** (Otwórz).
5. **Połączenie pulpitu zdalnego** może ostrzegać, że nie można zidentyfikować wydawcy tego połączenia zdalnego. Kliknij pozycję **Połącz**.
6. Zabezpieczenia systemu Windows monitują o wprowadzenie poświadczeń w celu nawiązania połączenia z adresem IP podstawowego kontrolera domeny. Kliknij **pozycję Użyj innego konta**. W obszarze **Nazwa użytkownika**wpisz **contoso\DomainAdmin**. To konto zostało skonfigurowane po ustawieniu nazwy użytkownika administratora w szablonie. Użyj złożonego hasła wybranego podczas konfigurowania szablonu.
7. **Pulpit zdalny** może ostrzegać, że komputer zdalny nie może być uwierzytelniony z powodu problemów z certyfikatem zabezpieczeń. Pokazuje nazwę certyfikatu zabezpieczeń. Jeśli po tutorialu, nazwa jest **sqlserver-0.contoso.com**. Kliknij **przycisk Tak**.

Teraz masz połączenie z usługą RDP z maszyną wirtualną programu SQL Server. Można otworzyć program SQL Server Management Studio, połączyć się z domyślnym wystąpieniem programu SQL Server i sprawdzić, czy grupa dostępności jest skonfigurowana.
