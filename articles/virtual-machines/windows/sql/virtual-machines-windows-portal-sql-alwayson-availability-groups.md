---
title: Konfigurowanie wysokiej dostępności dla maszyn wirtualnych platformy Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć zawsze włączonej grupy dostępności przy użyciu maszyn wirtualnych platformy Azure w trybie usługi Azure Resource Manager.
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
ms.openlocfilehash: bddc83d55c8909412f7f935a4324a6f316a82cd7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129557"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Skonfiguruj w usłudze Azure Virtual Machines zawsze włączonych grup dostępności automatycznie: Resource Manager

W tym samouczku dowiesz się, jak utworzyć grupy dostępności programu SQL Server, która używa maszyn wirtualnych usługi Azure Resource Manager. Samouczek używa bloków w witrynie Azure, aby skonfigurować szablon. Można przejrzeć ustawienia domyślne, wpisz wymagane ustawienia i Aktualizuj bloków w witrynie portal, jak opisano w tym samouczku.

Kompletny samouczek tworzy grupy dostępności programu SQL Server na maszynach wirtualnych Azure, które zawierają następujące elementy:

* Sieć wirtualna, która ma wiele podsieci, w tym frontonu i podsieci wewnętrznej bazy danych
* Dwa kontrolery domeny, które mają domenę usługi Active Directory
* Dwie maszyny wirtualne, które Uruchom program SQL Server i są wdrażane w podsieci wewnętrznej bazy danych i przyłączone do domeny usługi Active Directory
* Klaster trybu failover z trzema węzłami z modelu kworum Większość węzłów
* Grupy dostępności, która ma dwie repliki zatwierdzania synchronicznego bazy danych dostępności

Następująca ilustracja przedstawia kompletnego rozwiązania.

![Architektura laboratorium testowego dla grup dostępności na platformie Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Wszystkie zasoby w tym rozwiązaniu należy do pojedynczej grupy zasobów.

Przed rozpoczęciem tego samouczka, wykonaj poniższe czynności:

* Masz już konto platformy Azure. Jeśli nie masz, [Załóż konto w wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Wiesz już, jak używać graficznego interfejsu użytkownika do aprowizowania maszyny wirtualnej programu SQL Server z galerii maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [obsługi maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Masz już to pełny opis grupy dostępności. Aby uzyskać więcej informacji, zobacz [zawsze włączonych grup dostępności (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Jeśli interesuje Cię przy użyciu grup dostępności z programem SharePoint, zobacz też [skonfigurować programu SQL Server 2012 zawsze włączonych grup dostępności dla programu SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
>
>

W tym samouczku Użyj portalu Azure, aby:

* Wybierz szablon Always On w portalu.
* Przejrzyj ustawienia szablon i zaktualizuj kilka ustawień konfiguracji w danym środowisku.
* Monitorowanie platformy Azure, ponieważ tworzy całego środowiska.
* Połącz się z kontrolerem domeny, a następnie na serwerze, na którym działa program SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Aprowizowanie klastra za pomocą galerii
System Azure oferuje obrazu z galerii całego rozwiązania. Aby zlokalizować szablonu:

1. Zaloguj się do witryny Azure portal przy użyciu swojego konta.
2. W witrynie Azure portal kliknij pozycję **Utwórz zasób** otworzyć **New** okienka.
3. Na **New** okienku i wyszukaj **AlwaysOn**.
   ![Znajdź szablon funkcji AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. W wynikach wyszukiwania Znajdź **klastra programu SQL Server AlwaysOn**.
   ![Szablon funkcji AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Na **wybierz model wdrożenia**, wybierz **usługi Resource Manager**.

### <a name="basics"></a>Podstawy
Kliknij przycisk **podstawy** i skonfiguruj następujące ustawienia:

* **Nazwa użytkownika administratora** jest konto użytkownika, który ma uprawnienia administratora domeny i jest członkiem programu SQL Server stałej roli serwera sysadmin w obu wystąpieniach programu SQL Server. W tym samouczku używane **administrator domeny**.
* **Hasło** jest hasłem do konta administratora domeny. Użyj złożone hasła. Potwierdź hasło.
* **Subskrypcja** jest subskrypcja tej platformie Azure są naliczane, aby uruchomić wszystkie wdrożone zasoby dla grupy dostępności. Jeśli Twoje konto ma wiele subskrypcji, możesz określić inną subskrypcję.
* **Grupa zasobów** jest nazwą grupy, do której należą wszystkie zasoby platformy Azure, które są tworzone za pomocą tego szablonu. W tym samouczku używane **SQL-HA-RG**. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Lokalizacja** to region platformy Azure, gdzie w tym samouczku zostaną utworzone zasoby. Wybierz region platformy Azure.

Poniższy zrzut ekranu przedstawia ukończone **podstawy** bloku:

![Podstawy](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kliknij przycisk **OK**.

### <a name="domain-and-network-settings"></a>Ustawienia domeny i sieci
Ten szablon galerii platformy Azure tworzy domenę i kontrolery domeny. Tworzy także sieć z dwoma podsieciami. Szablon nie może utworzyć serwery w istniejącej domenie lub w sieci wirtualnej. Następnym krokiem konfiguruje ustawienia domeny i sieci.

Na **domeny oraz ustawienia sieci** bloku, przejrzyj wstępnie zdefiniowane wartości dla domeny, a ustawienia sieciowe:

* **Nazwa domeny głównej lasu** to nazwa domeny dla domeny usługi Active Directory, który hostuje klaster. W przypadku tego samouczka użyj **contoso.com**.
* **Nazwa sieci wirtualnej** jest nazwą sieciową dla sieci wirtualnej platformy Azure. W przypadku tego samouczka użyj **autohaVNET**.
* **Nazwa podsieci kontrolera domeny** nazywa się częścią sieci wirtualnej, który hostuje kontrolera domeny. Użyj **subnet-1**. Ta podsieć używa prefiksu adresu **10.0.0.0/24**.
* **Nazwa podsieci programu SQL Server** nazywa się częścią sieci wirtualnej, który hostuje serwery, uruchom program SQL Server i udziału plików monitora. Użyj **podsieci 2**. Ta podsieć używa prefiksu adresu **10.0.1.0/26**.

Aby dowiedzieć się więcej na temat sieci wirtualnych na platformie Azure, zobacz [Omówienie usługi Virtual network](../../../virtual-network/virtual-networks-overview.md).  

**Domeny oraz ustawienia sieci** powinien wyglądać jak poniższy zrzut ekranu:

![Ustawienia domeny i sieci](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Jeśli to konieczne, możesz zmienić te wartości. W tym samouczku należy użyć wstępnie zdefiniowanych wartości.

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="availability-group-settings"></a>Ustawienia grupy dostępności
Na **ustawienia grupy dostępności**, sprawdź wstępnie zdefiniowane wartości dla grupy dostępności i odbiornika.

* **Nazwa grupy dostępności** jest nazwą klastrowanego zasobu dla grupy dostępności. W tym samouczku używane **Contoso-ag**.
* **Nazwa odbiornika grupy dostępności** jest używany przez klaster i wewnętrznego modułu równoważenia obciążenia. Klienci łączący się do programu SQL Server można używać tej nazwy, połączyć się z odpowiednią repliki bazy danych. W tym samouczku używane **Contoso-listener**.
* **Port odbiornika grupy dostępności** Określa port TCP odbiornika programu SQL Server. Na potrzeby tego samouczka użyj domyślnego portu **1433**.

Jeśli to konieczne, możesz zmienić te wartości. W tym samouczku należy użyć wstępnie zdefiniowanych wartości.  

![Ustawienia grupy dostępności](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kliknij przycisk **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Rozmiar maszyny wirtualnej, ustawienia magazynu
Na **rozmiar maszyny Wirtualnej, ustawienia magazynu**, a następnie wybierz rozmiar maszyny wirtualnej programu SQL Server i wyświetlić inne ustawienia.

* **Rozmiar maszyny wirtualnej programu SQL Server** rozmiar dla maszyn wirtualnych, zarówno z programem SQL Server. Wybierz rozmiar odpowiednią maszynę wirtualną, dla obciążenia. Jeśli tworzysz to środowisko dla tego samouczka, należy użyć **DS2**. W przypadku obciążeń produkcyjnych wybierz rozmiar maszyny wirtualnej, który może obsługiwać obciążenia. Wiele obciążeń produkcyjnych wymagają **DS4** lub większym. Szablon tworzy dwie maszyny wirtualne o takim rozmiarze i instaluje program SQL Server na każdym z nich. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure instaluje wersję Enterprise programu SQL Server. Koszt zależy od wersji i rozmiar maszyny wirtualnej. Aby uzyskać szczegółowe informacje o bieżącym kosztów, zobacz [ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Rozmiar maszyny wirtualnej kontrolera domeny** jest rozmiar maszyny wirtualnej dla kontrolerów domeny. Dla tego samouczka użyj **D2**.
* **Rozmiar maszyny wirtualnej monitora udostępniania plików** jest rozmiar maszyny wirtualnej dla monitora udziału plików. W tym samouczku używane **A1**.
* **Konto magazynu SQL** jest nazwą konta magazynu, która przechowuje dane programu SQL Server oraz dysków systemu operacyjnego. W tym samouczku używane **alwaysonsql01**.
* **Konto magazynu DC** jest nazwa konta magazynu dla kontrolerów domeny. W tym samouczku używane **alwaysondc01**.
* **Rozmiar dysku danych programu SQL Server** TB jest rozmiar dysku danych programu SQL Server w TB. Określ liczbę z zakresu od 1 do 4. W tym samouczku używane **1**.
* **Optymalizacja magazynu** Ustawia ustawienia konfiguracji określonego magazynu dla maszyn wirtualnych programu SQL Server, na podstawie typu obciążenia. Wszystkie maszyny wirtualne programu SQL Server, w tym scenariuszu za pomocą usługi premium storage ustawiony jako tylko do odczytu do pamięci podręcznej hosta dysku platformy Azure. Ponadto można optymalizować ustawień programu SQL Server dla obciążenia, wybierając jedną z tych trzech ustawień:

  * **Ogólne obciążenie** ustawia nie określonych ustawień konfiguracji.
  * **Przetwarzanie transakcyjne** zestawy śledzenia flagi 1117 i 1118.
  * **Magazynowanie danych** zestawy śledzenia flagi 1117 i 610.

W tym samouczku używane **ogólne obciążenie**.

![Ustawienia magazynu rozmiaru maszyny Wirtualnej](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

#### <a name="a-note-about-storage"></a>Uwaga dotycząca magazynu
Dodatkowe optymalizacje zależą od rozmiaru dysków danych programu SQL Server. Dla każdego terabajtów danych dysku platforma Azure dodaje dodatkowy magazyn premium 1 TB. Jeśli serwer wymaga 2 TB lub więcej, ten szablon tworzy pulę magazynów na każdej maszynie wirtualnej programu SQL Server. Puli magazynu jest formą zwirtualizować magazyn, w której wiele dysków są skonfigurowana pod kątem zapewnia wyższą wydajność, odporność i wydajność.  Szablon następnie tworzy miejsca do magazynowania w puli magazynu i przedstawia informacje o danych jednego dysku systemu operacyjnego. Szablon określa ten dysk jako dysk danych dla programu SQL Server. Szablon Dostraja puli magazynu dla programu SQL Server przy użyciu następujących ustawień:

* Rozmiar woluminu rozłożonego jest ustawieniem przeplotu dla dysku wirtualnego. Obciążeniami transakcyjnymi użyj 64 KB. Obciążeń magazynowania danych użyj 256 KB.
* Odporność to prosty (Brak odporności).

> [!NOTE]
> Usługi Azure premium storage jest lokalnie nadmiarowy i przechowuje trzy kopie danych w jednym regionie, dlatego dodatkową odporność w puli magazynów nie jest wymagane.
>
>

* Liczba kolumn jest równa liczbie dysków w puli magazynów.

Aby uzyskać dodatkowe informacje na temat miejsca do magazynowania i pule magazynu zobacz:

* [Miejsca do magazynowania — omówienie](https://technet.microsoft.com/library/hh831739.aspx)
* [Kopia zapasowa systemu Windows Server i pule magazynu](https://technet.microsoft.com/library/dn390929.aspx)

Aby uzyskać więcej informacji na temat najlepszych rozwiązań konfiguracji programu SQL Server, zobacz [najlepsze rozwiązania dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Ustawienia programu SQL Server
Na **ustawień programu SQL Server**, przejrzyj i zmodyfikuj prefiks nazwy maszyny wirtualnej programu SQL Server, SQL Server w wersji, konto usługi programu SQL Server i hasło i harmonogram konserwacji automatyczne stosowanie poprawek SQL.

* **SQL Server Nazwa prefiksu** służy do tworzenia nazwy dla każdej maszyny wirtualnej programu SQL Server. W tym samouczku używane **sqlserver**. Szablon nazwy maszyny wirtualnej programu SQL Server *sqlserver 0* i *sqlserver 1*.
* **Wersja programu SQL Server** jest wersja programu SQL Server. Dla tego samouczka użyj **programu SQL Server 2014**. Można również wybrać **programu SQL Server 2012** lub **programu SQL Server 2016**.
* **Nazwa użytkownika konta usługi programu SQL Server** to nazwa konta domeny dla usługi SQL Server. W tym samouczku używane **SQL Service**.
* **Hasło** jest hasłem do konta usługi programu SQL Server.  Użyj złożone hasła. Potwierdź hasło.
* **Automatyczne stosowanie poprawek SQL harmonogram konserwacji** określa dzień tygodnia, że Azure automatycznie poprawek programu SQL Server. W tym samouczku wpisz **niedziela**.
* **Godzina rozpoczęcia konserwacji automatyczne stosowanie poprawek SQL** jest godzinę dla regionu platformy Azure, po rozpoczęciu funkcję automatycznego instalowania poprawek.

> [!NOTE]
> Okno stosowania poprawek dla każdej maszyny wirtualnej jest rozłożona według jedną godzinę. Tylko jedna maszyna wirtualna jest poprawić w czasie, aby uniknąć przerw w działaniu usług.
>
>

![Ustawienia programu SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="summary"></a>Podsumowanie
Na stronie podsumowania Azure sprawdza poprawność ustawień. Można również pobrać szablonu. Przejrzyj podsumowanie. Kliknij przycisk **OK**.

### <a name="buy"></a>Kup
Ten blok końcowy zawiera **warunki użytkowania**, i **zasady zachowania poufności informacji**. Przejrzyj te informacje. Gdy wszystko jest gotowe na platformę Azure, aby rozpocząć tworzenie maszyn wirtualnych i wszystkich innych wymaganych zasobów dla grupy dostępności, kliknij przycisk **Utwórz**.

Azure portal tworzy grupę zasobów i wszystkie zasoby.

## <a name="monitor-deployment"></a>Monitorowanie wdrożenia
Monitoruj postęp wdrażania w witrynie Azure portal. Ikona reprezentująca wdrożenia automatycznie został przypięty do pulpitu nawigacyjnego portalu platformy Azure.

![Pulpit nawigacyjny platformy Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server
Nowe wystąpienia programu SQL Server są uruchomione na maszynach wirtualnych, którzy mają adresy IP podłączonej do Internetu. Możesz to zrobić pulpitu zdalnego (RDP) bezpośrednio do każdej maszyny wirtualnej programu SQL Server.

Dla protokołu RDP do programu SQL Server wykonaj następujące kroki:

1. Na pulpicie nawigacyjnym platformy Azure portal Sprawdź, czy wdrożenia zakończyła się pomyślnie.
2. Kliknij przycisk **zasobów**.
3. W **zasobów** bloku kliknij **sqlserver 0**, czyli nazwę komputera w jednej z maszyn wirtualnych, na których działa program SQL Server.
4. W bloku **sqlserver 0**, kliknij przycisk **Connect**. Przeglądarka pyta, czy chcesz otworzyć lub zapisać obiekt połączenia zdalnego. Kliknij przycisk **Otwórz**.
5. **Podłączanie pulpitu zdalnego** może ostrzega użytkownika, że nie można zidentyfikować wydawcy tego połączenia zdalnego. Kliknij przycisk **Połącz**.
6. Zabezpieczenia Windows wyświetli monit o wprowadzenie swoje poświadczenia, aby nawiązać połączenie z adresu IP z podstawowego kontrolera domeny. Kliknij przycisk **Użyj innego konta**. Aby uzyskać **nazwa_użytkownika**, typ **contoso\DomainAdmin**. To konto jest skonfigurowane, gdy ustawiona nazwa użytkownika administratora w szablonie. Użyj złożone hasło, które zostały wybrane podczas konfigurowania szablonu.
7. **Pulpit zdalny** może ostrzega użytkownika, że nie można uwierzytelnić komputera zdalnego z powodu problemów z jego certyfikatem zabezpieczeń. Przedstawia on nazwę certyfikatu zabezpieczeń. Jeśli wykonano samouczka jest nazwa **sqlserver 0.contoso.com**. Kliknij przycisk **Yes** (Tak).

Masz teraz połączenie przy użyciu protokołu RDP z maszyną wirtualną programu SQL Server. Możesz Otwórz program SQL Server Management Studio, nawiązać połączenie z domyślnego wystąpienia programu SQL Server i sprawdź, czy grupa dostępności jest skonfigurowane.
