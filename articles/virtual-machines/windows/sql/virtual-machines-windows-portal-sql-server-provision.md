---
title: Aprowizuj maszynę wirtualną za pomocą witryny Azure portal
description: W tym przewodniku opisano opcje tworzenia maszyn wirtualnych programu Windows SQL Server 2017 w portalu azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1fdf776570b6f10a363fb98dfe343387d86219d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249791"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Jak aprowizować maszynę wirtualną systemu Windows SQL Server w portalu azure

Ten przewodnik zawiera szczegółowe informacje na temat różnych opcji dostępnych podczas tworzenia maszyny wirtualnej programu Windows SQL Server w witrynie Azure portal. W tym artykule opisano więcej opcji konfiguracji niż [szybki start maszyny Wirtualnej programu SQL Server](quickstart-sql-vm-create-portal.md), który przechodzi bardziej przez jedno możliwe zadanie inicjowania obsługi administracyjnej. 

Ten przewodnik służy do tworzenia własnej maszyny Wirtualnej programu SQL Server. Lub użyj go jako odwołanie do dostępnych opcji w witrynie Azure portal.

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>Obrazy galerii maszyn wirtualnych programu SQL Server

Podczas tworzenia maszyny wirtualnej programu SQL Server można wybrać jeden z kilku wstępnie skonfigurowanych obrazów z galerii maszyn wirtualnych. W poniższych krokach pokazano, jak wybrać jeden z obrazów programu SQL Server 2017.

1. Wybierz **sql platformy Azure** w menu po lewej stronie witryny Azure portal. Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz **+ Dodaj,** aby otworzyć stronę **opcji Wybierz wdrożenie SQL.** Dodatkowe informacje można wyświetlić, wybierając pozycję **Pokaż szczegóły**. 
1. Wpisz `2017` pole wyszukiwania obrazów programu SQL Server na kafelku **maszyn wirtualnych SQL,** a następnie wybierz z listy rozwijanej pozycję **Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016.** 


   ![Wybieranie obrazu maszyny wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > Wersja dewelopera jest używana w tym instruktażu, ponieważ jest to w pełni funkcjonalna, bezpłatna wersja programu SQL Server do testowania programów. Opłaty dotyczą tylko kosztów obsługi maszyny wirtualnej. Możesz jednak wybrać dowolny z obrazów do użycia w tym instruktażu. Aby uzyskać opis dostępnych obrazów, zobacz [omówienie programu SQL Server Windows Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Koszty licencjonowania programu SQL Server są uwzględniane w cenach na sekundę utworzonej maszyny wirtualnej i różnią się w zależności od wersji i rdzeni. Jednak SQL Server Developer edition jest bezpłatny do tworzenia/testowania (nie produkcji), a SQL Express jest wolny dla lekkich obciążeń (mniej niż 1 GB pamięci, mniej niż 10 GB pamięci). Można również przynieść własną licencję (BYOL) i płacić tylko za maszynę wirtualną. Nazwy tych obrazów mają prefiks {BYOL}. 
   >
   > Aby uzyskać więcej informacji na temat tych opcji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).


1. Wybierz **pozycję Utwórz**.


## <a name="1-configure-basic-settings"></a>1. Konfigurowanie podstawowych ustawień


Na karcie **Podstawy podaj** następujące informacje:

* W obszarze **Szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję. 
*  W sekcji **Grupa zasobów** wybierz istniejącą grupę zasobów z listy lub wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę zasobów. Grupa zasobów to kolekcja powiązanych zasobów platformy Azure (maszyny wirtualne, konta magazynu, sieci wirtualne itp.). 

    ![Subskrypcja](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Nowa grupa zasobów jest przydatna, jeśli tylko testujesz lub poznajesz wdrożenia programu SQL Server na platformie Azure. Po zakończeniu testu usuń grupę zasobów, aby automatycznie usunąć maszynę wirtualną i wszystkie skojarzone z nią zasoby. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


* W obszarze **Szczegóły wystąpienia:**
    1. Wprowadź unikatową **nazwę maszyny wirtualnej**.  
    1. Wybierz lokalizację dla swojego **regionu**. 
    1. Na potrzeby tego przewodnika pozostaw **opcje dostępności** ustawione na _Brak nadmiarowości infrastruktury._ Aby uzyskać więcej informacji o opcjach dostępności, zobacz [Dostępność](../../windows/availability.md). 
    1. Na liście **Obraz** wybierz _pozycję Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016_.  
    1. Wybierz opcję **Zmień rozmiar** **rozmiaru** maszyny wirtualnej i wybierz ofertę **A2 Basic.** Pamiętaj, aby wyczyścić swoje zasoby po zakończeniu z nimi, aby zapobiec nieoczekiwanym opłatom. W przypadku obciążeń produkcyjnych zobacz zalecane rozmiary i konfiguracje maszyn w artykule [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

    ![Szczegóły wystąpienia](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Szacowany koszt miesięczny wyświetlany w oknie **Wybieranie rozmiaru** nie uwzględnia kosztów licencjonowania programu SQL Server. To oszacowanie jest koszt maszyny Wirtualnej sam. W przypadku wersji ekspresowych i deweloperskich programu SQL Server oszacowanie to jest całkowitym szacowanym kosztem. W przypadku innych wersji zobacz [cennik maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i wybierz docelową wersję programu SQL Server. Zobacz też [wskazówki dotyczące cen maszyn wirtualnych](virtual-machines-windows-sql-server-pricing-guidance.md) i rozmiarów platformy Azure [dla maszyn wirtualnych.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* W obszarze **Konto administratora**podaj nazwę użytkownika i hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* W obszarze **Reguły portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty,** a następnie wybierz z listy rozwijanej pozycję **RDP (3389).** 

   ![Reguły portów wejściowych](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurowanie funkcji opcjonalnych

### <a name="disks"></a>Dyski

Na karcie **Dyski** skonfiguruj opcje dysku. 

* W obszarze **Typ dysku systemu operacyjnego**wybierz z listy rozwijanej typ dysku, który ma być przeznaczony dla systemu operacyjnego. Premium jest zalecany dla systemów produkcyjnych, ale nie jest dostępny dla podstawowej maszyny Wirtualnej. Aby korzystać z dysków SSD w wersji Premium, zmień rozmiar maszyny wirtualnej. 
* W obszarze **Zaawansowane**wybierz pozycję **Tak** w obszarze Używane **dyski zarządzane**.

   > [!NOTE]
   > Firma Microsoft zaleca funkcję Dyski zarządzane dla programu SQL Server. Funkcja Dyski zarządzane obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z funkcją Dyski zarządzane znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. Aby uzyskać więcej informacji, zobacz [Omówienie dysków zarządzanych platformy Azure](../managed-disks-overview.md). Aby uzyskać szczegóły dotyczące dysków zarządzanych w zestawie dostępności, zobacz sekcję [Use managed disks for VMs in availability set](../manage-availability.md) (Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności).

![Ustawienia dysku maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Obsługa sieci

Na karcie **Sieć** skonfiguruj opcje sieci. 

* Utwórz nową **sieć wirtualną**lub użyj istniejącej sieci wirtualnej dla maszyny Wirtualnej programu SQL Server. Wyznacz **Subnet** również podsieć. 

* W obszarze **Grupa zabezpieczeń sieciowej karty sieciowej**wybierz podstawową grupę zabezpieczeń lub zaawansowaną grupę zabezpieczeń. Wybranie opcji podstawowej umożliwia wybranie portów przychodzących dla maszyny Wirtualnej programu SQL Server (tych samych wartości, które zostały skonfigurowane na karcie **Podstawowe).** Wybranie opcji zaawansowanej umożliwia wybranie istniejącej sieciowej grupy zabezpieczeń lub utworzenie nowej. 

* Można wprowadzić inne zmiany w ustawieniach sieci lub zachować wartości domyślne.

![Ustawienia sieci maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorowanie

Na karcie **Monitorowanie** skonfiguruj monitorowanie i autoszuzułt. 

* Platforma Azure domyślnie włącza **diagnostykę rozruchu** przy tym samym koncie magazynu wyznaczonym dla maszyny Wirtualnej. Możesz zmienić te ustawienia tutaj, a także włączyć **diagnostykę gościa systemu operacyjnego**. 
* Na tej karcie można również włączyć **przypisaną przez system tożsamość zarządzana** i **autostygowanie.** 

![Ustawienia zarządzania maszyną wirtualną SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurowanie ustawień programu SQL Server

Na karcie **Ustawienia programu SQL Server** skonfiguruj określone ustawienia i optymalizacje dla programu SQL Server. Ustawienia, które można skonfigurować dla programu SQL Server, są następujące:

| Ustawienie |
| --- |
| [Łączność](#connectivity) |
| [Uwierzytelnianie](#authentication) |
| [Integracja z usługą Azure Key Vault](#azure-key-vault-integration) |
| [Konfiguracja usługi Storage](#storage-configuration) |
| [Automatyczne stosowanie poprawek](#automated-patching) |
| [Automatyczna kopia zapasowa](#automated-backup) |
| [Usługi uczenia maszynowego](#machine-learning-services) |


### <a name="connectivity"></a>Łączność

W obszarze **Łączność z serwerem SQL** określ żądany typ dostępu do wystąpienia programu SQL Server na tej maszynie wirtualnej. Na potrzeby tego przewodnika wybierz pozycję **Publiczne (internet),** aby zezwolić na połączenia z programem SQL Server z maszyn lub usług w Internecie. Po wybraniu tej opcji platforma Azure automatycznie konfiguruje zaporę i sieciową grupę zabezpieczeń, aby zezwolić na ruch na wybranym porcie.

> [!TIP]
> Domyślnie program SQL Server nasłuchuje na dobrze znanym porcie **1433**. Aby zwiększyć bezpieczeństwo, zmień port w poprzednim oknie dialogowym do nasłuchiwania na porcie innym niż domyślny, takim jak 1401. W przypadku zmiany portu należy połączyć się przy użyciu tego portu z dowolnych narzędzi klienckich, takich jak SSMS.

![SQL VM Security](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Aby ustanowić połączenie z programem SQL Server za pośrednictwem Internetu, musisz również włączyć funkcję Uwierzytelnianie programu SQL Server opisaną w następnej sekcji.

Jeśli nie chcesz zezwolić na połączenia z aparatem bazy danych za pośrednictwem Internetu, wybierz jedną z następujących opcji:

* **Lokalne (tylko wewnątrz maszyny wirtualnej)**, aby zezwolić na połączenia z programem SQL Server tylko z maszyny wirtualnej.
* **Prywatne (wewnątrz usługi Virtual Network)**, aby zezwolić na połączenia z programem SQL Server tylko z maszyn wirtualnych lub usług w tej samej sieci wirtualnej.

Ogólnie rzecz biorąc, można ulepszyć zabezpieczenia, wybierając łączność z najbardziej restrykcyjnymi ograniczeniami akceptowanymi w danym scenariuszu. Jednak wszystkie opcje można zabezpieczyć przy użyciu reguł sieciowej grupy zabezpieczeń i uwierzytelniania SQL/Windows. Możesz edytować sieciową grupę zabezpieczeń po utworzeniu maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Uwierzytelnianie

Jeśli wymagane jest uwierzytelnianie programu SQL Server, kliknij pozycję **Włącz** w obszarze **uwierzytelniania SQL** na karcie **Ustawienia programu SQL Server.**

![Uwierzytelnianie programu SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Jeśli planujesz uzyskać dostęp do programu SQL Server przez Internet (opcja Łączność publiczna), w tym miejscu należy włączyć uwierzytelnianie SQL. Publiczny dostęp do programu SQL Server wymaga użycia uwierzytelniania SQL.

Jeśli włączasz opcję uwierzytelniania programu SQL Server, podaj informacje w polach **Nazwa logowania** i **Hasło**. Ta nazwa logowania jest skonfigurowana jako logowanie uwierzytelniania programu SQL Server i członek roli serwera stałego **sysadmin.** Aby uzyskać więcej informacji na temat trybów uwierzytelniana, zobacz artykuł [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Wybieranie trybu uwierzytelniania).

Jeśli nie włączysz opcji uwierzytelniania programu SQL Server, możesz użyć konta administratora lokalnego na maszynie wirtualnej do ustanawiania połączeń z wystąpieniem programu SQL Server.


### <a name="azure-key-vault-integration"></a>Integracja magazynu kluczy Azure

Aby przechowywać wpisy tajne zabezpieczeń na platformie Azure w celu szyfrowania, wybierz **ustawienia programu SQL Server**i przewiń w dół do **integracji magazynu kluczy platformy Azure.** Wybierz **włącz** i wypełnij żądane informacje. 

![Integracja magazynu kluczy Azure](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

Poniższa tabela zawiera listę parametrów wymaganych do skonfigurowania integracji magazynu kluczy Azure.

| PARAMETR | OPIS | PRZYKŁAD |
| --- | --- | --- |
| **Adres URL magazynu kluczy** |Lokalizacja magazynu kluczy. |https:\//contosokeyvault.vault.azure.net/ |
| **Nazwa główna** |Nazwa główna usługi Azure Active Directory. Ta nazwa jest również nazywana identyfikatorem klienta. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Główny klucz tajny** |Główny klucz tajny usługi Azure Active Directory. Ten klucz tajny jest również nazywany kluczem tajnym klienta. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nazwa poświadczenia** |**Nazwa poświadczenia**: integracja usługi Azure Key Vault powoduje utworzenie poświadczenia w programie SQL Server, co umożliwia maszynie wirtualnej dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |moje_poświadczenie_1 |

Aby uzyskać więcej informacji, zobacz [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-ps-sql-keyvault.md) (Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych Azure).

### <a name="storage-configuration"></a>Konfiguracja usługi Storage

Na karcie **Ustawienia programu SQL Server** w obszarze **Konfiguracja magazynu**wybierz pozycję Zmień **konfigurację,** aby otworzyć stronę Konfiguracja magazynu zoptymalizowana pod kątem wydajności i określić wymagania dotyczące magazynu.

![Konfiguracja magazynu maszyn wirtualnych SQL](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

W obszarze **Usługa Storage zoptymalizowana dla** wybierz jedną z następujących opcji:

* **Ogólne** to ustawienie domyślne obsługujące większość obciążeń.
* **Przetwarzanie transakcyjne** optymalizuje magazyn dla tradycyjnych obciążeń OLTP bazy danych.
* **Magazynowanie danych** optymalizuje magazyn dla tradycyjnych obciążeń związanych z analizą i raportami.

![Konfiguracja magazynu maszyn wirtualnych SQL](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Można pozostawić wartości domyślnie lub ręcznie zmienić topologię magazynu, aby dostosować je do potrzeb we/wy. Aby uzyskać więcej informacji, zobacz [konfiguracja magazynu](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>Licencja programu SQL Server
Jeśli jesteś klientem pakietu Software Assurance, możesz skorzystać z [korzyści hybrydowej platformy Azure,](https://azure.microsoft.com/pricing/hybrid-benefit/) aby uzyskać własną licencję programu SQL Server i zaoszczędzić na zasobach. 

![Licencja maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatyczne stosowanie poprawek

Opcja **Automatyczne stosowanie poprawek** jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Określ dzień tygodnia, godzinę i czas trwania okna obsługi. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi korzysta z ustawień regionalnych godziny maszyny wirtualnej. Jeśli nie chcesz, aby platforma Azure automatycznie stosowała poprawki programu SQL Server i systemu operacyjnego, kliknij pozycję **Wyłącz**.  

![Automatyczne łatanie maszyn wirtualnych SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Aby uzyskać więcej informacji, zobacz [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Automatyczne stosowanie poprawek programu SQL Server w usłudze Azure Virtual Machines).

### <a name="automated-backup"></a>Automatyczne kopie zapasowe

Automatyczną obsługę kopii zapasowych baz danych możesz włączyć dla wszystkich baz danych w obszarze **Automatyczne tworzenie kopii zapasowych**. Opcja Automatyczne kopie zapasowe jest domyślnie wyłączona.

Po włączeniu automatycznej obsługi kopii zapasowych SQL możesz skonfigurować poniższe ustawienia:

* Okres przechowywania (dni) kopii zapasowych
* Konto usługi Storage używane dla kopii zapasowych
* Opcja szyfrowania i hasło dla kopii zapasowych
* Bazy danych systemu tworzenia kopii zapasowych
* Konfigurowanie harmonogramu tworzenia kopii zapasowych

Aby szyfrować kopie zapasowe, kliknij pozycję **Włącz**. Następnie określ ustawienie **Hasło**. Platforma Azure tworzy certyfikat do szyfrowania kopii zapasowych i używa określonego hasła do ochrony tego certyfikatu. Domyślnie harmonogram jest ustawiany automatycznie, ale można utworzyć harmonogram ręczny, wybierając **opcję Ręczny**. 

![Automatyczne kopie zapasowe maszyn wirtualnych SQL](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Aby uzyskać więcej informacji, zobacz [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Automatyczne tworzenie kopii zapasowych dla programu SQL Server w usłudze Azure Virtual Machines).


### <a name="machine-learning-services"></a>Machine Learning Services

Masz możliwość włączenia usług [uczenia maszynowego](/sql/advanced-analytics/). Ta opcja umożliwia korzystanie z uczenia maszynowego z Python i R w programie SQL Server 2017. Wybierz **pozycję Włącz** w oknie Ustawienia serwera **SQL.**


## <a name="4-review--create"></a>4. Recenzja + tworzenie

Na karcie **Recenzja + tworzenie** przejrzyj podsumowanie i wybierz pozycję **Utwórz,** aby utworzyć program SQL Server, grupę zasobów i zasoby określone dla tej maszyny Wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia.

> [!NOTE]
> Aby podać przykład czasu wymaganego do przeprowadzenia wdrożenia, wdrożono maszynę wirtualną SQL dla regionu Wschodnie stany USA z ustawieniami domyślnymi. Przeprowadzenie tego wdrożenia testowego trwało około 12 minut. Wdrażanie może jednak trwać krócej lub dłużej zależnie od regionu i wybranych ustawień.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a>Otwieranie maszyny Wirtualnej za pomocą pulpitu zdalnego

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a>Zdalne łączenie się z programem SQL Server

W tym instruktażu wybrano dostęp **publiczny** dla maszyny wirtualnej i **uwierzytelnianie programu SQL Server**. Te ustawienia powodują automatyczne skonfigurowanie maszyny wirtualnej do zezwalania na połączenia z programem SQL Server z dowolnego klienta za pośrednictwem Internetu (zakładając, że ma on poprawny identyfikator logowania SQL).

> [!NOTE]
> Jeśli podczas aprowizacji nie wybrano opcji Publiczne, możesz zmienić ustawienia łączności SQL za pośrednictwem portalu po aprowizacji. Aby uzyskać więcej informacji, zobacz [Zmiana ustawień łączności SQL](virtual-machines-windows-sql-connect.md#change).

W poniższych sekcjach pokazano, jak połączyć się przez Internet z wystąpieniem maszyny Wirtualnej programu SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > W tym przykładzie użyto wspólnego portu 1433. Jednak ta wartość będzie musiała zostać zmodyfikowana, jeśli podczas wdrażania maszyny Wirtualnej programu SQL Server określono inny port (na przykład 1401). 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać inne informacje na temat użycia programu SQL Server na platformie Azure, zobacz [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (Program SQL Server w usłudze Azure Virtual Machines) i [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Często zadawane pytania).
