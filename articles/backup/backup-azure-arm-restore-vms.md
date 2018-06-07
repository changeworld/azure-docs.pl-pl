---
title: 'Kopia zapasowa Azure: Przywracanie maszyn wirtualnych przy użyciu portalu Azure'
description: Przywracanie maszyny wirtualnej platformy Azure z punktu odzyskiwania za pomocą portalu Azure
services: backup
author: markgalioto
manager: carmonm
keywords: Przywracanie kopii zapasowej. jak przywrócić; punkt odzyskiwania;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: markgal
ms.openlocfilehash: 52ca7cf4a04ace2a43f15433c05a2db26bde3602
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605597"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Przywracanie maszyn wirtualnych za pomocą portalu Azure
Ochrona danych za tworzenie migawek danych w określonych odstępach czasu. Te migawki są określane jako punkty odzyskiwania, i w których są przechowywane w Magazyny usług odzyskiwania. Jeśli jest konieczne naprawienie lub skompiluj ponownie maszynę wirtualną (VM), można przywrócić maszynę Wirtualną z żadnym z punktów odzyskiwania zapisane. Po przywróceniu punkt odzyskiwania, można:

* Utwórz nową maszynę Wirtualną, która odzwierciedla w momencie kopii zapasowej maszyny Wirtualnej.
* Przywróć dysków i użyć szablonu, który jest dostarczany z procesu można dostosować przywróconą maszyną Wirtualną lub wykonaj odzyskiwanie poszczególnych plików. 

W tym artykule wyjaśniono, jak przywrócić Maszynę wirtualną do nowej maszyny Wirtualnej lub przywrócić wszystkie dyski kopii zapasowej. Aby odzyskiwanie poszczególnych plików, zobacz [odzyskać pliki z kopii zapasowej maszyny Wirtualnej Azure](backup-azure-restore-files-from-vm.md).

![Trzy sposoby przywrócić z kopii zapasowej maszyny Wirtualnej](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł zawiera informacje i procedury służące do przywrócenia wdrożone maszyny wirtualne za pomocą modelu usługi Resource Manager.
>
>

Przywrócenie maszyny Wirtualnej lub wszystkich dysków z maszyny Wirtualnej kopii zapasowej obejmuje dwa kroki:

* Wybierz punkt przywracania do przywrócenia.
* Wybierz typ przywracania, Utwórz nową maszynę Wirtualną lub przywrócenia dysków i określ wymagane parametry. 

## <a name="select-a-restore-point-for-restore"></a>Wybierz punkt przywracania dla przywracania
1. Zaloguj się w [Portalu Azure](http://portal.azure.com/).

2. W Azure menu wybierz **Przeglądaj**. Na liście usług, wpisz **usług odzyskiwania**. Na liście usług można dostosować do wpisany. Po wyświetleniu **Magazyny usług odzyskiwania**, zaznacz go.

    ![Magazyn usługi Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zostanie wyświetlona lista magazynów w subskrypcji.

    ![Lista usług odzyskiwania magazynów](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Z listy wybierz magazyn skojarzonych z maszyną Wirtualną, aby przywrócić. Po wybraniu magazyn zostanie otwarty jego pulpitu nawigacyjnego.

    ![Wybrany magazyn usług odzyskiwania](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. Na pulpicie nawigacyjnym magazynu na **kopii zapasowej elementów** kafelka, wybierz opcję **maszyny wirtualne Azure**.

    ![pulpitem nawigacyjnym magazynu](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **Kopii zapasowej elementów** bloku otwiera i wyświetla listę maszyn wirtualnych platformy Azure.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Z listy wybierz maszyny Wirtualnej, aby otworzyć pulpit nawigacyjny. Maszyna wirtualna pulpitu nawigacyjnego otwiera do obszaru monitorowania, który zawiera **punkty przywracania** kafelka.

    ![Punkty przywracania](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. W menu nawigacyjnym maszyny Wirtualnej, wybierz **przywrócić**.

    ![Przycisk przywracania](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    **Przywrócić** zostanie otwarty blok.

    ![Przywróć blok](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Na **przywrócić** bloku, wybierz opcję **punkt przywracania**. **Wybierz punkt przywracania** zostanie otwarty blok.

    ![Wybierz punkt przywracania](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Domyślnie okno dialogowe wyświetla wszystkie punkty przywracania z ostatnich 30 dni. Użyj **filtru** zmienić zakres czasu punktów przywracania wyświetlane. Domyślnie są wyświetlane wszystkie consistencies punktów przywracania. Modyfikowanie **wszystkie punkty przywracania** filtr, aby wybrać spójności punktu przywracania określonych. Aby uzyskać więcej informacji na temat poszczególnych typów punktu przywracania, zobacz [spójność danych](backup-azure-vms-introduction.md#data-consistency).

    Opcje spójności punkt przywracania:

     * Punkty przywracania na poziomie awarii
     * Punkty przywracania na poziomie aplikacji
     * Punkty przywracania na poziomie systemu plików
     * Wszystkie punkty przywracania

8. Wybierz punkt przywracania, a następnie wybierz **OK**.

    ![Opcje punktu przywracania](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **Przywrócić** bloku pokazuje, że ustawiono punktu przywracania.

9. Jeśli jeszcze nie jest istnieje, przejdź do **przywrócić** bloku. Upewnij się, że [jest wybrany punkt przywracania](#select-a-restore-point-for-restore)i wybierz **przywracania**. **Przywracania** zostanie otwarty blok.

## <a name="choose-a-vm-restore-configuration"></a>Wybierz konfigurację przywracania maszyny Wirtualnej
Wybierz punkt przywracania i wybrać konfigurację przywracania maszyny Wirtualnej. Aby skonfigurować przywróconą maszyną Wirtualną, używając portalu Azure lub programu PowerShell.

1. Jeśli jeszcze nie jest istnieje, przejdź do **przywrócić** bloku. Upewnij się, że [jest wybrany punkt przywracania](#select-a-restore-point-for-restore)i wybierz **przywracania**. **Przywracania** zostanie otwarty blok.

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. Na **przywracania** bloku dostępne są dwie opcje:

   * **Tworzenie maszyny wirtualnej**

   * **Przywracanie dysków**

Portal zawiera **szybkie tworzenie** opcji przywróconej maszyny wirtualnej. Aby dostosować konfigurację maszyny Wirtualnej lub nazwy zasobów utworzone podczas tworzenia nowego wyboru maszyny Wirtualnej, należy użyć programu PowerShell lub portalu do przywrócenia kopii zapasowej dysków. Dołącz je do wybraną konfigurację maszyny Wirtualnej za pomocą poleceń programu PowerShell. Lub skorzystać z szablonu, który jest dostarczany z przywróconą dysków, aby dostosować przywróconą maszyną Wirtualną. Aby uzyskać informacje dotyczące przywracania maszyny Wirtualnej, która ma wiele kart sieciowych lub należących do modułu równoważenia obciążenia, zobacz [przywrócić Maszynę wirtualną z konfiguracjami sieci specjalne](#restore-vms-with-special-network-configurations). Jeśli maszyna wirtualna systemu Windows używa [Centrum licencjonowania](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Przywróć dysków i użyć programu PowerShell/szablonu określona w tym artykule do utworzenia maszyny Wirtualnej. Upewnij się, że podajesz **typu licencji** jako "Windows_Server" podczas tworzenia maszyny Wirtualnej, aby wykorzystać zalety Centrum na przywróconą maszyną Wirtualną. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Utwórz nową maszynę Wirtualną z punktu przywracania
1. Jeśli nie masz już istnieje, [wybierz punkt przywracania](#restore-a vm-with-special-network-configurations) przed rozpoczęciem tworzenia nowej maszyny Wirtualnej z punktu przywracania. Po wybraniu punktu przywracania na **przywracania** bloku, wprowadź lub wybierz wartości dla każdego z następujących pól:

    a. **Przywróć typu**. Tworzy maszynę wirtualną.

    b. **Nazwa maszyny wirtualnej**. Podaj nazwę maszyny Wirtualnej. Nazwa musi być unikatowa dla grupy zasobów (dla usługi Azure Resource Manager wdrożone maszyny Wirtualnej) lub usługi w chmurze (w przypadku klasycznych maszyn wirtualnych). Nie można zamienić maszyny Wirtualnej, jeśli już istnieje w subskrypcji.

    c. **Grupa zasobów**. Użyj istniejącej grupy zasobów lub Utwórz nową. W przypadku przywracania klasyczne maszyny Wirtualnej, to pole służy do określania nazwy nowej usługi w chmurze. Jeśli tworzysz nową usługę chmury/grupy zasobów musi być globalnie unikatowe nazwy. Zazwyczaj nazwa usługi w chmurze jest skojarzone z adresem URL publicznych: na przykład [cloudservice]. cloudapp.net. Jeśli spróbujesz użyć nazwy dla usługi chmury/grupy zasobów w chmurze już używani Azure przypisuje usługi chmury/grupy zasobów taką samą nazwę jak maszyny Wirtualnej. Azure Wyświetla usług chmury/grupy zasobów i maszyny wirtualne nie są skojarzone z grup koligacji. Aby uzyskać więcej informacji, zobacz [jak przeprowadzić migrację z grup koligacji do regionalnej sieci wirtualnej](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Sieć wirtualna**. Wybierz sieć wirtualną podczas tworzenia maszyny Wirtualnej. Pole zawiera wszystkie sieci wirtualne skojarzone z subskrypcją. Grupa zasobów maszyny wirtualnej jest wyświetlany w nawiasach.

    e. **Subnet**. Jeśli sieć wirtualna ma podsieci, pierwszej podsieci jest domyślnie zaznaczona. Jeśli istnieją dodatkowe podsieci, wybierz podsieć, którą chcesz.

    f. **Konto magazynu**. To menu zawiera listę kont magazynu w tej samej lokalizacji co magazyn usług odzyskiwania. Konta magazynu, które są obszar strefowo nadmiarowy nie są obsługiwane. Jeśli nie ma żadnych kont magazynu z tej samej lokalizacji co magazyn usług odzyskiwania, należy go utworzyć przed uruchomieniem operacji przywracania. Typ replikacji konta magazynu jest wyświetlany w nawiasach.

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Po przywróceniu maszyn wirtualnych wdrożonych przez Menedżera zasobów, należy wskazać sieci wirtualnej. Sieć wirtualna jest opcjonalny w przypadku klasycznych maszyn wirtualnych.

    > * Po przywróceniu maszyn wirtualnych z dyskami zarządzanego upewnij się, że wybrane konto magazynu nie jest włączone dla szyfrowanie usługi Magazyn Azure w jego okres istnienia.

    > * Na podstawie magazynu typu konta magazynu wybranego (premium lub standard), wszystkie dyski przywrócić będzie premium lub dyski standardowe. Obecnie nie obsługujemy trybu mieszanego dysków podczas przywracania.
    >
    >

2. Na **przywracania** bloku, wybierz opcję **OK** aby ukończyć konfigurację przywracania. Na **przywrócić** bloku, wybierz opcję **przywrócić** wyzwalanie operacji przywracania.

## <a name="restore-backed-up-disks"></a>Przywracanie kopii zapasowej dysków
Aby dostosować maszyna wirtualna ma zostać utworzona z dysków kopii zapasowej inny niż co znajduje się w **przywracania** bloku, wybierz opcję **przywrócenia dysków** jako wartość **przywrócić typu**. Ten wybór poprosi o podanie konta magazynu, w którym mają zostać skopiowane dysków z kopii zapasowych. Po wybraniu konta magazynu, wybierz konto, które współużytkują tej samej lokalizacji co magazyn usług odzyskiwania. Konta magazynu, które są obszar strefowo nadmiarowy nie są obsługiwane. Jeśli nie ma żadnych kont magazynu z tej samej lokalizacji co magazyn usług odzyskiwania, należy go utworzyć przed uruchomieniem operacji przywracania. Typ replikacji konta magazynu jest wyświetlany w nawiasach.

Po zakończeniu operacji przywracania, można:

* [Za pomocą szablonu można dostosować przywróconą maszyną Wirtualną.](#use-templates-to-customize-restore-vm)
* [Użyj dysków przywróconej do dołączenia do istniejącej maszyny Wirtualnej](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Utwórz nową maszynę Wirtualną za pomocą programu PowerShell z przywróconą dysków](./backup-azure-vms-automation.md#restore-an-azure-vm)

Na **przywracania** bloku, wybierz opcję **OK** aby ukończyć konfigurację przywracania. Na **przywrócić** bloku, wybierz opcję **przywrócić** wyzwalanie operacji przywracania.

![Konfiguracja odzyskiwania została zakończona](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Śledź operacji przywracania
Po wyzwalanie operacji przywracania, usługi tworzenia kopii zapasowej tworzy zadanie śledzenia operacji przywracania. Usługi tworzenia kopii zapasowej tworzy także i tymczasowe wyświetlanie powiadomień w **powiadomienia** części portalu. Jeśli nie widzisz powiadomienia, wybierz **powiadomienia** symbolu, aby wyświetlić powiadomienia.

![Przywracanie wyzwalane](./media/backup-azure-arm-restore-vms/restore-notification.png)

Aby wyświetlić operacji podczas przetwarzania lub aby go wyświetlić, po zakończeniu Otwórz **kopii zapasowej zadania** listy.

1. W Azure menu wybierz **Przeglądaj**, a na liście usług, wpisz **usług odzyskiwania**. Na liście usług można dostosować do wpisany. Po wyświetleniu **Magazyny usług odzyskiwania**, zaznacz go.

    ![Otwórz magazyn usług odzyskiwania](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zostanie wyświetlona lista magazynów w subskrypcji.

    ![Lista usług odzyskiwania magazynów](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Z listy wybierz magazyn skojarzonych z maszyną Wirtualną można przywrócić. Po wybraniu magazyn zostanie otwarty jego pulpitu nawigacyjnego.

3. Na pulpicie nawigacyjnym magazynu na **zadania tworzenia kopii zapasowej** kafelka, wybierz opcję **maszyn wirtualnych platformy Azure** Aby wyświetlić zadania związane z magazynem.

    ![pulpitem nawigacyjnym magazynu](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **Kopii zapasowej zadania** bloku otwiera i wyświetla listę zadań.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Dostosowywanie przywróconą maszyną Wirtualną za pomocą szablonów
Po [zakończeniu operacji przywracania dysków](#Track-the-restore-operation), użyj szablonu, który został wygenerowany w ramach operacji przywracania do tworzenia nowej maszyny Wirtualnej z konfiguracją inny niż konfiguracji kopii zapasowej. Możesz również służy do dostosowywania nazw zasobów, które zostały utworzone podczas procesu tworzenia nowej maszyny Wirtualnej z punktu przywracania. 

> [!NOTE]
> Szablony są dodawane jako część przywracania dysków dla punktów odzyskiwania po 1 marca 2017 r. Są one odpowiednie dla niezarządzanych dysku maszyn wirtualnych. Obsługa dysków zarządzanych maszyn wirtualnych będzie dostępna w kolejnych wersjach. 
>
>

Aby wyświetlić szablon, który został wygenerowany jako część opcji dysków przywracania:

1. Przejdź do szczegółów zadania przywracania odpowiadający zadaniu.

2. Na **szczegóły zadania przywracania** ekranu wybierz **wdrażanie szablonu** do inicjowania wdrożenia szablonu. 

     ![Przywróć przechodzenia zadania](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. Na **wdrażanie szablonu** bloku niestandardowe wdrożenie, użyj szablonu wdrożenia do [edytować i wdrożyć szablon](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) lub dołączyć więcej dostosowań przez [tworzenia szablonu](../azure-resource-manager/resource-group-authoring-templates.md) przed wdrożeniem. 

   ![Wdrażanie szablonu obciążenia](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. Po wprowadzeniu wymaganych wartości akceptuje **warunków i postanowień** i wybierz **zakupu**.

   ![Przesyłanie szablonu wdrożenia](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Wykonywane po przywróceniu kroki
* Jeśli używasz dystrybucji systemu Linux init oparte na chmurze, takich jak Ubuntu, ze względów bezpieczeństwa hasło jest zablokowana post przywracania. Użyj rozszerzenia VMAccess przywróconej maszynę wirtualną i [resetowania hasła](../virtual-machines/linux/reset-password.md). Zalecamy używanie kluczy SSH w tych dystrybucji w celu uniknięcia Resetowanie hasła przywracania post.
* Podczas tworzenia kopii zapasowej konfiguracji rozszerzenia są zainstalowane, ale nie jest włączony. Jeśli widzisz problemu, ponownie zainstaluj rozszerzenia. 
* Jeśli kopia zapasowa maszyny Wirtualnej ma statycznego adresu IP post przywracania, przywróconej maszyny Wirtualnej ma dynamicznego adresu IP, aby uniknąć konfliktu podczas tworzenia przywróconą maszyną Wirtualną. Dowiedz się więcej o sposób [dodać statyczny adres IP z przywróconą maszyną wirtualną](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Przywróconą maszyną Wirtualną nie ma wartość dostępności. Firma Microsoft zaleca używanie dysków przywracania możliwość [dodać zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) podczas tworzenia maszyny Wirtualnej za pomocą programu PowerShell lub szablonów przy użyciu przywrócić dysków. 


## <a name="backup-for-restored-vms"></a>Tworzenie kopii zapasowej dla przywróconej maszyny wirtualne
Jeśli maszyna wirtualna jest przywrócone do tej samej grupie zasobów o takiej samej nazwie co maszyna wirtualna pierwotnie kopii zapasowej, kopia zapasowa nadal przy przywracaniu post maszyny Wirtualnej. Jeśli przywróconej maszyny Wirtualnej do innej grupie zasobów lub określić inną nazwę przywróconą maszyną Wirtualną, maszyny Wirtualnej jest traktowana tak, jakby była nowej maszyny Wirtualnej. Należy skonfigurować tworzenia kopii zapasowej dla maszyny Wirtualnej przywróconej.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Przywracanie maszyny Wirtualnej podczas awarii centrum danych Azure
Kopia zapasowa Azure umożliwia przywracanie kopii zapasowej maszyn wirtualnych do sparowanego centrum danych w przypadku awarii napotka podstawowego centrum danych, gdy maszyny wirtualne są uruchomione i skonfigurowane być geograficznie nadmiarowego magazynu kopii zapasowych. W takich scenariuszach wybierz konto magazynu, który jest obecny w parach centrum danych. Pozostała część procesu przywracania jest taka sama. Kopii zapasowej używa usługi obliczeniowe z geograficznie sparowanego do utworzenia przywróconą maszyną Wirtualną. Aby uzyskać więcej informacji, zobacz [odporności centrum danych Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Przywracanie kontrolera domeny maszyny wirtualne
Tworzenie kopii zapasowych kontrolera domeny (DC) maszyn wirtualnych jest obsługiwany scenariusz przy użyciu kopii zapasowej. Jednak należy zachować ostrożność podczas procesu przywracania. Proces przywracania poprawne zależy od struktura domeny. Ogólnie rzecz biorąc masz pojedynczy kontroler domeny w jednej domenie. Najczęściej w przypadku obciążeń produkcyjnych masz jednej domeny z wielu kontrolerów domeny, prawdopodobnie w przypadku niektórych kontrolerów domeny lokalnej. Na koniec konieczne może być lesie z wieloma domenami. 

Z perspektywy usługi Active Directory maszyna wirtualna Azure jest podobnie jak inne maszyny Wirtualnej w nowoczesnych obsługiwanej funkcji hypervisor. Główna różnica z lokalnymi funkcji hypervisor jest dostępnej na platformie Azure jest konsoli maszyny Wirtualnej. Konsola jest wymagana dla niektórych scenariuszach, takich jak odzyskiwanie za pomocą odzyskiwania zera (BMR) — typ kopii zapasowej. Jednak przywrócenie maszyny Wirtualnej z magazynu kopii zapasowych nie zastępuje pełnego odzyskiwania systemu od ZERA. Trybu przywracania usług katalogowych (DSRM) jest również dostępna, więc działało wszystkie scenariusze odzyskiwania usługi Active Directory. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące tworzenia kopii zapasowej i przywracania dla zwirtualizowanych kontrolerów domeny](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) i [planowanie odzyskiwania lasu usługi Active Directory](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Pojedynczy kontroler domeny w jednej domenie
Można przywrócić maszyny Wirtualnej (na przykład innych maszyn wirtualnych) z portalu Azure lub za pomocą programu PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Wiele kontrolerów domeny w jednej domenie
Jeśli inne kontrolery domeny w tej samej domeny, można połączyć się za pośrednictwem sieci, takich jak żadnej maszyny Wirtualnej można przywrócić kontrolera domeny. Jeśli jest ostatniego pozostałego kontrolera domeny w domenie lub odzyskiwania w sieci izolowanej jest wykonywane, musi następować procedury odzyskiwania lasu.

### <a name="multiple-domains-in-one-forest"></a>Wiele domen w jednym lesie
Jeśli inne kontrolery domeny w tej samej domeny, można połączyć się za pośrednictwem sieci, takich jak żadnej maszyny Wirtualnej można przywrócić kontrolera domeny. We wszystkich innych przypadkach zaleca się przywrócenie lasu.

## <a name="restore-vms-with-special-network-configurations"></a>Przywracanie maszyn wirtualnych z konfiguracjami sieci specjalne
Istnieje możliwość kopie zapasowe i przywracanie maszyn wirtualnych z następujących konfiguracji sieciowych. Jednak te konfiguracje wymagają niektórych szczególną uwagę podczas przechodzenia przez proces przywracania:

* Maszyny wirtualne w ramach usługi równoważenia obciążenia (wewnętrznych i zewnętrznych)
* Maszyny wirtualne z wielu zastrzeżonych adresów IP
* Maszyny wirtualne z wieloma kartami sieciowymi

> [!IMPORTANT]
> Podczas tworzenia konfiguracji specjalnych sieci dla maszyn wirtualnych należy tworzyć maszyny wirtualne z przywróconą dysków za pomocą programu PowerShell.
>
>

Aby pełni odtworzyć maszyn wirtualnych po przywróceniu do dysku, wykonaj następujące kroki:

1. Przywracanie dysków z usług odzyskiwania magazynu przy użyciu [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. Utwórz konfigurację maszyny Wirtualnej wymagana dla usługi równoważenia obciążenia / wiele kart/wiele zastrzeżony adres IP za pomocą poleceń cmdlet programu PowerShell. Umożliwia ona tworzenie maszyny Wirtualnej z konfiguracją, które mają:

   a. Tworzenie maszyny Wirtualnej w usłudze w chmurze z [wewnętrznego modułu równoważenia obciążenia](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Utwórz maszynę Wirtualną do nawiązania połączenia [modułu równoważenia obciążenia internetowy](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Utwórz maszynę Wirtualną z [wiele kart sieciowych](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Utwórz maszynę Wirtualną z [wielu zastrzeżone adresy IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Kolejne kroki
Teraz, można przywrócić maszyn wirtualnych, zobacz artykuł dotyczący rozwiązywania problemów, aby uzyskać informacje o typowych problemów z maszynami wirtualnymi. Ponadto zapoznaj się z artykułu na temat zarządzania zadania związane z maszyn wirtualnych.

* [Rozwiązywanie problemów z błędami](backup-azure-vms-troubleshoot.md#restore)
* [Zarządzanie maszynami wirtualnymi](backup-azure-manage-vms.md)
