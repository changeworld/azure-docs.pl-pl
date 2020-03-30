---
title: Przygotowanie serwera programu DPM do utworzenia kopii zapasowej obciążeń
description: W tym artykule dowiesz się, jak przygotować się do tworzenia kopii zapasowych programu System Center Data Protection Manager (DPM) na platformie Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 2119d46ca6102286ca879777058a49938b501ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273464"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Przygotowanie do utworzenia kopii zapasowej obciążeń na platformie Azure za pomocą programu System Center DPM

W tym artykule wyjaśniono, jak przygotować się do tworzenia kopii zapasowych programu Us Manager (DPM) w usłudze Azure przy użyciu usługi Azure Backup.

Artykuł zawiera:

- Omówienie wdrażania programu DPM za pomocą usługi Azure Backup.
- Wymagania wstępne i ograniczenia dotyczące korzystania z usługi Azure Backup z programem DPM.
- Kroki dotyczące przygotowywania platformy Azure, w tym konfigurowanie magazynu kopii zapasowych usług odzyskiwania i opcjonalnie modyfikowanie typu magazynu platformy Azure dla magazynu.
- Kroki dotyczące przygotowywania serwera programu DPM, w tym pobieranie poświadczeń magazynu, instalowanie agenta usługi Azure Backup i rejestrowanie serwera programu DPM w magazynie.
- Porady dotyczące rozwiązywania problemów z typowymi błędami.

## <a name="why-back-up-dpm-to-azure"></a>Dlaczego warto zrobić kopii zapasowej programu DPM na platformie Azure?

[Program System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) kopiuje zapasy danych plików i aplikacji. Program DPM współdziała z usługą Azure Backup w następujący sposób:

- **Program DPM uruchomiony na serwerze fizycznym lub lokalnej maszynie wirtualnej** — oprócz kopii zapasowej dysku i taśmy można wykonać kopię zapasową danych w magazynie kopii zapasowych na platformie Azure.
- **Program DPM uruchomiony na maszynie wirtualnej platformy Azure** — z programu System Center 2012 R2 z aktualizacją 3 lub nowszą można wdrożyć program DPM na maszynie Wirtualnej platformy Azure. Można wykonać kopię zapasową danych na dyskach platformy Azure dołączonych do maszyny Wirtualnej lub użyć usługi Azure Backup do utworzenia kopii zapasowej danych w magazynie kopii zapasowej.

Korzyści biznesowe wynikające z tworzenia kopii zapasowych serwerów programu DPM na platformie Azure obejmują:

- W przypadku lokalnego programu DPM usługa Azure Backup stanowi alternatywę dla długoterminowego wdrażania na taśmie.
- W przypadku programu DPM uruchomionego na maszynie Wirtualnej platformy Azure usługa Azure Backup umożliwia odciążanie magazynu z dysku platformy Azure. Przechowywanie starszych danych w magazynie kopii zapasowych umożliwia skalowanie firmy przez przechowywanie nowych danych na dysku.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

**Ustawienie** | **Wymaganie**
--- | ---
Program DPM na maszynie wirtualnej platformy Azure | System Center 2012 R2 z pakietem zbiorczym aktualizacji programu DPM 2012 R2 3 lub nowszym.
Program DPM na serwerze fizycznym | System Center 2012 z dodatku SP1 lub nowszym; System Center 2012 R2.
Program DPM na maszynie wirtualnej funkcji Hyper-V | System Center 2012 z dodatku SP1 lub nowszym; System Center 2012 R2.
Program DPM na maszynie wirtualnej VMware | System Center 2012 R2 z pakietem zbiorczym aktualizacji 5 lub nowszym.
Składniki | Serwer programu DPM powinien mieć zainstalowane programy Windows PowerShell i .NET Framework 4.5.
Obsługiwane aplikacje | [Dowiedz się,](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) dla jakich elementów można wykonywać kopie zapasowe w programie DPM.
Obsługiwane typy plików | Tworzenie kopii zapasowej tych typów plików za pomocą usługi Azure Backup: Encrypted (tylko pełne kopie zapasowe); Skompresowane (obsługiwane przyrostowe kopie zapasowe); Rozrzedzone (obsługiwane przyrostowe kopie zapasowe); Skompresowane i rzadkie (traktowane jako rzadkie).
Nieobsługiwały typy plików | Serwery w systemach plików z uwzględnieniem wielkości liter; twarde linki (pominięte); punkty reparse (pominięte); szyfrowane i skompresowane (pominięte); zaszyfrowane i rzadkie (pominięte); Skompresowany strumień; strumień analizy.
Magazyn lokalny | Każdy komputer, którego kopię zapasową chcesz uzyskać, musi mieć lokalny wolny magazyn, który ma co najmniej 5% rozmiaru danych, których kopię zapasową jest. Na przykład tworzenie kopii zapasowej 100 GB danych wymaga co najmniej 5 GB wolnego miejsca w lokalizacji podstaw.
Przechowywanie w przechowalni | Nie ma limitu ilości danych, które można utworzyć kopię zapasową w magazynie kopii zapasowej platformy Azure, ale rozmiar źródła danych (na przykład maszyny wirtualnej lub bazy danych) nie powinien przekraczać 54 400 GB.
Azure ExpressRoute | Jeśli usługa Azure ExpressRoute jest skonfigurowana z prywatną lub microsoftową komunikacją równorzędną, nie można jej użyć do utworzenia kopii zapasowej danych na platformie Azure.<br/><br/> Jeśli usługa Azure ExpressRoute jest skonfigurowana przy użyciu komunikacji równorzędnej publicznej, może służyć do tworzenia kopii zapasowych danych na platformie Azure.<br/><br/> **Uwaga:** Publiczne peering jest przestarzałe dla nowych obwodów.
Agent usługi Azure Backup | Jeśli program DPM jest uruchomiony w dodatku SP1 programu System Center 2012, zainstaluj pakiet zbiorczy 2 lub nowszy dla dodatku SP1 programu DPM. Jest to wymagane do instalacji agenta.<br/><br/> W tym artykule opisano sposób wdrażania najnowszej wersji agenta usługi Azure Backup, znanego również jako agent usługi odzyskiwania platformy Microsoft Azure (MARS). Jeśli masz wcześniej wdrożoną wersję, zaktualizuj do najnowszej wersji, aby upewnić się, że kopia zapasowa działa zgodnie z oczekiwaniami.

Przed rozpoczęciem potrzebujesz konta platformy Azure z włączoną funkcją Azure Backup. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Przeczytaj o [cenach usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modyfikowanie ustawień magazynu

Można wybrać magazyn geograficznie nadmiarowy i magazyn lokalnie nadmiarowy.

- Domyślnie magazyn jest nadmiarowy geograficznie.
- Jeśli magazyn jest podstawową kopią zapasową, pozostaw opcję ustawioną na magazyn geograficznie nadmiarowy. Jeśli chcesz tańszą opcję, która nie jest tak trwałe, należy użyć poniższej procedury, aby skonfigurować lokalnie nadmiarowego magazynu.
- Dowiedz się więcej o [usłudze Azure Storage](../storage/common/storage-redundancy.md)oraz o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego.](../storage/common/storage-redundancy-lrs.md)
- Zmodyfikuj ustawienia magazynu przed początkową kopią zapasową. Jeśli kopia zapasowa elementu została już utworzona, przed zmodyfikowaniem ustawień magazynu należy zatrzymać jego kopię zapasową w przechowalni.

Aby edytować ustawienia replikacji magazynu:

1. Otwórz pulpit nawigacyjny przechowalni.

2. W **obszarze Zarządzanie**kliknij pozycję **Infrastruktura kopii zapasowych**.

3. W menu **Konfiguracja kopii zapasowej** wybierz opcję magazynowania dla przechowalni.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Pobieranie poświadczeń magazynu

Poświadczenia magazynu są używane podczas rejestrowania serwera programu DPM w przechowalni.

- Plik poświadczeń magazynu jest to certyfikat wygenerowany przez portal dla każdego magazynu kopii zapasowych.
- Portal przekazuje następnie klucz publiczny do usługi Access Control Service (ACS).
- Podczas przepływu pracy rejestracji maszyny klucz prywatny certyfikatu jest udostępniany użytkownikowi, co uwierzytelnia urządzenie.
- Na podstawie uwierzytelniania usługa Azure Backup wysyła dane do zidentyfikowanego magazynu.

### <a name="best-practices-for-vault-credentials"></a>Najważniejsze wskazówki dotyczące poświadczeń magazynu

Aby uzyskać poświadczenia, pobierz plik poświadczeń magazynu za pośrednictwem bezpiecznego kanału z witryny Azure Portal:

- Poświadczenia magazynu są używane tylko podczas przepływu pracy rejestracji.
- Twoim obowiązkiem jest upewnienie się, że plik poświadczeń magazynu jest bezpieczny i nie został naruszony.
  - Jeśli kontrola poświadczeń zostanie utracona, poświadczenia magazynu mogą służyć do rejestrowania innych komputerów do magazynu.
  - Jednak dane kopii zapasowej są szyfrowane przy użyciu hasła należącego do klienta, więc nie można naruszyć istniejących danych kopii zapasowej.
- Upewnij się, że plik jest zapisywany w lokalizacji, do którą można uzyskać dostęp z serwera programu DPM. Jeśli jest przechowywany w udziale plików/SMB, sprawdź uprawnienia dostępu.
- Poświadczenia magazynu wygasają po 48 godzinach. Możesz pobrać nowe poświadczenia magazynu tyle razy, ile potrzeba. Jednak tylko ostatni plik poświadczeń magazynu może być używany podczas przepływu pracy rejestracji.
- Usługa Azure Backup nie jest świadoma klucza prywatnego certyfikatu, a klucz prywatny nie jest dostępny w portalu ani w usłudze.

Pobierz plik poświadczeń przechowalni do komputera lokalnego w następujący sposób:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Otwórz magazyn, w którym chcesz zarejestrować serwer programu DPM.
3. W **obszarze Ustawienia**kliknij pozycję **Właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. W **obszarze Właściwości kopii** > **zapasowej poświadczeń**kliknij przycisk **Pobierz**. Portal generuje plik poświadczeń magazynu przy użyciu kombinacji nazwy przechowalni i bieżącej daty i udostępnia go do pobrania.

    ![Pobierz](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Kliknij **przycisk Zapisz,** aby pobrać poświadczenia przechowalni do folderu, lub **zapisz jako** i określ lokalizację. Trwa do minuty, aby plik został wygenerowany.

## <a name="install-the-backup-agent"></a>Instalowanie agenta kopii zapasowej

Na każdym komputerze, na który jest utworzona kopia zapasowa usługi Azure Backup, musi być zainstalowany agent kopii zapasowej (znany również jako agent marsjańskiej usługi odzyskiwania platformy Microsoft Azure). Zainstaluj agenta na serwerze programu DPM w następujący sposób:

1. Otwórz magazyn, do którego chcesz zarejestrować serwer programu DPM.
2. W **obszarze Ustawienia**kliknij pozycję **Właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na stronie **Właściwości** pobierz agenta kopii zapasowej platformy Azure.

    ![Pobierz](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Po pobraniu uruchom plik MARSAGENTInstaller.exe. , aby zainstalować agenta na komputerze DPM.
5. Wybierz folder instalacyjny i folder pamięci podręcznej dla agenta. Wolne miejsce w pamięci podręcznej musi wynosić co najmniej 5% danych kopii zapasowej.
6. Jeśli używasz serwera proxy do łączenia się z Internetem, na ekranie **konfiguracji serwera proxy** wprowadź szczegóły serwera proxy. Jeśli używasz uwierzytelnionego serwera proxy, wprowadź nazwę użytkownika i szczegóły hasła na tym ekranie.
7. Agent usługi Azure Backup instaluje programy .NET Framework 4.5 i Windows PowerShell (jeśli nie są zainstalowane), aby ukończyć instalację.
8. Po zainstalowaniu agenta **Zamknij** okno.

    ![Zamykanie](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Rejestrowanie serwera programu DPM w przechowalni

1. W konsoli Administrator programu DPM > **Zarządzanie**kliknij pozycję **Online**. Wybierz pozycję **Zarejestruj**. Otworzy się Kreator rejestru serwera.
2. W **obszarze Konfiguracja serwera proxy**należy określić ustawienia serwera proxy zgodnie z wymaganiami.

    ![Konfiguracja serwera proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. W **programie Backup Vault**przejdź do pobranego pliku poświadczeń przechowalni i wybierz go.

    ![Poświadczenia magazynu](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. W **ustawieniach ograniczania przepustowości**można opcjonalnie włączyć ograniczanie przepustowości dla kopii zapasowych. Można ustawić ograniczenia prędkości dla określania godzin pracy i dni.

    ![Ustawienie ograniczania przepustowości](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. W **ustawieniach folderów odzyskiwania**określ lokalizację, która może być używana podczas odzyskiwania danych.

    - Usługa Azure Backup używa tej lokalizacji jako tymczasowego obszaru przechowywania odzyskanych danych.
    - Po zakończeniu odzyskiwania danych usługa Azure Backup oczyści dane w tym obszarze.
    - Lokalizacja musi mieć wystarczająco dużo miejsca, aby pomieścić elementy, które można przewidzieć odzyskiwanie równolegle.

    ![Ustawienie folderu odzyskiwania](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. W **ustawieniach szyfrowania**wygeneruj lub podaj hasło.

    - Hasło służy do szyfrowania kopii zapasowych do chmury.
    - Określ co najmniej 16 znaków.
    - Zapisz plik w bezpiecznej lokalizacji, jest potrzebny do odzyskania.

    ![Szyfrowanie](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Jesteś właścicielem hasła szyfrowania i Microsoft nie ma wglądu w nim.
    > Jeśli hasło zostanie utracone lub zapomniane; Firma Microsoft nie może pomóc w odzyskiwaniu danych kopii zapasowej.

7. Kliknij **przycisk Zarejestruj się,** aby zarejestrować serwer programu DPM w przechowalni.

Po pomyślnym zarejestrowaniu serwera w magazynie i gotowym do rozpoczęcia tworzenia kopii zapasowej na platformie Microsoft Azure. Musisz skonfigurować grupę ochrony w konsoli programu DPM do tworzenia kopii zapasowych obciążeń na platformie Azure. [Dowiedz się, jak](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) wdrożyć grupy ochrony.

## <a name="troubleshoot-vault-credentials"></a>Rozwiązywanie problemów z poświadczeniami magazynu

### <a name="expiration-error"></a>Błąd wygaśnięcia

Plik poświadczeń magazynu jest ważny tylko przez 48 godzin (po pobraniu z portalu). Jeśli napotkasz błąd na tym ekranie (na przykład "Plik poświadczeń magazynu pod warunkiem wygasł"), zaloguj się do witryny Azure portal i ponownie pobierz plik poświadczeń magazynu.

### <a name="access-error"></a>Błąd programu Access

Upewnij się, że plik poświadczeń magazynu jest dostępny w lokalizacji, do którego aplikacja instaluje dostęp. Jeśli wystąpią błędy związane z dostępem, skopiuj plik poświadczeń magazynu do tymczasowej lokalizacji na tym komputerze i ponów próbę wykonania operacji.

### <a name="invalid-credentials-error"></a>Nieprawidłowy błąd poświadczeń

Jeśli wystąpi nieprawidłowy błąd poświadczeń magazynu (na przykład "Nieprawidłowe poświadczenia magazynu pod warunkiem"), plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania.

- Ponów próbę wykonania operacji po pobraniu nowego pliku poświadczeń magazynu z portalu.
- Ten błąd jest zazwyczaj widoczny po kliknięciu opcji **Pobierz poświadczenia magazynu** w witrynie Azure portal, dwa razy w krótkim odstępie czasu. W takim przypadku tylko drugi plik poświadczeń magazynu jest prawidłowy.
