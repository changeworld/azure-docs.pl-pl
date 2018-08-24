---
title: Tworzenie kopii zapasowych obciążeń na platformie Azure przy użyciu programu DPM
description: Wprowadzenie do tworzenia kopii zapasowej danych programu DPM w magazynie usługi Azure Recovery Services.
services: backup
author: adigan
manager: nkolli
keywords: System Center Data Protection Manager, programu data protection manager, kopia zapasowa programu dpm
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 873e7066bcf51b32c3a7a54e845ffd5a744f407f
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745439"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Przygotowywanie do tworzenia kopii zapasowych obciążeń na platformie Azure przy użyciu programu DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

W tym artykule wyjaśniono, jak utworzyć kopię zapasową danych System Center Data Protection Manager (DPM) do platformy Azure; w tym:

* Jak utworzyć kopię zapasową danych z serwera programu DPM na platformie Azure
* Wymagania wstępne dotyczące uzyskania bezproblemowe środowisko tworzenia kopii zapasowej
* Napotkano błędy typowe i radzenia sobie z nimi
* Obsługiwane scenariusze

> [!NOTE]
> Platforma Azure oferuje dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [usługi Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł zawiera informacje i procedury w celu przywrócenia maszyn wirtualnych wdrożonych przy użyciu modelu usługi Resource Manager.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) tworzy kopie zapasowe danych plików i aplikacji. Więcej informacji na temat obsługiwanych obciążeń znajduje się [tutaj](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Dane kopii zapasowej program DPM może być przechowywany na taśmie, na dysku, lub kopię zapasową na platformie Azure z usługą Microsoft Azure Backup. Program DPM korzysta z usługi Azure Backup w następujący sposób:

* **Wdrożenie programu DPM jako fizyczny serwer lub w środowisku lokalnym maszynę wirtualną** — Program DPM wdrożony jako serwera fizycznego lub lokalnej maszyny wirtualnej funkcji Hyper-V tworzy kopię zapasową danych w magazynie usługi Recovery Services, oprócz dysku i taśmy kopii zapasowej.
* **Program DPM wdrożony jako maszyna wirtualna platformy Azure** — z programu System Center 2012 R2 z aktualizacją Update 3 na program DPM można wdrożyć na maszynie wirtualnej platformy Azure. Program DPM jest wdrożony na maszynie wirtualnej platformy Azure, można utworzyć kopię zapasową danych na dyskach platformy Azure dołączonych do maszyny Wirtualnej lub odciążyć magazyn danych przez tworzenie kopii zapasowych w magazynie usługi Recovery Services.

## <a name="why-back-up-dpm-to-azure"></a>Dlaczego warto tworzyć kopie zapasowe programu DPM na platformie Azure?
Korzyści biznesowe wynikające z tworzenia kopii zapasowej serwerów programu DPM na platformie Azure obejmują:

* Lokalnego wdrożenia programu DPM należy używać systemu Azure jako alternatywę do wdrożenia długoterminowych na taśmie.
* Do wdrożenia programu DPM na maszynie Wirtualnej na platformie Azure, należy odciążyć magazyn z dysku platformy Azure. Przechowywania starszych danych w magazynie usługi Recovery Services umożliwia skalowanie w górę swoją firmę dzięki przechowywaniu nowych danych na dysku.

## <a name="prerequisites"></a>Wymagania wstępne
Przygotowywanie usługi Azure Backup, aby utworzyć kopię zapasową danych programu DPM w następujący sposób:

1. **Utwórz magazyn usługi Recovery Services** — Utwórz magazyn w witrynie Azure portal.
2. **Pobierz poświadczenia magazynu** — Pobierz poświadczenia, których używasz, aby zarejestrować serwer programu DPM przy użyciu magazynu usługi Recovery Services.
3. **Zainstaluj agenta usługi Azure Backup** — Zainstaluj agenta na każdym serwerze programu DPM.
4. **Zarejestruj serwer** — zarejestrować serwer programu DPM przy użyciu magazynu usługi Recovery Services.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Kluczowe definicje
Oto niektóre kluczowe definicje dla kopii zapasowej na platformie Azure dla programu DPM:

1. **Poświadczenia magazynu** — poświadczenia magazynu są wymagane do uwierzytelniania komputera do wysyłania danych kopii zapasowej do zidentyfikowanego magazynu w usłudze Azure Backup. Ona być pobrany z magazynu i jest ważny przez 48 godzin.
2. **Hasło** — hasło jest używane do szyfrowania kopii zapasowych w chmurze. Zapisz plik w bezpiecznym miejscu, ponieważ jest to wymagane podczas operacji odzyskiwania.
3. **Zabezpieczający numer PIN** — Jeśli włączono [ustawienia zabezpieczeń](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) magazynu, zabezpieczający numer PIN jest wymagany w przypadku wykonywania krytycznej operacji tworzenia kopii zapasowej. To uwierzytelnianie wieloskładnikowe dodawana jest kolejna warstwa zabezpieczeń. 
4. **Folder odzyskiwania** — jest frazę, która wykonywania kopii zapasowych w chmurze są tymczasowo pobrane podczas operacji odzyskiwania w chmurze. Jego rozmiar około powinna być równa wielkości elementów kopii zapasowych, które chcesz odzyskać równolegle.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Edytuj replikacji magazynu

Replikacja magazynu umożliwia wybór między magazyn geograficznie nadmiarowy i Magazyn lokalnie nadmiarowy. Domyślnie magazyn jest nadmiarowy geograficznie. Jeśli magazyn jest Twoja podstawowa kopia zapasowa, pozostaw tę opcję ustawioną na magazyn geograficznie nadmiarowy. Chcąc tańszych opcji, która nie jest dość jako trwałe poniższej procedury do skonfigurowania magazyn lokalnie nadmiarowy. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w temacie [Azure Storage replication overview](../storage/common/storage-redundancy.md) (Omówienie replikacji usługi Azure Storage).

Aby edytować ustawienia replikacji magazynu:

> [!NOTE] 
> Konfigurowanie replikacji magazynu, przed włączeniem tworzenia początkowej kopii zapasowej. Jeśli zdecydujesz się zmienić konfigurację replikacji magazynu po wykonaniu kopii zapasowej chronionego elementu, należy zatrzymać ochronę w magazynie przed zmianą konfiguracji magazynu.
>  

1. Wybierz magazyn i otworzyć jego pulpit nawigacyjny magazynu.

2. W **Zarządzaj** kliknij **infrastruktura zapasowa**.

3. Na **konfiguracji kopii zapasowej** menu, wybierz opcję replikacji swojego magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    Po wybraniu opcji magazynu dla swojego magazynu możesz skojarzyć z nim maszynę wirtualną. Aby rozpocząć kojarzenie, należy odnaleźć i zarejestrować maszyny wirtualne Azure.

## <a name="download-vault-credentials"></a>Pobieranie poświadczeń magazynu
Plik poświadczeń magazynu jest to certyfikat wygenerowany przez portal dla każdego magazynu kopii zapasowych. Portal przekazuje następnie klucz publiczny do usługi Access Control Service (ACS). Podczas rejestracji przepływ pracy automatu klucz prywatny certyfikatu jest udostępniany użytkownikowi, który uwierzytelnia komputer. Na podstawie metody uwierzytelniania, usługa Azure Backup wysyła dane do zidentyfikowanego magazynu.

Poświadczenie magazynu jest używane tylko podczas przepływu pracy związanego z rejestracją. Jest odpowiedzialny za użytkownika, aby upewnić się, że plik poświadczeń magazynu nie zostaną ujawnione. W przypadku utraty kontrolę nad poświadczeniami poświadczenia magazynu może służyć do rejestrowania innych maszyn do magazynu. Jednak dane kopii zapasowej są szyfrowane przy użyciu hasła należącego do klienta, dlatego istniejące dane kopii zapasowej nie zostaną ujawnione. Aby rozwiązać ten problem, poświadczenia magazynu wygasają po upływie 48 godzin. Pobierz nowe poświadczenia magazynu dowolną liczbę razy. Jednak podczas przepływu pracy rejestracji można tylko najnowszy plik poświadczeń magazynu.

Plik poświadczeń magazynu jest pobierana za pośrednictwem bezpiecznego kanału w witrynie Azure portal. Usługa Azure Backup nie jest świadomy klucz prywatny certyfikatu, a klucz prywatny nie jest dostępna w portalu lub usługi. Wykonaj następujące kroki, aby pobrać plik poświadczeń magazynu na komputerze lokalnym.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz magazyn usługi Recovery Services, który ma zostać zarejestrowany na serwerze DPM.

3. Otwiera menu Ustawienia domyślne. Jeśli jest zamknięty, kliknij przycisk **ustawienia** na pulpicie nawigacyjnym magazynu, aby otworzyć menu. W **ustawienia** menu, kliknij przycisk **właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Na stronie właściwości w obszarze **kopię zapasową poświadczeń** kliknij **Pobierz**. Portal generuje plik poświadczeń magazynu, który ma zostać udostępnione do pobrania.

    ![Do pobrania](./media/backup-azure-dpm-introduction/vault-credentials.png)

Portal generuje poświadczenia magazynu, przy użyciu kombinacji nazwy magazynu i bieżącą datą. Kliknij przycisk **Zapisz** Pobierz poświadczenia magazynu do folderu pliki do pobrania konta lokalnego lub wybierz Zapisz jako z menu Zapisz, aby określić lokalizację dla poświadczenia magazynu. Jego zajmuje do minuty plik zostanie wygenerowany.

### <a name="note"></a>Uwaga
* Upewnij się, że plik poświadczeń magazynu jest zapisany w lokalizacji, w których może uzyskać dostęp z Twojego komputera. Jeśli jest on przechowywany w udziale plików/SMB, sprawdź, czy uprawnienia dostępu.
* Plik poświadczeń magazynu jest używana tylko podczas przepływu pracy rejestracji.
* Plik poświadczeń magazynu wygasa po upływie 48hrs i można je pobrać z portalu.

## <a name="install-backup-agent"></a>Zainstaluj agenta usługi Kopia zapasowa
Po utworzeniu magazynu usługi Azure Backup agent powinien być zainstalowany na wszystkich maszynach Windows (Windows Server, klienta Windows, serwer System Center Data Protection Manager lub maszynie usługi Azure Backup Server), które umożliwia tworzenie kopii zapasowych danych i aplikacji na platformie Azure .

1. Otwórz magazyn usługi Recovery Services, który chcesz zarejestrować maszynie programu DPM.
2. Otwiera menu Ustawienia domyślne. Jeśli jest zamknięty, kliknij polecenie **ustawienia** można otworzyć menu Ustawienia. W menu Ustawienia, kliknij polecenie **właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na stronie Ustawienia, kliknij przycisk **Pobierz** w obszarze **agenta usługi Azure Backup**.

    ![Do pobrania](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Po pobraniu agenta, uruchom MARSAgentInstaller.exe można uruchomić instalację agenta usługi Azure Backup. Wybierz folder instalacji i folder tymczasowy wymagane dla agenta. Określona lokalizacja pamięci podręcznej musi mieć ilość wolnego miejsca, czyli co najmniej 5% danych kopii zapasowej.

4. Jeśli używasz serwera proxy, aby nawiązać połączenie z Internetem w **konfigurację serwera Proxy** ekranu, należy wprowadzić szczegóły serwera proxy. Jeśli korzystasz z uwierzytelnionego serwera proxy, należy wprowadzić szczegóły nazwy i hasła użytkownika na tym ekranie.

5. Agent usługi Azure Backup instaluje .NET Framework 4.5 i programu Windows PowerShell (Jeśli nie jest dostępne) do ukończenia instalacji.

6. Po zainstalowaniu agenta **Zamknij** okna.

   ![Zamykanie](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. Aby **zarejestrować serwer programu DPM** w magazynie w **zarządzania** karcie, kliknij przycisk **Online**. Następnie wybierz **zarejestrować**. Zostanie otwarty Kreator instalacji zarejestrować.

8. Jeśli używasz serwera proxy, aby nawiązać połączenie z Internetem w **konfigurację serwera Proxy** ekranu, należy wprowadzić szczegóły serwera proxy. Jeśli korzystasz z uwierzytelnionego serwera proxy, należy wprowadzić szczegóły nazwy i hasła użytkownika na tym ekranie.

    ![Konfiguracja serwera proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Na ekranie poświadczenia magazynu przejdź do, a następnie wybierz plik poświadczeń magazynu, który został wcześniej pobrany.

    ![Poświadczenia magazynu](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    Plik poświadczeń magazynu jest prawidłowy tylko w przypadku 48 godzin (po jej pobraniu, z poziomu portalu). Jeśli wystąpi błąd ten ekran (na przykład "poświadczenia magazynu wygasło podany plik"), zaloguj się do witryny Azure portal i pobierania poświadczeń magazynu plik ponownie.

    Upewnij się, że plik poświadczeń magazynu jest dostępny w lokalizacji, które są dostępne dla aplikacji Instalatora. Jeśli wystąpią błędy związane z dostępu, skopiuj plik poświadczeń magazynu do tymczasowej lokalizacji na tej maszynie i ponów operację.

    Jeśli wystąpi błąd poświadczenia magazynu nieprawidłowa (na przykład, "Nieprawidłowy magazyn poświadczeń pod warunkiem") plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. Spróbuj ponownie wykonać operację po pobraniu nowy plik poświadczeń magazynu z portalu. Ten błąd zazwyczaj występuje, gdy użytkownik kliknie **pobierania poświadczeń magazynu** opcji w portalu Azure, szybko. W takim przypadku tylko drugi plik poświadczeń magazynu jest prawidłowa.

10. Do kontrolowania użycia przepustowości sieci podczas pracy i godzin innych niż robocze w **ustawienie ograniczenia przepustowości** ekranu, możesz ustawić limity użycia przepustowości i zdefiniować godziny pracy i innych niż robocze.

    ![Ustawienia ograniczania](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. W **ustawienia folderu odzyskiwania** ekranu, przejdź do folderu, w której pliki są pobierane z platformy Azure zostaną tymczasowo umieszczone.

    ![Ustawienia folderu odzyskiwania](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. W **ustawienie szyfrowania** ekranu, możesz wygenerować hasło albo podać hasło (minimum 16 znaków). Pamiętaj, aby zapisać hasło w bezpiecznej lokalizacji.

    ![Szyfrowanie](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > W przypadku zgubienia lub zapomnienia; hasło Firma Microsoft nie może pomóc w odzyskaniu danych kopii zapasowej. Hasło szyfrowania należą do użytkownika końcowego i firmy Microsoft nie ma wglądu w hasło używane przez użytkownika końcowego. Zapisz plik w bezpiecznym miejscu, ponieważ jest to wymagane podczas operacji odzyskiwania.
    >
    >

13. Po kliknięciu **zarejestrować** przycisk, komputer został pomyślnie zarejestrowany w magazynie i są teraz gotowe do rozpoczęcia wykonywania kopii zapasowych Microsoft Azure.

14. Korzystając z programu Data Protection Manager, można zmodyfikować ustawienia określone podczas rejestracji przepływu pracy, klikając **Konfiguruj** opcję, wybierając **Online** w obszarze **zarządzania**  Kartę.

## <a name="requirements-and-limitations"></a>Wymagania (i ograniczenia)
* Program DPM może działać jako serwer fizyczny lub maszyny wirtualnej funkcji Hyper-V zainstalowana na System Center 2012 SP1 lub System Center 2012 R2. Mogą również działać jako maszynę wirtualną platformy Azure, uruchomiony w programie System Center 2012 R2 z co najmniej programu DPM 2012 R2 Update Rollup 3 lub maszynie wirtualnej Windows w środowisku programu VMWare działające co najmniej program System Center 2012 R2 z pakietem zbiorczym aktualizacji 5.
* Jeśli używasz programu DPM za pomocą programu System Center 2012 SP1 należy zainstalować pakietu zbiorczego aktualizacji 2 dla programu System Center Data Protection Manager SP1. Jest to wymagane, aby można było zainstalować agenta usługi Azure Backup.
* Serwer programu DPM powinien mieć, programu Windows PowerShell i .net Framework 4.5 zainstalowane.
* Program DPM może wykonać kopię zapasową większości obciążeń usługi Azure Backup. Aby uzyskać pełną listę, co ma obsługiwane Zobacz usługi Azure Backup obsługuje poniższe elementy.
* Nie można odzyskać dane przechowywane w usłudze Azure Backup przy użyciu opcji "Kopiuj na taśmę".
* Będzie potrzebne konto platformy Azure z włączoną funkcją kopia zapasowa Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Przeczytaj o [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Korzystanie z usługi Azure Backup wymaga agenta usługi Azure Backup, należy zainstalować na serwerach, które chcesz utworzyć kopię zapasową. Każdy serwer musi mieć co najmniej 5% rozmiaru danych, która jest tworzona kopia zapasowa, dostępne jako lokalne ilość wolnego miejsca. Na przykład tworzenie kopii zapasowej 100 GB danych wymaga co najmniej 5 GB wolnego miejsca w lokalizacji pliki tymczasowe.
* Dane będą przechowywane w magazynie Azure storage. Nie ma żadnego limitu ilości danych, które użytkownik może wykonywać kopie zapasowe w usłudze Azure Backup magazynu, ale rozmiar źródła danych (na przykład maszyny wirtualnej lub bazy danych) nie może przekraczać 54400 GB.

Tworzenie kopii zapasowej na platformie Azure są obsługiwane następujące typy plików:

* Zaszyfrowane (tylko pełne kopie zapasowe)
* Skompresowane (obsługą przyrostowych kopii zapasowych)
* Rozrzedzone (obsługą przyrostowych kopii zapasowych)
* Skompresowane i rozrzedzone (traktowane jako rozrzedzone)

I te nie są obsługiwane:

* Serwery w systemach plików rozróżniana wielkość liter nie są obsługiwane.
* Twarde linki (pomijane)
* (Pomijane) punkty ponownej analizy
* Zaszyfrowane i skompresowane (pomijane)
* Zaszyfrowane i rozrzedzone (pomijane)
* Skompresowany strumień
* Rozrzedzony strumień

> [!NOTE]
> Z programu System Center 2012 DPM z dodatkiem SP1 lub nowszym można wykonać kopię zapasową chronionych obciążeń na platformie Azure.
>
>
