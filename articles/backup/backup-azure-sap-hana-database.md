---
title: Utwórz kopię zapasową bazy danych SAP HANA na platformie Azure przy użyciu usługi Azure Backup | Dokumentacja firmy Microsoft
description: W tym samouczku wyjaśniono, jak utworzyć kopię zapasową bazy danych SAP HANA na platformie Azure przy użyciu usługi Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: a16ed7134fc9f3c159715f58f116de3fb30e8aca
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481139"
---
# <a name="back-up-an-sap-hana-database"></a>Tworzenie kopii zapasowej bazy danych SAP HANA

[Usługa Azure Backup](backup-overview.md) obsługuje tworzenie kopii zapasowych baz danych SAP HANA na platformie Azure.

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Obecnie nie jest już gotowe do produkcji i nie ma gwarancji umowy SLA. 

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane obszary geograficzne** | Australia Południowo-Wschodnia, Australia Wschodnia <br> Brazylia Południowa <br> Kanada Środkowa, Kanada Wschodnia <br> Azja południowo-wschodnia, Azja Wschodnia <br> Wschodnie stany USA, wschodnie stany USA 2, zachodnie środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Północna środkowe stany USA, środkowe stany USA, południowo-środkowe stany USA<br> Indie środkowe, Indie Południowe <br> Japonia, część wschodnia; Japonia, część zachodnia<br> Korea Środkowa, Korea Południowa <br> Europa Północna, Europa Zachodnia <br> Południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo
**Obsługiwane systemy operacyjne maszyny Wirtualnej** | SLES 12 z dodatkiem SP2 lub z dodatkiem SP3.
**Obsługiwane wersje platformy HANA** | SDC na platformie HANA 1.x, MDC na platformie HANA 2.x < = SPS03

### <a name="current-limitations"></a>Bieżące ograniczenia

- Możesz tylko tworzenie kopii zapasowej baz danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure.
- Kopia zapasowa oprogramowania SAP HANA można skonfigurować tylko w witrynie Azure portal. Nie można skonfigurować tę funkcję za pomocą programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST.
- Możesz tylko tworzenie kopii zapasowej bazy danych w trybie skalowanie w górę.
- Kopii zapasowych dzienników bazy danych co 15 minut. Kopie zapasowe dziennika tylko rozpoczyna się przepływ po ukończeniu pomyślne pełnej kopii zapasowej dla bazy danych.
- Możesz korzystać z pełnych i różnicowych kopii zapasowych. Przyrostowa kopia zapasowa nie jest obecnie obsługiwane.
- Nie można modyfikować zasad tworzenia kopii zapasowej, po zastosowaniu kopii zapasowych oprogramowania SAP HANA. Jeśli chcesz utworzyć kopię zapasową z różnymi ustawieniami, Utwórz nowe zasady lub przypisać inne zasady.
  - Aby utworzyć nowe zasady, w magazynie kliknij **zasady** > **zasady tworzenia kopii zapasowych** >  **+ Dodaj** > **platformy SAP HANA w Maszyna wirtualna platformy Azure**, a następnie określ ustawienia zasad.
  - Aby przypisać inne zasady, we właściwościach maszyny Wirtualnej, w którym działa baza danych, kliknij przycisk bieżącą nazwę zasad. Następnie na **zasad tworzenia kopii zapasowej** strony można wybrać inne zasady służące do tworzenia kopii zapasowej.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonasz następujące czynności, aby skonfigurować tworzenie kopii zapasowych:

1. Na maszynie Wirtualnej uruchomienie bazy danych SAP HANA, zainstaluj program Microsoft oficjalne [platformy .NET Core środowiska uruchomieniowego 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) pakietu. Należy pamiętać o następujących kwestiach:
    - Dzięki temu wystarczy **2.1-dotnet-środowiska uruchomieniowego** pakietu. Nie ma potrzeby **aspnetcore-środowiska uruchomieniowego — 2.1**.
    - Jeśli maszyna wirtualna nie ma internetowego dostępu, dublowania lub zapewnienia — pamięci podręcznej offline 2.1-dotnet-środowiska uruchomieniowego (i wszystkich zależnych pakietów rpm) z pakietu Microsoft, źródła danych określone na stronie.
    - Podczas instalacji pakietu aktualizacji może zostać wyświetlony o określić opcję. Jeśli tak, podaj **rozwiązanie 2**.

        ![Opcja instalacji pakietu](./media/backup-azure-sap-hana-database/hana-package.png)

2. Na maszynie Wirtualnej należy zainstalować i włączyć pakiety sterowników ODBC z oficjalnego SLES pakietu/nośnika przy użyciu zypper, w następujący sposób:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. Zezwalać na łączność z maszyny Wirtualnej do Internetu, dzięki czemu można dotrzeć do platformy Azure, zgodnie z opisem w procedurze [poniżej](#set-up-network-connectivity).

4. Uruchom skrypt wstępnej rejestracji na maszynie wirtualnej, w zainstalowanym HANA jako użytkownik root. Skrypt jest dostarczany w stanie [w portalu](#discover-the-databases) przepływu i jest wymagane do skonfigurowania [kliknij prawym przyciskiem myszy uprawnienia](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Skonfiguruj połączenie sieciowe

Dla wszystkich operacji maszyna wirtualna SAP HANA, musi mieć łączność platformy Azure z publicznymi adresami IP. Operacje maszyny Wirtualnej (odnajdowaniu baz danych, skonfigurowanie kopii zapasowych, Planowanie tworzenia kopii zapasowych, przywracania punktów odzyskiwania i tak dalej) nie może działać bez połączenia. Nawiązać połączenie, zezwalając na dostęp do zakresów IP centrum danych platformy Azure: 

- Możesz pobrać [zakresów adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) dla centrów danych platformy Azure, a następnie zezwolić na dostęp do tych adresów IP.
- Jeśli używasz grup zabezpieczeń sieci (NSG), możesz użyć AzureCloud [tag usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) zezwalająca na wszystkie platformy Azure, publiczny adres IP adresów. Możesz użyć [polecenia cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) do zmodyfikowania reguły sieciowej grupy zabezpieczeń.

## <a name="onboard-to-the-public-preview"></a>Dołączanie do publicznej wersji zapoznawczej

Dołączanie do publicznej wersji zapoznawczej w następujący sposób:

- W portalu, należy zarejestrować identyfikator subskrypcji do dostawcy usług Recovery Services przez [podanych w tym artykule](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- W przypadku programu PowerShell Uruchom to polecenie cmdlet. Powinno to zająć jako "Zarejestrowane".

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Odnajdowanie baz danych

1. W magazynie w **wprowadzenie**, kliknij przycisk **kopii zapasowej**. W **gdzie jest uruchomione Twoje obciążenie?** , wybierz opcję **danych SAP HANA na maszynie Wirtualnej platformy Azure**.
2. Kliknij przycisk **Uruchom odnajdowanie**. Spowoduje to zainicjowanie odnajdywania niechronionych maszyn wirtualnych systemu Linux w regionie magazynu.

   - Po odnalezieniu, niechronione maszyny wirtualne są wyświetlane w portalu, według nazwy i grupie zasobów.
   - Jeśli maszyna wirtualna nie ma na liście zgodnie z oczekiwaniami, sprawdź, czy już kopii zapasowej w magazynie.
   - Wiele maszyn wirtualnych może mieć takiej samej nazwie, ale należą do różnych grup zasobów.

3. W **wybierz maszyny wirtualne**, kliknij link, aby pobrać skrypt, który zapewnia uprawnienia do dostępu SAP HANA maszyny wirtualne w odnajdowaniu baz danych za pomocą usługi Kopia zapasowa Azure
4. Uruchom skrypt na każdej maszynie Wirtualnej hostowania baz danych SAP HANA, które chcesz utworzyć kopię zapasową.
5. Po uruchomieniu skryptu na maszynach wirtualnych, w **wybierz maszyny wirtualne**, wybierz maszyny wirtualne. Następnie kliknij przycisk **odnajdowanie baz danych**.
6. Usługa Azure Backup umożliwia odnalezienie wszystkich baz danych SAP HANA na maszynie Wirtualnej. Podczas odnajdowania usługi Azure Backup rejestruje maszyny Wirtualnej z magazynem i instaluje rozszerzenie na maszynie Wirtualnej. Agent nie jest zainstalowany w bazie danych.

    ![Odnajdowanie baz danych SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

Teraz Włącz kopię zapasową.

1. W kroku 2 kliknij **konfigurowania kopii zapasowej**.
2. W **wybierz elementy, aby utworzyć kopię zapasową**, wybierz wszystkie bazy danych, które chcesz chronić > **OK**.
3. W **zasad tworzenia kopii zapasowej** > **wybierz zasady tworzenia kopii zapasowej**, Utwórz nowe zasady kopii zapasowych baz danych, zgodnie z poniższymi instrukcjami.
4. Po utworzeniu zasad, na **kopii zapasowej** menu, kliknij przycisk **Włącz kopię zapasową**.
5. Śledź postęp konfiguracji kopii zapasowej w **powiadomienia** obszaru portalu.

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych definiuje, gdy kopie zapasowe są pobierane i jak długo są przechowywane.

- Zasady są tworzone na poziomie magazynu.
- Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ ustawienia zasad, w następujący sposób:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.
2. W obszarze **Zasady pełnej kopii zapasowej** wybierz opcję **Częstotliwość tworzenia kopii zapasowej** (**Codziennie** lub **Co tydzień**).
   - **Codzienne**: Wybierz godzinę i strefę czasową, w którym rozpoczyna się zadanie tworzenia kopii zapasowej.
   
       - Pełna kopia zapasowa, należy uruchomić. Nie można wyłączyć tę opcję.
       - Kliknij pozycję **Pełna kopia zapasowa**, aby wyświetlić zasady.
       - Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.
       
   - **Co tydzień**: Wybierz dzień tygodnia, godzinę i strefę czasową, w którym działa zadanie tworzenia kopii zapasowej.
3. W **zakres przechowywania**, skonfigurować ustawienia przechowywania dla pełnej kopii zapasowej.
    - Domyślnie wszystkie opcje są zaznaczone. Wyczyść jakiekolwiek ograniczenia zakresu przechowywania, których nie chcesz, a następnie ustaw te, które należy wykonać.
    - Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełnej/różnicowej/log) wynosi siedem dni.
    - Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    - Tworzenie kopii zapasowej dla określonego dnia są oznaczone i przechowywane na podstawie co tydzień zakres przechowywania i ustawienia.
    - Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

4. W **zasad pełnej kopii zapasowej** menu, kliknij przycisk **OK** aby zaakceptować ustawienia.
5. Wybierz **różnicowej kopii zapasowej** dodać różnicowe zasady.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.
    - Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.
    - Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    > [!NOTE]
    > Przyrostowe kopie zapasowe nie są obecnie obsługiwane. 

7. Kliknij przycisk **OK** do zapisania zasad i wróć do strony głównej **zasady tworzenia kopii zapasowej** menu.
8. Wybierz **kopii zapasowej dziennika** dodać zasadę tworzenia kopii zapasowej dziennika transakcji
    - W **kopii zapasowej dziennika**, wybierz opcję **Włącz**.
    - Ustawienie kontroli częstotliwości i przechowywania.

    > [!NOTE]
    > Kopie zapasowe dziennika tylko rozpoczyna się przepływ po pomyślnym pełnej kopii zapasowej zostało zakończone.

9. Kliknij przycisk **OK** do zapisania zasad i wróć do strony głównej **zasady tworzenia kopii zapasowej** menu.
10. Po zdefiniowaniu zasad tworzenia kopii zapasowej kliknij **OK**.


## <a name="run-an-on-demand-backup"></a>Uruchomienie w kopii zapasowej na żądanie

Wykonywane kopie zapasowe zgodnie z harmonogramem zasad. Można uruchomić tworzenia kopii zapasowej na żądanie w następujący sposób:


1. W menu magazynu kliknij **kopii zapasowych elementów**.
2. W **elementy kopii zapasowej**, wybierz maszynę Wirtualną, w którym działa baza danych SAP HANA, a następnie kliknij przycisk **Utwórz teraz kopię zapasową**.
3. W **Utwórz teraz kopię zapasową**, wybierz ostatni dzień, który ma być przechowywana punkt odzyskiwania przy użyciu kontrolki kalendarza. Następnie kliknij przycisk **OK**.
4. Monitoruj powiadomienia z portalu. Możesz monitorować postęp zadania na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > w toku. W zależności od rozmiaru bazy danych tworzenie początkowej kopii zapasowej może chwilę potrwać.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Uruchom oprogramowanie SAP HANA Studio wykonywanie kopii zapasowej bazy danych za pomocą usługi Azure Backup włączone

Aby móc lokalnej kopii zapasowej (przy użyciu platformy HANA Studio) bazy danych, która jest tworzona kopia zapasowa za pomocą usługi Azure Backup, należy wykonać następujące czynności:

1. Zaczekaj, aż wszystkie pełnej lub kopie zapasowe dziennika dla bazy danych zakończyć. Sprawdź stan w programie SAP HANA Studio.
2. Wyłącz kopie zapasowe dziennika i ustawić wykaz kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
3. Aby to zrobić, kliknij dwukrotnie **systemdb** > **konfiguracji** > **wybierz bazę danych** > **filtru (dziennik)** .
4. Ustaw **enable_auto_log_backup** do **nie**.
5. Ustaw **log_backup_using_backint** do **False**.
6. Podjąć ad-hoc pełnej kopii zapasowej bazy danych.
7. Poczekaj, aż pełnej kopii zapasowej oraz wykaz kopii zapasowych, aby zakończyć.
8. Przywróć poprzednie ustawienia do wartości dla platformy Azure:
    - Ustaw **enable_auto_log_backup** do **tak**.
    - Ustaw **log_backup_using_backint** do **True**.



## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o](backup-azure-sap-hana-database-troubleshoot.md) jak rozwiązywać problemy z typowymi błędami podczas używania kopii zapasowych oprogramowania SAP HANA na maszynach wirtualnych Azure.
[Dowiedz się więcej o](backup-azure-arm-vms-prepare.md) tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.
