---
title: 'Usługa Azure Backup: Przywracanie maszyn wirtualnych przy użyciu witryny Azure portal'
description: Przywracanie maszyn wirtualnych z punktu odzyskiwania przy użyciu witryny Azure portal
services: backup
author: geethalakshmig
manager: vijayts
keywords: Przywracanie kopii zapasowych. jak przywrócić; punkt odzyskiwania;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: eb47b1d8d3f2859b2b5c0b79633b2d37e5a40756
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380169"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Przywracanie maszyn wirtualnych za pomocą witryny Azure portal
Chroń swoje dane, tworząc migawki danych w określonych odstępach czasu. Te migawki są nazywane punktami odzyskiwania i są one przechowywane w magazynach usługi Recovery Services. Jeśli jest to konieczne naprawić lub ponownie utworzyć maszynę wirtualną (VM), można przywrócić maszynę Wirtualną z dowolnego z punktów odzyskiwania zapisanych. Przywracanie punktu odzyskiwania, możesz wykonywać następujące czynności:

* Utwórz nową maszynę Wirtualną, która jest w momencie reprezentacja kopii zapasowej maszyny Wirtualnej.
* Przywróć dyski i użyć szablonu, który jest dostarczany z procesu dostosowywania przywróconej maszyny Wirtualnej lub wykonać odzyskiwanie pojedynczych plików. 

W tym artykule wyjaśniono, jak przywrócić maszynę Wirtualną do nowej maszyny Wirtualnej lub przywracanie wszystkich dysków kopii zapasowej. Aby odzyskiwanie poszczególnych plików, zobacz [odzyskiwanie plików z kopii zapasowej maszyny Wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md).

![Trzy sposoby, aby przywrócić z kopii zapasowych maszyn wirtualnych](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Platforma Azure oferuje dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł zawiera informacje i procedury służące do przywrócenia maszyn wirtualnych wdrożonych przy użyciu modelu usługi Resource Manager.
>
>

Przywracanie maszyny Wirtualnej lub wszystkich dysków z maszyny Wirtualnej z kopii zapasowej obejmuje dwa kroki:

* Wybierz punkt przywracania do przywracania.
* Wybierz typ przywracania, Utwórz nową maszynę Wirtualną lub Przywróć dyski i określ wymagane parametry. 

## <a name="select-a-restore-point-for-restore"></a>Wybierz punkt przywracania do przywracania
1. Zaloguj się w [Portalu Azure](http://portal.azure.com/).

1. W menu platformy Azure wybierz **Przeglądaj**. Na liście usług wpisz **usługi Recovery Services**. Na liście usług dostosowuje się do jakiej został wpisany. Po wyświetleniu **Magazyny usługi Recovery Services**, wybierz ją.

    ![Magazyn usługi Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zostanie wyświetlona lista magazynów w subskrypcji.

    ![Magazyny listę usług Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
1. Z listy wybierz magazyn skojarzony z maszyną Wirtualną, którą chcesz przywrócić. Po wybraniu magazynu, zostanie otwarty jego pulpit nawigacyjny.

    ![Wybrany magazyn usługi Recovery Services](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
1. Na pulpicie nawigacyjnym magazynu na **elementy kopii zapasowej** kafelka, wybierz opcję **maszyn wirtualnych platformy Azure**.

    ![Pulpitu nawigacyjnego magazynu](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **Elementy kopii zapasowej** bloku otwiera i wyświetla listę maszyn wirtualnych platformy Azure.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
1. Z listy wybierz maszynę Wirtualną w celu otwarcia pulpitu nawigacyjnego. Pulpit nawigacyjny maszyny Wirtualnej, który zostanie otwarty do obszaru monitorowania, który zawiera **punkty przywracania** kafelka.

    ![Punkty przywracania](./media/backup-azure-arm-restore-vms/vm-blade.png)
1. W menu pulpitu nawigacyjnego maszyny Wirtualnej wybierz **przywrócić**.

    ![Przycisk przywracania](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    **Przywrócić** zostanie otwarty blok.

    ![Przywróć blok](./media/backup-azure-arm-restore-vms/restore-blade.png)
1. Na **przywrócić** bloku wybierz **punkt przywracania**. **Wybierz punkt przywracania** zostanie otwarty blok.

    ![Wybierz punkt przywracania](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Domyślnie okno dialogowe wyświetla wszystkie punkty przywracania z ostatnich 30 dni. Użyj **filtru** zmienić zakres czasu punktów przywracania wyświetlane. Domyślnie są wyświetlane wszystkie wyborami punktów przywracania. Modyfikowanie **wszystkie punkty przywracania** filtru, aby wybrać spójność punktu przywracania określone. Aby uzyskać więcej informacji na temat każdego rodzaju punkt przywracania, zobacz [wyjaśnienie pojęcia spójności danych](backup-azure-vms-introduction.md#data-consistency).

    Opcje spójność punktu przywracania:

     * Punkty przywracania na poziomie awarii
     * Punkty przywracania na poziomie aplikacji
     * Punkty przywracania na poziomie systemu plików
     * Wszystkie punkty przywracania

1. Wybierz punkt przywracania, a następnie wybierz pozycję **OK**.

    ![Opcje punktu przywracania](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **Przywrócić** blok, że ustawiono punkt przywracania.

1. Jeśli jeszcze nie masz, przejdź do **przywrócić** bloku. Upewnij się, że [po wybraniu punktu przywracania](#select-a-restore-point-for-restore)i wybierz **przywracania konfiguracji**. **Przywracania konfiguracji** zostanie otwarty blok.

## <a name="choose-a-vm-restore-configuration"></a>Wybierz konfigurację przywracania maszyny Wirtualnej
Po wybraniu punktu przywracania, należy wybrać konfigurację przywracania maszyny Wirtualnej. Aby skonfigurować przywróconej maszyny Wirtualnej, można użyć witryny Azure portal lub programu PowerShell.

1. Jeśli jeszcze nie masz, przejdź do **przywrócić** bloku. Upewnij się, że [po wybraniu punktu przywracania](#select-a-restore-point-for-restore)i wybierz **przywracania konfiguracji**. **Przywracania konfiguracji** zostanie otwarty blok.

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
1. Na **przywracania konfiguracji** bloku dostępne są dwie opcje:

   * **Tworzenie maszyny wirtualnej**

   * **Przywróć dyski**

Portal zawiera **szybkie tworzenie** opcję dla przywróconej maszyny Wirtualnej. Aby dostosować konfigurację maszyny Wirtualnej lub nazwy zasobów utworzonych jako część tworzenia nowych wybór maszyny Wirtualnej, przy użyciu programu PowerShell lub portalu do przywrócenia kopii zapasowej dysków. Aby dołączyć je do dowolnie konfiguracji maszyny Wirtualnej, należy użyć poleceń programu PowerShell. Lub możesz użyć szablonu, który jest dostarczany z przywróconych dysków, aby dostosować przywróconej maszyny Wirtualnej. Aby uzyskać informacje o tym, jak przywrócić maszynę Wirtualną, która ma wiele kart sieciowych lub w ramach modułu równoważenia obciążenia, zobacz [przywrócić maszynę Wirtualną ze specjalnymi konfiguracjami sieci](#restore-vms-with-special-network-configurations). Jeśli maszyna wirtualna Windows używa [Centrum licencjonowania](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Przywróć dyski i szablon programu PowerShell/określonych w tym artykule do utworzenia maszyny Wirtualnej. Upewnij się, że podajesz **typu licencji** jako "Windows_Server" podczas tworzenia maszyny Wirtualnej, aby móc korzystać z zalet Centrum do przywróconej maszyny wirtualnej. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Utwórz nową maszynę Wirtualną z punktu przywracania
1. Jeśli nie masz już istnieje, [wybierz punkt przywracania](#restore-a vm-with-special-network-configurations) przed rozpoczęciem tworzenia nowej maszyny Wirtualnej z punktu przywracania. Po wybraniu punktu przywracania **przywracania konfiguracji** bloku, wprowadź lub wybierz wartości dla każdego z następujących pól:

    a. **Typ przywracania**. Tworzy maszynę wirtualną.

    b. **Nazwa maszyny wirtualnej**. Podaj nazwę dla maszyny Wirtualnej. Nazwa musi być unikatowy dla grupy zasobów (na Maszynie wirtualnej wdrożone usługi Azure Resource Manager) lub usługa w chmurze (klasyczna maszyna wirtualna). Nie można zastąpić maszyny Wirtualnej, jeśli już istnieje w subskrypcji.

    c. **Grupa zasobów**. Użyj istniejącej grupy zasobów lub Utwórz nową. W przypadku przywracania klasycznej maszyny Wirtualnej, użyj tego pola, aby określić nazwę nowej usługi w chmurze. Jeśli tworzysz nowy zasób grupy lub usługi w chmurze, nazwa musi być globalnie unikatowa. Zazwyczaj nazwa usługi w chmurze jest skojarzone z publicznego adresu URL: na przykład [elementu cloudservice]. cloudapp.net. Jeśli spróbujesz użyć nazwy usługi/w chmurze grupy zasobów w chmurze już w użyciu, platforma Azure przypisuje zasób grupy lub usługi w chmurze taką samą nazwę jak maszyna wirtualna. Azure Wyświetla zasobów grupy/cloud services i maszyny wirtualne nie są skojarzone z żadnej grupy koligacji. Aby uzyskać więcej informacji, zobacz [jak przeprowadzić migrację z grupy koligacji do regionalnej sieci wirtualnej](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Sieć wirtualna**. Wybierz sieć wirtualną, podczas tworzenia maszyny Wirtualnej. Pole zawiera wszystkie sieci wirtualne, skojarzony z subskrypcją. Grupa zasobów maszyny Wirtualnej jest wyświetlany w nawiasach.

    e. **Subnet**. Jeśli sieć wirtualna ma podsieci, pierwszej podsieci jest domyślnie zaznaczone. Jeśli istnieją dodatkowe podsieci, wybierz podsieć, którą chcesz.

    f. **Konto magazynu**. To menu zawiera listę kont magazynu w tej samej lokalizacji co magazyn usługi Recovery Services. Konta magazynu, które są strefowo nadmiarowe nie są obsługiwane. Jeśli nie ma żadnych kont magazynu, w tej samej lokalizacji co magazyn usługi Recovery Services, należy utworzyć jedną przed rozpoczęciem operacji przywracania. Typ replikacji konta magazynu jest wyświetlany w nawiasach.

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * W przypadku przywracania maszyn wirtualnych wdrożonych przez usługę Resource Manager, należy zidentyfikować sieć wirtualną. Sieć wirtualna jest opcjonalny w przypadku klasycznej maszyny Wirtualnej.

    > * W przypadku przywracania maszyn wirtualnych z dyskami zarządzanymi, upewnij się, że wybrane konto magazynu nie jest włączona dla usługi Azure Storage Service Encryption w okresie swojego istnienia.

    > * Oparte na typ magazynu wybrane konto magazynu (premium lub standardowa), wszystkie dyski przywrócić będzie dyski w warstwie standardowa lub premium. Obecnie nie obsługujemy tryb mieszany dysków podczas przywracania.
    >
    >

1. Na **przywracania konfiguracji** bloku wybierz **OK** aby zakończyć konfigurowanie przywracania. Na **przywrócić** bloku wybierz **przywrócić** wyzwalanie operacji przywracania.

## <a name="restore-backed-up-disks"></a>Przywracanie kopii zapasowych dysków
Dostosowywanie maszyny Wirtualnej, której chcesz utworzyć z kopii zapasowej dysków różni się od co to jest obecny w **przywracania konfiguracji** bloku wybierz **Przywróć dyski** jako wartość pozycji **przywrócić typ**. Ten wybór pyta, czy dla konta magazynu, w którym mają być kopiowane dyski z kopii zapasowych. Po wybraniu konta magazynu, wybierz konto, który współużytkuje tej samej lokalizacji co magazyn usługi Recovery Services. Konta magazynu, które są strefowo nadmiarowe nie są obsługiwane. Jeśli nie ma żadnych kont magazynu, w tej samej lokalizacji co magazyn usługi Recovery Services, należy utworzyć jedną przed rozpoczęciem operacji przywracania. Typ replikacji konta magazynu jest wyświetlany w nawiasach.

Po zakończeniu operacji przywracania, można:

* [Szablon służy do dostosowywania przywróconą maszyną Wirtualną.](#use-templates-to-customize-restore-vm)
* [Użyj przywróconych dysków do dołączenia do istniejącej maszyny Wirtualnej](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Utwórz nową maszynę Wirtualną przy użyciu programu PowerShell z przywróconych dysków](./backup-azure-vms-automation.md#restore-an-azure-vm)

Na **przywracania konfiguracji** bloku wybierz **OK** aby zakończyć konfigurowanie przywracania. Na **przywrócić** bloku wybierz **przywrócić** wyzwalanie operacji przywracania.

![Konfiguracja odzyskiwania została ukończona](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania
Po użytkownik zainicjuje operację przywracania, usługa backup tworzy zadanie śledzenia operacji przywracania. Usługa kopii zapasowej tworzy również i tymczasowe wyświetlanie powiadomień w **powiadomienia** obszaru portalu. Jeśli nie zostanie wyświetlone powiadomienie, wybierz opcję **powiadomienia** symbolu, aby wyświetlić powiadomienia.

![Przywracanie wyzwolone](./media/backup-azure-arm-restore-vms/restore-notification.png)

Aby wyświetlić operacji podczas przetwarzania lub go wyświetlić, po jego zakończeniu, otwórz **zadania tworzenia kopii zapasowej** listy.

1. W menu platformy Azure, wybierz **Przeglądaj**, a następnie na liście usług wpisz **usługi Recovery Services**. Na liście usług dostosowuje się do jakiej został wpisany. Po wyświetleniu **Magazyny usługi Recovery Services**, wybierz ją.

    ![Otwórz magazyn usługi Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zostanie wyświetlona lista magazynów w subskrypcji.

    ![Magazyny listę usług Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
1. Z listy wybierz magazyn skojarzony z maszyną Wirtualną, możesz przywrócić. Po wybraniu magazynu, zostanie otwarty jego pulpit nawigacyjny.

1. Na pulpicie nawigacyjnym magazynu na **zadania tworzenia kopii zapasowej** kafelka, wybierz opcję **maszyn wirtualnych platformy Azure** Aby wyświetlić zadania skojarzonego z magazynem.

    ![Pulpitu nawigacyjnego magazynu](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **Zadania tworzenia kopii zapasowej** bloku otwiera i wyświetla listę zadań.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Szablony umożliwiają dostosowywanie przywróconą maszyną Wirtualną.
Po [zakończeniu operację przywracania dysków](#Track-the-restore-operation), użyj szablonu, który został wygenerowany w ramach operacji przywracania do tworzenia nowej maszyny Wirtualnej z konfiguracją różni się od konfiguracji kopii zapasowej. Możesz również służy do dostosowywania nazw zasobów, które zostały utworzone podczas procesu tworzenia nowej maszyny Wirtualnej z punktu przywracania. 

> [!NOTE]
> Szablony są dodawane jako część przywracanie dysków dla punktów odzyskiwania po 1 marca 2017 r. Są one odpowiednie dla niezarządzanych dysków maszyn wirtualnych. Obsługa maszyn wirtualnych dysku zarządzanego zostanie dodana w przyszłych wydaniach. 
>
>

Aby wyświetlić szablon, który został wygenerowany jako część opcji przywracania dysków:

1. Przejdź do szczegółów zadania przywracania, które są odpowiednie do zadania.

1. Na **szczegóły zadania przywracania** ekranu, wybierz opcję **wdrażania szablonu** do inicjowania wdrożenia szablonu. 

     ![Przechodzenie do szczegółów zadania przywracania](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
1. Na **Wdróż szablon** bloku wdrożenia niestandardowego wdrożenia na podstawie szablonu użyj [Edytuj oraz jak wdrożyć szablon](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) lub dołączyć więcej dostosowań, [tworzenia szablonu](../azure-resource-manager/resource-group-authoring-templates.md) przed wdrożeniem. 

   ![Ładowanie szablonu wdrożenia](./media/backup-azure-arm-restore-vms/loading-template.png)
   
1. Po wprowadzeniu wymaganych wartości, należy zaakceptować **warunków i postanowień** i wybierz **zakupu**.

   ![Przesyłanie szablonu wdrażania](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Wykonywane po przywróceniu kroki
* Jeśli używasz init oparte na chmurze dystrybucji systemu Linux, takie jak Ubuntu, ze względów bezpieczeństwa hasło zostanie zablokowane po przywracania. Należy użyć rozszerzenia VMAccess do przywróconej maszyny wirtualnej do [resetowania hasła](../virtual-machines/linux/reset-password.md). Firma Microsoft zaleca używanie kluczy SSH na tych dystrybucji, aby uniknąć resetowania hasła przywracania wpis.
* Rozszerzenia podczas konfiguracji kopii zapasowej są zainstalowane, ale nie będą one włączone. Jeśli widzisz problem, należy ponownie zainstalować rozszerzenia. 
* Jeśli maszyna wirtualna utworzona kopia zapasowa ma statycznego adresu IP wpis przywracania, przywrócona maszyna wirtualna ma dynamiczny adres IP, aby uniknąć konfliktu, tworząc przywróconej maszyny Wirtualnej. Dowiedz się więcej o tym, jak [dodać statyczny adres IP do przywróconej maszyny Wirtualnej](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Przywrócona maszyna wirtualna nie ma wartość dostępności. Firma Microsoft zaleca używanie opcji przywracania dysków do [dodać zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) po utworzeniu maszyny Wirtualnej z programu PowerShell lub szablonów przy użyciu przywrócić dyski. 


## <a name="backup-for-restored-vms"></a>Tworzenie kopii zapasowej dla maszyn wirtualnych z przywróconych
Jeśli przywrócono Maszynę wirtualną w tej samej grupie zasobów o takiej samej nazwie co maszyna wirtualna pierwotnie kopii zapasowej, kopii zapasowych jest kontynuowane od Przywracanie wpis maszyny Wirtualnej. Jeśli przywrócić maszynę Wirtualną do innej grupy zasobów lub określić inną nazwę dla przywróconej maszyny Wirtualnej, maszyna wirtualna jest traktowana tak, jakby była nowej maszyny Wirtualnej. Należy skonfigurować kopię zapasową dla przywróconej maszyny Wirtualnej.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Przywracanie maszyny Wirtualnej podczas awarii centrum danych platformy Azure
Usługa Azure Backup umożliwia przywracanie kopii zapasowych maszyn wirtualnych w sparowanym centrum danych, w przypadku, gdy głównym w centrum danych, w którym działają maszyny wirtualne napotka awarii i skonfigurowany magazyn kopii zapasowych jako geograficznie nadmiarowe. W takich przypadkach należy wybrać konto magazynu, który jest obecny w sparowanym centrum danych. Pozostała część procesu przywracania pozostaje taki sam. Kopia zapasowa używa usługi obliczeniowej z sparowanego geograficznie można utworzyć przywróconej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [odporności centrum danych platformy Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Przywracanie maszyn wirtualnych kontrolerów domeny
Kontroler domeny (DC) tworzenie kopii zapasowych maszyn wirtualnych jest to obsługiwany scenariusz, przy użyciu kopii zapasowej. Jednakże należy zachować ostrożność podczas procesu przywracania. Proces przywracania poprawne zależy od struktury domeny. W najprostszym przypadku istnieje pojedynczy kontroler domeny w jednej domenie. Najczęściej w przypadku obciążeń produkcyjnych masz jednej domeny przy użyciu wielu kontrolerów domeny, prawdopodobnie z niektórych kontrolerów domeny w środowisku lokalnym. Na koniec Niewykluczone, że las z wieloma domenami. 

Z perspektywy usługi Active Directory maszyny Wirtualnej platformy Azure jest jak każdej innej maszyny Wirtualnej w nowoczesnych obsługiwanej funkcji hypervisor. Główna różnica za pomocą funkcji hypervisor w środowisku lokalnym jest dostępna na platformie Azure jest nie konsoli maszyny Wirtualnej. Konsola jest wymagana dla niektórych scenariuszach, takich jak odzyskiwanie za pomocą odzyskiwania zera (BMR) — typ kopii zapasowej. Przywracanie maszyny Wirtualnej z magazynu kopii zapasowych jest jednak zastępuje pełnego odzyskiwania systemu od ZERA. Trybu przywracania usług katalogowych (DSRM) jest również dostępna, wszystkie scenariusze odzyskiwania usługi Active Directory są możliwe. Aby uzyskać więcej informacji, zobacz [zagadnienia i przywracania kopii zapasowych dla kontrolerów domeny zwirtualizowanych](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) i [planowanie odzyskiwania lasu usługi Active Directory](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Pojedynczy kontroler domeny w jednej domenie
W witrynie Azure portal lub przy użyciu programu PowerShell, można przywrócić maszyny Wirtualnej (np. innej maszyny Wirtualnej).

### <a name="multiple-dcs-in-a-single-domain"></a>Wiele kontrolerów domeny w jednej domenie
Gdy inne kontrolery domeny w tej samej domeny, można z Tobą skontaktować za pośrednictwem sieci, takich jak dowolnej maszyny Wirtualnej można przywrócić kontrolera domeny. Jeśli jest ostatnim pozostałe kontrolera domeny w domenie lub odzyskiwania w sieci izolowanej jest wykonywane, musi następować procedury odzyskiwania lasu.

### <a name="multiple-domains-in-one-forest"></a>Wiele domen w jednym lesie
Gdy inne kontrolery domeny w tej samej domeny, można z Tobą skontaktować za pośrednictwem sieci, takich jak dowolnej maszyny Wirtualnej można przywrócić kontrolera domeny. We wszystkich innych przypadkach zaleca się przywrócenie lasu.

## <a name="restore-vms-with-special-network-configurations"></a>Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami sieci
Istnieje możliwość tworzenia kopii zapasowej i przywracanie maszyn wirtualnych za pomocą następujących specjalnymi konfiguracjami sieci. Te konfiguracje wymagają jednak niektóre szczególną ostrożność podczas przechodzenia przez proces przywracania:

* Maszyny wirtualne w ramach usługi równoważenia obciążenia (wewnętrznych i zewnętrznych)
* Maszyny wirtualne za pomocą wielu zastrzeżonych adresów IP
* Maszyny wirtualne z wieloma kartami sieciowymi

> [!IMPORTANT]
> Podczas tworzenia konfiguracji specjalnych sieci dla maszyn wirtualnych, należy utworzyć maszyny wirtualne z przywróconych dysków za pomocą programu PowerShell.
>
>

Aby w pełni ponownie utworzyć maszyny wirtualne po przywróceniu do dysku, wykonaj następujące kroki:

1. Przywracanie dysków z usługi Recovery Services vault przy użyciu [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

1. Utwórz konfigurację maszyny Wirtualnej wymagana dla modułu równoważenia obciążenia / wiele kart Sieciowych/wielu zarezerwowanych adresów IP przy użyciu poleceń cmdlet programu PowerShell. Użyj go do utworzenia maszyny Wirtualnej z żądaną konfiguracją:

   a. Tworzenie maszyny Wirtualnej w usłudze w chmurze za pomocą [wewnętrznego modułu równoważenia obciążenia](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Tworzenie maszyny Wirtualnej, aby nawiązać połączenie [modułu równoważenia obciążenia dostępnego z Internetu](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Utwórz Maszynę wirtualną za pomocą [wiele kart sieciowych](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Utwórz Maszynę wirtualną za pomocą [wielu zastrzeżone adresy IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Kolejne kroki
Teraz, aby można było przywrócić maszyny wirtualne, zobacz artykuł dotyczący rozwiązywania problemów dla informacji o typowych problemów z maszynami wirtualnymi. Ponadto zapoznaj się z artykuł na temat zarządzania zadaniami z maszynami wirtualnymi.

* [Rozwiązywanie problemów z błędami](backup-azure-vms-troubleshoot.md#restore)
* [Zarządzanie maszynami wirtualnymi](backup-azure-manage-vms.md)
