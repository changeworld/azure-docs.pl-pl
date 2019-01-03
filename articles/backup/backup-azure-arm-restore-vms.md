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
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793380"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Przywracanie maszyn wirtualnych za pomocą witryny Azure portal
Chroń swoje dane, tworząc migawki danych w określonych odstępach czasu. Te migawki są nazywane punktami odzyskiwania i są one przechowywane w magazynach usługi Recovery Services. Jeśli jest to konieczne naprawić lub ponownie utworzyć maszynę wirtualną (VM), można przywrócić maszynę Wirtualną z dowolnego z punktów odzyskiwania zapisanych. Przywracanie punktu odzyskiwania, możesz wykonywać następujące czynności:

* Utwórz nową maszynę Wirtualną: Z punktu odzyskiwania w momencie kopii zapasowej maszyny wirtualnej.
* Przywróć dyski: Użyj szablonu, który jest dostarczany z procesu dostosowywania przywróconej maszyny Wirtualnej lub wykonać odzyskiwanie pojedynczych plików.

W tym artykule wyjaśniono, jak przywrócić maszynę Wirtualną do nowej maszyny Wirtualnej lub przywracanie wszystkich dysków kopii zapasowej. Aby odzyskiwanie poszczególnych plików, zobacz [odzyskiwanie plików z kopii zapasowej maszyny Wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md).

![Trzy sposoby, aby przywrócić z kopii zapasowych maszyn wirtualnych](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


Przywracanie maszyny Wirtualnej lub wszystkich dysków z maszyny Wirtualnej z kopii zapasowej obejmuje dwa kroki:

* Wybierz punkt przywracania do przywracania.
* Wybierz typ przywracania
    - Opcja 1: Utwórz nową maszynę Wirtualną
    - Opcja 2: Przywróć dyski.

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

    - Aby przywrócić dane przy użyciu punktu przywracania z ostatnich 30 dni, kliknij prawym przyciskiem myszy maszynę Wirtualną > **przywrócić maszynę Wirtualną**.
    - Aby przywrócić dane przy użyciu punktu przywracania starsze niż 30 dni, kliknij link w obszarze **punktów przywracania**.
    - Kliknij, aby wyświetlić listę i filtrowania maszyn wirtualnych znajdują się daty w niestandardowych **przywrócić maszynę Wirtualną** w menu. Użyj filtru, aby zmodyfikować przedział czasu dla punktów przywracania wyświetlane. Można również filtrować różnego rodzaju wyjaśnienie pojęcia spójności danych.
8. Przejrzyj ustawienia punktu przywracania:
    - Pokazuje spójności danych [typ spójności](backup-azure-vms-introduction.md#consistency-types) punktu odzyskiwania.
    - **Typu odzyskiwania** pokazuje przechowywania punkt (w ramach konta magazynu, w magazynie lub obu. [Dowiedz się więcej](https://azure.microsoft.com/blog/large-disk-support/) o punktach natychmiastowe odzyskiwanie.

  ![Punkty przywracania](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. Wybierz punkt przywracania.

10. Wybierz **przywracania konfiguracji**. **Przywracania konfiguracji** zostanie otwarty blok.

## <a name="choose-a-vm-restore-configuration"></a>Wybierz konfigurację przywracania maszyny Wirtualnej
Po wybraniu punktu przywracania, należy wybrać konfigurację przywracania maszyny Wirtualnej. Aby skonfigurować przywróconej maszyny Wirtualnej, można użyć witryny Azure portal lub programu PowerShell.

### <a name="restore-options"></a>Opcje przywracania

**Opcja** | **Szczegóły**
--- | ---
**Utwórz nowy — tworzenie maszyny Wirtualnej** | Wartość równoważna szybkie tworzenie maszyny wirtualnej. Pobiera pracę podstawowej maszyny Wirtualnej z punktu przywracania.<br/><br/> Ustawienia maszyny Wirtualnej można zmodyfikować w ramach procesu przywracania.
**Tworzenie nowego przywracania dysku** | Tworzy maszynę Wirtualną, którą można dostosować w ramach procesu przywracania.<br/><br/> Ta opcja powoduje skopiowanie wirtualnych dysków twardych do konta magazynu, które określisz.<br/><br/> — Konto magazynu powinny być w tej samej lokalizacji co magazyn.<br/><br/> Jeśli nie masz konta usługi storage odpowiedniego należy ją utworzyć.<br/><br/> Typ replikacji konta magazynu jest wyświetlane w nawiasach. Magazyn strefowo nadmiarowy (ZRS) nie jest obsługiwane.<br/><br/> Z konta magazynu możesz dołączyć przywróconych dysków do istniejącej maszyny Wirtualnej lub utworzyć nową maszynę Wirtualną z przywróconych dysków przy użyciu programu PowerShell.
**Zamień istniejące** | Ta opcja nie trzeba utworzyć Maszynę wirtualną.<br/><br/> Musi istnieć bieżącej maszyny Wirtualnej. Jeśli zostanie usunięta ta opcja nie może być używana.<br/><br/> Usługa Azure Backup tworzy migawkę istniejącej maszyny Wirtualnej i zapisuje go w tymczasowej lokalizacji określonej. Następnie zastępuje istniejące dyski podłączone do maszyny Wirtualnej z wybranego punktu przywracania. Migawki został wcześniej utworzony jest kopiowane do magazynu i przechowywane jako punkt odzyskiwania, zgodnie z zasadami przechowywania kopii zapasowych.<br/><br/> Zastąp istniejące jest obsługiwana dla niezaszyfrowane zarządzane maszyny wirtualne. Nie jest obsługiwana w przypadku dysków niezarządzanych [uogólniony maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), lub dla maszyn wirtualnych [utworzone za pomocą niestandardowych obrazów](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżącej maszyny Wirtualnej, następnie liczbę dysków w punkcie przywracania tylko odzwierciedlają maszyny Wirtualnej.

> [!NOTE]
> Jeśli chcesz przywrócić maszynę Wirtualną z Zaawansowane ustawienia sieciowe, na przykład, jeśli są zarządzane przez moduł równoważenia obciążenia wewnętrznego lub zewnętrznego lub ma wiele kart sieciowych lub wielu zastrzeżonych adresów IP, należy przywrócić za pomocą programu PowerShell. [Dowiedz się więcej](#restore-vms-with-special-network-configurations).
> Jeśli korzysta z maszyny Wirtualnej z systemem Windows [Centrum licencjonowania](../virtual-machines/windows/hybrid-use-benefit-licensing.md), użyj **Utwórz nowy przywracania dysku** opcji, a następnie użyć do utworzenia maszyny Wirtualnej przy użyciu programu PowerShell lub szablonu przywracania **typu licencji** równa **Windows_Server**. To ustawienie można zastosować w taki sposób, po utworzeniu maszyny Wirtualnej.


Określ przywracania, ustawiając w następujący sposób:

1. W **przywrócić**, wybierz opcję [punkt przywracania](#select-a-restore-point-for-restore) > **przywracania konfiguracji**.
2. W **przywracania konfiguracji**, wybierz, w jaki sposób chcesz wykonać przywrócenie maszyny Wirtualnej zgodnie z ustawieniami podsumowane w tabeli powyżej.

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>Utwórz nowy — tworzenie maszyny Wirtualnej

1. W **przywracania konfiguracji** > **Utwórz nową** > **przywrócić typ**, wybierz opcję **Utwórz maszynę wirtualną** .
2. W **nazwę maszyny wirtualnej**, określ maszynę Wirtualną, która nie istnieje w subskrypcji.
3. W **grupy zasobów**, wybierz istniejącą grupę zasobów dla nowej maszyny Wirtualnej lub utworzyć nową globalnie unikatową nazwą. Jeśli przypiszesz nazwę, aby już istniejący, platforma Azure przypisuje grupy taką samą nazwę jak maszyna wirtualna.
4. W **sieć wirtualna**, wybierz sieć wirtualną, w którym zostaną umieszczone maszyny Wirtualnej. Wyświetlane są wszystkie sieci wirtualne, skojarzony z subskrypcją. Wybierz podsieć. Pierwszej podsieci jest domyślnie zaznaczone.
5. W **lokalizacji magazynu**, należy określić typ magazynu używany dla maszyny Wirtualnej.

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. W **przywracania konfiguracji**, wybierz opcję **OK**. W **przywrócić**, kliknij przycisk **przywrócić** wyzwalanie operacji przywracania.



## <a name="create-new-restore-disks"></a>Utwórz nowy przywracanie dysków

1. W **przywracania konfiguracji** > **Utwórz nową** > **przywrócić typ**, wybierz opcję **Przywróć dyski**.
2. W **grupy zasobów**, wybierz istniejącą grupę zasobów dla przywróconych dysków lub Utwórz nową globalnie unikatową nazwą.
3. W **konta magazynu**, określ konto do którego zostaną skopiowane wirtualne dyski twarde. Upewnij się, że konto znajduje się w tym samym regionie co magazyn.

    ![Konfiguracja odzyskiwania została ukończona](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. W **przywracania konfiguracji**, wybierz opcję **OK**. W **przywrócić**, kliknij przycisk **przywrócić** wyzwalanie operacji przywracania.
5. Po przywróceniu dysku, możesz tworzyć dowolne z następujących czynności, aby ukończyć operację przywracania maszyny Wirtualnej.

    - Dostosowywanie ustawień w celu wyzwolenia wdrożenia maszyny Wirtualnej, użyj szablonu, który został wygenerowany w ramach operacji przywracania. Zmodyfikuj domyślne ustawienia szablonu, a następnie przesyłanie szablonu podczas wdrażania maszyny Wirtualnej.
    - Możesz [dołączyć przywróconych dysków](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps) do istniejącej maszyny Wirtualnej.
    - Możesz [Utwórz nową maszynę Wirtualną](backup-azure-vms-automation.md#restore-an-azure-vm) z przywróconych dysków przy użyciu programu PowerShell.


## <a name="replace-existing-disks"></a>Zastąp istniejące dyski

Użyj tej opcji, aby zastąpić istniejące dyski na maszynie wirtualnej i bieżący punkt przywracania.

1. W **przywracania konfiguracji**, kliknij przycisk **zamienić istniejący**.
2. W **przywrócić typ**, wybierz opcję **zastąpić dysku/s** (punkt przywracania, który spowoduje zastąpienie istniejącego dysku maszyny Wirtualnej lub dysków).
3. W **Lokalizacja tymczasowa**, określ, gdzie mają być zapisywane migawki bieżącego dysków zarządzanych.

   ![Zastąp istniejący Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania
Po użytkownik zainicjuje operację przywracania, usługa backup tworzy zadanie śledzenia operacji przywracania. Usługa kopii zapasowej tworzy również i tymczasowe wyświetlanie powiadomień w **powiadomienia** obszaru portalu. Jeśli nie zostanie wyświetlone powiadomienie, wybierz opcję **powiadomienia** symbolu, aby wyświetlić powiadomienia.

![Przywracanie wyzwolone](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Kliknij hiperlink powiadomienia, aby przejść do **BackupJobs** listy. Wszystkie szczegóły operacji dla danego zadania jest dostępna w **BackupJobs** listy. Możesz przejść do **BackupJobs** na pulpicie nawigacyjnym magazynu, klikając pozycję zadania tworzenia kopii zapasowej kafelka, wybierz **maszyny wirtualnej platformy Azure** Aby wyświetlić zadania skojarzonego z magazynem.

**Zadania tworzenia kopii zapasowej** bloku otwiera i wyświetla listę zadań.

![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

**Pasek postępu** jest teraz dostępna w **przywrócić szczegóły** bloku. **Przywrócić szczegóły** bloku można otworzyć, klikając pozycję Wszystkie zadania przywracania, który znajduje się w stanie **w toku**. **Pasek postępu** jest dostępny w wszystkich wariantów przeprowadzać operacje przywracania, takich jak **Utwórz nowy**, **Przywróć dyski** i **zamienić istniejący**. Szczegóły przez pasek postępu przywracania są **szacowany czas przywracania**, **procent przywracania** i **liczba bajtów przesłanych**.

Przywróć pasek postępu, szczegóły są podane poniżej:

- **Szacowany czas przywracania** początkowo zawiera czas potrzebny do ukończenia operacji przywracania. Podczas operacji w miarę, czas zmniejsza i będzie wynosić 0, po zakończeniu operacji przywracania.
- **Wartość procentowa przywracania** udostępnia dane wartość procentową operacji przywracania zostało zakończone.
- **Liczba bajtów przesłanych** jest dostępna w podzadanie podczas przywracania odbywają się za pośrednictwem tworzenia nowej maszyny Wirtualnej. Zawiera ona szczegółowe informacje o liczby bajtów zostały przeniesione z całkowitą liczbą bajtów, które mają zostać przeniesione.


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
