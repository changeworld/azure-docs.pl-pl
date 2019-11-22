---
title: Funkcje zabezpieczeń pomagające w ochronie obciążeń w chmurze
description: Dowiedz się, jak używać funkcji zabezpieczeń w programie Azure Backup, aby tworzyć kopie zapasowe bardziej bezpieczne.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: b6ce2f9400ad46150fbd4ee86f126b137b5f7800
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278226"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funkcje zabezpieczeń pomagające w ochronie obciążeń w chmurze korzystających z Azure Backup

Obawy dotyczące problemów z zabezpieczeniami takich jak złośliwe oprogramowanie, oprogramowanie wymuszające okup oraz włamania wciąż rosną. Te problemy z zabezpieczeniami mogą być kosztowne, zarówno pod względem finansowym, jak i w kwestii danych. Aby ochronić przed takimi atakami, Azure Backup teraz zapewnia funkcje zabezpieczeń, które ułatwiają ochronę danych kopii zapasowej nawet po usunięciu. Jedną z tych funkcji jest usuwanie nietrwałe. W przypadku usuwania nietrwałego, nawet jeśli złośliwy aktor usuwa kopię zapasową maszyny wirtualnej (lub dane kopii zapasowej są przypadkowo usuwane), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. Te dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z kosztem dla klienta.

> [!NOTE]
> Usuwanie nietrwałe chroni tylko usunięte dane kopii zapasowej. Jeśli maszyna wirtualna zostanie usunięta bez kopii zapasowej, funkcja usuwania nietrwałego nie będzie zachować danych. Wszystkie zasoby powinny być chronione za pomocą Azure Backup, aby zapewnić pełną odporność.
>

## <a name="soft-delete"></a>Usuwanie nietrwałe

### <a name="supported-regions"></a>Obsługiwane regiony

Usuwanie nietrwałe jest obecnie obsługiwane w regionie zachodnie stany USA, Azja Wschodnia, Kanada środkowa, Kanada Wschodnia, Francja środkowa, Francja Południowa, Korea środkowa, Korea Południowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Australia Wschodnia, Australia Południowo-Wschodnia, Ameryka Północna, zachodnie stany USA, zachodnie stany USA, Południowo-środkowe, Południowe Azja Wschodnia, Północno-środkowe stany USA, Południowo-środkowe stany USA, Japonia Wschodnia, Japonia Zachodnia, Indie Południowe, Indie Środkowe, Indie Zachodnie, Wschodnie stany USA 2, Szwajcaria Północna, Szwajcaria Zachodnia i wszystkie regiony narodowe.

### <a name="soft-delete-for-vms"></a>Usuwanie nietrwałe dla maszyn wirtualnych

1. Aby można było usunąć dane kopii zapasowej maszyny wirtualnej, należy zatrzymać wykonywanie kopii zapasowej. W Azure Portal przejdź do magazynu usługi Recovery Services, kliknij prawym przyciskiem myszy element kopii zapasowej, a następnie wybierz polecenie **Zatrzymaj tworzenie kopii zapasowej**.

   ![Zrzut ekranu przedstawiający elementy Azure Portal kopii zapasowej](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. W poniższym oknie zostanie wybrana opcja usuwania lub zachowywania danych kopii zapasowej. Jeśli wybierzesz pozycję **Usuń dane kopii zapasowej** , a następnie **Zatrzymaj kopię zapasową**, kopia zapasowa maszyny wirtualnej nie zostanie trwale usunięta. Zamiast tego dane kopii zapasowej będą przechowywane przez 14 dni w stanie nietrwałego usunięcia. W przypadku wybrania opcji **Usuń dane kopii zapasowej** do skonfigurowanego identyfikatora poczty e-mail zostanie wysłany alert z informacją o tym, że 14 dni pozostaną Rozszerzone przechowywanie danych kopii zapasowej. Ponadto w 12-dniowym dniu jest wysyłany alert e-mail z informacją o tym, że pozostały jeszcze dwa dni, aby przywracania aktywności usunięte dane. Usuwanie zostanie odroczone do 15-dniowego dnia, po upływie którego nastąpi trwałe usunięcie i zostanie wysłany końcowy alert e-mail z informacją o trwałym usunięciu danych.

   ![Zrzut ekranu przedstawiający ekran Azure Portal, Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. W ciągu 14 dni w magazynie Recovery Services nietrwałe usunięte maszyny wirtualne będą wyświetlane z czerwoną ikoną "miękki-Delete".

   ![Zrzut ekranu przedstawiający Azure Portal, maszynę wirtualną w stanie usuwania nietrwałego](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Jeśli w magazynie znajdują się jakiekolwiek nietrwałe usunięte elementy kopii zapasowej, nie można w tym momencie usunąć magazynu. Spróbuj usunąć magazyn po usunięciu trwałych elementów kopii zapasowej. w magazynie nie ma żadnego elementu usuniętego nietrwałego stanu.

4. Aby przywrócić maszynę wirtualną usuniętą z nietrwałego, należy najpierw ją usunąć. Aby cofnąć usunięcie, wybierz maszynę wirtualną, która została usunięta, a następnie wybierz opcję **Cofnij usunięcie**.

   ![Zrzut ekranu przedstawiający Azure Portal, cofanie usunięcia maszyny wirtualnej](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Zostanie wyświetlone okno z ostrzeżeniem, że w przypadku wybrania opcji Cofnij usuwanie wszystkie punkty przywracania dla maszyny wirtualnej będą cofnięte i dostępne do wykonania operacji przywracania. Maszyna wirtualna zostanie zachowana w stanie "Zatrzymaj ochronę przy zachowaniu danych" z wstrzymanymi kopiami zapasowymi, a dane kopii zapasowej są zachowywane w nieskończoność bez względu na to, czy zasady tworzenia

   ![Zrzut ekranu przedstawiający Azure Portal, potwierdź Cofnięcie usunięcia maszyny wirtualnej](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   W tym momencie można również przywrócić maszynę wirtualną, wybierając pozycję **Przywróć maszynę wirtualną** z wybranego punktu przywracania.  

   ![Zrzut ekranu przedstawiający Azure Portal, opcja Przywróć maszynę wirtualną](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Moduł wyrzucania elementów bezużytecznych będzie uruchamiał i czyścił wygasłe punkty odzyskiwania dopiero po wykonaniu operacji **wznawiania kopii zapasowej** .

5. Po ukończeniu procesu cofania usuwania stan zwróci wartość "Zatrzymaj tworzenie kopii zapasowej z zachowaniem danych", a następnie wybierz pozycję **Wznów tworzenie kopii zapasowej**. Operacja **Wznów tworzenie kopii zapasowej** powoduje przywrócenie elementu kopii zapasowej w stanie aktywnym, skojarzony z zasadami tworzenia kopii zapasowych wybranym przez użytkownika w celu zdefiniowania harmonogramów tworzenia kopii zapasowych i przechowywania.

   ![Zrzut ekranu przedstawiający Azure Portal, Wznów opcję tworzenia kopii zapasowej](./media/backup-azure-security-feature-cloud/resume-backup.png)

Ten wykres przepływu przedstawia różne kroki i Stany elementu kopii zapasowej po włączeniu usuwania nietrwałego:

![Cykl życia nietrwałego usuniętego elementu kopii zapasowej](./media/backup-azure-security-feature-cloud/lifecycle.png)

Aby uzyskać więcej informacji, zobacz sekcję [często zadawane pytania](backup-azure-security-feature-cloud.md#frequently-asked-questions) poniżej.

## <a name="disabling-soft-delete"></a>Wyłączanie usuwania nietrwałego

Usuwanie nietrwałe jest domyślnie włączone dla nowo utworzonych magazynów w celu ochrony danych kopii zapasowej przed przypadkowym lub złośliwym usunięciem.  Wyłączenie tej funkcji nie jest zalecane. Jedyną okolicznością, w której należy rozważyć wyłączenie usuwania nietrwałego, jest to, że planujesz przeniesienie chronionych elementów do nowego magazynu i nie będzie można odczekać 14 dni przed usunięciem i ponownym włączeniem ochrony (na przykład w środowisku testowym). Tę funkcję można wyłączyć tylko przez administratora kopii zapasowej. Jeśli wyłączysz tę funkcję, wszystkie usunięcia chronionych elementów spowodują natychmiastowe usunięcie, bez możliwości przywrócenia. Dane kopii zapasowej w stanie nietrwałego usunięcia przed wyłączeniem tej funkcji pozostaną w stanie nietrwałego usunięcia. Jeśli chcesz trwale usunąć te elementy natychmiast, musisz cofnąć ich usunięcie i usunąć je ponownie, aby trwale usunąć.

Aby wyłączyć usuwanie nietrwałe, wykonaj następujące kroki:

1. W Azure Portal przejdź do magazynu, a następnie przejdź do pozycji **ustawienia** -> **Właściwości**.
2. W okienku właściwości wybierz pozycję **Ustawienia zabezpieczeń** -> **Aktualizacja**.  
3. W okienku ustawienia zabezpieczeń w obszarze **usuwanie nietrwałe**wybierz pozycję **Wyłącz**.


![Wyłącz usuwanie nietrwałe](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Trwałe usuwanie nietrwałych usuniętych elementów kopii zapasowej

Dane kopii zapasowej w stanie nietrwałego usunięcia przed wyłączeniem tej funkcji pozostaną w stanie nietrwałego usunięcia. Jeśli chcesz trwale usunąć te elementy natychmiast, usuń je i Usuń ponownie, aby uzyskać trwałe usunięcie. 

Wykonaj następujące kroki:

1. Postępuj zgodnie z instrukcjami, aby [wyłączyć usuwanie nietrwałe](#disabling-soft-delete). 
2. W Azure Portal przejdź do swojego magazynu, przejdź do **pozycji elementy kopii zapasowej** i wybierz nietrwałe usunięte maszyny wirtualne 

![Wybierz nietrwałe usunięte maszyny wirtualne](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Wybierz opcję **Cofnij usunięcie**.

![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. Zostanie wyświetlone okno. Wybierz pozycję **Cofnij usunięcie**.

![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Wybierz pozycję **Usuń dane kopii zapasowej** , aby trwale usunąć dane kopii zapasowej.

![Wybierz pozycję Usuń dane kopii zapasowej](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Wpisz nazwę elementu kopii zapasowej, aby potwierdzić, że chcesz usunąć punkty odzyskiwania.

![Wpisz nazwę elementu kopii zapasowej](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Aby usunąć dane kopii zapasowej dla elementu, wybierz pozycję **Usuń**. Komunikat z powiadomieniem informuje o tym, że dane kopii zapasowej zostały usunięte.


## <a name="other-security-features"></a>Inne funkcje zabezpieczeń

### <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Usługa Azure Storage automatycznie szyfruje dane podczas ich utrwalania w chmurze. Szyfrowanie chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows. Azure Backup automatycznie szyfruje dane przed ich zapisaniem. Usługa Azure Storage odszyfrowuje dane przed ich pobraniem.  

W ramach platformy Azure dane przesyłane między usługą Azure Storage i magazynem są chronione za pośrednictwem protokołu HTTPS. Te dane pozostają w sieci szkieletowej platformy Azure.

Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage w przypadku przechowywanych danych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

### <a name="vm-encryption"></a>Szyfrowanie maszyny wirtualnej

Można tworzyć kopie zapasowe i przywracać maszyny wirtualne systemu Windows lub Linux z szyfrowanymi dyskami przy użyciu usługi Azure Backup. Aby uzyskać instrukcje, zobacz [wykonywanie kopii zapasowej i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

### <a name="protection-of-azure-backup-recovery-points"></a>Ochrona punktów odzyskiwania Azure Backup

Konta magazynu używane przez magazyny usługi Recovery Services są izolowane i nie są dostępne dla użytkowników w przypadku jakichkolwiek złośliwych celów. Dostęp jest dozwolony tylko za pomocą Azure Backup operacji zarządzania, takich jak przywracanie. Te operacje zarządzania są kontrolowane za pośrednictwem Access Control opartej na rolach (RBAC).

Aby uzyskać więcej informacji, zobacz [używanie Access Control opartej na rolach w celu zarządzania Azure Backup punktów odzyskiwania](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="soft-delete"></a>Usuwanie nietrwałe

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Czy muszę włączyć funkcję usuwania nietrwałego dla każdego magazynu?

Nie. jest on tworzony i domyślnie włączony dla wszystkich magazynów usługi Recovery Services.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Czy można skonfigurować liczbę dni, przez jaką dane będą przechowywane w stanie nieusuniętym, po zakończeniu operacji usuwania?

Nie, po operacji usuwania zostanie ustalony 14-dniowy dodatkowy czas przechowywania.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Czy muszę płacić za to dodatkowe 14 dni?

Nie, to 14-dniowe dodatkowe przechowywanie jest bezpłatne w ramach funkcji usuwania nietrwałego.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Czy można wykonać operację przywracania, gdy moje dane są w stanie usuwania nietrwałego?

Nie, Usuń nietrwały zasób nietrwałego w celu przywrócenia. Operacja cofnięcia usunięcia spowoduje przywrócenie zasobu z powrotem do **stanu Zatrzymaj ochronę przy zachowaniu danych** , w którym można przywrócić wszystkie punkty w czasie. Moduł wyrzucania elementów bezużytecznych pozostaje wstrzymany w tym stanie.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Czy moje migawki będą zgodne z tym samym cyklem życia jak moje punkty odzyskiwania w magazynie?

Tak.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak można ponownie wyzwolić zaplanowane kopie zapasowe dla nietrwałego zasobu?

Cofnięcie usunięcia po wykonaniu operacji Wznów spowoduje ponowne włączenie ochrony zasobu. Operacja Wznów kojarzy zasady tworzenia kopii zapasowych, aby wyzwolić zaplanowane kopie zapasowe z wybranym okresem przechowywania. Ponadto Moduł wyrzucania elementów bezużytecznych jest uruchamiany zaraz po zakończeniu operacji wznawiania. Jeśli chcesz wykonać przywracanie z punktu odzyskiwania, który przekroczył datę wygaśnięcia, zaleca się wykonanie tej czynności przed wyzwoleniem operacji wznowienia.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Czy mogę usunąć swój magazyn, jeśli w magazynie istnieją nietrwałe elementy usunięte?

Nie można usunąć magazynu Recovery Services, jeśli w magazynie istnieją elementy kopii zapasowej w stanie nieusuniętym. Usunięte elementy nietrwałe są usuwane przez 14 dni po operacji usuwania. Jeśli nie możesz zaczekać 14 dni, [Wyłącz usuwanie nietrwałe](#disabling-soft-delete), Cofnij usunięcie nieusuniętych elementów i usuń je ponownie, aby trwale usunąć. Po upewnieniu się, że nie ma żadnych elementów chronionych i nie usunięto żadnych nietrwałych elementów, można usunąć magazyn.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Czy mogę usunąć dane przed usunięciem z okresu usuwania nietrwałego z 14 dni?

Nie. Nie można wymusić usunięcia nieusuniętych elementów, zostaną one automatycznie usunięte po 14 dniach. Ta funkcja zabezpieczeń umożliwia ochronę danych kopii zapasowych przed przypadkowym lub złośliwym usunięciem.  Przed wykonaniem jakiejkolwiek innej akcji na maszynie wirtualnej należy poczekać 14 dni.  Elementy usunięte nietrwale nie będą obciążane opłatami.  Jeśli potrzebujesz ponownie chronić maszyny wirtualne oznaczone do usunięcia nietrwałego w ciągu 14 dni do nowego magazynu, skontaktuj się z pomocą techniczną firmy Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Czy można wykonywać operacje usuwania nietrwałego w programie PowerShell lub interfejsie wiersza polecenia?

Nie, obsługa programu PowerShell lub interfejsu wiersza polecenia nie jest obecnie dostępna.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Czy usuwanie nietrwałe jest obsługiwane dla innych obciążeń w chmurze, takich jak SQL Server na maszynach wirtualnych platformy Azure i SAP HANA na maszynach wirtualnych platformy Azure?

Nie. Obecnie usuwanie nietrwałe jest obsługiwane tylko dla maszyn wirtualnych platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [kontrolkach zabezpieczeń dla Azure Backup](backup-security-controls.md).
