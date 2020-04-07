---
title: Tworzenie magazynów usług odzyskiwania
description: W tym artykule dowiesz się, jak utworzyć magazyny usług odzyskiwania, które przechowują kopie zapasowe i punkty odzyskiwania.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 439f102e8f13bff63ab388be8f10df07ab2dc7d2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672844"
---
# <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usług odzyskiwania to jednostka, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn usług odzyskiwania zawiera również zasady tworzenia kopii zapasowych skojarzone z chronionymi maszynami wirtualnymi.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/backup-create-rs-vault/click-all-services.png)

3. W oknie dialogowym **Wszystkie usługi** wprowadź frazę **Recovery Services**. Lista filtrów zasobów zgodnie z wejściem. Na liście zasobów wybierz pozycję **Magazyny usług odzyskiwania**.

    ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Na **pulpicie** nawigacyjnym magazynów usług odzyskiwania wybierz pozycję **Dodaj**.

    ![Dodawanie magazynu usług odzyskiwania](./media/backup-create-rs-vault/add-button-create-vault.png)

    Zostanie otwarte okno dialogowe **Magazyn usług odzyskiwania.** Podaj wartości **nazwy,** **subskrypcji,** **grupy zasobów**i **lokalizacji**.

    ![Konfigurowanie magazynu usług odzyskiwania](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: Wprowadź przyjazną nazwę, aby zidentyfikować przechowalnię. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa, ale nie więcej niż 50 znaków. Nazwa musi zaczynać się od litery i składać się tylko z liter, cyfr i łączników.
   - **Subskrypcja**: Wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure.
   - **Grupa zasobów:** Użyj istniejącej grupy zasobów lub utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącego**, a następnie wybierz zasób z pola listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nowy** i wprowadź jej nazwę. Aby uzyskać pełne informacje o grupach zasobów, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Lokalizacja**: Wybierz region geograficzny przechowalni. Aby utworzyć magazyn w celu ochrony maszyn wirtualnych, magazyn **musi** znajdować się w tym samym regionie co maszyny wirtualne.

      > [!IMPORTANT]
      > Jeśli nie masz pewności co do lokalizacji maszyny Wirtualnej, zamknij okno dialogowe. Przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w kilku regionach, utwórz magazyn usług odzyskiwania w każdym regionie. Utwórz przechowalnię w pierwszej lokalizacji, zanim utworzysz przechowalnię dla innej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn usług odzyskiwania i usługa Azure Backup obsługują to automatycznie.
      >
      >

5. Gdy będziesz gotowy do utworzenia magazynu usług odzyskiwania, wybierz pozycję **Utwórz**.

    ![Tworzenie magazynu usług odzyskiwania](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu usług odzyskiwania może chwilę potrwać. Monitorowanie powiadomień o stanie w obszarze **Powiadomienia** w prawym górnym rogu portalu. Po utworzeniu przechowalni jest ona widoczna na liście magazynów usług odzyskiwania. Jeśli nie widzisz przechowalni, wybierz pozycję **Odśwież**.

     ![Odświeżanie listy magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Ustawianie nadmiarowości pamięci masowej

Usługa Azure Backup automatycznie obsługuje magazyn dla magazynu. Należy określić sposób replikowania tego magazynu.

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn. W sekcji **Ustawienia** kliknij pozycję **Właściwości**.
2. W **obszarze Właściwości**w obszarze **Konfiguracja kopii zapasowej**kliknij pozycję **Aktualizuj**.

3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Zaleca się, że jeśli używasz platformy Azure jako podstawowy punkt końcowy magazynu kopii zapasowych, nadal używać domyślnego ustawienia **geob nadmiarowe.**
   - Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
   - Dowiedz się więcej o [nadmiarowości geograficznej](../storage/common/storage-redundancy-grs.md) i [lokalnej.](../storage/common/storage-redundancy-lrs.md)

> [!NOTE]
> Przed skonfigurowaniem kopii zapasowych w przechowalni należy wykonać **zmianę typu replikacji magazynu** (Lokalnie nadmiarowa/ Geograficznie nadmiarowa) dla magazynu usług odzyskiwania. Po skonfigurowaniu kopii zapasowej opcja modyfikowania jest wyłączona i nie można zmienić **typu Replikacja magazynu**.

## <a name="set-cross-region-restore"></a>Ustawianie przywracania regionu krzyżowego

Jako jedna z opcji przywracania przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest [regionem sparowanym na platformie Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) Ta opcja umożliwia:

- przeprowadzać ćwiczenia, gdy istnieje wymóg audytu lub zgodności
- przywrócić maszynę wirtualną lub jej dysk, jeśli występuje awaria w regionie podstawowym.

Aby wybrać tę funkcję, wybierz **włącz przywracanie między regionem** z bloku **Konfiguracja kopii zapasowej.**

W tym procesie istnieją implikacje cenowe, ponieważ jest na poziomie magazynu.

>[!NOTE]
>Przed rozpoczęciem:
>
>- Przejrzyj [macierz pomocy technicznej](backup-support-matrix.md#cross-region-restore) dla listy obsługiwanych typów zarządzanych i regionów.
>- Funkcja przywracania między regionami (CRR) jest teraz dostępna w podglądzie we wszystkich regionach publicznych platformy Azure.
>- CRR to funkcja opt-in na poziomie przechowalni dla każdego magazynu GRS (domyślnie wyłączona).
>- Użyj następującego polecenia, aby włączyć subskrypcję dla tej funkcji:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Jeśli ta funkcja jest dołączana podczas publicznej ograniczonej wersji zapoznawczej, wiadomość e-mail z zatwierdzeniem recenzji będzie zawierać szczegóły zasad cenowych.
>- Po wyliczeniem zgody może upłynąć do 48 godzin, aby elementy kopii zapasowej były dostępne w regionach pomocniczych.
>- Obecnie crr jest obsługiwany tylko dla typu zarządzania kopiami zapasowymi — ARM Azure VM (klasyczna maszyna wirtualna platformy Azure nie będzie obsługiwana).  Gdy dodatkowe typy zarządzania obsługują crr, zostaną one **automatycznie** zarejestrowane.

### <a name="configure-cross-region-restore"></a>Konfigurowanie przywracania między regionami

Przechowalnia utworzona z nadmiarowością GRS zawiera opcję konfigurowania funkcji Przywracanie między regionami. Każdy magazyn GRS będzie miał baner, który będzie link do dokumentacji. Aby skonfigurować crr dla magazynu, przejdź do bloku Konfiguracja kopii zapasowej, który zawiera opcję, aby włączyć tę funkcję.

 ![Baner konfiguracji kopii zapasowej](./media/backup-azure-arm-restore-vms/banner.png)

1. W portalu przejdź do sekcji Usługi odzyskiwania > ustawienia > właściwości.
2. Kliknij **pozycję Włącz przywracanie między regionami w tym przechowalni,** aby włączyć tę funkcję.

   ![Przed kliknięciem przycisku Włącz przywracanie między regionem w tym przechowalni](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Po kliknięciu przycisku Włącz przywracanie między regionem w tym przechowalni](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Dowiedz się, jak [wyświetlić elementy kopii zapasowej w regionie pomocniczym](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Dowiedz się, jak [przywrócić w regionie pomocniczym](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Dowiedz się, jak [monitorować zadania przywracania regionu pomocniczego](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Modyfikowanie ustawień domyślnych

Przed skonfigurowaniem kopii zapasowych w przechowalni zalecamy przejrzenie ustawień domyślnych **dla typu replikacji magazynu** i ustawień **zabezpieczeń.**

- **Typ replikacji magazynu** domyślnie jest ustawiony na **Geo-nadmiarowy**. Po skonfigurowaniu kopii zapasowej opcja modyfikowania jest wyłączona. Wykonaj następujące [kroki,](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) aby przejrzeć i zmodyfikować ustawienia.

- **Usuwanie nietrwałe** domyślnie jest **włączone** w nowo utworzonych magazynach w celu ochrony danych kopii zapasowej przed przypadkowymi lub złośliwymi usunięciami. Wykonaj następujące [kroki,](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) aby przejrzeć i zmodyfikować ustawienia.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny usług odzyskiwania.
[Dowiedz się więcej o](backup-azure-delete-vault.md) Usuń magazyny usług odzyskiwania.
