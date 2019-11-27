---
title: Tworzenie kopii zapasowej bazy danych SAP HANA na platformie Azure przy użyciu Azure Backup
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową bazy danych SAP HANA na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed47f18c9dabc685d6fbe02804562ef86a93190a
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285791"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure

Bazy danych SAP HANA są obciążeniami krytycznymi, które wymagają niewielkiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure, korzystając z [Azure Backup](backup-overview.md).

W tym artykule pokazano, jak utworzyć kopię zapasową SAP HANA baz danych, które są uruchomione na maszynach wirtualnych platformy Azure, do magazynu Recovery Services Azure Backup.

W tym artykule dowiesz się jak:
> [!div class="checklist"]
>
> * Tworzenie i Konfigurowanie magazynu
> * Odnajdywanie baz danych
> * Skonfiguruj kopie zapasowe
> * Uruchamianie zadania tworzenia kopii zapasowej na żądanie

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z sekcjami [wymagania wstępne](tutorial-backup-sap-hana-db.md#prerequisites) i [Ustawienia uprawnień](tutorial-backup-sap-hana-db.md#setting-up-permissions) , aby skonfigurować bazę danych do tworzenia kopii zapasowych.

### <a name="set-up-network-connectivity"></a>Konfigurowanie łączności sieciowej

W przypadku wszystkich operacji maszyna wirtualna SAP HANA wymaga łączności z publicznymi adresami IP platformy Azure. Operacje maszyny wirtualnej (Odnajdywanie bazy danych, konfigurowanie kopii zapasowych, Planowanie kopii zapasowych, przywracanie punktów odzyskiwania itd.) nie działają bez łączności. Ustanów połączenie, zezwalając na dostęp do zakresów adresów IP centrum danych platformy Azure:

* Możesz pobrać [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) dla centrów danych platformy Azure, a następnie zezwolić na dostęp do tych adresów IP.
* Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń), możesz użyć [znacznika usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud, aby zezwolić na wszystkie publiczne adresy IP platformy Azure. Aby zmodyfikować reguły sieciowej grupy zabezpieczeń, można użyć [polecenia cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) .
* Port 443 należy dodać do listy dozwolonych, ponieważ transport odbywa się za pośrednictwem protokołu HTTPS.

## <a name="onboard-to-the-public-preview"></a>Dołączanie do publicznej wersji zapoznawczej

Dołączanie do publicznej wersji zapoznawczej w następujący sposób:

* W portalu Zarejestruj swój identyfikator subskrypcji dla dostawcy usług Recovery Services, wykonując [ten artykuł](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Dla programu PowerShell uruchom to polecenie cmdlet. Powinna zostać zakończona jako "zarejestrowane".

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Odnajdywanie baz danych

1. W magazynie w **wprowadzenie**kliknij pozycję **kopia zapasowa**. W **miejscu, w którym jest uruchomione Twoje obciążenie?** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**.
2. Kliknij przycisk **Rozpocznij odnajdywanie**. Spowoduje to zainicjowanie odnajdywania niechronionych maszyn wirtualnych z systemem Linux w regionie magazynu.

   * Po odnajdywaniu w portalu są wyświetlane niechronione maszyny wirtualne wymienione według nazwy i grupy zasobów.
   * Jeśli maszyna wirtualna nie znajduje się w oczekiwany sposób, sprawdź, czy kopia zapasowa została już utworzona w magazynie.
   * Wiele maszyn wirtualnych może mieć taką samą nazwę, ale należą do różnych grup zasobów.

3. W obszarze **wybierz Virtual Machines**kliknij link, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup do uzyskiwania dostępu do maszyn wirtualnych SAP HANA na potrzeby odnajdywania bazy danych.
4. Uruchom skrypt na każdej maszynie wirtualnej, w której znajdują się SAP HANA bazy danych, których kopię zapasową chcesz utworzyć.
5. Po uruchomieniu skryptu na maszynach wirtualnych w obszarze **wybierz Virtual Machines**Wybierz Maszyny wirtualne. Następnie kliknij pozycję **odkryj baz danych**.
6. Azure Backup odnajduje wszystkie SAP HANA bazy danych na maszynie wirtualnej. Podczas odnajdywania Azure Backup rejestruje maszynę wirtualną w magazynie i instaluje rozszerzenie na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych.

    ![Odnajdź SAP HANA bazy danych](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

Teraz Włącz tworzenie kopii zapasowej.

1. W kroku 2 kliknij pozycję **Konfiguruj kopię zapasową**.

    ![Skonfiguruj kopię zapasową](./media/backup-azure-sap-hana-database/configure-backup.png)
2. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**wybierz wszystkie bazy danych, które mają być chronione > **OK**.

    ![Wybierz elementy do utworzenia kopii zapasowej](./media/backup-azure-sap-hana-database/select-items.png)
3. W obszarze **zasady tworzenia kopii zapasowych** > **Wybierz pozycję Zasady tworzenia kopii**zapasowych, Utwórz nowe zasady tworzenia kopii zapasowych baz danych, zgodnie z poniższymi instrukcjami.

    ![Wybieranie zasad kopii zapasowych](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Po utworzeniu zasad w menu **kopia zapasowa** kliknij pozycję **Włącz kopię zapasową**.

    ![Włącz tworzenie kopii zapasowej](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Śledź postęp konfiguracji kopii zapasowej w obszarze **powiadomienia** portalu.

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych definiują, kiedy są tworzone kopie zapasowe, oraz czas ich przechowywania.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ ustawienia zasad w następujący sposób:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.

   ![Wprowadź nazwę zasad](./media/backup-azure-sap-hana-database/policy-name.png)
2. W obszarze **Zasady pełnej kopii zapasowej** wybierz opcję **Częstotliwość tworzenia kopii zapasowej** (**Codziennie** lub **Co tydzień**).
   * **Codziennie**: Wybierz godzinę i strefę czasową, w której rozpoczyna się zadanie tworzenia kopii zapasowej.
       * Należy uruchomić pełną kopię zapasową. Nie można wyłączyć tej opcji.
       * Kliknij pozycję **Pełna kopia zapasowa**, aby wyświetlić zasady.
       * Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.
   * **Co tydzień**: Wybierz dzień tygodnia, godzinę i strefę czasową, w której jest uruchamiane zadanie tworzenia kopii zapasowej.

   ![Wybierz częstotliwość tworzenia kopii zapasowych](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. W obszarze **Zakres przechowywania**Skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
    * Domyślnie wszystkie opcje są zaznaczone. Wyczyść limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które chcesz wykonać.
    * Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny/różnicowa/log) wynosi siedem dni.
    * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    * Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
    * Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

4. W menu **pełne zasady tworzenia kopii zapasowej** kliknij przycisk **OK** , aby zaakceptować ustawienia.
5. Wybierz pozycję **różnicowa kopia zapasowa** , aby dodać zasady różnicowe.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.
    * Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.
    * Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    ![Zasady różnicowych kopii zapasowych](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Przyrostowe kopie zapasowe nie są obecnie obsługiwane.

7. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
8. Wybierz pozycję **kopia zapasowa dziennika** , aby dodać zasady kopii zapasowej dziennika transakcyjnego,
    * W obszarze **kopia zapasowa dziennika**wybierz pozycję **Włącz**.  Nie można go wyłączyć, ponieważ SAP HANA zarządza wszystkimi kopiami zapasowymi dzienników.
    * Ustaw kontrolkę częstotliwość i przechowywanie.

    > [!NOTE]
    > Kopie zapasowe dzienników zaczynają przepływać dopiero po pomyślnym wykonaniu pełnej kopii zapasowej.

9. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
10. Po zdefiniowaniu zasad tworzenia kopii zapasowej kliknij przycisk **OK**.

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:

1. W menu magazyn kliknij pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej**wybierz maszynę wirtualną z uruchomioną SAP HANA bazą danych, a następnie kliknij pozycję **Utwórz kopię zapasową teraz**.
3. W obszarze **kopia zapasowa**Użyj formantu kalendarza, aby wybrać ostatni dzień przechowywania punktu odzyskiwania. Następnie kliknij przycisk **OK**.
4. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**. W zależności od rozmiaru bazy danych Tworzenie początkowej kopii zapasowej może chwilę potrwać.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Uruchamianie kopii zapasowej SAP HANA Studio w bazie danych z włączoną funkcją Azure Backup

Jeśli chcesz utworzyć lokalną kopię zapasową bazy danych, której kopia zapasowa jest tworzona przy użyciu Azure Backup, wykonaj następujące czynności:

1. Poczekaj na zakończenie wszystkich pełnych lub dzienników kopii zapasowych bazy danych. Sprawdź stan w SAP HANA Studio/Panel sterowania.
2. Wyłącz kopie zapasowe dzienników i ustaw wykaz kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
3. Aby to zrobić, kliknij dwukrotnie pozycję **systemdb** > **Configuration** > **Wybierz pozycję Database** > **Filter (log)** .
4. Ustaw **enable_auto_log_backup** na wartość **nie**.
5. Ustaw **log_backup_using_backint** na **wartość false**.
6. Wykonaj pełną kopię zapasową bazy danych.
7. Poczekaj na zakończenie pełnej kopii zapasowej i kopii zapasowej wykazu.
8. Przywróć poprzednie ustawienia z powrotem do tych dla platformy Azure:
    * Ustaw wartość **enable_auto_log_backup** na **tak**.
    * Ustaw **log_backup_using_backint** na **wartość true**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
