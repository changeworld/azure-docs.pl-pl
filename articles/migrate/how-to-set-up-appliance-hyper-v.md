---
title: Konfigurowanie urządzenia dla platformy Azure migracji ocena/Migracja serwera dla maszyn wirtualnych funkcji Hyper-V | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania urządzenia na potrzeby odnajdywania, oceny i migracji bez wykorzystania agentów maszyn wirtualnych funkcji Hyper-V za pomocą usługi Azure migracji ocena/Migracja serwera.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811767"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurowanie urządzenia, dla maszyn wirtualnych funkcji Hyper-V

W tym artykule opisano sposób konfigurowania urządzenia w usłudze Azure Migrate, jeśli jesteś oceny maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia oceny Server migracji platformy Azure lub migrowania maszyn wirtualnych VMware do platformy Azure przy użyciu narzędzia do migracji serwera migracji platformy Azure.

Urządzenie maszyn wirtualnych funkcji Hyper-V to lekki urządzenie używane przez Azure migracji ocena/Migracja serwera, wykonaj następujące czynności:

- Odnajdywanie maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym.
- Wyślij dane metadanych i wydajności dla odnalezionych maszyn wirtualnych do platformy Azure migracji ocena/Migracja serwera.

[Dowiedz się więcej](migrate-appliance.md) dotyczące urządzenia Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie możesz:
- Pobierz skompresowany wirtualnego dysku twardego funkcji Hyper-V w witrynie Azure portal.
- Utworzyć to urządzenie, a następnie sprawdź, czy można nawiązać oceny Server migracji platformy Azure. 
- Konfigurowanie urządzenia po raz pierwszy, a następnie zarejestruj go w projekcie usługi Azure Migrate.

## <a name="download-the-vhd"></a>Pobieranie wirtualnego dysku twardego

Pobierz skompresowany szablon wirtualnego dysku twardego dla urządzenia.

1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Ocena serwera**, kliknij przycisk **odnajdź**.
2. W **odnajdź maszyny** > **są maszynach zwirtualizowane?** , kliknij przycisk **tak, przy użyciu funkcji Hyper-V**.
3. Kliknij przycisk **Pobierz** można pobrać pliku wirtualnego dysku twardego.

    ![Pobieranie maszyny Wirtualnej](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Weryfikacja zabezpieczeń

Sprawdzanie pliku zip jest bezpieczne, przed jego wdrożeniem.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku dysku VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Dla wersji urządzenia 1.19.05.10 wygenerowany skrót powinien odpowiadać następującym ustawieniom.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>Tworzenie urządzenia maszyny Wirtualnej

Zaimportuj pobrany plik, a następnie utwórz maszynę Wirtualną.

1. Wyodrębnij spakowany plik wirtualnego dysku twardego do folderu na hoście funkcji Hyper-V, który będzie obsługiwać urządzenia maszyny Wirtualnej. Trzy foldery zostały wyodrębnione.
2. Otwórz Menedżera funkcji Hyper-V. W **akcje**, kliknij przycisk **importowanie maszyny wirtualnej**.

    ![Wdrażanie wirtualnego dysku twardego](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. W Kreatorze importowania maszyny wirtualnej > **przed rozpoczęciem**, kliknij przycisk **dalej**.
3. W **zlokalizuj Folder**, określ folder zawierający wyodrębnione wirtualnego dysku twardego. Następnie kliknij przycisk **Next** (Dalej).
1. W **Wybieranie maszyny wirtualnej**, kliknij przycisk **dalej**.
2. W **wybierz typ importu**, kliknij przycisk **kopiowania maszyny wirtualnej (Utwórz nowy unikatowy identyfikator)** . Następnie kliknij przycisk **Next** (Dalej).
3. W **wybierz lokalizację docelową**, pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W **foldery przechowywania**, pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W **sieci wybierz**, określ przełącznik wirtualny, który będzie używany przez maszynę Wirtualną. Przełącznik wymaga połączenia internetowego z wysyłania danych do platformy Azure.
6. W **Podsumowanie**, przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **maszyn wirtualnych**, uruchom maszynę Wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Sprawdź dostęp urządzenia do platformy Azure

Upewnij się, że urządzenie maszyn wirtualnych można połączyć się z [adresów URL usługi Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. W Menedżerze funkcji Hyper-V > **maszyn wirtualnych**, kliknij prawym przyciskiem myszy maszynę Wirtualną > **Connect**.
2. Zapewniają języka, strefy czasowej i hasła dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną Wirtualną i otwórz adres URL aplikacji sieci web urządzenia: **https://*urządzenia nazwa lub adres IP*: 44368**.

   Alternatywnie możesz otworzyć aplikację na pulpicie urządzenia, klikając skrót aplikacji.
1. W aplikacji sieci web > **Skonfiguruj wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność**: Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij przycisk **ustawienia serwera Proxy**, a następnie określ adres serwera proxy i port nasłuchujący w formie http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Czas synchronizacji**: Czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym odnajdowania maszyn wirtualnych zapewnić prawidłowe działanie.
    - **Instalowanie aktualizacji**: Ocena serwera migracji platformy Azure sprawdza, czy urządzenie ma zainstalowane najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w usłudze Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli nie pojawia się, upewnij się, że zostały wyłączone, blokowanie wyskakujących okienek w przeglądarce.
2. Na nowej karcie Zaloguj się przy użyciu swoich poświadczeń platformy Azure. 
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Zaloguj się przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Przejdź wstecz do aplikacji sieci web.
4. Wybierz subskrypcję, w której został utworzony projekt usługi Azure Migrate. Następnie wybierz projekt.
5. Określ nazwę dla tego urządzenia. Nazwa powinna być alfanumeryczne z 14 znaków lub mniej.
6. Kliknij przycisk **zarejestrować**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla wirtualnych dysków twardych SMB

Jeśli korzystasz z wirtualnych dysków twardych w małych i średnich firmach, należy włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. Aby to zrobić przez urządzenie:

1. Na urządzeniu maszyny Wirtualnej Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowe nazwy hosta.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Alternatywnie wykonaj następujące czynności w lokalnej edytorze zasad grupy na urządzeniu:
    - W **lokalne zasady komputera** > **konfiguracji komputera**, kliknij przycisk **Szablony administracyjne** > **systemu**  >  **Poświadczenia delegowania**.
    - Kliknij dwukrotnie **Zezwalaj na delegowanie świeżych poświadczeń**i wybierz **włączone**.
    - W **opcje**, kliknij przycisk **Pokaż**, i Dodaj każdy host funkcji Hyper-V, odnajdywać do listy, za pomocą **wsman /** jako prefiksu.
    - W **delegowanie poświadczeń**, kliknij dwukrotnie **Zezwalaj na delegowanie świeżych poświadczeń przy użyciu uwierzytelniania NTLM — serwer tylko**. Ponownie Dodaj każdy host funkcji Hyper-V, odnajdywać do listy, za pomocą **wsman /** jako prefiksu.

## <a name="start-continuous-discovery"></a>Uruchom odnajdowanie ciągłego

Nawiązywanie połączenia z urządzenia lub klastrów hostów funkcji Hyper-V i uruchom odnajdowanie maszyn wirtualnych.

1. W **nazwa_użytkownika** i **hasło**, określ poświadczenia konta, które urządzenie ma użyć do odnalezienia maszyn wirtualnych. Określ przyjazną nazwę dla poświadczeń, a następnie kliknij przycisk **Zapisz szczegóły**.
2. Kliknij przycisk **Dodaj host**i określ szczegóły host/klaster funkcji Hyper-V.
3. Kliknij przycisk **zweryfikować**. Po zakończeniu walidacji jest wyświetlana liczba maszyn wirtualnych, które mogą być wykrywane na każdy host/klaster.
    - Jeśli sprawdzanie poprawności nie powiedzie się z hostem, Przejrzyj tekst błędu, umieszczając kursor myszy nad ikoną w **stan** kolumny. Rozwiąż problemy i ponownie zweryfikować.
    - Aby usunąć hosty lub klastry, zaznacz > **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Usuwać można tylko te całego klastra.
    - Możesz dodać klaster, nawet jeśli występują problemy z określonych hostów w klastrze.
4. Po zakończeniu walidacji kliknij **Zapisz i uruchom odnajdywanie** można uruchomić procesu odnajdywania.

Spowoduje to uruchomienie odnajdywania. Trwa około 15 minut metadanych wykrytych maszynach wirtualnych są wyświetlane w witrynie Azure portal. 

## <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu odnajdowania, można sprawdzić, czy maszyny wirtualne są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. W **usługi Azure Migrate — serwery** > **usługi Azure Migrate: Ocena serwera** kliknij ikonę, która wyświetla liczbę elementów w **odnalezione serwery**. 


## <a name="next-steps"></a>Kolejne kroki

Wypróbuj [oceny funkcji Hyper-V](tutorial-assess-hyper-v.md) wraz z oceną serwera migracji platformy Azure.