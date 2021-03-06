---
title: Konfigurowanie urządzenia migracji platformy Azure dla serwerów fizycznych
description: Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure do oceny serwera fizycznego.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b60a30e5e30ee81cbaca7d5e4691ccedac2462b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598174"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurowanie urządzenia dla serwerów fizycznych

W tym artykule opisano sposób konfigurowania urządzenia migracji platformy Azure, jeśli oceniasz serwery fizyczne za pomocą narzędzia Azure Migrate: Server Assessment.

Urządzenie migracji platformy Azure jest urządzeniem lekkim, używanym przez usługę Azure Migrate Server Assessment w celu wykonania następujących czynności:

- Odnajdowanie serwerów lokalnych.
- Wysyłanie metadanych i danych o wydajności wykrytych serwerów do oceny serwera migracji platformy Azure.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu migracji platformy Azure.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie, które:
- Pobierz spakowany plik za pomocą skryptu instalatora migracji usługi Azure z witryny Azure portal.
- Wyodrębnij zawartość z pliku spakowane. Uruchom konsolę programu PowerShell z uprawnieniami administracyjnymi.
- Wykonaj skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj go w projekcie migracji platformy Azure.

## <a name="download-the-installer-script"></a>Pobierz skrypt instalatora

Pobierz spakowany plik urządzenia.

1. W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, kliknij nie **zwirtualizowany/Inne**.
3. Kliknij **przycisk Pobierz,** aby pobrać spakowany plik.

    ![Pobieranie maszyny wirtualnej](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Weryfikowanie zabezpieczeń

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla dysku VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  W przypadku najnowszej wersji urządzenia wygenerowany skrót powinien być zgodny z tymi [ustawieniami](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security).



## <a name="run-the-azure-migrate-installer-script"></a>Uruchamianie skryptu instalatora migracji platformy Azure
Skrypt instalatora wykonuje następujące czynności:

- Instaluje agentów i aplikację sieci web do wykrywania i oceny serwera fizycznego.
- Zainstaluj role systemu Windows, w tym Usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobierz i zainstaluje moduł wielokrotnego zapisu usługi IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) ze szczegółami ustawień trwałych dla migracji platformy Azure.
- Tworzy następujące pliki pod ścieżką:
    - **Pliki konfiguracyjne:**%Programdata%\Microsoft Azure\Config
    - **Pliki dziennika:**%Programdata%\Microsoft Azure\Logs

Uruchom skrypt w następujący sposób:

1. Wyodrębnij spakowany plik do folderu na serwerze, w który będzie obsługiwał urządzenie.
2. Uruchom program PowerShell na powyższym serwerze z uprawnieniami administracyjnymi (podwyższonymi).
3. Zmień katalog programu PowerShell na folder, w którym zawartość została wyodrębniona z pobranego pliku spakowanym.
4. Uruchom skrypt o nazwie **AzureMigrateInstaller.ps1,** uruchamiając następujące polecenie:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
Skrypt uruchomi aplikację internetową urządzenia po pomyślnym zakończeniu.

W przypadku jakichkolwiek problemów można uzyskać dostęp do dzienników skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log w celu rozwiązania problemu.

> [!NOTE]
> Proszę nie wykonywać skryptu instalatora migracji platformy Azure na istniejącym urządzeniu migracji platformy Azure.

### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może łączyć się z wymaganymi [adresami URL platformy Azure](migrate-appliance.md#url-access).

## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. Otwórz przeglądarkę na dowolnym komputerze, który może połączyć się z maszyną wirtualną, i otwórz adres URL aplikacji internetowej urządzenia: **https:// nazwę urządzenia lub adres*IP:* 44368**.

   Alternatywnie możesz otworzyć aplikację z pulpitu, klikając skrót do aplikacji.
2. W aplikacji sieci web > **Konfigurowanie wymagań wstępnych**wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność:** Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres http://ProxyIPAddress http://ProxyFQDNserwera proxy i port nasłuchiwania w formularzu lub .
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu:** czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym, aby odnajdowanie maszyn wirtualnych działało poprawnie.
    - **Zainstaluj aktualizacje:** Ocena programu Azure Migrate Server sprawdza, czy urządzenie ma zainstalowane najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie za pomocą usługi Azure Migrate

1. Kliknij **pozycję Zaloguj**się . Jeśli nie jest wyświetlany, upewnij się, że wyłączono blokowanie wyskakujących wyskakujących w przeglądarce.
2. Na nowej karcie zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu swojej nazwy użytkownika i hasła.
    - Logowanie za pomocą numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu wróć do aplikacji sieci web.
4. Wybierz subskrypcję, w której został utworzony projekt migracji platformy Azure. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij **pozycję Zarejestruj**.


## <a name="start-continuous-discovery"></a>Rozpocznij ciągłe odnajdowanie

Połącz się z urządzenia z serwerami fizycznymi i rozpocznij odnajdowanie.

1. Kliknij **przycisk Dodaj poświadczenia,** aby określić poświadczenia konta używane przez urządzenie do odnajdywać serwery.  
2. Określ **system operacyjny**, przyjazną nazwę poświadczeń, nazwę **użytkownika** i **hasło** oraz kliknij przycisk **Dodaj**.
Można dodać jeden zestaw poświadczeń dla serwerów Windows i Linux.
4. Kliknij **przycisk Dodaj serwer**i określ szczegóły serwera — adres FQDN/IP i przyjazną nazwę poświadczeń (jeden wpis na wiersz), aby połączyć się z serwerem.
3. Kliknij pozycję **Validate** (Waliduj). Po weryfikacji zostanie wyświetlona lista serwerów, które można odnajdować.
    - Jeśli sprawdzanie poprawności nie powiedzie się dla serwera, przejrzyj błąd, najeżdżając kursorem na ikonę w kolumnie **Stan.** Rozwiązywanie problemów i sprawdzanie poprawności ponownie.
    - Aby usunąć serwer, zaznacz opcję > **Usuń**.
4. Po weryfikacji kliknij przycisk **Zapisz i rozpocznij odnajdowanie,** aby rozpocząć proces odnajdywania.

To rozpoczyna odnajdowanie. Trwa około 15 minut dla metadanych odnalezionych maszyn wirtualnych do wyświetlenia w witrynie Azure portal.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po zakończeniu odnajdywania można sprawdzić, czy serwery są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny migracji platformy Azure.
2. W **obszarze Migracja platformy Azure — serwery, strona** > **Migracja platformy Azure: Ocena serwera,** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.


## <a name="next-steps"></a>Następne kroki

Wypróbuj [ocenę serwerów fizycznych za](tutorial-assess-physical.md) pomocą oceny serwera migracji usługi Azure.
