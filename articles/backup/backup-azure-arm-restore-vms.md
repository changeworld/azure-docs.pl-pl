---
title: Przywracanie maszyn wirtualnych przy użyciu witryny Azure portal
description: Przywracanie maszyny wirtualnej platformy Azure z punktu odzyskiwania przy użyciu witryny Azure portal
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 4bb0a07db39f5f9953a1e41e55b8be28fcace71b
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668815"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Jak przywrócić dane maszyny wirtualnej platformy Azure w witrynie Azure portal

W tym artykule opisano sposób przywracania danych maszyny Wirtualnej platformy Azure z punktów odzyskiwania przechowywanych w magazynach usług [Azure Backup](backup-overview.md) Recovery Services.

## <a name="restore-options"></a>Opcje przywracania

Usługa Azure Backup udostępnia wiele sposobów przywracania maszyny wirtualnej.

**Opcja Przywróć** | **Szczegóły**
--- | ---
**Utworzenie nowej maszyny wirtualnej.** | Szybko tworzy i pobiera podstawową maszynę wirtualną i działa z punktu przywracania.<br/><br/> Można określić nazwę maszyny Wirtualnej, wybrać grupę zasobów i sieć wirtualną (VNet), w której zostanie ona umieszczona, i określić konto magazynu dla przywróconej maszyny Wirtualnej. Nowa maszyna wirtualna musi zostać utworzona w tym samym regionie co źródłowy maszynę wirtualna.
**Przywracanie dysku** | Przywraca dysk maszyny Wirtualnej, który następnie może służyć do tworzenia nowej maszyny Wirtualnej.<br/><br/> Usługa Azure Backup udostępnia szablon ułatwiające dostosowywanie i tworzenie maszyny Wirtualnej. <br/><br> Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny Wirtualnej i utworzenia maszyny Wirtualnej.<br/><br/> Dyski są kopiowane do określonej grupy zasobów.<br/><br/> Alternatywnie można dołączyć dysk do istniejącej maszyny Wirtualnej lub utworzyć nową maszynę wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę wirtualną, dodać ustawienia konfiguracji, których nie było w czasie tworzenia kopii zapasowej, lub dodać ustawienia, które muszą być skonfigurowane przy użyciu szablonu lub programu PowerShell.
**Zamiana istniejącego** | Można przywrócić dysk i użyć go do zastąpienia dysku na istniejącej maszynie wirtualnej.<br/><br/> Bieżąca maszyna wirtualna musi istnieć. Jeśli ta opcja została usunięta, nie można jej użyć.<br/><br/> Usługa Azure Backup wykonuje migawkę istniejącej maszyny Wirtualnej przed wymianą dysku i przechowuje ją w określonej lokalizacji przejściowej. Istniejące dyski podłączone do maszyny Wirtualnej są zastępowane wybranym punktem przywracania.<br/><br/> Migawka jest kopiowana do magazynu i zachowywana zgodnie z zasadami przechowywania. <br/><br/> Po operacji wymiany dysku oryginalny dysk jest zachowywany w grupie zasobów. Można ręcznie usunąć oryginalne dyski, jeśli nie są one potrzebne. <br/><br/>Zastępowanie istniejących jest obsługiwane dla niezaszyfrowanych zarządzanych maszyn wirtualnych. Nie jest obsługiwany dla dysków niezarządzanych, [uogólnionych maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)ani maszyn wirtualnych [utworzonych przy użyciu obrazów niestandardowych.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżąca maszyna wirtualna, liczba dysków w punkcie przywracania będzie odzwierciedlać tylko konfigurację maszyny Wirtualnej.<br><br> Zastąp istniejące nie jest obsługiwane dla maszyn wirtualnych z połączonych zasobów (takich jak [przypisane przez użytkownika tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lub [Magazynu kluczy),](https://docs.microsoft.com/azure/key-vault/key-vault-overview)ponieważ kopia zapasowa klient-aplikacja nie ma uprawnień do tych zasobów podczas wykonywania przywracania.<br/><br/>Klasyczne maszyny wirtualne nie są obsługiwane.
**Region krzyżowy (region pomocniczy)** | Przywracanie między regionem może służyć do przywracania maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest [regionem sparowanym na platformie Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Można przywrócić wszystkie maszyny wirtualne platformy Azure dla wybranego punktu odzyskiwania, jeśli kopia zapasowa jest wykonywana w regionie pomocniczym.<br><br> Ta funkcja jest dostępna dla poniższych opcji:<br> * [Tworzenie maszyny Wirtualnej](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Przywracanie dysków](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Obecnie nie obsługujemy opcji [Zamień istniejące dyski.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Uprawnienia<br> Operację przywracania w regionie pomocniczym mogą wykonywać administratorzy kopii zapasowych i administratorzy aplikacji.

> [!NOTE]
> Można również odzyskać określone pliki i foldery na maszynie Wirtualnej platformy Azure. [Dowiedz się więcej](backup-azure-restore-files-from-vm.md).
>
> Jeśli używasz [najnowszej wersji](backup-instant-restore-capability.md) usługi Azure Backup dla maszyn wirtualnych platformy Azure (znanej jako przywracanie błyskawiczne), migawki są przechowywane przez maksymalnie siedem dni i możesz przywrócić maszynę wirtualną z migawek przed wysłaniem danych kopii zapasowej do magazynu. Jeśli chcesz przywrócić maszynę wirtualną z kopii zapasowej z ostatnich siedmiu dni, szybciej jest przywrócić z migawki, a nie z magazynu.

## <a name="storage-accounts"></a>Konta magazynu

Niektóre szczegóły dotyczące kont magazynu:

- **Utwórz maszynę wirtualną:** Podczas tworzenia nowej maszyny Wirtualnej maszyna wirtualna zostanie umieszczona na określonym koncie magazynu.
- **Przywróć dysk:** Podczas przywracania dysku dysk jest kopiowany do określonego konta magazynu. Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny Wirtualnej. Ten szablon jest umieszczany na określonym koncie magazynu.
- **Zamień dysk:** Po wymianie dysku na istniejącej maszynie wirtualnej usługa Azure Backup wykonuje migawkę istniejącej maszyny Wirtualnej przed wymianą dysku. Migawka jest przechowywana w określonej lokalizacji przemieszczania (koncie magazynu). To konto magazynu służy do tymczasowego przechowywania migawki podczas procesu przywracania i zaleca się utworzenie nowego konta, aby to zrobić, które można łatwo usunąć później.
- **Lokalizacja konta magazynu:** Konto magazynu musi znajdować się w tym samym regionie co przechowalnia. Wyświetlane są tylko te konta. Jeśli w lokalizacji nie ma żadnych kont magazynu, należy je utworzyć.
- **Typ magazynu:** magazyn obiektów blob nie jest obsługiwany.
- **Nadmiarowość pamięci masowej:** Magazyn strefowy (ZRS) nie jest obsługiwany. Informacje o replikacji i nadmiarowości dla konta są wyświetlane w nawiasach po nazwie konta.
- **Pamięć masowa w jakości:**
  - Podczas przywracania maszyn wirtualnych innych niż premium konta magazynu w jakości nie są obsługiwane.
  - Podczas przywracania zarządzanych maszyn wirtualnych konta magazynu w jakości skonfigurowane z regułami sieciowymi nie są obsługiwane.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby przywrócić maszynę wirtualną (utworzyć nową maszynę wirtualną), upewnij się, że masz poprawne uprawnienia kontroli dostępu opartej na [rolach](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) (RBAC) dla operacji Przywracanie maszyny Wirtualnej.

Jeśli nie masz uprawnień, możesz [przywrócić dysk](#restore-disks), a następnie po przywróceniu dysku, można użyć [szablonu,](#use-templates-to-customize-a-restored-vm) który został wygenerowany w ramach operacji przywracania, aby utworzyć nową maszynę wirtualną.

## <a name="select-a-restore-point"></a>Wybieranie punktu przywracania

1. W przechowalni skojarzonej z maszyną wirtualną, którą chcesz przywrócić, kliknij pozycję **Zapas elementów** > **usługi Azure Virtual Machine**.
2. Kliknij maszynę wirtualną. Domyślnie na pulpicie nawigacyjnym maszyny Wirtualnej są wyświetlane punkty odzyskiwania z ostatnich 30 dni. Można wyświetlić punkty odzyskiwania starsze niż 30 dni lub filtrować, aby znaleźć punkty odzyskiwania na podstawie dat, zakresów czasu i różnych typów spójności migawki.
3. Aby przywrócić maszynę wirtualną, kliknij przycisk **Przywróć maszynę wirtualną**.

    ![Punkt przywracania](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Wybierz punkt przywracania, który ma być używany do odzyskiwania.

## <a name="choose-a-vm-restore-configuration"></a>Wybieranie konfiguracji przywracania maszyny Wirtualnej

1. W **konfiguracji Przywracanie**wybierz opcję przywracania:
    - **Utwórz nowy**: Użyj tej opcji, jeśli chcesz utworzyć nową maszynę wirtualną. Można utworzyć maszynę wirtualną z prostymi ustawieniami lub przywrócić dysk i utworzyć dostosowaną maszynę wirtualną.
    - **Zastąp istniejące**: Użyj tej opcji, jeśli chcesz zastąpić dyski na istniejącej maszynie wirtualnej.

        ![Kreator przywracania konfiguracji](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Określ ustawienia wybranej opcji przywracania.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Jako jedna z [opcji przywracania](#restore-options)można szybko utworzyć maszynę wirtualną z podstawowymi ustawieniami z punktu przywracania.

1. W **obszarze Przywracanie konfiguracji** > **Utwórz nowy** > **typ przywracania**wybierz pozycję **Utwórz maszynę wirtualną**.
2. W **nazwie maszyny wirtualnej**określ maszynę wirtualną, która nie istnieje w subskrypcji.
3. W **grupie zasobów**wybierz istniejącą grupę zasobów dla nowej maszyny Wirtualnej lub utwórz nową o unikatowej nazwie. Jeśli zostanie przypisana nazwa, która już istnieje, platforma Azure przypisuje grupie taką samą nazwę jak maszyna wirtualna.
4. W **sieci wirtualnej**wybierz sieć wirtualną, w której zostanie umieszczona maszyna wirtualna. Zostaną wyświetlone wszystkie sieci wirtualne skojarzone z subskrypcją. Wybierz podsieć. Pierwsza podsieć jest zaznaczona domyślnie.
5. W **lokalizacji magazynu**określ konto magazynu dla maszyny Wirtualnej. [Dowiedz się więcej](#storage-accounts).

    ![Kreator przywracania konfiguracji](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. W **obszarze Konfiguracyjny przycisk Przywracania**wybierz pozycję **OK**. W **trybie Przywracanie**kliknij przycisk **Przywróć,** aby wyzwolić operację przywracania.

## <a name="restore-disks"></a>Przywracanie dysków

Jako jedna z [opcji przywracania](#restore-options)można utworzyć dysk z punktu przywracania. Następnie za pomocą dysku można wykonać jedną z następujących czynności:

- Użyj szablonu, który jest generowany podczas operacji przywracania, aby dostosować ustawienia i wyzwolić wdrożenie maszyny Wirtualnej. Edytuj domyślne ustawienia szablonu i prześlij szablon do wdrożenia maszyny Wirtualnej.
- [Dołącz przywrócone dyski](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) do istniejącej maszyny Wirtualnej.
- [Utwórz nową maszynę wirtualną](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) z przywróconych dysków przy użyciu programu PowerShell.

1. W **konfiguracji** > **Przywracanie Utwórz nowy** > **typ przywracania**wybierz pozycję **Przywróć dyski**.
2. W **grupie zasobów**wybierz istniejącą grupę zasobów dla przywróconych dysków lub utwórz nową grupę o unikatowej nazwie.
3. W **obszarze Konto magazynu**określ konto, na które mają być kopiowane dyski VHD. [Dowiedz się więcej](#storage-accounts).

    ![Ukończono konfigurację odzyskiwania](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. W **obszarze Konfiguracyjny przycisk Przywracania**wybierz pozycję **OK**. W **trybie Przywracanie**kliknij przycisk **Przywróć,** aby wyzwolić operację przywracania.

Gdy maszyna wirtualna używa dysków zarządzanych i wybierz opcję **Utwórz maszynę wirtualną,** usługa Azure Backup nie używa określonego konta magazynu. W przypadku **przywracania dysków** i **przywracania błyskawicznego**konto magazynu jest używane tylko do przechowywania szablonu. Dyski zarządzane są tworzone w określonej grupie zasobów.
Gdy maszyna wirtualna używa dysków niezarządzanych, są przywracane jako obiekty blob do konta magazynu.

### <a name="use-templates-to-customize-a-restored-vm"></a>Dostosowywanie przywróconej maszyny Wirtualnej za pomocą szablonów

Po przywróceniu dysku użyj szablonu, który został wygenerowany w ramach operacji przywracania, aby dostosować i utworzyć nową maszynę wirtualną:

1. Otwórz **szczegóły zadania przywracania** dla odpowiedniego zadania.

2. W **obszarze Przywróć szczegóły zadania**wybierz pozycję **Wdrażanie szablonu,** aby zainicjować wdrażanie szablonu.

    ![Przywracanie pracy w drążeniu](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Aby dostosować ustawienie maszyny Wirtualnej podane w szablonie, kliknij przycisk **Edytuj szablon**. Aby dodać więcej dostosowań, kliknij przycisk **Edytuj parametry**.
    - [Dowiedz się więcej](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) o wdrażaniu zasobów z szablonu niestandardowego.
    - [Dowiedz się więcej](../azure-resource-manager/templates/template-syntax.md) o szablonach tworzenia.

   ![Załaduj wdrożenie szablonu](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Wprowadź wartości niestandardowe maszyny Wirtualnej, zaakceptuj **warunki i** kliknij pozycję **Kup**.

   ![Przesyłanie wdrożenia szablonu](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Zastępowanie istniejących dysków

Jako jedna z [opcji przywracania](#restore-options)można zastąpić istniejący dysk maszyny Wirtualnej wybranym punktem przywracania. [Przejrzyj](#restore-options) wszystkie opcje przywracania.

1. W **yw.** **Replace existing**
2. W **obszarze Typ przywracania**wybierz pozycję **Zamień dysk/s**. Jest to punkt przywracania, który będzie używany zastąpić istniejące dyski maszyn wirtualnych.
3. W **obszarze Lokalizacja przemieszczania**określ, gdzie migawki bieżących dysków zarządzanych powinny być zapisywane podczas procesu przywracania. [Dowiedz się więcej](#storage-accounts).

   ![Kreator przywracania konfiguracji Zamień istniejące](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Przywracanie między regionami

Jako jedna z [opcji przywracania](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options), Cross Region Restore (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest regionem sparowanym na platformie Azure.

Aby włączyć funkcję do funkcji podczas podglądu, przeczytaj [sekcję Przed rozpoczęciem](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Aby sprawdzić, czy crr jest włączony, postępuj zgodnie z instrukcjami w [temacie Konfigurowanie przywracania między regionami](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Wyświetlanie elementów kopii zapasowej w regionie pomocniczym

Jeśli crr jest włączona, można wyświetlić elementy kopii zapasowej w regionie pomocniczym.

1. W portalu przejdź do pozycji**Elementy kopii zapasowej** **magazynu** > usług recovery Services
2. Kliknij **pozycję Region pomocniczy,** aby wyświetlić elementy w regionie pomocniczym.

![Maszyny wirtualne w regionie pomocniczym](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Wybierz region pomocniczy](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Przywracanie w regionie pomocniczym

Środowisko przywracania regionu pomocniczego będzie podobne do środowiska przywracania regionu podstawowego. Podczas konfigurowania szczegółów w bloku Konfiguracyjny przywracania w celu skonfigurowania przywracania zostanie wyświetlony monit o podanie tylko pomocniczych parametrów regionu.

![Wybieranie maszyny wirtualnej do przywrócenia](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Wybierz punkt przywracania](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Przywróć konfigurację](./media/backup-azure-arm-restore-vms/rest-config.png)

![Powiadomienie o przywracaniu wyzwalacza w toku](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Aby przywrócić i utworzyć maszynę [wirtualną,](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm)zobacz Tworzenie maszyny Wirtualnej .
- Aby przywrócić jako dysk, należy zapoznać się [z polem Przywróć dyski](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Po wyzwoleniu przywracania i w fazie transferu danych nie można anulować zadania przywracania.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorowanie zadań przywracania regionu pomocniczego

1. Z portalu przejdź do zadań**kopii zapasowej** **magazynu** > usług odzyskiwania
2. Kliknij **pozycję Region pomocniczy,** aby wyświetlić elementy w regionie pomocniczym.

![Zadania tworzenia kopii zapasowych filtrowane](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami

Istnieje wiele typowych scenariuszy, w których może być konieczne przywrócenie maszyn wirtualnych.

**Scenariusz** | **Wskazówki**
--- | ---
**Przywracanie maszyn wirtualnych przy użyciu korzyści użycia hybrydowego** | Jeśli maszyna wirtualna z systemem Windows korzysta z [licencji Hybrid Use Benefit (HUB),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)przywróć dyski i utwórz nową maszynę wirtualną przy użyciu dostarczonego szablonu (z **typem licencji** ustawionym na **Windows_Server)** lub Programem PowerShell.  To ustawienie można również zastosować po utworzeniu maszyny Wirtualnej.
**Przywracanie maszyn wirtualnych podczas awarii centrum danych platformy Azure** | Jeśli magazyn używa GRS i podstawowego centrum danych dla maszyny Wirtualnej ujmuje, usługa Azure Backup obsługuje przywracanie kopii zapasowych maszyn wirtualnych do sparowanego centrum danych. Wybierz konto magazynu w sparowanym centrum danych i przywracasz go normalnie. Usługa Azure Backup używa usługi obliczeniowej w sparowanym regionie do utworzenia przywróconej maszyny Wirtualnej. [Dowiedz się więcej](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) o odporności centrum danych.
**Przywracanie maszyny Wirtualnej pojedynczego kontrolera domeny w jednej domenie** | Przywróć maszynę wirtualną, jak każdą inną maszynę wirtualną. Należy pamiętać, że:<br/><br/> Z punktu widzenia usługi Active Directory maszyna wirtualna platformy Azure jest jak każda inna maszyna wirtualna.<br/><br/> Dostępny jest również tryb przywracania usług katalogowych (DSRM), dzięki czemu wszystkie scenariusze odzyskiwania usługi Active Directory są opłacalne. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) o zagadnieniach tworzenia kopii zapasowych i przywracania zwirtualizowanych kontrolerów domeny.
**Przywracanie wielu maszyn wirtualnych kontrolera domeny w jednej domenie** | Jeśli można uzyskać dostęp do innych kontrolerów domeny w tej samej domenie za pośrednictwem sieci, można przywrócić kontroler domeny, jak każdą maszynę wirtualną. Jeśli jest to ostatni pozostały kontroler domeny w domenie lub wykonywane jest odzyskiwanie w izolowanej sieci, użyj [odzyskiwania lasu](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Przywracanie wielu domen w jednym lesie** | Zalecamy [odzyskiwanie lasów](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Przywracanie bez metalu** | Główną różnicą między maszynami wirtualnymi platformy Azure a lokalnymi hipernadzorcami jest to, że na platformie Azure nie ma dostępnej konsoli maszyn wirtualnych. Konsola jest wymagana w niektórych scenariuszach, takich jak odzyskiwanie przy użyciu kopii zapasowej typu odzyskiwania systemu operacyjnego (BMR) typu . Jednak przywracanie maszyny Wirtualnej z magazynu jest pełnym zamiennikiem BMR.
**Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami sieci** | Specjalne konfiguracje sieci obejmują maszyny wirtualne korzystające z wewnętrznego lub zewnętrznego równoważenia obciążenia, przy użyciu wielu kart sieciowych lub wielu zastrzeżonych adresów IP. Przywracasz te maszyny wirtualne za pomocą [opcji przywróć dysk](#restore-disks). Ta opcja sprawia, że kopia VHD na określonym koncie magazynu, a następnie można utworzyć maszynę wirtualną z [wewnętrznym](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) lub [zewnętrznym](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) modułem równoważenia obciążenia, [wieloma kartami SIECI I sieci lub](../virtual-machines/windows/multiple-nics.md)wieloma [zastrzeżonymi adresami IP,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)zgodnie z konfiguracją.
**Sieciowa grupa zabezpieczeń (NSG) w sieciowej/podsieci** | Kopia zapasowa maszyny Wirtualnej platformy Azure obsługuje informacje o kopii zapasowej i przywracaniu sieci płciowych na poziomie sieci wirtualnej, podsieci i karty sieciowej.
**Maszyny wirtualne przypięte do strefy** | Usługa Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn wirtualnych przypiętych strefowo. [Dowiedz się więcej](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania

Po wyzwoleniu operacji przywracania usługa tworzenia kopii zapasowych tworzy zadanie śledzenia. Usługa Azure Backup wyświetla powiadomienia o zadaniu w portalu. Jeśli nie są one widoczne, wybierz symbol **Powiadomienia,** a następnie wybierz pozycję **Wyświetl wszystkie zadania,** aby wyświetlić stan procesu przywracania.

![Wyzwalanie przywracania](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Przywracanie ścieżki w następujący sposób:

1. Aby wyświetlić operacje dla zadania, kliknij hiperłącze powiadomień. Alternatywnie w przechowalni kliknij pozycję **Zadania kopii zapasowej**, a następnie kliknij odpowiednią maszynę wirtualną.

    ![Lista maszyn wirtualnych w przechowalni](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Aby monitorować postęp przywracania, kliknij dowolne zadanie przywracania o stanie **W toku**. Spowoduje to wyświetlenie paska postępu, na którym są wyświetlane informacje o postępie przywracania:

    - **Szacowany czas przywracania:** Początkowo zapewnia czas, który został ukończony do ukończenia operacji przywracania. W miarę postępu operacji czas pracy zmniejsza się i osiąga zero po zakończeniu operacji przywracania.
    - **Procent przywracania**. Pokazuje procent operacji przywracania, która została wykonana.
    - **Liczba bajtów przeniesionych:** Jeśli przywracasz, tworząc nową maszynę wirtualną, pokazuje bajty, które zostały przeniesione względem całkowitej liczby bajtów, które mają zostać przeniesione.

## <a name="post-restore-steps"></a>Kroki po przywróceniu

Istnieje wiele rzeczy, na które należy zwrócić uwagę po przywróceniu maszyny Wirtualnej:

- Rozszerzenia obecne podczas konfiguracji kopii zapasowej są instalowane, ale nie włączone. Jeśli widzisz problem, zainstaluj ponownie rozszerzenia.
- Jeśli kopia zapasowa maszyny Wirtualnej miał statyczny adres IP, przywrócona maszyna wirtualna będzie miała dynamiczny adres IP, aby uniknąć konfliktu. Do [przywróconej maszyny Wirtualnej](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)można dodać statyczny adres IP .
- Przywrócona maszyna wirtualna nie ma zestawu dostępności. Jeśli używasz opcji dysku przywracania, można [określić zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) podczas tworzenia maszyny Wirtualnej z dysku przy użyciu dostarczonego szablonu lub programu PowerShell.
- Jeśli używasz dystrybucji Linuksa opartej na chmurze, takiej jak Ubuntu, ze względów bezpieczeństwa hasło jest blokowane po przywróceniu. Użyj rozszerzenia VMAccess na przywróconej maszynie [wirtualnej,](../virtual-machines/linux/reset-password.md)aby zresetować hasło . Zalecamy użycie kluczy SSH w tych dystrybucjach, więc nie trzeba resetować hasła po przywróceniu.
- Jeśli nie możesz uzyskać dostępu do maszyny Wirtualnej po przywróceniu z powodu zerwania relacji z kontrolerem domeny, wykonaj poniższe czynności, aby przywołać maszynę wirtualną:
  - Dołącz dysk systemu operacyjnego jako dysk danych do odzyskanej maszyny Wirtualnej.
  - Ręcznie zainstaluj agenta maszyny Wirtualnej, jeśli okaże się, że usługa Azure Agent nie odpowiada, wykonując to [łącze.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)
  - Włącz dostęp konsoli szeregowej na maszynie Wirtualnej, aby umożliwić dostęp do wiersza polecenia do maszyny Wirtualnej

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Po przebudowie maszyny Wirtualnej użyj witryny Azure Portal, aby zresetować konto administratora lokalnego i hasło
  - Użyj dostępu do konsoli szeregowej i współrzędnościowej maszyny cmd, aby odłączyć maszynę Wirtualną od domeny

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Po odłączeniu maszyny Wirtualnej i ponownym uruchomieniu, będzie można pomyślnie RDP do maszyny Wirtualnej z lokalnymi poświadczeniami administratora i ponownie dołączyć maszynę wirtualną z powrotem do domeny pomyślnie.

## <a name="backing-up-restored-vms"></a>Tworzenie kopii zapasowej przywróconych maszyn wirtualnych

- Jeśli przywrócono maszynę wirtualną do tej samej grupy zasobów o tej samej nazwie co pierwotnie utworzona kopia zapasowa maszyny Wirtualnej, kopia zapasowa będzie kontynuowana na maszynie Wirtualnej po przywróceniu.
- Jeśli maszyna wirtualna została przywrócona do innej grupy zasobów lub określono inną nazwę przywróconej maszyny Wirtualnej, należy skonfigurować kopię zapasową dla przywróconej maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Jeśli występują trudności podczas procesu przywracania, [przejrzyj](backup-azure-vms-troubleshoot.md#restore) typowe problemy i błędy.
- Po przywróceniu maszyny wirtualnej dowiedz się więcej o [zarządzaniu maszynami wirtualnymi](backup-azure-manage-vms.md)
