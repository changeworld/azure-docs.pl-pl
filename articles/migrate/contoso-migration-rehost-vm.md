---
title: Rehost aplikacji Contoso za pomocą migracji na maszynach wirtualnych platformy Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rehost aplikacji lokalnych i migracji przyrostu shift Azure do migracji maszyn lokalnych przy użyciu usługi Azure Site Recovery usługi.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 13b36398afdf8eb4db3adeee4ebb821411d813f5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300790"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migracja Contoso: Rehost aplikację lokalnie na maszynach wirtualnych platformy Azure


W tym artykule przedstawiono sposób Contoso rehost SmartHotel aplikacji na platformie Azure, przeprowadź migrację aplikacji maszyn wirtualnych do maszyn wirtualnych platformy Azure.


Ten dokument jest jedną z szeregu artykuły, które pokazują, jak fikcyjnej firmy Contoso migruje ich zasobów lokalnych do chmury Microsoft Azure. Seria zawiera informacje i scenariusze, które przedstawiają Konfigurowanie infrastruktury migracji, oceny zasobów lokalnych do migracji i z różnymi typami migracji. Scenariusze zwiększa się złożoność i dodamy dodatkowe artykuły wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje, których będziemy używać. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Oceny zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak Contoso uruchamia ocenę ich lokalnych dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Ich oceny maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyny wirtualne platformy Azure i zarządzanych wystąpienie serwera SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikacji frontonu maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [migracja bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview) usługi migrację do zarządzanego wystąpienie serwera SQL. | Dostępne
Artykuł 5: Rehost na maszynach wirtualnych platformy Azure (w tym artykule) | Pokazuje, jak Contoso migracji aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu usługi Site Recovery.
[Artykuł 6: Rehost maszyny wirtualne platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Site Recovery ich użyć do migracji aplikacji maszyny wirtualne i usługi migracja bazy danych do migracji aplikacji bazy danych do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Rehost aplikacji systemu Linux na maszynach wirtualnych Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Rehost aplikacji Linux maszyny wirtualne platformy Azure i serwerem MySQL Azure](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery i przeprowadzanie migracji aplikacji bazy danych do wystąpienia serwera usługi MySQL Azure przy użyciu MySQL Workbench. | Dostępne

W tym artykule Contoso będą migrowane dwuwarstwowa systemu Windows. Aplikacja NET SmartHotel uruchomione na maszynach wirtualnych VMware, na platformie Azure. Jeśli chcesz korzystać z tej aplikacji jest podana jako open source i można go pobrać z [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Czynniki biznesowe

Kadry kierowniczej IT ściśle działał z partnerami biznesowymi zrozumienie mają osiągnięcie przy tej migracji:

- **Adres rozwoju firmy**: rośnie Contoso i w związku z tym istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Ogranicz ryzyko**: SmartHotel aplikacji jest szczególnie ważne w firmie Contoso. Chcą przenieść ją do platformy Azure z zero ryzyka.
- **Rozszerzanie**: Contoso nie chce zmodyfikować aplikację. Chcą po prostu upewnij się, że jest stabilna.


## <a name="migration-goals"></a>Celów migracji

Zespół chmury Contoso ma przypięty dół celów migracji. Te cele są używane do określania najlepszej metody migracji:

- Po migracji aplikacji na platformie Azure powinien mieć te same możliwości wydajności, jak obecnie w środowisku programu VMware.  Aplikacja będzie nadal jako krytyczne w chmurze, ponieważ jest on lokalnymi. 
- Contoso nie mają inwestycji w tej aplikacji.  Ważne jest, aby firmy, ale w jego bieżącym formularza po prostu chcą bezpiecznie przenieść go do chmury.
- Contoso nie chce zmienić model ops dla tej aplikacji. Chcą korzystać z niego w chmurze w taki sam sposób jak teraz.
- Contoso nie chce zmienić dowolne funkcje aplikacji. Tylko lokalizacji aplikacji zostanie zmieniona.

## <a name="proposed-architecture"></a>Architektura proponowanych

Oto bieżącego środowiska

- Aplikacja jest warstwowa w dwóch maszyn wirtualnych (**WEBVM** i **SQLVM**).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Zarządza w środowisku programu VMware vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalnego datacenter (contoso-centrum danych), z lokalnym kontrolerem domeny (**contosodc1**).
- Będzie można zlikwidować lokalnymi maszynami wirtualnymi w centrum danych firmy Contoso, po ukończeniu migracji.

![Architektura scenariusza](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>proces migracji

Contoso przeprowadzona migracja aplikacji serwera sieci Web i bazy danych maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery:

- Pierwszym krokiem one będzie przygotować i konfigurowanie składników usługi Azure Site Recovery i przygotować lokalnej infrastruktury VMware.
- Mają ich [infrastruktury platformy Azure](contoso-migration-infrastructure.md) w miejscu, więc po prostu muszą dodać kilka składników Azure specjalnie z myślą o usługi Site Recovery.
- Z całą zawartością przygotowany można uruchomić, replikowanie maszyn wirtualnych.
— Po włączeniu replikacji i pracy, migrowania maszyny Wirtualnej przez awaryjne na platformie Azure.

![proces migracji](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwania po awarii dla maszyn wirtualnych platformy Azure i lokalnymi maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure Azure Storage jest obciążany.  Maszyny wirtualne platformy Azure są tworzone i spowodować naliczenie opłat, podczas pracy awaryjnej. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) dotyczące opłat i cenach.


## <a name="prerequisites"></a>Wymagania wstępne

Oto, co użytkownik (i firmy Contoso) muszą działać w tym scenariuszu.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Należy utworzono już subskrypcji podczas wcześniejszego artykuły w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, należy do pracy z administratora przypisanie uprawnienia właściciela lub współautora.<br/><br/> Aby uzyskać bardziej szczegółowe uprawnienia, przejrzyj [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktury platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.<br/><br/> Dowiedz się więcej na temat konkretnego [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Serwery lokalne** | Lokalna Usługa vCenter Server musi korzystać z wersji 5.5, 6.0 lub 6.5<br/><br/> Hostach ESXi 5.5, 6.0 lub 6.5 powinno być ono uruchomione<br/><br/> Maszyny wirtualne VMware powinna być uruchomiona na hoście ESXi.
**Lokalne maszyny wirtualne** | Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroki w scenariuszu

Oto, jak Contoso będzie uruchamiane migracji:

> [!div class="checklist"]
> * **Krok 1: Przygotowanie usługi Azure Site Recovery**: tworzenia konta magazynu Azure do przechowywania replikowanych danych i magazynu usług odzyskiwania.
> * **Krok 2: Przygotowanie VMware lokalnymi Site Recovery**: przygotowanie kont dla maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi Azure po pracy awaryjnej.
> * **Krok 3: Replikacja maszyn wirtualnych**: Konfigurowanie replikacji i Rozpocznij replikację maszyn wirtualnych do magazynu Azure.
> * **Krok 4: Migrowanie maszyn wirtualnych z usługą Site Recovery**: one testować tryb failover, aby się upewnić, że wszystko działa, a następnie uruchom pełne trybu failover do migracji maszyn wirtualnych na platformie Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Przygotowanie Azure do usługi Site Recovery

Poniżej przedstawiono składniki Azure Contoso wymaga migracji maszyn wirtualnych na platformie Azure:

- Sieci wirtualnej, w którym maszyny wirtualne Azure zostaną umieszczone podczas są tworzone podczas pracy awaryjnej.
- Konto magazynu Azure do przechowywania replikowanych danych. 
- W magazynie usług odzyskiwania Azure.

One skonfigurowane je w następujący sposób:

1. Contoso już konfigurowania sieci można użyć do odzyskiwania lokacji podczas ich [wdrożyć infrastrukturę platformy Azure](contoso-migration-infrastructure.md)

    - Aplikacja SmartHotel jest aplikacji produkcyjnej, a maszyny wirtualne zostaną zmigrowane do środowiska produkcyjnego Azure (VNET-produkcyjną EUS2) w regionie podstawowym wschodnie stany USA 2.
    - Obie maszyny wirtualne zostaną umieszczone w grupie zasobów ContosoRG, który służy do zasobów w środowisku produkcyjnym.
    - Fronton aplikacji maszyny Wirtualnej (WEBVM) będą migrowane do podsieci frontonu (produkcyjną-FE-EUS2), w sieci produkcyjnej.
    - Bazy danych aplikacji maszyny Wirtualnej (SQLVM) będą migrowane do podsieci (produkcyjną-DB-EUS2), bazy danych w sieci produkcyjnej.

2. Contoso tworzy konto magazynu platformy Azure (contosovmsacc20180528) w regionie podstawowym.
    - Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
    - Korzystają z konta ogólnego przeznaczenia, z magazynu standardowego i replikacji LRS. 

    ![Magazyn odzyskiwania lokacji](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Z sieci i magazynu konta w miejscu Contoso jest teraz tworzy magazyn usług odzyskiwania (ContosoMigrationVault) i umieszcza je w grupie zasobów ContosoFailoverRG w regionie podstawowym wschodnie stany USA 2.

    ![Magazyn usługi Recovery Services](./media/contoso-migration-rehost-vm/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Konfigurowanie usługi Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Przygotowanie VMware lokalnymi Site Recovery

Oto co Contoso przygotowuje lokalnie:

- Konto vCenter server lub vSphere ESXi hosta, aby zautomatyzować odnajdywania maszyny Wirtualnej.
- Konta, które umożliwia automatyczne instalacji usługi mobilności na maszynach wirtualnych VMware. 
- Lokalnymi ustawienia maszyny Wirtualnej, dzięki czemu Contoso mogą łączyć się replikowanych maszyn wirtualnych platformy Azure po pracy awaryjnej.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. 
- Organizowania replikacji, trybu failover i powrotu po awarii dla maszyn wirtualnych.
- Wymagane jest co najmniej konto tylko do odczytu. Konto powinno być możliwe uruchamianie operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso Ustawia konto:

1. Tworzenia roli na poziomie vCenter.
2. Ta rola one przypisz wymagane uprawnienia.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Musi być zainstalowana usługa mobilności na każdej maszynie Wirtualnej.

- Site Recovery może zrobić instalacji automatycznego wypychanej usługi mobilności, gdy jest włączona replikacja maszyny Wirtualnej.
- Konto jest wymagane, umożliwiając dostęp do maszyn wirtualnych dla instalacji wypychanej usługi Site Recovery. To konto można określić po skonfigurowaniu replikacji.
- Konto może być domeny lub lokalnych z uprawnieniami do zainstalowania na maszynach wirtualnych.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover firma Contoso chce nawiązać połączenia z maszynami wirtualnymi Azure. Aby to zrobić, ich wykonaj następujące czynności przed migracją:

1. Aby uzyskać dostęp za pośrednictwem Internetu one:

 - Włącz protokół RDP na lokalnej maszynie Wirtualnej przed trybu failover
 - Upewnij się, że reguły TCP i UDP są dodawane do **publicznego** profilu.
 - Sprawdź, czy RDP jest dozwolony w **zapory systemu Windows** > **dozwolone aplikacje** we wszystkich profilach.
 
2. Aby uzyskać dostęp za pośrednictwem sieci VPN typu lokacja lokacja ich:

 - Włącz protokół RDP na maszynie lokalnej.
 - Zezwalaj na RDP w **zapory systemu Windows** -> **dozwolone aplikacje i funkcje**, dla **domeny i prywatnej** sieci.
 - Ustaw zasady sieci SAN systemu operacyjnego na lokalnej maszynie Wirtualnej, aby **OnlineAll**.

Ponadto po uruchomieniu trybu failover muszą sprawdź następujące kwestie:

- Powinien istnieć nie oczekujące aktualizacje systemu Windows na maszynie Wirtualnej, gdy wyzwolenie pracy awaryjnej. Jeśli, nie będzie mógł zalogować się do maszyny Wirtualnej do momentu ukończenia aktualizacji.
- Po przejściu w tryb failover, można sprawdzić **diagnostyki rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli to nie pomoże, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzeć te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli automatycznego wykrywania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikacja maszyn wirtualnych lokalnie

Contoso przed uruchomieniem migracji na platformie Azure, musi skonfigurować i włączyć replikację.

### <a name="set-a-replication-goal"></a>Ustaw celem replikacji

1. W magazynie, w obszarze Nazwa magazynu (ContosoVMVault) wybierają celem replikacji (**wprowadzenie** > **usługi Site Recovery** > **przygotowanie infrastruktury** .
2. Określają, czy ich maszyn znajdujących się lokalnie, systemem VMware i replikację do platformy Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Upewnij się, planowanie wdrożenia

Aby kontynuować, ich upewnij się, że ukończył Planowanie wdrożenia, wybierając **tak, już to zostało zrobione**. W tym scenariuszu Contoso tylko migrowania dwóch maszyn wirtualnych i nie jest konieczne Planowanie wdrożenia.


### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Contoso musi skonfigurować ich środowiska źródłowego. Aby to zrobić, ich Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji usługi Site Recovery jako o wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu serwera konfiguracji one Zarejestruj go w magazynie.

Serwer konfiguracji jest uruchamiana liczby składników:

- Składnik serwera konfiguracji, która koordynuje komunikacji między lokalną i platformą Azure i zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.



Contoso wykonaj następujące kroki w następujący sposób:

1. W magazynie, będą oni mogli pobrać szablonu OVF **przygotowanie infrastruktury** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. One zaimportuj szablon do programu VMware do tworzenia i wdrażania maszyny Wirtualnej.

    ![Szablon OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  Podczas włączania maszyny Wirtualnej po raz pierwszy, on uruchamiany w procesie instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, ich Zaloguj się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzia konfiguracji odzyskiwania lokacji Azure domyślnie uruchamia.
5. W narzędziu Określa nazwę, aby zarejestrować serwer konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia zalogowaniu do subskrypcji platformy Azure. Poświadczenia musi mieć dostęp do magazynu, w którym będzie Zarejestruj serwer konfiguracji.

    [Zarejestruj serwer konfiguracji](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. One ponownie zalogować się w komputerze i Kreatora konfiguracji serwera zarządzania jest uruchamiana automatycznie.
9. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić tego ustawienia, po przeprowadzeniu konfiguracji.
10. Wybierają subskrypcji, grupy zasobów i magazynu, w którym można zarejestrować serwer konfiguracji.
        ![Magazyn](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Ich pobrać i zainstalować serwer MySQL i VMWare PowerCLI. 
11. Po sprawdzeniu poprawności określają adres FQDN lub adres IP hosta serwera lub vSphere vCenter. Pozostaw domyślny port i określ przyjazną nazwę dla serwera w systemie Azure.
12. Określa konto, utworzony automatycznego wykrywania i poświadczenia, które są używane do automatycznego zainstalowania usługi mobilności. W przypadku komputerów z systemem Windows konto musi uprawnienia administratora lokalnego na maszynach wirtualnych.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Po zakończeniu rejestracji w portalu Azure Contoso podwójne sprawdza, czy serwer konfiguracji i serwera VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może zająć 15 minut lub dłużej. 
8. Odzyskiwanie lokacji łączy się przy użyciu określonych ustawień serwerów VMware i umożliwia odnajdywanie maszyn wirtualnych.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Contoso określa ustawień replikacji obiektu docelowego.

1. W **przygotowanie infrastruktury** > **docelowego**, wybierają ustawienia obiektu docelowego.
2. Usługa Site Recovery sprawdza, czy istnieje konto magazynu Azure i sieci w miejscu docelowym określonym.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Teraz Contoso można utworzyć zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawienia replikacji** > **zasad replikacji** >  **tworzenie i Skojarz**, tworzenia zasad **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg RPO**: domyślny 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Przechowywania punktu odzyskiwania**. Domyślna wartość 24 godzin. Ta wartość określa, jak długo trwa okna przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, na którym są tworzone migawki spójne z aplikacjami.

        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Włącz replikację WEBVM

Z wszystko w miejscu Contoso można włączyć replikację dla maszyn wirtualnych. Zaczynają WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, czy chcą włączyć maszyn wirtualnych, wybierz serwer vCenter i serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Wybierają ustawień obiektu docelowego, w tym grupy zasobów i sieć platformy Azure i konto magazynu.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Wybiera contoso **WebVM** replikacji, sprawdza zasady replikacji i pozwala usłudze replikacji.

    - Na tym etapie Contoso tylko wybiera WEBVM, ponieważ należy wybrać sieć wirtualną i podsieć, a Contoso jest umieszczenie aplikacji maszyn wirtualnych w różnych podsieciach.
    - Usługa Site Recovery automatycznie instaluje usługi mobilności na maszynie Wirtualnej po włączeniu replikacji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. One śledzić postęp replikacji w **zadania**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
6. W **Essentials** w portalu Azure Contoso można wyświetlić strukturę dla maszyn wirtualnych replikowanych do platformy Azure.


### <a name="enable-replication-for-sqlvm"></a>Włącz replikację SQLVM

Contoso można teraz rozpocząć replikację maszyny SQLVM, za pomocą tego samego procesu jak powyżej.

1. Wybierają ustawienia źródła.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Następnie określają ustawienia obiektu docelowego.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Wybierają SQLVM replikacji. 

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Zastosowanie tej samej zasady replikacji, którego użyto do WEBVM i włączyć replikację.

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm/essentials.png)

**Potrzebujesz dodatkowej pomocy?**

- Pełny przewodnik wszystkie te kroki można znaleźć [ustawienia odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrożenie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Użytkownik może dowiedzieć się więcej o [włączenia replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrowanie maszyn wirtualnych 

Contoso uruchomić szybki test trybu failover, a następnie pełna trybu failover, aby przeprowadzić migrację maszyn wirtualnych.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Test trybu failover pomaga zapewnić, że wszystko działa zgodnie z oczekiwaniami. 

1. Contoso uruchamia test trybu failover do najnowszego dostępnego punktu w czasie (**najnowsze przetworzone**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu próbuje zamknąć źródłowej maszyny Wirtualnej, aby mogło nastąpić wyzwolenie pracy awaryjnej usługi Site Recovery. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Tryb failover uruchomień testów: 

    - Uruchamia się upewnić, że wszystkie warunki wymagane do migracji w miejscu Sprawdzanie wymagań wstępnych.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.
    
3. Po zakończeniu pracy awaryjnej, replika maszyny Wirtualnej Azure pojawia się w portalu Azure. Contoso sprawdza, czy maszyna wirtualna jest odpowiedni rozmiar, połączony z siecią prawo i jest uruchomiona. 
4. Po zweryfikowaniu testowania trybu failover, one wyczyścić trybu failover i zarejestrować i zapisać wszelkie obserwacje. 

### <a name="create-and-customize-a-recovery-plan"></a>Tworzenie i Dostosowywanie planu odzyskiwania

 Po zweryfikowaniu, że test trybu failover działał zgodnie z oczekiwaniami, Contoso tworzy plan odzyskiwania do migracji. 

- Plan odzyskiwania określa kolejność pracą w trybie failover występuje i wskazuje, jak maszynach wirtualnych platformy Azure zostanie przeniesiony online na platformie Azure.
- Ponieważ aplikacja jest dwuwarstwowa, ich dostosowywania planu odzyskiwania, tak aby dane maszyny Wirtualnej (SQLVM) rozpoczyna się przed frontonu (WEBVM).

1. W **plany odzyskiwania (lokacji odzyskiwania)** > **+ planu odzyskiwania**, tworzenie planu i Dodaj do niej maszyny wirtualne.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Po utworzeniu planu, ich dostosowywania (**plany odzyskiwania** > **SmartHotelMigrationPlan** > **Dostosuj**).
2.  Będą oni mogli usunąć WEBVM z **Grupa 1: Start**.  Dzięki temu pierwszą akcją start tylko dotyczy SQLVM.
3.  W **+ grupy** > **Dodaj chronione elementy**, co zwiększa WEBVM grupa 2: Start.  Maszyny wirtualne muszą znajdować się w dwóch różnych grup.


### <a name="migrate-the-vms"></a>Migrowanie maszyn wirtualnych


Contoso można teraz uruchomić pełne pracy awaryjnej, aby ukończyć migrację.

1. Wybierają planu odzyskiwania > **pracy awaryjnej**.
2. Wybierają do trybu failover do najnowszego punktu odzyskiwania, i czy odzyskiwania lokacji należy dążyć do zamykania maszyny Wirtualnej lokalnego, aby mogło nastąpić wyzwolenie pracy awaryjnej. Postęp trybu failover może wykonać **zadania** strony.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover1.png)


3. Po pracy awaryjnej ich Sprawdź, czy maszyny Wirtualnej Azure pojawia się zgodnie z oczekiwaniami w portalu Azure.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover2.png)  

3. Po zakończeniu weryfikacji ich Zakończenie migracji dla każdej maszyny Wirtualnej. Zatrzymuje replikacji dla maszyny Wirtualnej i zatrzymuje usługi Site Recovery rozliczeń dla niego.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover3.png)

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) uruchomiony test trybu failover. 
- [Dowiedz się](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenie w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Czyszczenie po migracji

Po zakończeniu migracji SmartHotel warstwy aplikacji są obecnie uruchomione na maszynach wirtualnych Azure.

Contoso musi teraz wykonać te kroki oczyszczania:  

- Usuń maszynę WEBVM ze spisu vCenter.
- Usuń maszynę SQLVM ze spisu vCenter.
- Usuń WEBVM i SQLVM z lokalnym zadania tworzenia kopii zapasowej.
- Aktualizuj wewnętrzne dokumentacji w celu wyświetlenia nowej lokalizacji i adresów IP dla maszyn wirtualnych.
- Przejrzyj wszystkie zasoby, które współdziałają z maszyn wirtualnych, a następnie zaktualizować wszystkie odpowiednie ustawienia lub dokumentacji odzwierciedlą nowej konfiguracji.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Gdy aplikacja jest teraz uruchomiona Contoso musi teraz do w pełni operacjonalizacji i zabezpieczyć go na platformie Azure.

### <a name="security"></a>Bezpieczeństwo

Zespół ds Contoso przegląda maszynach wirtualnych platformy Azure, aby określić wszystkie problemy z zabezpieczeniami.

- Aby kontrolować dostęp, przeglądu grup zabezpieczeń sieci (NSG) dla maszyn wirtualnych. Grupy NSG są używane do zapewnienia, że tylko ruch do aplikacji można uzyskać do niej dostęp.
- Uznają również Zabezpieczanie danych na dysku przy użyciu szyfrowania dysków Azure i KeyVault.

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych

Firma Contoso ma kopię zapasową danych na maszynach wirtualnych przy użyciu usługi Kopia zapasowa Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja kosztów i licencjonowania

1. Contoso ma istniejącej licencji dla ich maszyn wirtualnych i będzie korzystać z asysty hybrydowe platformy Azure.  Przekonwertuje one na istniejących maszynach wirtualnych Azure, aby móc korzystać z tej cennik.
2. Contoso spowoduje włączenie zarządzania koszt Azure licencjonowane przez Cloudyn, oddziału firmy Microsoft. To rozwiązanie do zarządzania koszt usługi chmury, które pozwala korzystać z oraz zarządzania platformy Azure i innych zasobów w chmurze. [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o Azure kosztów zarządzania. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehosted aplikacji SmartHotel na platformie Azure przy użyciu funkcji migracji aplikacji maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. 


## <a name="next-steps"></a>Kolejne kroki

W artykule w serii firma Microsoft będzie pokazują, jak Contoso rehost frontonu aplikacji SmartHotel maszyny Wirtualnej na platformie Azure przy użyciu usługi Site Recovery i migrację bazy danych do grupy dostępności AlwaysOn programu SQL Azure.

