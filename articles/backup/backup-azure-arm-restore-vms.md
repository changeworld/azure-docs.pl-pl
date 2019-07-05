---
title: 'Usługa Azure Backup: Przywracanie maszyn wirtualnych przy użyciu witryny Azure portal'
description: Przywracanie maszyn wirtualnych z punktu odzyskiwania przy użyciu witryny Azure portal
services: backup
author: geethalakshmig
manager: vijayts
keywords: Przywracanie kopii zapasowych. jak przywrócić; punkt odzyskiwania;
ms.service: backup
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: geg
ms.openlocfilehash: 62e10f382882e70d488f9814cb00c2b86b8b9691
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460230"
---
# <a name="restore-azure-vms"></a>Przywracanie maszyn wirtualnych platformy Azure

W tym artykule opisano sposób przywracania danych maszyny Wirtualnej platformy Azure z punktów odzyskiwania przechowywanych na [kopia zapasowa Azure](backup-overview.md) Magazyny usługi Recovery Services.



## <a name="restore-options"></a>Opcje przywracania

Usługa Azure Backup udostępnia wiele sposobów, aby przywrócić maszynę Wirtualną.

**Opcja przywracania** | **Szczegóły**
--- | ---
**Utwórz nową maszynę Wirtualną** | Szybko tworzy i pobiera pracę podstawowej maszyny Wirtualnej z punktu przywracania.<br/><br/> Można określić nazwę dla maszyny Wirtualnej, wybierz grupę zasobów i virtual network (VNet) w której zostaną umieszczone i określ konto magazynu dla przywróconej maszyny Wirtualnej.
**Przywracanie dysku** | Przywraca dysku maszyny Wirtualnej, które następnie mogą służyć do tworzenia nowej maszyny Wirtualnej.<br/><br/> Usługa Azure Backup udostępnia szablon, aby pomóc użytkownikom w dostosowywaniu i tworzenie maszyny Wirtualnej. <br/><br> Zadania przywracania generuje szablon, możesz pobrać i umożliwia określenie niestandardowych ustawień maszyny Wirtualnej i tworzenie maszyny Wirtualnej.<br/><br/> Dyski są kopiowane do konta magazynu, które określisz.<br/><br/> Alternatywnie możesz Dołącz dysk do istniejącej maszyny Wirtualnej lub utworzyć nową maszynę Wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę Wirtualną, Dodaj ustawienia konfiguracji, które nie były dostępne w czasie wykonywania kopii zapasowej lub dodać ustawienia, które muszą być skonfigurowane przy użyciu szablonu lub programu PowerShell.
**Zamień istniejące** | Przywracanie dysku i użyć go do wymienić dysk na istniejącej maszynie Wirtualnej.<br/><br/> Musi istnieć bieżącej maszyny Wirtualnej. Jeśli zostanie usunięta ta opcja nie może być używana.<br/><br/> Usługa Azure Backup tworzy migawkę istniejącej maszyny Wirtualnej przed zamianą na dysku i zapisuje go w lokalizacji tymczasowej, które określisz. Istniejące dyski podłączone do maszyny Wirtualnej są zastępowane punkt przywracania.<br/><br/> Migawka jest kopiowane do magazynu i przechowywane zgodnie z zasadami przechowywania. <br/><br/> Zastąp istniejące jest obsługiwana dla niezaszyfrowane zarządzane maszyny wirtualne. Nie jest obsługiwana w przypadku dysków niezarządzanych [uogólniony maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), lub dla maszyn wirtualnych [utworzone za pomocą niestandardowych obrazów](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżącej maszyny Wirtualnej, następnie liczbę dysków w punkcie przywracania będzie odzwierciedlają tylko konfiguracji maszyny Wirtualnej.<br/><br/>


> [!NOTE]
> Można również odzyskać określonych plików i folderów na Maszynie wirtualnej platformy Azure. [Dowiedz się więcej](backup-azure-restore-files-from-vm.md).
>
> Jeśli korzystasz z [najnowszej wersji](backup-instant-restore-capability.md) z usługi Azure Backup dla maszyn wirtualnych platformy Azure (nazywanych, natychmiastowe przywracanie), migawki są przechowywane przez maksymalnie siedem dni, a przed wysłaniem danych kopii zapasowej w magazynie, można przywrócić Maszynę wirtualną z migawki. Przywracanie maszyny Wirtualnej z kopii zapasowej z ostatnich siedmiu dni jest szybsze do przywrócenia z migawki, a nie z magazynu.

## <a name="storage-accounts"></a>Konta magazynu

Informacje na temat kont magazynu:

- **Tworzenie maszyny Wirtualnej**: Podczas tworzenia nowej maszyny Wirtualnej, zostaną umieszczone maszyny Wirtualnej na koncie magazynu, które określisz.
- **Przywracanie dysku**: Podczas przywracania dysku dysku jest kopiowana do konta magazynu, które określisz. Zadania przywracania generuje szablon, który można pobrać i umożliwia określenie niestandardowych ustawień maszyny Wirtualnej. Ten szablon jest umieszczany w podanego konta magazynu.
- **Wymiana dysku**: Podczas zastępowania dysku, istniejącej maszyny wirtualnej usługi Azure Backup tworzy migawkę istniejącej maszyny Wirtualnej przed zamianą na dysku. Migawki są przechowywane w lokalizacji tymczasowej (konta), należy określić. To konto magazynu jest używane do tymczasowego przechowywania migawki podczas procesu przywracania, a firma Microsoft zaleca, aby utworzyć nowe konto, aby to zrobić, którą można łatwo usunąć później.
- **Lokalizacja konta magazynu** : Konto magazynu musi być w tym samym regionie co magazyn. Tylko te konta są wyświetlane. Jeśli nie ma żadnych kont magazynu w lokalizacji, należy ją utworzyć.
- **Typ magazynu** : Magazyn obiektów blob nie jest obsługiwane.
- **Nadmiarowość magazynu**: Magazyn strefowo nadmiarowy (ZRS) nie jest obsługiwane. Informacje o replikacji i nadmiarowości konta jest wyświetlana w nawiasie po nazwie konta. 
- **Usługa Premium storage**:
    - Podczas przywracania maszyn wirtualnych innych niż premium, kont usługi premium storage nie są obsługiwane.
    - W przypadku przywracania zarządzanych maszyn wirtualnych, kont usługi premium storage skonfigurowane za pomocą reguł sieci nie są obsługiwane.


## <a name="before-you-start"></a>Przed rozpoczęciem

Aby przywrócić Maszynę wirtualną (Utwórz nową maszynę Wirtualną) upewnij się, kontrola poprawne dostępu opartej na rolach (RBAC) [uprawnienia](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) dla operacji przywracania maszyny Wirtualnej.

Jeśli nie masz uprawnień, możesz to zrobić [przywracanie dysku](#restore-disks), a następnie po przywróceniu dysk możesz [szablon](#use-templates-to-customize-a-restored-vm) wygenerowany jako część operacji przywracania, aby utworzyć nową maszynę Wirtualną.



## <a name="select-a-restore-point"></a>Wybierz punkt przywracania

1. W magazynie skojarzony z maszyną Wirtualną, którą chcesz przywrócić, kliknij przycisk **kopii zapasowych elementów** > **maszyny wirtualnej platformy Azure**.
2. Kliknij Maszynę wirtualną. Domyślnie na pulpicie nawigacyjnym maszyn wirtualnych są wyświetlane punkty odzyskiwania z ostatnich 30 dni. Możesz wyświetlić punkty odzyskiwania starsze niż 30 dni lub filtr pod kątem wyszukiwania punktów odzyskiwania na podstawie daty, zakresów czasu i różnego rodzaju migawki spójności.
3. Aby przywrócić maszynę Wirtualną, kliknij przycisk **przywrócić maszynę Wirtualną**.

    ![Punkt przywracania](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Wybierz punkt przywracania do użycia podczas odzyskiwania.

## <a name="choose-a-vm-restore-configuration"></a>Wybierz konfigurację przywracania maszyny Wirtualnej

1. W **przywracania konfiguracji**, wybierz opcję przywracania:
    - **Utwórz nową**: Użyj tej opcji, jeśli chcesz utworzyć nową maszynę Wirtualną. Możesz utworzyć Maszynę wirtualną z prostymi ustawieniami, lub przywrócić dysk i tworzenia niestandardowych maszyn wirtualnych.
    - **Zamień istniejące**: Użyj tej opcji, jeśli chcesz zastąpić dyskami na istniejącej maszyny Wirtualnej.

        ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Określ ustawienia dla swoją opcję przywracania.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Jako jeden z [opcje przywracania](#restore-options), Utwórz Maszynę wirtualną szybko z podstawowymi ustawieniami z punktu przywracania.

1. W **przywracania konfiguracji** > **Utwórz nową** > **przywrócić typ**, wybierz opcję **Utwórz maszynę wirtualną** .
2. W **nazwę maszyny wirtualnej**, określ maszynę Wirtualną, która nie istnieje w subskrypcji.
3. W **grupy zasobów**, wybierz istniejącą grupę zasobów dla nowej maszyny Wirtualnej lub utworzyć nową globalnie unikatową nazwą. Jeśli przypiszesz nazwy, która już istnieje, platforma Azure przypisuje grupy taką samą nazwę jak maszyna wirtualna.
4. W **sieć wirtualna**, wybierz sieć wirtualną, w którym zostaną umieszczone maszyny Wirtualnej. Wyświetlane są wszystkie sieci wirtualne, skojarzony z subskrypcją. Wybierz podsieć. Pierwszej podsieci jest domyślnie zaznaczone.
5. W **lokalizacji magazynu**, określ konto magazynu dla maszyny Wirtualnej. [Dowiedz się więcej](#storage-accounts).

    ![Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. W **przywracania konfiguracji**, wybierz opcję **OK**. W **przywrócić**, kliknij przycisk **przywrócić** wyzwalanie operacji przywracania.


## <a name="restore-disks"></a>Przywróć dyski

Jako jeden z [opcje przywracania](#restore-options), możesz utworzyć dysk z punktu przywracania. Następnie za pomocą dysku, możesz wykonać jedną z następujących czynności:

- Użyj szablonu, który jest generowany podczas operacji przywracania Dostosowywanie ustawień w celu wyzwolenia wdrożenia maszyny Wirtualnej. Zmodyfikuj domyślne ustawienia szablonu, a następnie przesyłanie szablonu podczas wdrażania maszyny Wirtualnej.
- [Dołącz przywróconych dysków](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) do istniejącej maszyny Wirtualnej.
- [Utwórz nową maszynę Wirtualną](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) z przywróconych dysków przy użyciu programu PowerShell.

1. W **przywracania konfiguracji** > **Utwórz nową** > **przywrócić typ**, wybierz opcję **Przywróć dyski**.
2. W **grupy zasobów**, wybierz istniejącą grupę zasobów dla przywróconych dysków lub Utwórz nową globalnie unikatową nazwą.
3. W **konta magazynu**, określ konto do którego zostaną skopiowane wirtualne dyski twarde. [Dowiedz się więcej](#storage-accounts).

    ![Konfiguracja odzyskiwania została ukończona](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. W **przywracania konfiguracji**, wybierz opcję **OK**. W **przywrócić**, kliknij przycisk **przywrócić** wyzwalanie operacji przywracania.

Podczas przywracania maszyny Wirtualnej usługi Azure Backup nie używa konta magazynu. Jednak w przypadku programu **Przywróć dyski** i **natychmiastowe Przywracanie**, konto magazynu jest używane do przechowywania szablonu.

### <a name="use-templates-to-customize-a-restored-vm"></a>Szablony umożliwiają dostosowywanie przywróconą maszyną Wirtualną.

Po przywróceniu dysku Użyj szablonu, który został wygenerowany w ramach operacji przywracania, można dostosować i utworzyć nową maszynę Wirtualną:

1. Otwórz **szczegóły zadania przywracania** dla odpowiedniego zadania.

2. W **szczegóły zadania przywracania**, wybierz opcję **wdrażania szablonu** do inicjowania wdrożenia szablonu.

    ![Przechodzenie do szczegółów zadania przywracania](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Aby dostosować ustawienia maszyny Wirtualnej, udostępnionego w szablonie, kliknij przycisk **Edytuj szablon**. Jeśli chcesz dodać więcej dostosowań, kliknij przycisk **Edytuj parametry**.
    - [Dowiedz się więcej](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) informacji o wdrażaniu zasobów z szablonu niestandardowego.
    - [Dowiedz się więcej](../azure-resource-manager/resource-group-authoring-templates.md) o tworzeniu szablonów.

   ![Ładowanie szablonu wdrożenia](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Wprowadź własne wartości dla maszyny Wirtualnej, Zaakceptuj **warunków i postanowień** i kliknij przycisk **zakupu**.

   ![Przesyłanie szablonu wdrażania](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Zastąp istniejące dyski

Jako jeden z [opcje przywracania](#restore-options), można zastąpić istniejącego dysku maszyny Wirtualnej z wybranego punktu przywracania. [Przegląd](#restore-options) wszystkie opcje przywracania.

1. W **przywracania konfiguracji**, kliknij przycisk **zamienić istniejący**.
2. W **przywrócić typ**, wybierz opcję **zastąpić dysku/s**. Jest to punkt przywracania, który ma być używane Zastąp istniejące dyski maszyny Wirtualnej.
3. W **Lokalizacja tymczasowa**, określ, którym chcesz zapisać migawki bieżącego dyskami zarządzanymi podczas procesu przywracania. [Dowiedz się więcej](#storage-accounts).

   ![Zastąp istniejący Kreator konfiguracji przywracania](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami

Istnieje kilka typowych scenariuszy, w których konieczne może być przywracanie maszyn wirtualnych.

**Scenariusz** | **Wskazówki**
--- | ---
**Przywracanie maszyn wirtualnych za pomocą korzyści użycia hybrydowego** | Jeśli korzysta z maszyny Wirtualnej z systemem Windows [Licencjonowanie korzyści użycia hybrydowego (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Przywróć dyski i Utwórz nową maszynę Wirtualną za pomocą podanego szablonu (z **typu licencji** równa **Windows_Server**) , lub programu PowerShell.  To ustawienie można zastosować w taki sposób, po utworzeniu maszyny Wirtualnej.
**Przywracanie maszyn wirtualnych podczas awarii centrum danych platformy Azure** | Jeśli korzysta z magazynu GRS, ulegnie awarii głównego centrum danych dla maszyny Wirtualnej usługi Azure Backup obsługuje przywracanie kopii zapasowych maszyn wirtualnych w sparowanym centrum danych. Wybierz konto magazynu w sparowanym centrum danych, a następnie przywrócić w zwykły sposób. Usługa Azure Backup korzysta z usługi obliczeniowej w sparowanych lokalizacji można utworzyć przywróconej maszyny Wirtualnej. [Dowiedz się więcej](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) dotyczące odporności centrum danych.
**Przywracanie jednego kontrolera domeny maszyny Wirtualnej w jednej domenie** | Przywracanie maszyny Wirtualnej, takie jak każdej innej maszyny Wirtualnej. Należy pamiętać o następujących kwestiach:<br/><br/> Z perspektywy usługi Active Directory maszyny Wirtualnej platformy Azure jest jak każdej innej maszyny Wirtualnej.<br/><br/> Trybu przywracania usług katalogowych (DSRM) jest również dostępna, wszystkie scenariusze odzyskiwania usługi Active Directory są możliwe. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) dotyczących tworzenia kopii zapasowych i przywracania dla zwirtualizowanych kontrolerów domeny.
**Przywracanie kontrolera domeny wielu maszyn wirtualnych w jednej domenie** | Jeśli inne kontrolery domeny w tej samej domenie, można z Tobą skontaktować za pośrednictwem sieci, takich jak dowolnej maszyny Wirtualnej można przywrócić na kontrolerze domeny. Jeśli jest ostatnim pozostałe kontrolera domeny w domenie lub przeprowadzane jest Odzyskiwanie w sieci izolowanej, użyj [lasu odzyskiwania](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Przywracanie wielu domen w jednym lesie** | Firma Microsoft zaleca [lasu odzyskiwania](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Przywracanie bez systemu operacyjnego** | Główna różnica między maszynami wirtualnymi platformy Azure i funkcji hypervisor w środowisku lokalnym jest dostępna na platformie Azure jest nie konsoli maszyny Wirtualnej. Konsola jest wymagana dla niektórych scenariuszach, takich jak odzyskiwanie za pomocą odzyskiwania zera (BMR) — typ kopii zapasowej. Przywracanie maszyny Wirtualnej z magazynu jest jednak zastępuje pełnego odzyskiwania systemu od ZERA.
**Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami sieci** | Specjalne konfiguracje sieci obejmują maszyny wirtualne przy użyciu wewnętrzne lub zewnętrzne Równoważenie obciążenia, przy użyciu wielu kart Sieciowych lub wielu zastrzeżonych adresów IP. Przywróć te maszyny wirtualne za pomocą [przywracania dysku](#restore-disks). Ta opcja wykonuje kopię wirtualne dyski twarde do podanego konta magazynu, a następnie możesz utworzyć maszynę Wirtualną z [wewnętrzny](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) lub [zewnętrznych](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) usługi load balancer, [wiele kart Sieciowych](../virtual-machines/windows/multiple-nics.md), lub [wielu zastrzeżone adresy IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), zgodnie z konfiguracją.
**Sieciowa grupa zabezpieczeń (NSG) w kart Sieciowych/podsieć** | Kopia zapasowa maszyny Wirtualnej platformy Azure obsługuje tworzenia kopii zapasowych i przywracania dla sieciowej grupy zabezpieczeń informacji w sieci wirtualnej, podsieci i poziomie karty Sieciowej.
**Strefa przypięte maszyn wirtualnych** | Usługa Azure Backup obsługuje tworzenia kopii zapasowych i przywracanie nieupakowaną przypiętych maszyn wirtualnych. [Dowiedz się więcej](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania
Po użytkownik zainicjuje operację przywracania, usługa backup tworzy zadanie śledzenia. Usługa Azure Backup Wyświetla powiadomienia dotyczące zadania w portalu. Jeśli nie są one widoczne, kliknij polecenie **powiadomienia** symbolu, aby je zobaczyć.

![Przywracanie wyzwolone](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Śledź przywracania w następujący sposób:

1. Aby wyświetlić operacje dla zadania, kliknij hiperlink powiadomienia. Alternatywnie w magazynie, kliknij przycisk **zadania tworzenia kopii zapasowej**, a następnie kliknij przycisk odpowiednich maszyn wirtualnych.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Aby monitorować postęp przywracania, kliknij pozycję Wszystkie zadania przywracania ze stanem **w toku**. Spowoduje to wyświetlenie pasek postępu, który wyświetla informacje o postępie przywracania:

    - **Szacowany czas przywracania**: Początkowo zawiera czas potrzebny do ukończenia operacji przywracania. Podczas operacji w miarę, czas zmniejsza i osiągnie zero, po zakończeniu operacji przywracania.
    - **Wartość procentowa przywracania**. Przedstawia wartość procentową operacji przywracania, która została wykonana.
    - **Liczba bajtów przesłanych**: W przypadku przywracania, tworząc nową maszynę Wirtualną, pokazuje bajtów, które zostały przeniesione z całkowitą liczbą bajtów, które mają zostać przeniesione.

## <a name="post-restore-steps"></a>Wykonywane po przywróceniu kroki

Istnieje kilka kwestii, które należy pamiętać, po przywróceniu maszyny Wirtualnej:

- Rozszerzenia podczas konfiguracji kopii zapasowej są zainstalowane, ale nie jest włączona. Jeśli widzisz problem, należy ponownie zainstalować rozszerzenia.
- Jeśli kopia zapasowa maszyny Wirtualnej ma statyczny adres IP, przywrócona maszyna wirtualna ma dynamiczny adres IP, aby uniknąć konfliktu. Możesz [dodać statyczny adres IP do przywróconej maszyny Wirtualnej](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Przywrócona maszyna wirtualna nie ma dostępności zestawu. Jeśli używasz opcji przywracania dysków, a następnie możesz [zadat skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) podczas tworzenia maszyny Wirtualnej z dysku za pomocą podanego szablonu lub programu PowerShell.
- Jeśli używasz init oparte na chmurze dystrybucji systemu Linux, takie jak Ubuntu, ze względów bezpieczeństwa hasło zablokowaniu po przywróceniu. Należy użyć rozszerzenia VMAccess do przywróconej maszyny wirtualnej do [resetowania hasła](../virtual-machines/linux/reset-password.md). Firma Microsoft zaleca używanie kluczy SSH na tych dystrybucji, więc nie trzeba zresetować hasła po przywróceniu.


## <a name="backing-up-restored-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych z przywróconych

- Jeśli przywróconej maszyny Wirtualnej w tej samej grupie zasobów o takiej samej nazwie co maszyna wirtualna pierwotnie kopii zapasowej, kopii zapasowych jest kontynuowane na maszynie Wirtualnej po przywróceniu.
- Jeśli przywrócono maszyny Wirtualnej do innej grupy zasobów lub określić inną nazwę dla przywróconej maszyny Wirtualnej, należy skonfigurować kopię zapasową dla przywróconej maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli wystąpią problemy podczas procesu przywracania [Przejrzyj](backup-azure-vms-troubleshoot.md#restore) najczęściej występujących problemów i błędów.
- Po przywróceniu maszyny Wirtualnej, Dowiedz się więcej o [zarządzania maszynami wirtualnymi](backup-azure-manage-vms.md)
