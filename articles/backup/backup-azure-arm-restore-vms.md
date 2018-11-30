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
ms.openlocfilehash: 0d78ae294cea383fbe59a1f7968d8bf18b1942d1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422960"
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
1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com/).

2. W menu platformy Azure wybierz **wszystkich usług**. Na liście usług wpisz **usługi Recovery Services** lub przejdź do **MAGAZYNU** gdzie **magazynów usługi Recovery Services** jest na liście, wybierz ją.

    ![Magazyn usługi Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. Zostanie wyświetlona lista magazynów w subskrypcji.

    ![Magazyny listę usług Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. Z listy magazynów usług odzyskiwania wybierz magazyn skojarzony z maszyną Wirtualną, którą chcesz przywrócić. Po wybraniu magazynu, zostanie otwarty jego pulpit nawigacyjny.

    ![Wybrany magazyn usługi Recovery Services](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. Na pulpicie nawigacyjnym magazynu na **elementy kopii zapasowej** kafelka, wybierz opcję **maszyny wirtualnej platformy Azure**.

    ![Pulpitu nawigacyjnego magazynu](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. **Elementy kopii zapasowej** zostanie otwarty blok zawierający listę maszyn wirtualnych platformy Azure.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. Z listy wybierz maszynę Wirtualną w celu otwarcia pulpitu nawigacyjnego. Pulpit nawigacyjny maszyny Wirtualnej, który zostanie otwarty do obszaru monitorowania, który zawiera **punktów odzyskiwania**. Wszystkich operacji na poziomie maszyny Wirtualnej, takie jak **Utwórz teraz kopię zapasową**, **pliku odzyskiwania**, **Zatrzymaj kopię zapasową** mogą być wykonywane z tego bloku.
Przywracanie można przeprowadzić na wiele sposobów, w tym bloku. Należy pamiętać, że ten blok zawiera listę ostatnich 30 dni punktów odzyskiwania.

    ) kliknij prawym przyciskiem myszy punkt odzyskiwania, w tym bloku (mniej niż 30 dni) i zainicjować **przywrócić maszynę Wirtualną**.

    (b) do przywrócenia odzyskiwania wskazuje większą niż 30 dni, kliknij tutaj są podane w bloku, które mogą być używane.

    c) **przywrócić maszynę Wirtualną** w menu nagłówek zawiera opcję do listy i filtrowania maszyn wirtualnych w niestandardowe daty, jako preferowane.

    Użyj filtru, aby zmienić zakres czasu punktów przywracania są wyświetlane. Domyślnie są wyświetlane wszystkie wyborami punktów przywracania. Zmodyfikuj wszystkie filtru przywracania w punktach, aby wybrać spójność punktu przywracania określone. Aby uzyskać więcej informacji na temat każdego rodzaju punkt przywracania, zobacz [wyjaśnienie pojęcia spójności danych](backup-azure-vms-introduction.md#data-consistency).

    Opcje spójność punktu przywracania:
    - Punkty przywracania na poziomie awarii
    - Punkty przywracania na poziomie aplikacji
    - Punkty przywracania na poziomie systemu plików
    - Wszystkie punkty przywracania

  ![Punkty przywracania](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > Typ odzyskiwania reprezentują, jeśli jest on w odbiorcy konta magazynu, w magazynie i / lub. Dowiedz się więcej o [punktu natychmiastowe odzyskiwanie](https://azure.microsoft.com/blog/large-disk-support/).

8. Na **przywrócić** bloku wybierz **punkt przywracania**.

    ![Wybierz punkt przywracania](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    **Przywrócić** blok, że punkt przywracania jest ustawiona po kliknięciu przycisku **OK**.
9. Jeśli jeszcze nie masz, przejdź do **przywrócić** bloku. Upewnij się, że [po wybraniu punktu przywracania](#select-a-restore-point-for-restore)i wybierz **przywracania konfiguracji**. **Przywracania konfiguracji** zostanie otwarty blok.

## <a name="choose-a-vm-restore-configuration"></a>Wybierz konfigurację przywracania maszyny Wirtualnej
Po wybraniu punktu przywracania, należy wybrać konfigurację przywracania maszyny Wirtualnej. Aby skonfigurować przywróconej maszyny Wirtualnej, można użyć witryny Azure portal lub programu PowerShell.

1. Jeśli jeszcze nie masz, przejdź do **przywrócić** bloku. Upewnij się, że [po wybraniu punktu przywracania](#select-a-restore-point-for-restore)i wybierz **przywracania konfiguracji**. **Przywracania konfiguracji** zostanie otwarty blok.
2. Ten blok zawiera obecnie dwie opcje jeden jest **Utwórz nowy** czyli domyślna, a drugi jest **zamienić istniejący** czyli przywracania w miejscu zastąpić dyski tylko Zachowywanie istniejących konfiguracji i rozszerzenia.

> [!NOTE]
> Pracujemy nad zastępując dysków, ustawienia sieciowe, konfiguracji i rozszerzenia całą maszynę Wirtualną w ciągu następnych kilku miesięcy.
>
>

 W **Utwórz nowy** opcji, które przywraca dane do nowej maszyny Wirtualnej lub nowe dyski, dostępne są dwie opcje:

 ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - **Tworzenie maszyny wirtualnej**
 - **Przywróć dyski**

![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

Portal zawiera **szybkie tworzenie** opcję dla przywróconej maszyny Wirtualnej. Aby dostosować konfigurację maszyny Wirtualnej lub nazwy zasobów utworzonych jako część tworzenia nowych wybór maszyny Wirtualnej, przy użyciu programu PowerShell lub portalu do przywrócenia kopii zapasowej dysków. Aby dołączyć je do dowolnie konfiguracji maszyny Wirtualnej, należy użyć poleceń programu PowerShell. Lub możesz użyć szablonu, który jest dostarczany z przywróconych dysków, aby dostosować przywróconej maszyny Wirtualnej. Aby uzyskać informacje o tym, jak przywrócić maszynę Wirtualną, która ma wiele kart sieciowych lub w ramach modułu równoważenia obciążenia, zobacz [przywrócić maszynę Wirtualną ze specjalnymi konfiguracjami sieci](#restore-vms-with-special-network-configurations). Jeśli maszyna wirtualna Windows używa [Centrum licencjonowania](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Przywróć dyski i szablon programu PowerShell/określonych w tym artykule do utworzenia maszyny Wirtualnej. Upewnij się, że podajesz **typu licencji** jako "Windows_Server" podczas tworzenia maszyny Wirtualnej, aby móc korzystać z zalet Centrum do przywróconej maszyny wirtualnej. Należy pamiętać, że można to zrobić później, po utworzeniu maszyny Wirtualnej również.

## <a name="create-a-new-vm-from-a-restore-point"></a>Utwórz nową maszynę Wirtualną z punktu przywracania
1. Na **przywracania konfiguracji** bloku wymienionych w przed sekcją, wprowadź lub wybierz wartości dla każdego z następujących pól:

    a. **Typ przywracania**. Tworzy maszynę wirtualną.

    b. **Nazwa maszyny wirtualnej**. Wprowadź nazwę maszyny Wirtualnej, która nie istnieje w subskrypcji.

    c. **Grupa zasobów**. Użyj istniejącej grupy zasobów lub Utwórz nową. W przypadku przywracania klasycznej maszyny Wirtualnej, użyj tego pola, aby określić nazwę nowej usługi w chmurze. Jeśli tworzysz nowy zasób grupy lub usługi w chmurze, nazwa musi być globalnie unikatowa. Zazwyczaj nazwa usługi w chmurze jest skojarzone z publicznego adresu URL: na przykład [elementu cloudservice]. cloudapp.net. Jeśli spróbujesz użyć nazwy usługi/w chmurze grupy zasobów w chmurze już w użyciu, platforma Azure przypisuje zasób grupy lub usługi w chmurze taką samą nazwę jak maszyna wirtualna. Azure Wyświetla zasobów grupy/cloud services i maszyny wirtualne nie są skojarzone z żadnej grupy koligacji. Aby uzyskać więcej informacji, zobacz [jak przeprowadzić migrację z grupy koligacji do regionalnej sieci wirtualnej](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Sieć wirtualna**. Wybierz sieć wirtualną, podczas tworzenia maszyny Wirtualnej. Pole zawiera wszystkie sieci wirtualne, skojarzony z subskrypcją. Grupa zasobów maszyny Wirtualnej jest wyświetlany w nawiasach.

    e. **Subnet**. Jeśli sieć wirtualna ma podsieci, pierwszej podsieci jest domyślnie zaznaczone. Jeśli istnieją dodatkowe podsieci, wybierz podsieć, którą chcesz.

    f. **Lokalizacja magazynu**. Konta magazynów znajdują się w lokalizacji tymczasowej. To menu zawiera listę kont magazynu w tej samej lokalizacji co magazyn usługi Recovery Services. Konta magazynu, które są strefowo nadmiarowe nie są obsługiwane. Jeśli nie ma żadnych kont magazynu, w tej samej lokalizacji co magazyn usługi Recovery Services, należy utworzyć jedną przed rozpoczęciem operacji przywracania. Typ replikacji konta magazynu jest wyświetlany w nawiasach.

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * Sieć wirtualna jest opcjonalny w przypadku klasycznej maszyny Wirtualnej i obowiązkowe dla wdrożonych przez Menedżera zasobów maszyny Wirtualnej.

    > * Magazyn typu podana w magazynie konta (premium lub standardowa) w lokalizacji tymczasowej decyduje, typ magazynu dyskowego przywracania. Obecnie nie obsługujemy tryb mieszany dysków podczas przywracania.
    >
    >

2. Na **przywracania konfiguracji** bloku wybierz **OK** aby zakończyć konfigurowanie przywracania. Na **przywrócić** bloku wybierz **przywrócić** wyzwalanie operacji przywracania.

## <a name="restore-backed-up-disks"></a>Przywracanie kopii zapasowych dysków
Przywróć wartość typu **przywracania dysku** w **przywracania konfiguracji** bloku pozwala utworzyć maszynę Wirtualną przy użyciu konfiguracji niestandardowych. Podczas przywracania dysków, konto magazynu należy wybrać należy w tej samej lokalizacji co magazyn usługi Recovery services. Jest to konieczne do utworzenia konta magazynu, jeśli nie ma żadnych kont magazynu w tej samej lokalizacji co magazyn usługi Recovery Services. Konta magazynu ZRS nie są obsługiwane. Typ replikacji konta magazynu jest wyświetlany w nawiasach.

Po operacji przywracania, skorzystaj z poniższych:
* [Szablon służy do dostosowywania przywróconą maszyną Wirtualną.](#use-templates-to-customize-restore-vm)
* [Użyj przywróconych dysków do dołączenia do istniejącej maszyny Wirtualnej](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Utwórz nową maszynę Wirtualną przy użyciu programu PowerShell z przywróconych dysków](./backup-azure-vms-automation.md#restore-an-azure-vm)

Na **przywracania konfiguracji** bloku wybierz **OK** aby zakończyć konfigurowanie przywracania. Na **przywrócić** bloku wybierz **przywrócić** wyzwalanie operacji przywracania.

![Konfiguracja odzyskiwania została ukończona](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

W **miejscu Przywróć** jest wykonywana za pomocą karty **Zastąp istniejący**.

## <a name="replace-existing-disks-from-a-restore-point"></a>Zastąp istniejące dyski z punktu przywracania
**Zamień istniejące** opcji pozwala zastąpić istniejące dyski na maszynie wirtualnej i bieżący punkt przywracania. Tę operację można wykonać tylko, jeśli istnieje w bieżącej maszyny Wirtualnej. Jeśli została ona usunięta z powodów wszelkie, nie można wykonać tej operacji; Alternatywnie, zaleca się, należy wykonać **Utwórz nową** operacje przywracania maszyny Wirtualnej lub dysków, aby kontynuować. Podczas tej operacji, jako środek zapobiegawczy możemy tworzyć kopie zapasowe danych przed inicjowanie zamiany dysków operacji. Spowoduje to utworzenie migawki, a także punktu odzyskiwania w magazynie z okresem przechowywania, zgodnie z harmonogramem w skonfigurowanych zasad tworzenia kopii zapasowej. Jeśli punkt przywracania dysków więcej/mniejszości bieżącej maszyny Wirtualnej, a następnie liczbę dysków w punkcie przywracania będzie odzwierciedlają tylko na maszynie wirtualnej. **Zamień istniejące** opcja jest obecnie obsługiwane dla dysków niezarządzanych i zaszyfrowanych maszyn wirtualnych. Nie jest również obsługiwana dla [uogólniony maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) i maszyn wirtualnych utworzonych za pomocą [obrazów niestandardowych](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).  

 Na **przywracania konfiguracji** jest tylko dane wejściowe, które są potrzebne do wybrania bloku **Lokalizacja tymczasowa**.

   ![Zastąp istniejący Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a. **Typ przywracania**. Zastąp dyski reprezentującą, czy wybrany punkt przywracania zastąpi dyski w istniejącej maszyny Wirtualnej.

 b. **Lokalizacja tymczasowa**. Konta magazynu są tymczasowej lokalizacji dla dysków zarządzanych. To menu zawiera listę kont magazynu w tej samej lokalizacji co magazyn usługi Recovery Services. Konta magazynu, które są strefowo nadmiarowe nie są obsługiwane. Jeśli nie ma żadnych kont magazynu, w tej samej lokalizacji co magazyn usługi Recovery Services, należy utworzyć jedną przed rozpoczęciem operacji przywracania. Typ replikacji konta magazynu jest wyświetlany w nawiasach.

## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania
Po użytkownik zainicjuje operację przywracania, usługa backup tworzy zadanie śledzenia operacji przywracania. Usługa kopii zapasowej tworzy również i tymczasowe wyświetlanie powiadomień w **powiadomienia** obszaru portalu. Jeśli nie zostanie wyświetlone powiadomienie, wybierz opcję **powiadomienia** symbolu, aby wyświetlić powiadomienia.

![Przywracanie wyzwolone](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Kliknij hiperlink powiadomienia, aby przejść do **BackupJobs** listy. Wszystkie szczegóły operacji dla danego zadania jest dostępna w **BackupJobs** listy. Możesz przejść do **BackupJobs** na pulpicie nawigacyjnym magazynu, klikając pozycję zadania tworzenia kopii zapasowej kafelka, wybierz **maszyny wirtualnej platformy Azure** Aby wyświetlić zadania skojarzonego z magazynem.

**Zadania tworzenia kopii zapasowej** bloku otwiera i wyświetla listę zadań.

![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

## <a name="use-templates-to-customize-a-restored-vm"></a>Szablony umożliwiają dostosowywanie przywróconą maszyną Wirtualną.
Po [zakończeniu operację przywracania dysków](#Track-the-restore-operation), użyj szablonu, który został wygenerowany w ramach operacji przywracania do tworzenia nowej maszyny Wirtualnej z konfiguracją różni się od konfiguracji kopii zapasowej. Możesz również służy do dostosowywania nazw zasobów, które zostały utworzone podczas procesu tworzenia nowej maszyny Wirtualnej z punktu przywracania.

![Przechodzenie do szczegółów zadania przywracania](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

Aby wyświetlić szablon, który został wygenerowany jako część opcji przywracania dysków:

1. Przejdź do **szczegóły zadania przywracania** odpowiadający zadania.

2. Na **szczegóły zadania przywracania** ekranu, wybierz opcję **wdrażania szablonu** do inicjowania wdrożenia szablonu.

3. Na **Wdróż szablon** bloku wdrożenia niestandardowego wdrożenia na podstawie szablonu użyj [Edytuj oraz jak wdrożyć szablon](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) lub dołączyć więcej dostosowań, [tworzenia szablonu](../azure-resource-manager/resource-group-authoring-templates.md) przed wdrożeniem.

  ![Ładowanie szablonu wdrożenia](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Po wprowadzeniu wymaganych wartości, należy zaakceptować **warunków i postanowień** i wybierz **zakupu**.

  ![Przesyłanie szablonu wdrażania](./media/backup-azure-arm-restore-vms/submitting-template1.png)

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

   c. Utwórz Maszynę wirtualną za pomocą [wiele kart sieciowych](../virtual-machines/windows/multiple-nics.md).

   d. Utwórz Maszynę wirtualną za pomocą [wielu zastrzeżone adresy IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md).

## <a name="next-steps"></a>Kolejne kroki
Teraz, aby można było przywrócić maszyny wirtualne, zobacz artykuł dotyczący rozwiązywania problemów dla informacji o typowych problemów z maszynami wirtualnymi. Ponadto zapoznaj się z artykuł na temat zarządzania zadaniami z maszynami wirtualnymi.

* [Rozwiązywanie problemów z błędami](backup-azure-vms-troubleshoot.md#restore)
* [Zarządzanie maszynami wirtualnymi](backup-azure-manage-vms.md)
