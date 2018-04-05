---
title: Program DPM umożliwia tworzenie kopii zapasowej obciążeń w portalu Azure | Dokumentacja firmy Microsoft
description: Wprowadzenie do tworzenia kopii zapasowej serwerów DPM za pomocą usługi Kopia zapasowa Azure
services: backup
documentationcenter: ''
author: adigan
manager: nkolli
editor: ''
keywords: System Center Data Protection Manager, programu data protection manager, kopii zapasowych programu dpm
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: 05917705264965afda873fb32a28b70e4da87aa3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Przygotowywanie do tworzenia kopii zapasowych obciążeń na platformie Azure przy użyciu programu DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Ten artykuł zawiera wprowadzenie do używania kopia zapasowa Microsoft Azure w celu ochrony programu System Center Data Protection Manager (DPM) serwerów i obciążeń. Przeczytaj go, będzie zrozumieć:

* Jak działa kopii zapasowej serwera Azure DPM
* Wymagania wstępne dotyczące uzyskania płynnego środowisko tworzenia kopii zapasowej
* Napotkano błędy typowe i sposób postępowania z nimi
* Obsługiwane scenariusze

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł zawiera informacje i procedury dotyczące przywracania maszyn wirtualnych wdrożonych przy użyciu modelu Resource Manager.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) tworzy kopie zapasowe danych plików i aplikacji. Więcej informacji o obsługiwanych obciążeniach można znaleźć [tutaj](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Dane kopii zapasowej programu DPM można przechowywane na taśmie na dysku, lub kopię zapasową na platformie Azure w usłudze Kopia zapasowa Microsoft Azure. Program DPM współdziała z usługi Kopia zapasowa Azure w następujący sposób:

* **Program DPM wdrożony jako fizycznego serwera lub lokalnej maszyny wirtualnej** — Jeśli program DPM wdrożony jako serwera fizycznego lub lokalnej maszyny wirtualnej funkcji Hyper-V można tworzyć kopie zapasowe danych w magazynie usług odzyskiwania, oprócz dysków i taśm kopii zapasowych.
* **Program DPM wdrożony jako maszyna wirtualna platformy Azure** — z programu System Center 2012 R2 z pakietem Update 3 na program DPM można wdrożyć na maszynie wirtualnej platformy Azure. Program DPM wdrożony jako maszyna wirtualna platformy Azure, można utworzyć kopię zapasową danych na dyskach platformy Azure dołączonych do maszyny Wirtualnej lub odciążyć magazyn danych przez tworzenie kopii zapasowych magazynu usług odzyskiwania.

## <a name="why-back-up-dpm-to-azure"></a>Dlaczego warto utworzyć kopię zapasową programu DPM na platformie Azure?
Tworzenie kopii zapasowych serwerów programu DPM na platformie Azure korzyści biznesowe obejmują:

* Lokalnego wdrożenia programu DPM Użyj Azure zamiast wdrożenia długoterminowych na taśmie.
* Do wdrażania programu DPM na maszynie Wirtualnej na platformie Azure, odciążyć magazyn z dysku platformy Azure. Przechowywania starszych danych w magazynie usług odzyskiwania pozwala na skalowanie w górę firmy przez zapisanie nowych danych na dysku.

## <a name="prerequisites"></a>Wymagania wstępne
Przygotuj kopia zapasowa Azure, aby utworzyć kopię zapasową danych programu DPM w następujący sposób:

1. **Tworzenie magazynu usług odzyskiwania** — Tworzenie magazynu w portalu Azure.
2. **Pobierz poświadczenia magazynu** — Pobierz poświadczenia służy do rejestrowania serwera DPM w magazynie usług odzyskiwania.
3. **Zainstaluj agenta kopii zapasowej Azure** — Zainstaluj agenta na każdym serwerze DPM.
4. **Zarejestruj serwer** — rejestrowania serwera DPM w magazynie usług odzyskiwania.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Kluczowe definicje
Oto niektóre kluczowe definicje dla kopii zapasowej Azure dla programu DPM:

1. **Magazyn poświadczeń** — poświadczenia magazynu są potrzebne do uwierzytelnienia w celu wysyłania danych kopii zapasowej do określonych magazynu w usłudze Kopia zapasowa Azure maszyny. Można pobrać z magazynu, a jest ważny przez 48 godzin.
2. **Hasło** — hasło jest używane do szyfrowania kopii zapasowych w chmurze. Zapisz plik w bezpiecznym miejscu, ponieważ jest on wymagany podczas operacji odzyskiwania.
3. **Zabezpieczający numer PIN** — Jeśli włączono [ustawienia zabezpieczeń](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) magazynu, kod PIN zabezpieczeń jest potrzebne do wykonywania krytycznej operacji tworzenia kopii zapasowej. To uwierzytelnianie wieloskładnikowe dodawana jest kolejna warstwa zabezpieczeń. 
4. **Folder odzyskiwania** — jest fraza kopii zapasowych z chmury są tymczasowo pobrane podczas odzyskiwania chmury. Jego rozmiar około powinien być taki sam, jak rozmiar elementów kopii zapasowych, które chcesz odzyskać równolegle.


### <a name="1-create-a-recovery-services-vault"></a>1. Tworzenie magazynu usługi Recovery Services
Aby utworzyć magazyn usługi Recovery Services:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum kliknij opcję **Przeglądaj** i na liście zasobów wpisz ciąg **Usługi odzyskiwania**. Po rozpoczęciu pisania zawartość listy będzie filtrowana w oparciu o wpisywane dane. Kliknij opcję **Magazyn Usług odzyskiwania**.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    Zostanie wyświetlona lista magazynów usług odzyskiwania.
3. W menu **Magazyny usługi Recovery Services** kliknij pozycję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    Magazyn usług odzyskiwania otwiera menu monitowania o podanie **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 5](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikalna w tej subskrypcji platformy Azure. Wpisz nazwę o długości od 2 do 50 znaków. Musi ona rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.
5. Kliknij pozycję **Subskrypcja**, aby wyświetlić listę dostępnych subskrypcji. Jeśli nie masz pewności, której subskrypcji użyć, wybierz domyślną (lub sugerowaną). Większa liczba opcji do wyboru będzie dostępna tylko w przypadku, gdy konto organizacji jest skojarzone z wieloma subskrypcjami platformy Azure.
6. Kliknij pozycję **Grupa zasobów**, aby wyświetlić listę dostępnych grup zasobów, lub kliknij pozycję **Nowa**, aby utworzyć nową grupę zasobów. Pełne informacje na temat grup zasobów można znaleźć w temacie [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
7. Kliknij pozycję **Lokalizacja**, aby wybrać region geograficzny magazynu.
8. Kliknij przycisk **Utwórz**. Utworzenie magazynu Usług odzyskiwania może zająć trochę czasu. Monitoruj powiadomienia o stanie wyświetlane w prawej górnej części obszaru portalu.
   Po utworzeniu magazynu zostanie otwarty w portalu.

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu
Dla opcji replikacji magazynu można wybrać magazynowanie nadmiarowe geograficznie lub lokalnie. Domyślnie magazyn jest nadmiarowy geograficznie. Pozostaw tę opcję ustawioną na magazyn geograficznie nadmiarowy, jeśli jest to Twoja podstawowa kopia zapasowa. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz skorzystać z tańszej, ale mniej trwałej opcji. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w temacie [Azure Storage replication overview](../storage/common/storage-redundancy.md) (Omówienie replikacji usługi Azure Storage).

Aby edytować ustawienia replikacji magazynu:

1. Wybierz magazyn do otwarcia pulpitu nawigacyjnego magazynu i menu Ustawienia. Jeśli **ustawienia** nie zostanie otwarte menu, kliknij przycisk **wszystkie ustawienia** na pulpicie nawigacyjnym magazynu.
2. Na **ustawienia** menu, kliknij przycisk **infrastruktura kopii zapasowej** > **konfiguracji kopii zapasowej** otworzyć **konfiguracji kopii zapasowej**menu. Na **konfiguracji kopii zapasowej** menu, wybierz opcję replikacji magazynu dla magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Po wybraniu opcji magazynu dla swojego magazynu możesz skojarzyć z nim maszynę wirtualną. Aby rozpocząć kojarzenie, należy odnaleźć i zarejestrować maszyny wirtualne Azure.

### <a name="2-download-vault-credentials"></a>2. Pobieranie poświadczeń magazynu
Plik poświadczeń magazynu jest to certyfikat wygenerowany przez portal dla każdego magazynu kopii zapasowych. Portal przekazuje następnie klucz publiczny do usługi Access Control Service (ACS). Klucz prywatny certyfikatu jest udostępniany jako część przepływu pracy, który został podany jako dane wejściowe w przepływie pracy rejestracji maszyny. To jest uwierzytelniany w celu wysyłania danych kopii zapasowej do określonych magazynu w usłudze Kopia zapasowa Azure maszyny.

Poświadczenie magazynu jest używane tylko podczas przepływu pracy związanego z rejestracją. Jest odpowiedzialny za użytkownika, aby upewnić się, że plik poświadczeń magazynu nie zostaną ujawnione. Jeśli wpadnie on w ręce nieuczciwego użytkownika, może posłużyć do rejestrowania innych komputerów względem tego samego magazynu. Jednak ponieważ dane kopii zapasowej jest zaszyfrowany przy użyciu hasła, który należy do klienta, dane kopii zapasowej nie zostaną ujawnione. Aby uniknąć tego problemu, poświadczenia magazynu są ustawiane wygaśnie po upływie 48hrs. Możesz pobrać poświadczenia magazynu usług odzyskiwania dowolną liczbę razy —, lecz tylko najnowszy plik poświadczeń magazynu jest stosowany podczas rejestracji przepływ pracy.

Plik poświadczeń magazynu jest pobierana za pośrednictwem bezpiecznego kanału z portalu Azure. Usługi Azure Backup nie rozpoznaje klucza prywatnego certyfikatu i klucza prywatnego w portalu lub usługa nie jest trwały. Wykonaj następujące kroki, aby pobrać plik poświadczeń magazynu na komputerze lokalnym.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz magazyn usług odzyskiwania, który chcesz zarejestrować komputera DPM.
3. Otwiera menu Ustawienia domyślne. Jeśli jest zamknięty, kliknij polecenie **ustawienia** na pulpicie nawigacyjnym magazynu, aby otworzyć menu Ustawienia. W menu Ustawienia kliknij **właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. Na stronie właściwości kliknij **Pobierz** w obszarze **kopię zapasową poświadczeń**. Portalu generuje plik poświadczeń magazynu, który ma zostać udostępnione do pobrania.

    ![Do pobrania](./media/backup-azure-dpm-introduction/vault-credentials.png)

Portalu wygeneruje poświadczenie magazynu przy użyciu kombinacji nazwy magazynu i bieżącą datę. Kliknij przycisk **zapisać** Pobierz poświadczenia magazynu do konta lokalnego pobieranie folderu lub wybierz polecenie Zapisz jako z menu Zapisz, aby określić lokalizację dla poświadczenia magazynu. Go będzie zajmować na minutę dla pliku, który ma zostać wygenerowane.

### <a name="note"></a>Uwaga
* Upewnij się, że plik poświadczeń magazynu został zapisany w lokalizacji, w których może uzyskać dostęp z komputera. Jeśli jest on przechowywany w udziale plików/SMB, sprawdź, czy uprawnienia dostępu.
* Plik poświadczeń magazynu jest używana tylko podczas rejestracji przepływ pracy.
* Plik poświadczeń magazynu wygasa po 48hrs i może zostać pobrany z portalu.

### <a name="3-install-backup-agent"></a>3. Zainstaluj agenta kopii zapasowej
Po utworzeniu magazynu usługi Kopia zapasowa Azure, należy zainstalować agenta na wszystkich maszynach systemu Windows (Windows Server, klienta systemu Windows, serwer System Center Data Protection Manager lub serwer kopii zapasowej Azure machine), które umożliwia wykonywanie kopii zapasowych danych i aplikacji na platformie Azure.

1. Otwórz magazyn usług odzyskiwania, który chcesz zarejestrować komputera DPM.
2. Otwiera menu Ustawienia domyślne. Jeśli jest zamknięty, kliknij polecenie **ustawienia** aby otworzyć menu Ustawienia. W menu Ustawienia kliknij **właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na stronie Ustawienia kliknij **Pobierz** w obszarze **Agent usługi Kopia zapasowa Azure**.

    ![Do pobrania](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Pobrany agenta, uruchom MARSAgentInstaller.exe do uruchomienia instalacji agenta usługi Kopia zapasowa Azure. Wybierz folder instalacji i folderu pliki tymczasowe wymagane dla agenta. Określona lokalizacja pamięci podręcznej musi mieć wolnego miejsca, czyli co najmniej 5% danych kopii zapasowej.
4. Jeśli używasz serwera proxy, aby nawiązać połączenie z Internetem, w **konfiguracji serwera Proxy** ekranu, wprowadź szczegóły serwera proxy. Jeśli korzystasz z uwierzytelnionego serwera proxy, wprowadź szczegóły nazwy i hasła użytkownika na tym ekranie.
5. Agent usługi Kopia zapasowa Azure instaluje program .NET Framework 4.5 i programu Windows PowerShell (Jeśli nie jest dostępna) do ukończenia instalacji.
6. Po zainstalowaniu agenta **Zamknij** okna.

   ![Zamykanie](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. Aby **rejestrowania serwera DPM** w magazynie w **zarządzania** karcie, kliknij na **Online**. Następnie wybierz opcję **zarejestrować**. Zostanie otwarty Kreator instalacji zarejestrować.
8. Jeśli używasz serwera proxy, aby nawiązać połączenie z Internetem, w **konfiguracji serwera Proxy** ekranu, wprowadź szczegóły serwera proxy. Jeśli korzystasz z uwierzytelnionego serwera proxy, wprowadź szczegóły nazwy i hasła użytkownika na tym ekranie.

    ![Konfiguracja serwera proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Na ekranie poświadczenia magazynu Wyszukaj i wybierz plik poświadczeń magazynu, który został wcześniej pobrany.

    ![Poświadczenia magazynu](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    Plik poświadczeń magazynu jest prawidłowa tylko dla 48 godzin (po jej pobraniu, z portalu). Jeśli wystąpi błąd tego ekranu (na przykład "poświadczenia magazynu wygasł podany plik"), zaloguj się do portalu Azure i pobierania poświadczeń magazynu pliku ponownie.

    Upewnij się, że plik poświadczeń magazynu jest dostępny w lokalizacji, które są dostępne dla aplikacji Instalatora. Jeśli wystąpią dostępu pokrewne błędy, skopiuj plik poświadczeń magazynu do tymczasowej lokalizacji na tym komputerze i spróbuj ponownie wykonać operację.

    Jeśli wystąpi błąd poświadczeń nieprawidłowy magazynu (na przykład "magazynu nieprawidłowe poświadczenia podane") plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. Spróbuj ponownie wykonać operację po pobraniu nowego pliku poświadczeń magazynu z portalu. Ten błąd występuje zazwyczaj, gdy użytkownik kliknie **poświadczenia magazynu pobierania** opcji w portalu Azure, szybkie naciśnięcie. W takim przypadku tylko drugi plik poświadczeń magazynu jest nieprawidłowa.
10. Do kontrolowania użycia przepustowości sieci podczas pracy oraz godzin nieroboczych w **ustawienia ograniczania** ekranu, można ustawić limity użycia przepustowości oraz zdefiniować godziny pracy i pracy z systemem innym niż.

    ![Ustawienie ograniczenia przepustowości](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. W **ustawienia folderu odzyskiwania** ekranu, przejdź do folderu, w którym pliki pobierane z usługi Azure zostanie przemieszczony tymczasowo.

    ![Ustawienia folderu odzyskiwania](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. W **ustawienie szyfrowania** ekranu, można wygenerować hasło lub podać hasło (co najmniej 16 znaków). Pamiętaj, aby zapisać hasła w bezpiecznym miejscu.

    ![Szyfrowanie](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > W przypadku zgubienia lub zapomnienia hasła; Microsoft nie może pomóc w odzyskaniu danych kopii zapasowej. Hasło szyfrowania jest właścicielem użytkownika końcowego i Microsoft nie ma wgląd w miejscu hasło używane przez użytkownika końcowego. Zapisz plik w bezpiecznym miejscu, ponieważ jest on wymagany podczas operacji odzyskiwania.
    >
    >
13. Po kliknięciu **zarejestrować** przycisk, komputer został pomyślnie zarejestrowany w magazynie i są teraz rozpocząć tworzenie kopii zapasowych Microsoft Azure.
14. Podczas korzystania z programu Data Protection Manager, można zmodyfikować ustawienia określone podczas rejestracji przepływu pracy, klikając **Konfiguruj** opcję, wybierając **Online** w obszarze **zarządzania** kartę.

## <a name="requirements-and-limitations"></a>Wymagania (i ograniczenia)
* Program DPM może działać jako serwer fizyczny lub maszyny wirtualnej funkcji Hyper-V zainstalowanych w programie System Center 2012 SP1 lub System Center 2012 R2. Mogą również działać jako maszyna wirtualna platformy Azure z programu System Center 2012 R2 z co najmniej programu DPM 2012 R2 Update Rollup 3 lub maszyny wirtualnej systemu Windows w środowisku programu VMWare na nim uruchomione w programie System Center 2012 R2 z pakietem zbiorczym aktualizacji 5.
* Jeśli używasz programu DPM z programu System Center 2012 z dodatkiem SP1 należy zainstalować aktualizacji Przywróć zapasowej 2 dla programu System Center Data Protection Manager z dodatkiem SP1. Jest to wymagane, aby można było zainstalować agenta kopii zapasowej Azure.
* Serwer programu DPM powinien mieć środowiska Windows PowerShell i .net Framework 4.5 zainstalowane.
* Program DPM można wykonać kopię zapasową większości obciążeń kopia zapasowa Azure. Pełną listę, co ma obsługiwane Zobacz Kopia zapasowa Azure obsługuje poniższe elementy.
* Nie można odzyskać danych przechowywanych w usłudze Kopia zapasowa Azure przy użyciu opcji "Kopiuj na taśmę".
* Konieczne będzie konto platformy Azure z włączoną funkcją kopia zapasowa Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Przeczytaj informacje o [cennikiem usługi Kopia zapasowa Azure](https://azure.microsoft.com/pricing/details/backup/).
* Za pomocą usługi Kopia zapasowa Azure wymaga agenta kopii zapasowej Azure można zainstalować na serwerach, które chcesz utworzyć kopię zapasową. Każdy serwer musi mieć co najmniej 5% rozmiaru danych, która jest tworzona kopia zapasowa, dostępne jako wolnego miejsca w magazynie lokalnym. Na przykład tworzenie kopii zapasowej 100 GB danych wymaga co najmniej 5 GB wolnego miejsca w lokalizacji pliki tymczasowe.
* Dane będą przechowywane w magazynie usługi Azure storage. Nie ma żadnego limitu ilości danych, które użytkownik może wykonać kopię zapasową kopia zapasowa Azure magazynu, ale rozmiar źródła danych (na przykład maszyny wirtualnej lub bazy danych) nie powinna przekraczać 54400 GB.

Tworzenie kopii zapasowych Azure przypadku są obsługiwane następujące typy plików:

* Zaszyfrowane (tylko pełne kopie zapasowe)
* Skompresowane (obsługą przyrostowych kopii zapasowych)
* Rozrzedzone (obsługą przyrostowych kopii zapasowych)
* Skompresowane i rozrzedzone (traktowane jako rozrzedzone)

I te nie są obsługiwane:

* Serwery w systemach plików z uwzględnieniem wielkości liter nie są obsługiwane.
* Twarde linki (pomijane)
* (Pomijane) punkty ponownej analizy
* Zaszyfrowane i skompresowane (pomijane)
* Zaszyfrowane i rozrzedzone (pomijane)
* Skompresowany strumień
* Rozrzedzony strumień

> [!NOTE]
> Od w System Center 2012 DPM z dodatkiem SP1 lub nowszym można tworzyć kopie zapasowe dużych obciążeń chronionych przez program DPM na platformie Azure przy użyciu kopii zapasowej Microsoft Azure.
>
>
