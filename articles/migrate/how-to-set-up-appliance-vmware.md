---
title: Konfigurowanie urządzenia Azure Migrate dla oprogramowania VMware
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceniania i migrowania maszyn wirtualnych VMware.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 1489d29f854b02cf493493fe022c73dc050e2615
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185854"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurowanie urządzenia dla maszyn wirtualnych VMware

W tym artykule opisano sposób konfigurowania urządzenia Azure Migrate, jeśli oceniasz maszyny wirtualne VMware za pomocą narzędzia do oceny Azure Migrate Server lub przeprowadzisz migrację maszyn wirtualnych VMware na platformę Azure z migracją bez agenta za pomocą narzędzia migracji Azure Migrate Server.

Urządzenie maszyny wirtualnej VMware jest lekkim urządzeniem używanym przez Azure Migrate oceny/migracji serwera, aby wykonać następujące czynności:

- Odnajdywanie lokalnych maszyn wirtualnych VMware.
- Wysyłanie metadanych i danych wydajności dla odnalezionych maszyn wirtualnych w celu Azure Migrate oceny/migracji serwera.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie:
- Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

## <a name="download-the-ova-template"></a>Pobierz szablon komórki jajowe

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **Odnajdź maszyny** > **Czy maszyny są zwirtualizowane?** kliknij pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. Kliknij pozycję **Pobierz**, aby pobrać plik szablonu OVA.



### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla komórek jajowych:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. W przypadku najnowszej wersji urządzenia wygenerowany skrót powinien być zgodny z tymi ustawieniami.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


## <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję **File** (Plik)  >  **Deploy OVF Template** (Wdróż szablon OVF).
2. W Kreatorze wdrażania szablonu OVF > **Źródło**Określ lokalizację pliku komórek jajowych.
3. W polu **Nazwa** i **Lokalizacja**Określ przyjazną nazwę maszyny wirtualnej. Wybierz obiekt spisu, w którym będzie hostowana maszyna wirtualna.
5. W obszarze **host/klaster**Określ hosta lub klaster, na którym będzie URUCHAMIANA maszyna wirtualna.
6. W obszarze **Magazyn**określ miejsce docelowe magazynu dla maszyny wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W polu **mapowanie sieci**Określ sieć, z którą zostanie nawiązane połączenie z maszyną wirtualną. Sieć wymaga łączności z Internetem, aby można było wysyłać metadane do oceny serwera Azure Migrate.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z [adresami URL platformy Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
4. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Aby odnajdywanie działało prawidłowo, czas na urządzeniu powinien być zsynchronizowany z czasem Internetu.
    - **Zainstaluj aktualizacje**: Azure Migrate sprawdza, czy są zainstalowane najnowsze aktualizacje urządzeń.
    - **Zainstaluj VDDK**Azure Migrate: sprawdza, czy jest zainstalowany zestaw SDK programu VMware VSPHERE (VDDK).
        - Migracje platformy Azure używają VDDK do replikowania maszyn podczas migracji na platformę Azure.
        - Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu.

## <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
2. Na nowej karcie Zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
2. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate. Następnie wybierz projekt.
3. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
4. Kliknij pozycję **zarejestruj**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Rozpocznij odnajdowanie ciągłe, podając poświadczenia vCenter Server i maszyny wirtualnej

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

### <a name="specify-vcenter-server-details"></a>Określanie szczegółów programu vCenter Server
1. W obszarze **określ vCenter Server Szczegóły**Określ nazwę (FQDN) lub adres IP vCenter Server. Możesz opuścić port domyślny lub określić niestandardowy port, na którym nasłuchuje vCenter Server.
2. W polu **Nazwa użytkownika** i **hasło**określ poświadczenia konta tylko do odczytu, które będą używane przez urządzenie do odnajdywania maszyn wirtualnych na serwerze vCenter. Upewnij się, że konto ma [wymagane uprawnienia do odnajdywania](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Zakres odnajdywania można ograniczyć, ograniczając dostęp do konta vCenter odpowiednio; Więcej informacji o określaniu zakresu odnajdywania [znajdziesz tutaj](tutorial-assess-vmware.md#scoping-discovery).
3. Kliknij pozycję **Sprawdź poprawność połączenia** , aby upewnić się, że urządzenie może połączyć się z vCenter Server.

### <a name="specify-vm-credentials"></a>Określ poświadczenia maszyny wirtualnej
W przypadku odnajdywania aplikacji, ról i funkcji oraz wizualizacji zależności maszyn wirtualnych można podać poświadczenia maszyny wirtualnej, które mają dostęp do maszyn wirtualnych VMware. Można dodać jedno poświadczenie dla maszyn wirtualnych z systemem Windows i jedno poświadczenie dla maszyn wirtualnych z systemem Linux. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) o wymaganych uprawnieniach dostępu.

> [!NOTE]
> Dane wejściowe są opcjonalne i są konieczne do włączenia funkcji odnajdywania aplikacji i wizualizacji zależności bez wykorzystania agentów.

1. W obszarze **Znajdź aplikacje i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia**.
2. Wybierz **system operacyjny**.
3. Podaj przyjazną nazwę dla poświadczenia.
4. W polu **Nazwa użytkownika** i **hasło**Określ konto, które ma co najmniej dostęp gościa na maszynach wirtualnych.
5. Kliknij pozycję **Add** (Dodaj).

Po określeniu vCenter Server i poświadczeń maszyny wirtualnej (opcjonalnie) kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć odnajdywanie środowiska lokalnego.

Metadane wykrytych maszyn wirtualnych będą wyświetlane w portalu na około 15 minut. Odnajdywanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu, a czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku maszyn wirtualnych 500 potrzeba około 1 godziny, aby spis aplikacji był wyświetlany w portalu Azure Migrate.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z samouczkami dotyczącymi [oceny oprogramowania VMware](tutorial-assess-vmware.md) i [migracji bez wykorzystania agentów](tutorial-migrate-vmware.md).
