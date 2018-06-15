---
title: Konfigurowanie wysokiej dostępności dla maszyn wirtualnych platformy Azure Resource Manager | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia sposób tworzenia zawsze włączonej grupy dostępności z maszyn wirtualnych platformy Azure w trybie Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a612ffd5a68e34cb0a367a6a883495ef26aeb4bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401025"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Konfigurowanie zawsze włączonych grup dostępności w maszynach wirtualnych platformy Azure automatycznie: Menedżer zasobów

W tym samouczku przedstawiono sposób tworzenia grupy dostępności programu SQL Server, która używa usługi Azure Resource Manager maszyn wirtualnych. W samouczku Azure bloki Aby skonfigurować szablon. Można przejrzeć ustawienia domyślne, wpisz wymagane ustawienia i aktualizować bloków w portalu, jak przeprowadzenie tego samouczka.

Zakończenie samouczka tworzy grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure, które obejmują następujące elementy:

* Sieć wirtualna, która ma wiele podsieci, w tym frontonu i podsieci wewnętrznej bazy danych
* Dwa kontrolery domeny, które mają domenę usługi Active Directory
* Dwie maszyny wirtualne, które programem SQL Server wdrożony do podsieci wewnętrznej bazy danych i są przyłączone do domeny usługi Active Directory
* Klaster trybu failover węzła trzech modelu kworum Większość węzłów
* Grupy dostępności, która ma dwa zatwierdzania synchronicznego repliki bazy danych dostępności

Poniższa ilustracja przedstawia kompletnego rozwiązania.

![Architektura laboratorium testowego dla grup dostępności w systemie Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Wszystkie zasoby w tym rozwiązaniu należy do pojedynczej grupy zasobów.

Przed rozpoczęciem tego samouczka, upewnij się, poniżej:

* Masz już konto platformy Azure. Jeśli nie masz, [Załóż konto próbne](http://azure.microsoft.com/pricing/free-trial/).
* Znasz już aprowizowanie maszyny wirtualnej programu SQL Server w galerii maszyn wirtualnych przy użyciu graficznego interfejsu użytkownika. Aby uzyskać więcej informacji, zobacz [obsługi maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Masz już pełny opis grup dostępności. Aby uzyskać więcej informacji, zobacz [zawsze włączonych grup dostępności (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Jeśli interesuje Cię przy użyciu grup dostępności z programu SharePoint, zobacz też [konfigurowania programu SQL Server 2012 zawsze włączone grupy dostępności programu SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
>
>

W tym samouczku Użyj portalu Azure, aby:

* Wybierz szablon zawsze włączone w portalu.
* Przejrzyj ustawienia szablonu i zaktualizować kilka ustawień konfiguracji w danym środowisku.
* Monitorowanie Azure, ponieważ tworzy on całego środowiska.
* Połącz się z kontrolerem domeny, a następnie na serwerze, na którym działa program SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Należy do klastra z galerii
Platforma Azure udostępnia obrazu galerii dla całego rozwiązania. Aby zlokalizować szablonu:

1. Zaloguj się do portalu Azure przy użyciu swojego konta.
2. W portalu Azure kliknij **Utwórz zasób** otworzyć **nowy** okienka.
3. Na **nowy** okienka, wyszukiwanie **AlwaysOn**.
   ![Znajdź szablon (AlwaysOn)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. W wynikach wyszukiwania Znajdź **klastra programu SQL Server AlwaysOn**.
   ![Szablon (AlwaysOn)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Na **wybierz model wdrożenia**, wybierz **Resource Manager**.

### <a name="basics"></a>Podstawy
Kliknij przycisk **podstawy** i skonfiguruj następujące ustawienia:

* **Nazwa użytkownika administratora** jest konto użytkownika z uprawnieniami administratora domeny i jest członkiem roli serwera sysadmin programu SQL Server na obu wystąpień programu SQL Server. W tym samouczku, użyj **administrator domeny**.
* **Hasło** jest to hasło dla konta administratora domeny. Użyj złożone hasło. Potwierdź hasło.
* **Subskrypcja** jest subskrypcji tego Azure rachunków, aby uruchomić wszystkie wdrożone zasoby grupy dostępności. Jeśli konto ma wiele subskrypcji, możesz określić inną subskrypcję.
* **Grupa zasobów** jest nazwą grupy, do której należą wszystkie zasoby platformy Azure, które zostały utworzone przy użyciu tego szablonu. W tym samouczku, użyj **SQL-HA-zarządcy zasobów**. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Lokalizacja** to region platformy Azure, gdzie samouczka tworzy zasoby. Wybierz region platformy Azure.

Poniższy zrzut ekranu jest wypełniony **podstawy** bloku:

![Podstawy](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kliknij przycisk **OK**.

### <a name="domain-and-network-settings"></a>Ustawienia domeny i sieci
Ten szablon galerii Azure tworzy kontrolery domeny i domeny. Tworzy także sieć z dwoma podsieciami. Szablon nie może utworzyć serwerów w istniejącej domenie lub sieci wirtualnej. Następnym krokiem konfiguruje ustawienia domeny i sieci.

Na **ustawienia domeny i sieci** bloku, przejrzyj wstępnie zdefiniowane ustawienia domeny i ustawień sieciowych:

* **Nazwa domeny głównej lasu** jest nazwa domeny dla domeny usługi Active Directory, który jest hostem klastra. Samouczek, użyj **contoso.com**.
* **Nazwa sieci wirtualnej** jest nazwą sieci dla sieci wirtualnej platformy Azure. Samouczek, użyj **autohaVNET**.
* **Nazwa podsieci kontrolera domeny** to nazwa części sieci wirtualnej, która znajduje się na kontrolerze domeny. Użyj **podsieć 1**. Prefiks adresu używa tej podsieci **10.0.0.0/24**.
* **Nazwa podsieci programu SQL Server** to nazwa części sieci wirtualnej, która zawiera serwery, że działania programu SQL Server i udziału plików monitora. Użyj **2 podsieci**. Prefiks adresu używa tej podsieci **10.0.1.0/26**.

Aby dowiedzieć się więcej o sieciach wirtualnych platformy Azure, zobacz [omówienie sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md).  

**Ustawienia domeny i sieci** powinny wyglądać podobnie Poniższy zrzut ekranu:

![Ustawienia domeny i sieci](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

W razie potrzeby można zmienić tych wartości. W tym samouczku Użyj wstępnie zdefiniowane wartości.

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="availability-group-settings"></a>Ustawienia grupy dostępności
Na **ustawienia grupy dostępności**, przejrzyj wstępnie zdefiniowane wartości dla grupy dostępności i odbiornika.

* **Nazwa grupy dostępności** jest nazwą klastra zasobu dla grupy dostępności. W tym samouczku, użyj **Contoso ag**.
* **Nazwa odbiornika grupy dostępności** jest używane przez klaster i wewnętrznego modułu równoważenia obciążenia. Klienci łączący się z programem SQL Server umożliwia łączenie do odpowiednich repliki bazy danych tej nazwy. W tym samouczku, użyj **odbiornika Contoso**.
* **Port odbiornika grupy dostępności** Określa port TCP odbiornika programu SQL Server. W tym samouczku używany jest domyślny port, **1433**.

W razie potrzeby można zmienić tych wartości. W tym samouczku Użyj wstępnie zdefiniowane wartości.  

![Ustawienia grupy dostępności](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kliknij przycisk **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Rozmiar maszyny wirtualnej, ustawień magazynu
Na **rozmiar maszyny Wirtualnej, ustawienia magazynu**, wybierz rozmiar maszyny wirtualnej programu SQL Server i przeglądanie innych ustawień.

* **Rozmiar maszyny wirtualnej programu SQL Server** rozmiar dla maszyn wirtualnych, zarówno z programem SQL Server. Wybierz rozmiar odpowiedniej maszyny wirtualnej dla obciążenia. Jeśli tworzysz środowiska tego samouczka, użyj **DS2**. W przypadku obciążeń produkcyjnych wybierz rozmiar maszyny wirtualnej, który może obsługiwać obciążenie. Wiele obciążeń produkcyjnych wymagają **DS4** lub większą. Szablon tworzy dwie maszyny wirtualne o tym rozmiarze i instaluje program SQL Server w każdej z nich. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure instaluje wersji Enterprise programu SQL Server. Koszt zależy od wersji i rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje o bieżącym kosztów, zobacz [cennik maszyn wirtualnych](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Rozmiar maszyny wirtualnej kontrolera domeny** rozmiar maszyny wirtualnej dla kontrolerów domeny. Dla tego samouczka użyj **D2**.
* **Rozmiar maszyny wirtualnej monitora udostępniania plików** jest rozmiar maszyny wirtualnej do monitora udostępniania plików. W tym samouczku, użyj **A1**.
* **Konto magazynu SQL** jest nazwą konta magazynu, która przechowuje dane programu SQL Server i dysków systemu operacyjnego. W tym samouczku, użyj **alwaysonsql01**.
* **Konto magazynu DC** jest nazwa konta magazynu dla kontrolerów domeny. W tym samouczku, użyj **alwaysondc01**.
* **Rozmiar dysku danych programu SQL Server** TB jest rozmiar dysku danych programu SQL Server w TB. Określ liczbę z zakresu od 1 do 4. W tym samouczku, użyj **1**.
* **Optymalizacja magazynu** Ustawia ustawienia konfiguracji określonego magazynu dla maszyn wirtualnych programu SQL Server, na podstawie typu obciążenia. Wszystkie maszyny wirtualne programu SQL Server, w tym scenariuszu za pomocą magazyn w warstwie premium ustawiony jako tylko do odczytu do hosta dysku platformy Azure w pamięci podręcznej. Ponadto można zoptymalizować ustawienia programu SQL Server dla obciążeń, wybierając jedną z tych trzech ustawień:

  * **Ogólne obciążenie** ustawia nie określonych ustawień konfiguracji.
  * **Przetwarzania transakcyjnego** zestawy śledzenie flagę 1117 i 1118.
  * **Magazynowanie danych** zestawy śledzenie flagę 1117 i 610.

W tym samouczku, użyj **ogólne obciążenie**.

![Ustawienia magazynu rozmiar maszyny Wirtualnej](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

#### <a name="a-note-about-storage"></a>Uwagi dotyczące magazynu
Dodatkowe optymalizacje zależą od rozmiaru dysków danych programu SQL Server. Dla każdego terabajt dysku danych Azure dodaje dodatkowego magazynu premium 1 TB. Jeśli serwer wymaga 2 TB lub więcej, szablon powoduje utworzenie puli magazynu na każdej maszynie wirtualnej programu SQL Server. Puli magazynu jest formą wirtualizacji pamięci masowej, gdzie wiele dysków są skonfigurowana w celu zapewnienia wyższej wydajności, odporności i wydajności.  Następnie szablon tworzy miejsca do magazynowania w puli magazynu i przedstawia informacje o dysku danych do systemu operacyjnego. Szablon określa ten dysk jako dysk z danymi dla programu SQL Server. Szablon Dostraja puli magazynu dla programu SQL Server przy użyciu następujących ustawień:

* Rozmiar przeplotu jest ustawienie przeplotu dla dysku wirtualnego. Obciążeń transakcyjnych użyj 64 KB. Obciążeń magazynowania danych użyj 256 KB.
* Odporność jest prosty (Brak odporności).

> [!NOTE]
> Magazyn w warstwie premium systemu Azure jest magazyn lokalnie nadmiarowy i przechowuje trzy kopie danych w pojedynczym regionie, więc dodatkowe odporności w puli magazynu nie jest wymagane.
>
>

* Liczba kolumn jest równa liczbie dysków w puli magazynu.

Aby uzyskać dodatkowe informacje na temat miejsca do magazynowania i pule magazynu zobacz:

* [Omówienie funkcji miejsca do magazynowania](http://technet.microsoft.com/library/hh831739.aspx)
* [Kopia zapasowa systemu Windows Server i pule magazynu](http://technet.microsoft.com/library/dn390929.aspx)

Aby uzyskać więcej informacji o najlepszych rozwiązaniach konfiguracji programu SQL Server, zobacz [wydajności najlepsze rozwiązania dotyczące programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Ustawienia programu SQL Server
Na **ustawienia programu SQL Server**, przejrzyj i zmodyfikuj prefiks nazwy maszyny wirtualnej programu SQL Server, wersja programu SQL Server, konto usługi serwera SQL i hasło w harmonogramie konserwacji automatyczne stosowanie poprawek SQL.

* **Prefiks nazwy serwera SQL** służy do tworzenia nazwy dla każdej maszyny wirtualnej programu SQL Server. W tym samouczku, użyj **sqlserver**. Szablon nazwy maszyny wirtualnej programu SQL Server *sqlserver 0* i *sqlserver 1*.
* **Wersja programu SQL Server** jest to wersja programu SQL Server. Dla tego samouczka użyj **programu SQL Server 2014**. Można również wybrać **programu SQL Server 2012** lub **programu SQL Server 2016**.
* **Nazwa użytkownika konta usługi programu SQL Server** jest nazwa konta domeny dla usługi SQL Server. W tym samouczku, użyj **SQL Service**.
* **Hasło** jest to hasło dla konta usługi programu SQL Server.  Użyj złożone hasło. Potwierdź hasło.
* **Automatyczne stosowanie poprawek SQL harmonogramu konserwacji** identyfikuje dzień tygodnia, że Azure automatycznie poprawek serwerów SQL. W tym samouczku, wpisz **niedziela**.
* **Godzina rozpoczęcia konserwacji automatyczne stosowanie poprawek SQL** jest godzinę dla regionu Azure, po rozpoczęciu automatyczne stosowanie poprawek.

> [!NOTE]
> Okno stosowania poprawek dla każdej maszyny wirtualnej jest rozłożona przez jedną godzinę. Tylko jedna maszyna wirtualna jest poprawiono w czasie, aby uniknąć przerwy w świadczeniu usług.
>
>

![Ustawienia programu SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="summary"></a>Podsumowanie
Na stronie Podsumowanie Azure sprawdza poprawność ustawień. Możesz również pobrać szablonu. Przejrzyj podsumowanie. Kliknij przycisk **OK**.

### <a name="buy"></a>Kup
Ten blok końcowy zawiera **warunki użytkowania**, i **zasady zachowania poufności informacji**. Przejrzyj te informacje. Gdy wszystko będzie gotowe do platformy Azure można uruchomić tworzenia maszyn wirtualnych i wszystkie inne wymagane zasoby dla grupy dostępności, kliknij przycisk **Utwórz**.

Azure portal tworzy grupę zasobów i wszystkie zasoby.

## <a name="monitor-deployment"></a>Monitor wdrażania
Monitorowanie postępu wdrożenia z portalu Azure. Ikona reprezentuje wdrożenia jest automatycznie przypięty do pulpitu nawigacyjnego portalu Azure.

![Pulpit nawigacyjny platformy Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server
Nowe wystąpienia programu SQL Server są uruchomione na maszynach wirtualnych mających podłączonej do Internetu adresów IP. Możesz pulpitu zdalnego (RDP) bezpośrednio do każdej maszyny wirtualnej programu SQL Server.

Dla protokołu RDP do programu SQL Server wykonaj następujące kroki:

1. Z pulpitu nawigacyjnego portalu Azure Sprawdź, czy wdrożenie zakończyła się pomyślnie.
2. Kliknij przycisk **zasobów**.
3. W **zasobów** bloku, kliknij przycisk **sqlserver 0**, która jest nazwą komputera jednej z maszyn wirtualnych, na których działa program SQL Server.
4. W bloku **sqlserver 0**, kliknij przycisk **Connect**. Przeglądarka zapyta, czy chcesz otworzyć lub zapisać obiekt połączenia zdalnego. Kliknij przycisk **Otwórz**.
5. **Podłączanie pulpitu zdalnego** może ostrzeżenie, że nie można zidentyfikować wydawcy tego połączenia zdalnego. Kliknij przycisk **Połącz**.
6. Zabezpieczenia systemu Windows monituje o podanie poświadczeń do nawiązania połączenia z podstawowym kontrolerem domeny adres IP. Kliknij przycisk **Użyj innego konta**. Aby uzyskać **nazwy użytkownika**, typ **contoso\DomainAdmin**. To konto jest skonfigurowane, gdy ustawiona nazwa użytkownika administratora w szablonie. Użyj złożone hasło wybrany podczas konfigurowania tego szablonu.
7. **Pulpit zdalny** może ostrzeżenie, że komputer zdalny nie można uwierzytelnić z powodu problemów z jego certyfikatem zabezpieczeń. Przedstawia on nazwę certyfikatu zabezpieczeń. Po wykonaniu tego samouczka jest nazwa **sqlserver 0.contoso.com**. Kliknij przycisk **tak**.

Teraz połączenie z protokołem RDP z maszyną wirtualną programu SQL Server. Można Otwórz program SQL Server Management Studio, nawiązać połączenia z domyślnego wystąpienia programu SQL Server i sprawdź, czy grupa dostępności jest skonfigurowane.
