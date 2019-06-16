---
title: Przygotuj serwer programu DPM do tworzenia kopii zapasowych obciążeń na platformie Azure
description: Wprowadzenie do tworzenia kopii zapasowej danych programu DPM w magazynie usługi Azure Recovery Services.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 82e4278a130bb67a1af61ead981259d7bb4e1aa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427442"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Przygotowanie do tworzenia kopii zapasowych obciążeń na platformę Azure za pomocą programu System Center DPM

W tym artykule wyjaśniono, jak przygotować System Center Data Protection Manager (DPM) tworzenia kopii zapasowych na platformie Azure, za pomocą usługi Azure Backup.

Ten artykuł zawiera:

- Omówienie wdrażania programu DPM przy użyciu usługi Azure Backup.
- Wymagania wstępne i ograniczenia dotyczące używania usługi Azure Backup przy użyciu programu DPM.
- Kroki przygotowania systemu Azure, w tym Konfigurowanie magazynu kopii zapasowych usług odzyskiwania i opcjonalnie modyfikowaniu typu Azure storage dla magazynu.
- Kroki przygotowania serwera programu DPM, w tym pobieranie magazynu poświadczeń, instalowania agenta usługi Azure Backup i zarejestrowanie serwera DPM w magazynie.
- Wskazówki dotyczące rozwiązywania problemów dla typowych błędów.


## <a name="why-back-up-dpm-to-azure"></a>Dlaczego warto tworzyć kopie zapasowe programu DPM na platformie Azure?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) tworzy kopie zapasowe danych plików i aplikacji. Program DPM korzysta z usługi Azure Backup w następujący sposób:

* **Program DPM działający na serwerze fizycznym lub lokalnej maszyny Wirtualnej** — Ponadto można kopia zapasowa danych w magazynie kopii zapasowych na platformie Azure, na dysku i taśmie kopii zapasowych.
* **Program DPM działający na Maszynie wirtualnej platformy Azure** — z programu System Center 2012 R2 z aktualizacją Update 3 lub nowszej, możesz wdrożyć program DPM na Maszynie wirtualnej platformy Azure. Możesz utworzyć kopię zapasową danych na dyskach platformy Azure dołączonych do maszyny Wirtualnej lub usługa Azure Backup umożliwia tworzenie kopii zapasowych danych w magazynie kopii zapasowych.

Korzyści biznesowe wynikające z tworzenia kopii zapasowej serwerów programu DPM na platformie Azure obejmują:

* Programu DPM w środowisku lokalnym kopia zapasowa Azure stanowi alternatywę dla wdrożenia długoterminowych na taśmie.
* Program DPM na Maszynie wirtualnej platformy Azure usługi Azure Backup umożliwia można odciążyć magazyn z dysku platformy Azure. Przechowywania starszych danych w magazynie kopii zapasowych umożliwia skalowanie w górę swoją firmę dzięki przechowywaniu nowych danych na dysku.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

**Ustawienie** | **Wymaganie**
--- | ---
Program DPM na maszynie Wirtualnej platformy Azure | System Center 2012 R2 przy użyciu programu DPM 2012 R2 Update Rollup 3 lub nowszej.
Program DPM na serwerze fizycznym | System Center 2012 SP1 lub nowszym; System Center 2012 R2.
Program DPM na maszynie Wirtualnej funkcji Hyper-V | System Center 2012 SP1 lub nowszym; System Center 2012 R2.
Program DPM na maszynie Wirtualnej VMware | System Center 2012 R2 z pakietem zbiorczym aktualizacji 5 lub nowszym.
Składniki | Serwer programu DPM powinien mieć, programu Windows PowerShell i zainstalować program .NET Framework 4.5.
Obsługiwane aplikacje | [Dowiedz się,](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) dla jakich elementów można wykonywać kopie zapasowe w programie DPM.
Obsługiwane typy plików | Następujące typy plików można kopii zapasowej za pomocą usługi Azure Backup: Zaszyfrowane (tylko pełne kopie zapasowe); Skompresowane (obsługą przyrostowych kopii zapasowych); Rozrzedzone (obsługą przyrostowych kopii zapasowych); Skompresowane i rozrzedzone (traktowane jako rozrzedzone).
Nieobsługiwane typy plików | Serwery w systemach plików rozróżniana wielkość liter; twarde linki (pomijane); (pomijane); punkty ponownej analizy zaszyfrowane i skompresowane (pomijane); zaszyfrowane i rozrzedzone (pomijane); Skompresowany strumień; Przeanalizuj strumień.
Magazyn lokalny | Każda maszyna, który chcesz utworzyć kopię zapasową musi mieć lokalne ilość wolnego miejsca, który jest co najmniej 5% rozmiaru danych, która jest tworzona kopia zapasowa. Na przykład tworzenie kopii zapasowej 100 GB danych wymaga co najmniej 5 GB wolnego miejsca w lokalizacji pliki tymczasowe.
Vault storage | Nie ma żadnego limitu ilości danych, które można wykonać kopię zapasową do magazynu usługi Azure Backup, ale rozmiar źródła danych (na przykład maszyny wirtualnej lub bazy danych) nie może przekraczać 54400 GB.
Usługa ExpressRoute systemu Azure | Usługi Azure ExpressRoute jest skonfigurowany za pomocą komunikacji równorzędnej prywatnej lub firmy Microsoft, nie można wykonać kopię zapasową danych na platformie Azure.<br/><br/> Skonfigurowanie usługi Azure ExpressRoute w publicznej komunikacji równorzędnej może służyć do tworzenia kopii zapasowych danych na platformie Azure.<br/><br/> **Uwaga:** Publicznej komunikacji równorzędnej jest przestarzała w przypadku obwodów.
Agent usługi Azure Backup | Jeśli program DPM jest uruchomiony w programie System Center 2012 SP1, należy zainstalować Rollup 2 lub nowszego, programu DPM z dodatkiem SP1. Jest to wymagane do instalacji agenta.<br/><br/> W tym artykule opisano, jak wdrożyć najnowszą wersję agenta usługi Azure Backup, znany także jako agenta usługi Microsoft Azure Recovery Service (MARS). Jeśli masz wcześniejszą wersję wdrożone, zaktualizuj do najnowszej wersji, aby upewnić się, że tej kopii zapasowej działa zgodnie z oczekiwaniami.

Przed rozpoczęciem należy konto platformy Azure z włączoną funkcją kopia zapasowa Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Przeczytaj o [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modyfikuj ustawienia magazynu

Można wybrać magazyn geograficznie nadmiarowy i Magazyn lokalnie nadmiarowy.

- Domyślnie magazyn jest nadmiarowy geograficznie.
- Jeśli magazyn jest Twoja podstawowa kopia zapasowa, pozostaw tę opcję ustawioną na magazyn geograficznie nadmiarowy. Chcąc tańszych opcji, która nie jest dość jako trwałe poniższej procedury do skonfigurowania magazyn lokalnie nadmiarowy.
- Dowiedz się więcej o [usługi Azure storage](../storage/common/storage-redundancy.md)i [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowy](../storage/common/storage-redundancy-lrs.md) opcje magazynu.
- Zmodyfikuj ustawienia magazynu przed tworzenia początkowej kopii zapasowej. Jeśli utworzono już kopię elementu, należy zatrzymać jego kopii zapasowej w magazynie przed przystąpieniem do modyfikacji ustawień magazynu.

Aby edytować ustawienia replikacji magazynu:

1. Otwórz pulpit nawigacyjny magazynu.

2. W **Zarządzaj**, kliknij przycisk **infrastruktura zapasowa**.

3. W **konfiguracji kopii zapasowej** menu, wybierz opcję magazynu opcji magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Pobieranie poświadczeń magazynu

Poświadczenia magazynu są używane podczas zarejestrowania serwera DPM w magazynie.

- Plik poświadczeń magazynu jest to certyfikat wygenerowany przez portal dla każdego magazynu kopii zapasowych.
- Portal przekazuje następnie klucz publiczny do usługi Access Control Service (ACS).
- Podczas rejestracji przepływ pracy automatu klucz prywatny certyfikatu jest udostępniany użytkownikowi, który uwierzytelnia komputer.
- Na podstawie metody uwierzytelniania, usługa Azure Backup wysyła dane do zidentyfikowanego magazynu.

### <a name="best-practices-for-vault-credentials"></a>Najlepsze rozwiązania dotyczące poświadczeń magazynu

Aby uzyskać poświadczenia możesz pobrać plik poświadczeń magazynu za pośrednictwem bezpiecznego kanału w witrynie Azure portal:

- Poświadczenia magazynu są używane tylko podczas przepływu pracy rejestracji.
- Jest odpowiedzialny za upewnij się, że plik poświadczeń magazynu jest bezpieczne, a nie ze złamanymi zabezpieczeniami.
    - W przypadku utraty kontrolę nad poświadczeniami poświadczenia magazynu może służyć do rejestrowania innych maszyn do magazynu.
    - Jednak dane kopii zapasowej są szyfrowane przy użyciu hasła należącego do klienta, dlatego istniejące dane kopii zapasowej nie zostaną ujawnione.
- Upewnij się, że ten plik jest zapisywany w lokalizacji, do którego dostęp można uzyskać z serwera programu DPM. Jeśli jest on przechowywany w udziale plików/SMB, sprawdź, czy uprawnienia dostępu.
- Poświadczenia magazynu wygasają po upływie 48 godzin. Możesz pobrać nowe poświadczenia magazynu dowolną liczbę razy. Jednak podczas przepływu pracy rejestracji można tylko najnowszy plik poświadczeń magazynu.
- Usługa Azure Backup nie jest świadomy klucz prywatny certyfikatu, a klucz prywatny nie jest dostępna w portalu lub usługi.

Pobierz plik poświadczeń magazynu na komputerze lokalnym w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz magazyn, w którym chcesz zarejestrować serwer programu DPM.
3. W **ustawienia**, kliknij przycisk **właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. W **właściwości** > **kopię zapasową poświadczeń**, kliknij przycisk **Pobierz**. Portal generuje plik poświadczeń magazynu przy użyciu kombinacji nazwy magazynu i bieżącą datą i sprawia, że dostępny do pobrania.

    ![Do pobrania](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Kliknij przycisk **Zapisz** pobierania poświadczeń magazynu do folderu lub **Zapisz jako** i określ lokalizację. Jego zajmuje do minuty plik zostanie wygenerowany.


## <a name="install-the-backup-agent"></a>Zainstalować agenta kopii zapasowej

Do każdej maszyny, którego kopia zapasowa jest tworzona kopia zapasowa Azure musi być zainstalowany na nim agent kopii zapasowej (znany także jako agent usług Microsoft Azure Recovery Service (MARS)). Zainstaluj agenta na serwerze programu DPM w następujący sposób:

1. Otwórz magazyn, do którego chcesz zarejestrować serwer programu DPM.
2. W **ustawienia**, kliknij przycisk **właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na **właściwości** strony można pobrać agenta usługi Azure Backup.

    ![Do pobrania](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. Po pobraniu Uruchom MARSAgentInstaller.exe. Aby zainstalować agenta na maszynie programu DPM.
5. Wybierz folder instalacji i folderu pamięci podręcznej dla agenta. Wolne miejsce lokalizacji pamięci podręcznej musi być co najmniej 5% danych kopii zapasowej.
6. Jeśli używasz serwera proxy, aby nawiązać połączenie z Internetem w **konfigurację serwera Proxy** ekranu, należy wprowadzić szczegóły serwera proxy. Jeśli korzystasz z uwierzytelnionego serwera proxy, należy wprowadzić szczegóły nazwy i hasła użytkownika na tym ekranie.
7. Agent usługi Azure Backup instaluje .NET Framework 4.5 i programu Windows PowerShell (Jeśli nie są one zainstalowane) do ukończenia instalacji.
8. Po zainstalowaniu agenta **Zamknij** okna.

    ![Zamykanie](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Rejestrowanie serwera DPM w magazynie

1. W konsoli administratora programu DPM > **zarządzania**, kliknij przycisk **Online**. Wybierz pozycję **Zarejestruj**. Zostanie otwarty kreatora rejestrowania serwera.
2. W **konfigurację serwera Proxy**, określ ustawienia serwera proxy, zgodnie z wymaganiami.

    ![Konfiguracja serwera proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. W **magazynu kopii zapasowych**, wyszukaj i wybierz pobrany plik poświadczeń magazynu.

    ![Poświadczenia magazynu](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. W **ustawienie ograniczenia przepustowości**, można opcjonalnie włączyć ograniczenia przepustowości dla kopii zapasowych. Możesz ustawić limity szybkości dla określić godziny pracy i dni.

    ![Ustawienia ograniczania](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. W **ustawienia folderu odzyskiwania**, określ lokalizację, która może być używana podczas odzyskiwania danych.

    - Usługa Azure Backup korzysta z tej lokalizacji, a jako obszar przechowywania tymczasowych dla swoich danych danymi odzyskanymi.
    - Po odzyskaniu danych kończenie usługi Azure Backup wyczyścić dane, w tym obszarze.
    - Lokalizacja musi mieć wystarczającej ilości miejsca do przechowywania elementów, które możesz równolegle odzyskiwane.

    ![Ustawienia folderu odzyskiwania](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. W **ustawienie szyfrowania** wygenerować lub podaj hasło.

    - Hasło jest używane do szyfrowania kopii zapasowych w chmurze.
    - Określ co najmniej 16 znaków.
    - Zapisz plik w bezpiecznym miejscu jest potrzebny do odzyskania.

    ![Szyfrowanie](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Masz hasło szyfrowania i firmy Microsoft nie ma wglądu do niego.
    > W przypadku zgubienia lub zapomnienia; hasło Firma Microsoft nie może pomóc w odzyskaniu danych kopii zapasowej.

13. Kliknij przycisk **zarejestrować** Aby zarejestrować serwer programu DPM w magazynie.

Po zarejestrowaniu serwera pomyślnie do magazynu i są teraz gotowe do rozpoczęcia wykonywania kopii zapasowych Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Rozwiązywanie problemów z poświadczeń magazynu

### <a name="expiration-error"></a>Błąd wygaśnięcia

Plik poświadczeń magazynu jest prawidłowy tylko w przypadku 48 godzin (po jej pobraniu, z poziomu portalu). Jeśli wystąpi błąd ten ekran (na przykład "poświadczenia magazynu wygasło podany plik"), zaloguj się do witryny Azure portal i pobierania poświadczeń magazynu plik ponownie.

### <a name="access-error"></a>Błąd dostępu

Upewnij się, że plik poświadczeń magazynu jest dostępny w lokalizacji, które są dostępne dla aplikacji Instalatora. Jeśli wystąpią błędy związane z dostępu, skopiuj plik poświadczeń magazynu do tymczasowej lokalizacji na tej maszynie i ponów operację.

### <a name="invalid-credentials-error"></a>Błąd nieprawidłowe poświadczenia

Jeśli wystąpi błąd poświadczenia magazynu nieprawidłowa (na przykład, "Nieprawidłowy magazyn poświadczeń pod warunkiem") plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania.

- Spróbuj ponownie wykonać operację po pobraniu nowy plik poświadczeń magazynu z portalu.
- Ten błąd zazwyczaj występuje po kliknięciu **pobierania poświadczeń magazynu** opcji w witrynie Azure portal, dwa razy z rzędu szybkie. W takim przypadku tylko drugi plik poświadczeń magazynu jest prawidłowa.
