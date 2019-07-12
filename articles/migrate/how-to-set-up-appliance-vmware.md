---
title: Konfigurowanie urządzenia dla platformy Azure migracji ocena/Migracja serwera dla maszyn wirtualnych VMware | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania urządzenia na potrzeby odnajdywania, oceny i migracji bez wykorzystania agentów maszyn wirtualnych programu VMware przy użyciu usługi Azure migracji ocena/Migracja serwera.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811728"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurowanie urządzenia dla maszyn wirtualnych VMware

W tym artykule opisano sposób konfigurowania urządzenia w usłudze Azure Migrate, jeśli jesteś oceny maszyn wirtualnych VMware za pomocą narzędzia oceny Server migracji platformy Azure lub migrowania maszyn wirtualnych VMware na platformę Azure z migracją bez agenta przy użyciu narzędzia do migracji serwera migracji platformy Azure.

Urządzenie maszyna wirtualna oprogramowania VMware to uproszczone urządzenie używane przez Azure migracji ocena/Migracja serwera, wykonaj następujące czynności:

- Odnajdywanie lokalnych maszyn wirtualnych z programu VMware.
- Wyślij dane metadanych i wydajności dla odnalezionych maszyn wirtualnych do platformy Azure migracji ocena/Migracja serwera.

[Dowiedz się więcej](migrate-appliance.md) dotyczące urządzenia Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie możesz:
- Pobieranie pliku OVA szablonu, a następnie zaimportować go do serwera vCenter.
- Utworzyć to urządzenie, a następnie sprawdź, czy można nawiązać oceny Server migracji platformy Azure. 
- Konfigurowanie urządzenia po raz pierwszy, a następnie zarejestruj go w projekcie usługi Azure Migrate.

## <a name="download-the-ova-template"></a>Pobierz szablon OVA

1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Ocena serwera**, kliknij przycisk **odnajdź**.
2. W **odnajdź maszyny** > **są maszynach zwirtualizowane?** , kliknij przycisk **tak, przy użyciu funkcji VMWare vSphere hypervisor**.
3. Kliknij przycisk **Pobierz** do pobrania. Plik szablonu OVA.



### <a name="verify-security"></a>Weryfikacja zabezpieczeń

Sprawdź, czy plik OVA jest bezpieczne, przed przystąpieniem do wdrażania.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Dla wersji urządzenia 1.0.0.5 wygenerowany skrót powinien odpowiadać następującym ustawieniom. 

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Tworzenie urządzenia maszyny Wirtualnej

Zaimportuj pobrany plik, a następnie utwórz Maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję **File** (Plik)  >  **Deploy OVF Template** (Wdróż szablon OVF).
2. W Kreatorze wdrażania szablonu OVF > **źródła**, określ lokalizację pliku OVA.
3. W **nazwa** i **lokalizacji**, określ przyjazną nazwę dla maszyny Wirtualnej. Wybierz obiekt magazynu, w którym będzie hostowana maszyna wirtualna.
5. W **hostem/klastrem**, określ host lub klaster, na które uruchomi maszynę Wirtualną.
6. W **magazynu**, określ docelowy magazyn dla maszyny Wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W **mapowanie sieci**, określ sieć, z którą połączy się maszyna wirtualna. Sieć musi mieć połączenie z Internetem w celu wysyłania metadanych do oceny Server migracji platformy Azure.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


### <a name="verify-appliance-access-to-azure"></a>Sprawdź dostęp urządzenia do platformy Azure

Upewnij się, że urządzenie maszyn wirtualnych można połączyć się z [adresów URL usługi Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Zapewniają języka, strefy czasowej i hasła dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną Wirtualną i otwórz adres URL aplikacji sieci web urządzenia: **https://*urządzenia nazwa lub adres IP*: 44368**.

   Alternatywnie możesz otworzyć aplikację na pulpicie urządzenia, klikając skrót aplikacji.
4. W aplikacji sieci web > **Skonfiguruj wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność**: Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij przycisk **ustawienia serwera Proxy**, a następnie określ adres serwera proxy i port nasłuchujący w formie http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Czas synchronizacji**: Czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym do odnajdowania zapewnić prawidłowe działanie.
    - **Instalowanie aktualizacji**: Usługa Azure Migrate umożliwia sprawdzenie, czy są zainstalowane najnowsze aktualizacje urządzenia.
    - **Zainstaluj VDDK**: Usługa Azure Migrate sprawdza, czy zainstalowano VMWare vSphere wirtualnego dysku Development Kit (VDDK).
        - Azure Migrates używa VDDK do replikowania maszyn podczas migracji na platformę Azure.
        - Pobierz VDDK 6.7 z programu VMware i Wyodrębnij zawartość pliku zip pobranego w określonej lokalizacji na urządzeniu.

## <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w usłudze Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli nie pojawia się, upewnij się, że zostały wyłączone, blokowanie wyskakujących okienek w przeglądarce.
2. Na nowej karcie Zaloguj się przy użyciu swoich poświadczeń platformy Azure. 
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Zaloguj się przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Przejdź wstecz do aplikacji sieci web.
2. Wybierz subskrypcję, w której został utworzony projekt usługi Azure Migrate. Następnie wybierz projekt.
3. Określ nazwę dla tego urządzenia. Nazwa powinna być alfanumeryczne z 14 znaków lub mniej.
4. Kliknij przycisk **zarejestrować**.


## <a name="start-continuous-discovery"></a>Uruchom odnajdowanie ciągłego

Teraz nawiązać połączenie z urządzenia do programu vCenter Server i uruchom odnajdywanie maszyn wirtualnych. 

1. W **Określ szczegóły serwera vCenter**, określ nazwę (FQDN) lub adres IP serwera vcenter. Pozostaw domyślny port lub określić numery portów, na którym nasłuchuje serwer vCenter.
2. W **nazwa_użytkownika** i **hasło**, określ poświadczenia konta tylko do odczytu, które urządzenie ma użyć do odnalezienia maszyn wirtualnych na serwerze vCenter. Upewnij się, że konto ma [wymaganych uprawnień do odnajdywania](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Kliknij przycisk **sprawdzanie poprawności połączenia** aby upewnić się, czy urządzenie może połączyć się serwerem vCenter.
4. Po nawiązaniu połączenia kliknij **Zapisz i uruchom odnajdywanie**.


Spowoduje to uruchomienie odnajdywania. Trwa około 15 minut metadanych wykrytych maszynach wirtualnych są wyświetlane w portalu. 


## <a name="next-steps"></a>Kolejne kroki

Przejrzyj samouczki dotyczące [oceny VMware](tutorial-assess-vmware.md) i [migracji bez wykorzystania agentów](tutorial-migrate-vmware.md).
