---
title: Tworzenie kopii zapasowej bazy danych SAP HANA na platformie Azure
description: W tym samouczku wyjaśniono, jak utworzyć kopię zapasową bazy danych SAP HANA na platformie Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 519e47c6b6793c638e64c4e4bcc4fafdb678c9fb
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172742"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>Tworzenie kopii zapasowej bazy danych SAP HANA na platformie Azure

[Azure Backup](backup-overview.md) obsługuje tworzenie kopii zapasowych baz danych SAP HANA na platformie Azure.

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Obecnie nie jest ona gotowa do produkcji i nie ma gwarantowanej umowy SLA.

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane obszary geograficzne** | Australia Południowo-Wschodnia, Australia Wschodnia <br> Brazylia Południowa <br> Kanada środkowa, Kanada Wschodnia <br> Azja Wschodnia Południowe, Azja Wschodnia <br> Wschodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, środkowe stany USA, Południowo-środkowe stany USA<br> Indie Środkowe, Indie Południowe <br> Japonia, część wschodnia; Japonia, część zachodnia<br> Korea Środkowa, Korea Południowa <br> Europa Północna, Europa Zachodnia <br> Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo
**Obsługiwane systemy operacyjne maszyn wirtualnych** | SLES 12 z dodatkiem SP2 lub dodatkiem SP3.
**Obsługiwane wersje platformy HANA** | SDC na platformie HANA 1. x, MDC w systemie HANA 2. x < = SPS04 Rev 43

### <a name="current-limitations"></a>Bieżące ograniczenia

- Można tworzyć kopie zapasowe baz danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure.
- Można tworzyć kopie zapasowe tylko SAP HANA wystąpienia uruchomionego na jednej maszynie wirtualnej platformy Azure. Wiele wystąpień HANA na tej samej maszynie wirtualnej platformy Azure nie jest obecnie obsługiwane.
- Można tworzyć kopie zapasowe baz danych tylko w trybie skalowania w górę. Skalowanie w poziomie, czyli wystąpienie HANA na wielu maszynach wirtualnych platformy Azure, nie jest obecnie obsługiwane w przypadku tworzenia kopii zapasowych.
- Nie można utworzyć kopii zapasowej wystąpienia SAP HANA z obsługą dynamicznych warstw na rozszerzonym serwerze, tj. dynamiczną warstwą obecną w innym węźle. Jest to zasadniczo skalowanie, co nie jest obsługiwane.
- Nie można utworzyć kopii zapasowej wystąpienia SAP HANA z włączoną obsługą dynamicznych warstw na tym samym serwerze. Dynamiczne warstwowe nie są obecnie obsługiwane.
- SAP HANA kopii zapasowej można skonfigurować tylko w Azure Portal. Nie można skonfigurować funkcji przy użyciu programu PowerShell, interfejsu wiersza polecenia.
- Można utworzyć kopię zapasową dzienników bazy danych co 15 minut. Kopie zapasowe dzienników zaczynają przepływać dopiero po pomyślnym wykonaniu pełnej kopii zapasowej bazy danych.
- Możesz tworzyć pełne i różnicowe kopie zapasowe. Przyrostowa kopia zapasowa nie jest obecnie obsługiwana.
- Nie można zmodyfikować zasad tworzenia kopii zapasowych po zastosowaniu ich do SAP HANA kopii zapasowych. Jeśli chcesz utworzyć kopię zapasową przy użyciu różnych ustawień, Utwórz nowe zasady lub Przypisz inne zasady.
  - Aby utworzyć nowe zasady, w magazynie kliknij pozycję **zasady** > **zasady tworzenia kopii zapasowych** >  **+ Dodaj** > **SAP HANA na maszynie wirtualnej platformy Azure**i określ ustawienia zasad.
  - Aby przypisać inne zasady, we właściwościach maszyny wirtualnej z uruchomioną bazą danych kliknij nazwę bieżącej zasady. Następnie na stronie **zasady tworzenia kopii zapasowych** możesz wybrać inne zasady do użycia w ramach kopii zapasowej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem kopii zapasowych należy wykonać następujące czynności:

1. Na maszynie wirtualnej z uruchomioną SAP HANA bazą danych Zainstaluj i włącz pakiety sterowników ODBC z oficjalnego pakietu SLES/nośnika przy użyciu użyciu narzędzia zypper w następujący sposób:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

    > [!NOTE]
    > Jeśli nie aktualizujesz repozytoriów, upewnij się, że wersja unixODBC jest minimalna ilość 2.3.4. Aby znać wersję programu uniXODBC, uruchom ```odbcinst -j``` jako element główny

2. Zezwalaj na połączenie z poziomu maszyny wirtualnej z Internetem, aby można było uzyskać dostęp do platformy Azure, zgodnie z opisem w [poniższej](#set-up-network-connectivity)procedurze.

3. Uruchom skrypt poprzedzający rejestrację na maszynie wirtualnej, na której jest zainstalowany system HANA jako użytkownik główny. Skrypt jest dostępny [w portalu](#discover-the-databases) w przepływie i jest wymagany do skonfigurowania [odpowiednich uprawnień](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Konfigurowanie łączności sieciowej

W przypadku wszystkich operacji maszyna wirtualna SAP HANA wymaga łączności z publicznymi adresami IP platformy Azure. Operacje maszyny wirtualnej (Odnajdywanie bazy danych, konfigurowanie kopii zapasowych, Planowanie kopii zapasowych, przywracanie punktów odzyskiwania itd.) nie działają bez łączności. Ustanów połączenie, zezwalając na dostęp do zakresów adresów IP centrum danych platformy Azure: 

- Możesz pobrać [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) dla centrów danych platformy Azure, a następnie zezwolić na dostęp do tych adresów IP.
- Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń), możesz użyć [znacznika usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud, aby zezwolić na wszystkie publiczne adresy IP platformy Azure. Aby zmodyfikować reguły sieciowej grupy zabezpieczeń, można użyć [polecenia cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) .
- port 443 powinien być listy dozwolonych, ponieważ transport odbywa się za pośrednictwem protokołu HTTPS.

## <a name="onboard-to-the-public-preview"></a>Dołączanie do publicznej wersji zapoznawczej

Dołączanie do publicznej wersji zapoznawczej w następujący sposób:

- W portalu Zarejestruj swój identyfikator subskrypcji dla dostawcy usług Recovery Services, wykonując [ten artykuł](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Dla programu PowerShell uruchom to polecenie cmdlet. Powinna zostać zakończona jako "zarejestrowane".

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Odnajdywanie baz danych

1. W magazynie w **wprowadzenie**kliknij pozycję **kopia zapasowa**. W **miejscu, w którym jest uruchomione Twoje obciążenie?** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**.
2. Kliknij przycisk **Rozpocznij odnajdywanie**. Spowoduje to zainicjowanie odnajdywania niechronionych maszyn wirtualnych z systemem Linux w regionie magazynu.

   - Po odnajdywaniu w portalu są wyświetlane niechronione maszyny wirtualne wymienione według nazwy i grupy zasobów.
   - Jeśli maszyna wirtualna nie znajduje się w oczekiwany sposób, sprawdź, czy kopia zapasowa została już utworzona w magazynie.
   - Wiele maszyn wirtualnych może mieć taką samą nazwę, ale należą do różnych grup zasobów.

3. W obszarze **wybierz Virtual Machines**kliknij link, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup do uzyskiwania dostępu do maszyn wirtualnych SAP HANA na potrzeby odnajdywania bazy danych
4. Uruchom skrypt na każdej maszynie wirtualnej, w której znajdują się SAP HANA bazy danych, których kopię zapasową chcesz utworzyć.
5. Po uruchomieniu skryptu na maszynach wirtualnych w obszarze **wybierz Virtual Machines**Wybierz Maszyny wirtualne. Następnie kliknij pozycję **odkryj baz danych**.
6. Azure Backup odnajduje wszystkie SAP HANA bazy danych na maszynie wirtualnej. Podczas odnajdywania Azure Backup rejestruje maszynę wirtualną w magazynie i instaluje rozszerzenie na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych.

    ![Odnajdź SAP HANA bazy danych](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

Teraz Włącz tworzenie kopii zapasowej.

1. W kroku 2 kliknij pozycję **Konfiguruj kopię zapasową**.
2. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**wybierz wszystkie bazy danych, które mają być chronione > **OK**.
3. W obszarze **zasady tworzenia kopii zapasowych** > **Wybierz pozycję Zasady tworzenia kopii**zapasowych, Utwórz nowe zasady tworzenia kopii zapasowych baz danych, zgodnie z poniższymi instrukcjami.
4. Po utworzeniu zasad w menu **kopia zapasowa** kliknij pozycję **Włącz kopię zapasową**.
5. Śledź postęp konfiguracji kopii zapasowej w obszarze **powiadomienia** portalu.

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych definiują, kiedy są tworzone kopie zapasowe, oraz czas ich przechowywania.

- Zasady są tworzone na poziomie magazynu.
- Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ ustawienia zasad w następujący sposób:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.
2. W obszarze **Zasady pełnej kopii zapasowej** wybierz opcję **Częstotliwość tworzenia kopii zapasowej** (**Codziennie** lub **Co tydzień**).
   - **Codziennie**: Wybierz godzinę i strefę czasową, w której rozpoczyna się zadanie tworzenia kopii zapasowej.
   
       - Należy uruchomić pełną kopię zapasową. Nie można wyłączyć tej opcji.
       - Kliknij pozycję **Pełna kopia zapasowa**, aby wyświetlić zasady.
       - Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.
       
   - **Co tydzień**: Wybierz dzień tygodnia, godzinę i strefę czasową, w której jest uruchamiane zadanie tworzenia kopii zapasowej.
3. W obszarze **Zakres przechowywania**Skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
    - Domyślnie wszystkie opcje są zaznaczone. Wyczyść limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które chcesz wykonać.
    - Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny/różnicowa/log) wynosi siedem dni.
    - Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    - Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
    - Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

4. W menu **pełne zasady tworzenia kopii zapasowej** kliknij przycisk **OK** , aby zaakceptować ustawienia.
5. Wybierz pozycję **różnicowa kopia zapasowa** , aby dodać zasady różnicowe.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.
    - Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.
    - Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    > [!NOTE]
    > Przyrostowe kopie zapasowe nie są obecnie obsługiwane. 

7. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
8. Wybierz pozycję **kopia zapasowa dziennika** , aby dodać zasady kopii zapasowej dziennika transakcyjnego,
    - W obszarze **kopia zapasowa dziennika**wybierz pozycję **Włącz**.
    - Ustaw kontrolkę częstotliwość i przechowywanie.

    > [!NOTE]
    > Kopie zapasowe dzienników zaczynają przepływać dopiero po pomyślnym wykonaniu pełnej kopii zapasowej.

9. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
10. Po zdefiniowaniu zasad tworzenia kopii zapasowej kliknij przycisk **OK**.


## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:


1. W menu magazyn kliknij pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej**wybierz maszynę wirtualną z uruchomioną SAP HANA bazą danych, a następnie kliknij pozycję **Utwórz kopię zapasową teraz**.
3. W obszarze **kopia zapasowa**Użyj formantu kalendarza, aby wybrać ostatni dzień przechowywania punktu odzyskiwania. Następnie kliknij przycisk **OK**.
4. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > w toku. W zależności od rozmiaru bazy danych Tworzenie początkowej kopii zapasowej może chwilę potrwać.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Uruchamianie kopii zapasowej SAP HANA Studio w bazie danych z włączoną funkcją Azure Backup

Jeśli chcesz utworzyć lokalną kopię zapasową bazy danych, której kopia zapasowa jest tworzona przy użyciu Azure Backup, wykonaj następujące czynności:

1. Poczekaj na zakończenie wszystkich pełnych lub dzienników kopii zapasowych bazy danych. Sprawdź stan w SAP HANA Studio.
2. Wyłącz kopie zapasowe dzienników i ustaw wykaz kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
3. Aby to zrobić, kliknij dwukrotnie pozycję **systemdb** > **Configuration** > **Wybierz pozycję Database** > **Filter (log)** .
4. Ustaw **enable_auto_log_backup** na wartość **nie**.
5. Ustaw **log_backup_using_backint** na **wartość false**.
6. Utwórz pełną kopię zapasową bazy danych ad hoc.
7. Poczekaj na zakończenie pełnej kopii zapasowej i kopii zapasowej wykazu.
8. Przywróć poprzednie ustawienia z powrotem do tych dla platformy Azure:
    - Ustaw wartość **enable_auto_log_backup** na **tak**.
    - Ustaw **log_backup_using_backint** na **wartość true**.


## <a name="upgrading-protected-10-dbs-to-20"></a>Uaktualnianie chronionego 1,0 baz danych do 2,0

Jeśli chronisz SAP HANA 1,0 baz danych i chcesz przeprowadzić uaktualnienie do wersji 2,0, wykonaj czynności opisane poniżej.

- Zatrzymaj ochronę z zachowaniem Zachowaj dane dla starego SDC DB.
- Uruchom skrypt poprzedzający rejestrację z prawidłowymi szczegółami (SID i MDC). 
- Ponownie zarejestruj rozszerzenie (szczegóły widoku > kopii zapasowej — > wybierz odpowiednią pozycję Azure VM-> reregister). 
- Kliknij przycisk ponownie odkryj baz danych dla tej samej maszyny wirtualnej. Powinno to spowodować wyświetlenie nowego baz danych w kroku 2 z prawidłowymi szczegółami (SYSTEMDB i dzierżawcą bazy danych, a nie SDC). 
- Chroń te nowe bazy danych.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się](backup-azure-sap-hana-database-troubleshoot.md) , jak rozwiązywać typowe błędy podczas korzystania z kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure.
[Dowiedz się więcej o](backup-azure-arm-vms-prepare.md) tworzeniu kopii zapasowych maszyn wirtualnych platformy Azure.
