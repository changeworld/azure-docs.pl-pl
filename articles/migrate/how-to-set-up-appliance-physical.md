---
title: Skonfiguruj urządzenie do oceny serwerów fizycznych za pomocą oceny serwera Azure Migrate
description: Opisuje sposób konfigurowania urządzenia do oceny serwerów fizycznych przy użyciu oceny serwera Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/21/2019
ms.author: raynew
ms.openlocfilehash: ddd659e8cbcb54a36868848d0c6327f294d531b1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512628"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurowanie urządzenia dla serwerów fizycznych

W tym artykule opisano sposób konfigurowania urządzenia Azure Migrate w przypadku oceniania serwerów fizycznych za pomocą narzędzia do oceny serwera Azure Migrate:.

Urządzenie Azure Migrate jest lekkim urządzeniem używanym do oceny Azure Migrate Server, aby wykonać następujące czynności:

- Odnajdywanie serwerów lokalnych.
- Wysyłanie metadanych i danych wydajności dla odnalezionych serwerów w celu Azure Migrate oceny serwera.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie:
- Pobierz spakowany plik ze skryptem Instalatora Azure Migrate z Azure Portal.
- Wyodrębnij zawartość z pliku spakowanego. Uruchom konsolę programu PowerShell z uprawnieniami administracyjnymi.
- Wykonaj skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

## <a name="download-the-installer-script"></a>Pobierz skrypt Instalatora

Pobierz spakowany plik dla urządzenia.

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie maszyn** > **są zwirtualizowane maszyny?** kliknij pozycję **niezwirtualizowane/inne**.
3. Kliknij pozycję **Pobierz** , aby pobrać plik zip.

    ![Pobierz maszynę wirtualną](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla wirtualnego dysku twardego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  W przypadku 1.19.05.10 w wersji urządzenia wygenerowany skrót powinien być zgodny z tymi ustawieniami.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="run-the-azure-migrate-installer-script"></a>Uruchom skrypt Instalatora Azure Migrate
= Skrypt Instalatora wykonuje następujące czynności:

- Instaluje agentów i aplikację sieci Web na potrzeby odnajdywania i oceny serwera fizycznego.
- Zainstaluj role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobierz i zainstaluj moduł, który ma zostać przezapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) z trwałymi ustawieniami ustawień dla Azure Migrate.
- Tworzy następujące pliki w ścieżce:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Uruchom skrypt w następujący sposób:

1. Wyodrębnij spakowany plik do folderu na serwerze, który będzie hostować urządzenie.
2. Uruchom program PowerShell na powyższym serwerze z uprawnieniami administracyjnymi (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell do folderu, w którym zawartość została wyodrębniona z pobranego pliku spakowanego.
4. Uruchom skrypt, uruchamiając następujące polecenie:
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
Po pomyślnym zakończeniu działania skryptu zostanie uruchomiona aplikacja sieci Web urządzenia.



### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może nawiązać połączenie z wymaganymi [adresami URL platformy Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację z poziomu pulpitu, klikając skrót do aplikacji.
2. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby funkcja odnajdywania maszyn wirtualnych działała prawidłowo.
    - **Instalowanie aktualizacji**: ocena serwera Azure Migrate sprawdza, czy na urządzeniu zainstalowano najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
2. Na nowej karcie Zaloguj się przy użyciu poświadczeń platformy Azure. 
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
4. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij pozycję **zarejestruj**.


## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Nawiąż połączenie z urządzeniem z serwerami fizycznymi, a następnie Uruchom odnajdywanie.

1. Kliknij pozycję **Dodaj poświadczenia** , aby określić poświadczenia konta, które będą używane przez urządzenie do odnajdywania serwerów.  
2. Określ **system operacyjny**, przyjazną nazwę dla poświadczeń, **nazwę użytkownika** i **hasło** , a następnie kliknij przycisk **Dodaj**.
Można dodać jeden zestaw poświadczeń dla serwerów z systemami Windows i Linux.
4. Kliknij przycisk **Dodaj serwer**i określ szczegóły serwera — nazwa FQDN/adres IP i przyjazna nazwa poświadczenia (jeden wpis na wiersz), aby połączyć się z serwerem.
3. Kliknij przycisk **Weryfikuj**. Po sprawdzeniu poprawności zostanie wyświetlona lista serwerów, które mogą zostać odnalezione.
    - Jeśli walidacja nie powiedzie się dla serwera, przejrzyj błąd, umieszczając kursor nad ikoną w kolumnie **stan** . Usuń problemy i ponownie sprawdź poprawność.
    - Aby usunąć serwer, wybierz pozycję > **Usuń**.
4. Po sprawdzeniu poprawności kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć proces odnajdywania.

Spowoduje to uruchomienie odnajdywania. Metadane wykrytych maszyn wirtualnych mogą pojawić się w Azure Portal około 15 minut. 

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po zakończeniu odnajdywania możesz sprawdzić, czy serwery są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery** > **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**. 


## <a name="next-steps"></a>Następne kroki

Wypróbuj [ocenę serwerów fizycznych](tutorial-assess-physical.md) za pomocą oceny Azure Migrate Server.
