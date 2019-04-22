---
title: Aprowizowania przewodnika dla maszyn wirtualnych Windows SQL Server w witrynie Azure portal | Dokumentacja firmy Microsoft
description: W tym przewodniku opisano opcje tworzenia maszyn wirtualnych systemu Windows programu SQL Server 2017 w witrynie Azure portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bb051d37f3a1dd82d7d46bfe8b22c2ba1251be85
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259205"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Jak aprowizować maszynę wirtualną Windows SQL Server w witrynie Azure portal

Ten przewodnik zawiera szczegółowe informacje o różnych opcjach dostępnych podczas tworzenia maszyny wirtualnej systemu Windows programu SQL Server w witrynie Azure portal. W tym artykule opisano kolejne opcje konfiguracji niż [Szybki Start maszyny Wirtualnej programu SQL Server](quickstart-sql-vm-create-portal.md), które wykraczają więcej za pomocą możliwą inicjowania obsługi zadań. 

Użyj tego przewodnika, aby utworzyć własną maszynę Wirtualną SQL Server. Lub użyj go jako odniesienia zamieszczono opcje dostępne w witrynie Azure portal.

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a id="select"></a> Obrazy galerii maszyn wirtualnych programu SQL Server

Podczas tworzenia maszyny wirtualnej programu SQL Server, można wybrać jedną z wielu wstępnie skonfigurowanych obrazów z galerii maszyn wirtualnych. Poniższe kroki pokazują, jak wybrać jeden z obrazów programu SQL Server 2017.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu swojego konta.

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**. Portal otworzy okno **Nowe**.

1. W oknie **Nowe** kliknij grupę **Obliczenia**, a następnie kliknij pozycję **Zobacz wszystko**.

1. W polu wyszukiwania wpisz ciąg **SQL Server 2017**, a następnie naciśnij klawisz Enter.

1. W rozwijanych filtr, wybierz _systemu Windows Server 2016_ dla **systemu operacyjnego** i wybierz _Microsoft_ jako **wydawcy**. 

     ![Nowe okno Obliczenia](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Przejrzyj dostępne obrazy programu SQL Server. Każdy obraz identyfikuje wersję programu SQL Server i system operacyjny.

1. Wybierz obraz o nazwie **Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016**.

   > [!TIP]
   > W tym instruktażu jest używana wersja Developer, ponieważ jest w pełni funkcjonalne i bezpłatne wersji programu SQL Server do tworzenia, testowania. Opłaty dotyczą tylko kosztów obsługi maszyny wirtualnej. Jest jednak wybrać dowolny z obrazów do wykorzystania w tym przewodniku. Aby uzyskać opis dostępnych obrazów, zobacz [omówienie maszyn wirtualnych z programem SQL Server Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Koszty licencjonowania programu SQL Server są włączane do cen na sekundę maszyny wirtualnej, możesz utworzyć i różni się w wersji i liczba rdzeni. SQL Server Developer edition jest jednak bezpłatne dla projektowania i testowania (nie produkcyjnych) i programu SQL Express jest bezpłatne w przypadku obsługi małych obciążeń (mniej niż 1 GB pamięci, mniej niż 10 GB miejsca do magazynowania). Możesz również bring-your-own-license (BYOL) i płacić tylko za maszynę Wirtualną. Nazwy tych obrazów mają prefiks {BYOL}. 
   >
   > Aby uzyskać więcej informacji na temat tych opcji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. W obszarze **Wybierz model wdrożenia** sprawdź, czy pozycja **Resource Manager** została zaznaczona. Wdrażanie przy użyciu usługi Resource Manager jest zalecanym modelem wdrożenia dla nowych maszyn wirtualnych. 

1. Wybierz pozycję **Utwórz**.


## <a id="configure"></a> Opcje konfiguracji

Istnieje wiele kart do konfigurowania maszyny wirtualnej programu SQL Server. Na potrzeby tego przewodnika skupimy się od następujących czynników: 

| Krok | Opis |
| --- | --- |
| **Podstawy** |[Konfigurowanie ustawień podstawowych](#1-configure-basic-settings) |
| **Funkcje opcjonalne** |[Konfigurowanie funkcji opcjonalnych](#2-configure-optional-features) |
| **Ustawienia programu SQL Server** |[Konfigurowanie ustawień programu SQL Server](#3-configure-sql-server-settings) |
| **Przeglądanie + tworzenie** | [Przeglądanie podsumowania](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Konfigurowanie ustawień podstawowych


Na **podstawy** kartę, należy podać następujące informacje:

* W obszarze **Project Details**, upewnij się, że wybrano poprawną subskrypcję. 
*  W **grupy zasobów** sekcji, wybierz opcję istniejący zasób z listy lub wybierz **Utwórz nową** do tworzenia nowej grupy zasobów. Grupa zasobów to kolekcja powiązanych zasobów platformy Azure (maszyny wirtualne, konta magazynu, sieci wirtualne itp.). 

    ![Subskrypcja](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Nowa grupa zasobów jest przydatna, jeśli tylko testujesz lub poznajesz wdrożenia programu SQL Server na platformie Azure. Po zakończeniu testu usuń grupę zasobów, aby automatycznie usunąć maszynę wirtualną i wszystkie skojarzone z nią zasoby. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* W obszarze **wystąpienia szczegóły**:
    1. Wprowadź unikatową **nazwę maszyny wirtualnej**.  
    1. Wybierz lokalizację dla Twojego **Region**. 
    1. Na potrzeby tego przewodnika, pozostaw **opcji dostępności** równa _nie nadmiarowości infrastruktury wymagane_. Aby dowiedzieć się więcej informacji na temat opcji dostępności, zobacz [regiony platformy Azure i dostępność](../../windows/regions-and-availability.md). 
    1. W **obraz** listy wybierz _Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016_.  
    1. Możliwość **Zmień rozmiar** dla **rozmiar** maszyny wirtualnej, a następnie wybierz pozycję **podstawowa A2** oferty. Pamiętaj wyczyścić zasoby po zakończeniu korzystania z nich, aby uniknąć wszelkich nieoczekiwanych opłat. W przypadku obciążeń produkcyjnych zobacz zalecane rozmiary i konfiguracje maszyn w artykule [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

    ![Szczegóły wystąpienia](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Szacowany koszt miesięczny wyświetlany w oknie **Wybieranie rozmiaru** nie uwzględnia kosztów licencjonowania programu SQL Server. Te dane szacunkowe to koszt samej maszyny wirtualnej. W przypadku wersji Express i Developer programu SQL Server to Szacowanie jest szacowany łączny koszt. W przypadku innych wersji zobacz [cennik maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i wybierz docelową wersję programu SQL Server. Zobacz też [ceny wskazówki dotyczące maszyn wirtualnych programu SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md) i [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* W obszarze **konta administratora**, podaj nazwę użytkownika i hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* W obszarze **reguły portów wejściowych**, wybierz **Zezwalaj na wybranych portach** , a następnie wybierz **protokołu RDP (3389)** z listy rozwijanej. 

   ![Reguły portów wejściowych](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurowanie funkcji opcjonalnych

### <a name="disks"></a>Dyski

Na **dysków** skonfiguruj opcje dysku. 

* W obszarze **typ dysku systemu operacyjnego**, wybierz typ dysku dla Twojego systemu operacyjnego z listy rozwijanej. Premium jest zalecane dla systemów produkcyjnych, ale nie jest dostępna dla podstawowej maszyny Wirtualnej. Aby korzystać z dysków SSD w warstwie Premium, zmienić rozmiar maszyny wirtualnej. 
* W obszarze **zaawansowane**, wybierz opcję **tak** wykorzystanie **Managed Disks**.

   > [!NOTE]
   > Firma Microsoft zaleca funkcję Dyski zarządzane dla programu SQL Server. Funkcja Dyski zarządzane obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z funkcją Dyski zarządzane znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. Aby uzyskać więcej informacji, zobacz temat [Azure Omówienie usługi Managed Disks] [... / zarządzane — dyski overview.md). Aby uzyskać szczegółowe informacje na temat dysków zarządzanych w zestawie dostępności, zobacz [używaj dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności] (.. /manage-Availability.md.

![Ustawienia dysku maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Networking

Na **sieć** skonfiguruj opcje sieciowe w sieci. 

* Utwórz nową **sieci wirtualnej**, lub użyj istniejącej sieci wirtualnej dla maszyny Wirtualnej programu SQL Server. Wyznacz **podsieci** także. 

* W obszarze **karty Sieciowej grupy zabezpieczeń**, wybierz grupę zabezpieczeń podstawowych lub grupy zabezpieczeń zaawansowanych. Wybranie opcji podstawowa umożliwia wybranie portów przychodzących dla maszyny Wirtualnej programu SQL Server (te same wartości, które zostały skonfigurowane na **podstawowe** karty). Wybranie opcji zaawansowanych umożliwia wybierz istniejącą grupę zabezpieczeń sieci, lub utworzyć nowy. 

* Możesz wprowadzić inne zmiany ustawienia sieci, lub Zachowaj wartości domyślne.

![Ustawienia sieciowe maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorowanie

Na **monitorowanie** skonfiguruj monitorowanie i automatyczne zamykanie. 

* Usługa Azure umożliwia **monitorowania rozruchu** domyślnie za pomocą tego samego konta magazynu wyznaczonego dla maszyny Wirtualnej. Możesz zmienić te ustawienia tutaj, a także włączanie **systemu operacyjnego gościa diagnostyki**. 
* Aby umożliwić **zarządzanych tożsamości przypisanej przez System** i **automatycznego zamykania** na tej karcie, a także. 

![Ustawienia zarządzania maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurowanie ustawień programu SQL Server

Na **ustawień programu SQL Server** skonfiguruj określone ustawienia i optymalizacje dla programu SQL Server. Następujące ustawienia, które można skonfigurować dla programu SQL Server:



| Ustawienie |
| --- |
| [Łączność](#connectivity) |
| [Uwierzytelnianie](#authentication) |
| [Integracja z usługą Azure Key Vault](#azure-key-vault-integration) |
| [Konfiguracja usługi Storage](#storage-configuration) |
| [Automatyczne stosowanie poprawek](#automated-patching) |
| [Automatyczne kopie zapasowe](#automated-backup) |
| [Usługi języka R (zaawansowane analizy)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Łączność

W obszarze **Łączność z serwerem SQL** określ żądany typ dostępu do wystąpienia programu SQL Server na tej maszynie wirtualnej. Na potrzeby tego przewodnika wybierz **publiczne (internet)** połączeń programu SQL Server z maszyn wirtualnych lub usług w Internecie. Ta opcja jest zaznaczona platforma Azure automatycznie skonfiguruje zaporę i sieciową grupę zabezpieczeń, aby zezwolić na ruch na porcie wybrane.

> [!TIP]
> Domyślnie program SQL Server nasłuchuje na dobrze znanym porcie **1433**. Aby zwiększyć bezpieczeństwo, zmień port w poprzednim oknie dialogowym do nasłuchiwania na porcie innym niż domyślny, takim jak 1401. Jeśli zmienisz numer portu, należy połączyć przy użyciu tego portu z dowolnych narzędzi klienta, takich jak program SSMS.

![Zabezpieczenia maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Aby ustanowić połączenie z programem SQL Server za pośrednictwem Internetu, musisz również włączyć funkcję Uwierzytelnianie programu SQL Server opisaną w następnej sekcji.

Jeśli nie chcesz zezwolić na połączenia z aparatem bazy danych za pośrednictwem Internetu, wybierz jedną z następujących opcji:

* **Lokalne (tylko wewnątrz maszyny wirtualnej)**, aby zezwolić na połączenia z programem SQL Server tylko z maszyny wirtualnej.
* **Prywatne (wewnątrz usługi Virtual Network)**, aby zezwolić na połączenia z programem SQL Server tylko z maszyn wirtualnych lub usług w tej samej sieci wirtualnej.

Ogólnie rzecz biorąc, można ulepszyć zabezpieczenia, wybierając łączność z najbardziej restrykcyjnymi ograniczeniami akceptowanymi w danym scenariuszu. Jednak wszystkie opcje można zabezpieczyć przy użyciu reguł sieciowej grupy zabezpieczeń i uwierzytelniania SQL/Windows. Możesz edytować sieciową grupę zabezpieczeń po utworzeniu maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Authentication

Jeśli wymagasz uwierzytelniania programu SQL Server, kliknij pozycję **Włącz** w obszarze **Uwierzytelnianie SQL**.

![Uwierzytelnianie programu SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Jeśli planujesz dostęp do programu SQL Server za pośrednictwem Internetu (opcja łączności publicznej), należy włączyć uwierzytelnianie SQL, w tym miejscu. Publiczny dostęp do programu SQL Server wymaga użycia uwierzytelniania SQL.

Jeśli włączasz opcję uwierzytelniania programu SQL Server, podaj informacje w polach **Nazwa logowania** i **Hasło**. Ta nazwa logowania jest skonfigurowany jako identyfikator logowania uwierzytelniania programu SQL Server i członkiem **sysadmin** stałej roli serwera. Aby uzyskać więcej informacji na temat trybów uwierzytelniana, zobacz artykuł [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Wybieranie trybu uwierzytelniania).

Jeśli nie włączysz opcji uwierzytelniania programu SQL Server, możesz użyć konta administratora lokalnego na maszynie wirtualnej do ustanawiania połączeń z wystąpieniem programu SQL Server.

![Uwierzytelnianie programu SQL Server](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

### <a name="azure-key-vault-integration"></a>Integracja magazynu kluczy Azure

Aby przechowywać klucze tajne zabezpieczeń do szyfrowania na platformie Azure, kliknij pozycję **Integracja usługi Azure Key Vault**, a następnie kliknij pozycję **Włącz**.

![Integracja magazynu kluczy Azure](media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Poniższa tabela zawiera listę parametrów wymaganych do skonfigurowania integracji magazynu kluczy Azure.

| PARAMETR | OPIS | PRZYKŁAD |
| --- | --- | --- |
| **Adres URL magazynu kluczy** |Lokalizacja magazynu kluczy. |https:\//contosokeyvault.vault.azure.net/ |
| **Nazwa główna** |Nazwa główna usługi Azure Active Directory. Ta nazwa jest również nazywana identyfikatorem klienta. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Główny klucz tajny** |Główny klucz tajny usługi Azure Active Directory. Ten klucz tajny jest również nazywany kluczem tajnym klienta. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nazwa poświadczenia** |**Nazwa poświadczenia**: Integracja utworzenie poświadczenia w programie SQL Server, dzięki czemu maszyny Wirtualnej, aby mieć dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |moje_poświadczenie_1 |

Aby uzyskać więcej informacji, zobacz [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-ps-sql-keyvault.md) (Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych Azure).

### <a name="storage-configuration"></a>Konfiguracja usługi Storage

W obszarze **konfiguracji magazynu**, wybierz opcję **zmiana konfiguracji** pozwala określić wymagania dotyczące magazynu.


> [!NOTE]
> Jeśli maszynę wirtualną ręcznie skonfigurowano do korzystania z magazynu w warstwie Standardowa, ta opcja jest niedostępna. Automatyczna optymalizacja magazynu jest dostępna tylko dla usługi Premium Storage.

> [!TIP]
> Liczba zatrzymań i górne limity każdego suwaka zależą od wybranego rozmiaru maszyny wirtualnej. Większa i bardziej wydajna maszyna wirtualna umożliwia większe skalowanie w górę.

Możesz określić wymagania dotyczące na przykład liczby operacji wejścia/wyjścia na sekundę (IOPs), przepustowości w MB/s i całkowitego rozmiaru magazynu. Skonfiguruj te wartości przy użyciu suwaków. Możesz zmienić te ustawienia magazynu zależnie od obciążenia. Portal automatycznie oblicza liczbę dysków do dołączenia i skonfigurowania na podstawie tych wymagań.

W obszarze **Usługa Storage zoptymalizowana dla** wybierz jedną z następujących opcji:

* **Ogólne** to ustawienie domyślne obsługujące większość obciążeń.
* **Przetwarzanie transakcyjne** optymalizuje magazyn dla tradycyjnych obciążeń baz danych OLTP.
* **Magazynowanie danych** optymalizuje magazyn dla tradycyjnych obciążeń związanych z analizą i raportami.

![Konfiguracja magazynu maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>Licencja na program SQL Server
Jeśli jesteś klientem programu Software Assurance, możesz użyć [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) model dostarczania własnej licencji programu SQL Server i zapisać na zasobach. 

![Licencja maszyny Wirtualnej SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatyczne stosowanie poprawek

Opcja **Automatyczne stosowanie poprawek** jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Określ dzień tygodnia, godzinę i czas trwania okna obsługi. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi korzysta z ustawień regionalnych godziny maszyny wirtualnej. Jeśli nie chcesz, aby platforma Azure automatycznie stosowała poprawki programu SQL Server i systemu operacyjnego, kliknij pozycję **Wyłącz**.  

![Maszyna wirtualna SQL automatyczne stosowanie poprawek](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Aby uzyskać więcej informacji, zobacz [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Automatyczne stosowanie poprawek programu SQL Server w usłudze Azure Virtual Machines).

### <a name="automated-backup"></a>Automatyczne kopie zapasowe

Automatyczną obsługę kopii zapasowych baz danych możesz włączyć dla wszystkich baz danych w obszarze **Automatyczne tworzenie kopii zapasowych**. Opcja Automatyczne kopie zapasowe jest domyślnie wyłączona.

Po włączeniu automatycznej obsługi kopii zapasowych SQL możesz skonfigurować poniższe ustawienia:

* Okres przechowywania (dni) kopii zapasowych
* Konto usługi Storage używane dla kopii zapasowych
* Opcja szyfrowania i hasło dla kopii zapasowych
* Bazy danych systemu tworzenia kopii zapasowych
* Konfigurowanie harmonogramu tworzenia kopii zapasowych

Aby szyfrować kopie zapasowe, kliknij pozycję **Włącz**. Następnie określ ustawienie **Hasło**. Platforma Azure tworzy certyfikat do szyfrowania kopii zapasowych i używa określonego hasła do ochrony tego certyfikatu.

Aby uzyskać więcej informacji, zobacz [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Automatyczne tworzenie kopii zapasowych dla programu SQL Server w usłudze Azure Virtual Machines).


### <a name="r-services-advanced-analytics"></a>Usługi języka R (zaawansowane analizy)

Istnieje możliwość włączenia [SQL Server R Services (Advanced Analytics)](/sql/advanced-analytics/r/sql-server-r-services/). Ta opcja umożliwia korzystanie z zaawansowanych analiz za pomocą programu SQL Server 2017. Kliknij pozycję **Włącz** w oknie **Ustawienia programu SQL Server**.


## <a name="4-review--create"></a>4. Przeglądanie + tworzenie

Na **Przejrzyj + Utwórz** kartę, Przejrzyj podsumowanie, a następnie wybierz **Utwórz** do utworzenia programu SQL Server, grupę zasobów i zasoby określone dla tej maszyny Wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia.

> [!NOTE]
> Aby podać przykład czasu wymaganego do przeprowadzenia wdrożenia, wdrożono maszynę wirtualną SQL dla regionu Wschodnie stany USA z ustawieniami domyślnymi. Przeprowadzenie tego wdrożenia testowego trwało około 12 minut. Wdrażanie może jednak trwać krócej lub dłużej zależnie od regionu i wybranych ustawień.

## <a id="remotedesktop"></a> Otwieranie maszyny wirtualnej przy użyciu Pulpitu zdalnego

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a id="connect"></a> Zdalne ustanawianie połączenia z programem SQL Server

W tym przewodniku wybrano **publicznych** dostępu dla maszyny wirtualnej i **uwierzytelniania programu SQL Server**. Te ustawienia powodują automatyczne skonfigurowanie maszyny wirtualnej do zezwalania na połączenia z programem SQL Server z dowolnego klienta za pośrednictwem Internetu (zakładając, że ma on poprawny identyfikator logowania SQL).

> [!NOTE]
> Jeśli podczas aprowizacji nie wybrano opcji Publiczne, możesz zmienić ustawienia łączności SQL za pośrednictwem portalu po aprowizacji. Aby uzyskać więcej informacji, zobacz [Zmiana ustawień łączności SQL](virtual-machines-windows-sql-connect.md#change).

Poniższe sekcje pokazują, jak nawiązać połączenie za pośrednictwem Internetu z wystąpieniem maszyny Wirtualnej programu SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > W tym przykładzie użyto typowy port 1433. Jednak ta wartość będzie musiał można modyfikować, jeśli inny port (takim jak 1401) został określony podczas wdrażania maszyny Wirtualnej programu SQL Server. 


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać inne informacje na temat użycia programu SQL Server na platformie Azure, zobacz [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (Program SQL Server w usłudze Azure Virtual Machines) i [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Często zadawane pytania).