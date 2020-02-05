---
title: Azure VMware Solutions (Automatyczna synchronizacja) — model uprawnień chmury prywatnej
description: Opisuje model, grupy i kategorie uprawnień do chmury prywatnej dotyczącej automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9488c59ead23fb68633ccc56a0df905ebfeea079
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014950"
---
# <a name="avs-private-cloud-permission-model-of-vmware-vcenter"></a>Automatyczna synchronizacja modelu uprawnień chmury prywatnej programu VMware vCenter

Funkcja automatycznej synchronizacji zachowuje pełny dostęp administracyjny do środowiska chmury prywatnej do automatycznej synchronizacji. Każdy klient z numerami synchronizacji otrzymuje wystarczające uprawnienia administracyjne, aby móc wdrażać maszyny wirtualne i zarządzać nimi w ich środowisku. W razie potrzeby można tymczasowo eskalować uprawnienia do wykonywania funkcji administracyjnych.

## <a name="cloud-owner"></a>Właściciel chmury

Podczas tworzenia chmury prywatnej do automatycznej synchronizacji w domenie programu vCenter logowania jednokrotnego jest tworzony użytkownik **CloudOwner** z dostępem do **roli właściciel chmury** w celu zarządzania obiektami w chmurze prywatnej automatycznej synchronizacji. Ten użytkownik może również skonfigurować dodatkowe [źródła tożsamości vCenter](set-vcenter-identity.md)oraz innych użytkowników do programu vCenter w chmurze prywatnej chmury.

> [!NOTE]
> Domyślny użytkownik programu vCenter w chmurze prywatnej do automatycznej synchronizacji jest cloudowner@AVS.local, gdy zostanie utworzona chmura prywatna.

## <a name="user-groups"></a>Grupy użytkowników

Grupa o nazwie **Cloud-Owner-Group** jest tworzona podczas wdrażania chmury prywatnej automatycznej synchronizacji. Użytkownicy w tej grupie mogą administrować różnymi elementami środowiska vSphere w chmurze prywatnej automatycznej synchronizacji. Ta grupa ma automatycznie nadane uprawnienia **roli właściciela chmury** , a użytkownik **CloudOwner** jest dodawany jako członek tej grupy. Automatyczna synchronizacja tworzy dodatkowe grupy z ograniczonymi uprawnieniami w celu ułatwienia zarządzania. Można dodać dowolnego użytkownika do tych wstępnie utworzonych grup, a uprawnienia zdefiniowane poniżej są automatycznie przypisywane do użytkowników w grupach.

### <a name="pre-created-groups"></a>Wstępnie utworzone grupy

| Nazwa grupy | Przeznaczenie | Rola |
| -------- | ------- | ------ |
| Chmura-właściciel-Grupa | Członkowie tej grupy mają uprawnienia administracyjne do automatycznej synchronizacji w chmurze prywatnej programu vCenter | [Chmura-właściciel-rola](#cloud-owner-role) |
| Chmura-Global-Cluster-admin-Group | Członkowie tej grupy mają uprawnienia administracyjne w klastrze programu vCenter do automatycznej synchronizacji w chmurze prywatnej. | [Chmura-klaster-administrator-rola](#cloud-cluster-admin-role) |
| Chmura-Global-Storage-admin-Group | Członkowie tej grupy mogą zarządzać magazynem w ramach automatycznej synchronizacji w chmurze prywatnej programu vCenter | [Cloud-Storage-Administrator-rola](#cloud-storage-admin-role) |
| Chmura-sieć globalna-administrator-Grupa | Członkowie tej grupy mogą zarządzać sieciowymi i rozproszonymi grupami portów w chmurze prywatnej chmury programu vCenter | [Chmura-sieć-administrator-rola](#cloud-network-admin-role) |
| Chmura-Global-VM-admin-Group | Członkowie tej grupy mogą zarządzać maszynami wirtualnymi w chmurze prywatnej chmury programu vCenter | [Chmura-VM-administrator-rola](#cloud-vm-admin-role) |

Aby udzielić indywidualnym użytkownikom uprawnień do zarządzania chmurą prywatną automatycznej synchronizacji, należy utworzyć konta użytkowników Dodaj do odpowiednich grup.

> [!CAUTION]
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group  Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorzy* , a konta usług nie mogą być używane do logowania się do interfejsu użytkownika sieci Web vSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista uprawnień programu vCenter dla ról domyślnych

### <a name="cloud-owner-role"></a>Chmura-właściciel-rola

| **Kategoria** | **Kont** |
|----------|-----------|
| **Alarmy** | Potwierdź alarm <br> Utwórz alarm <br> Wyłącz akcję alarmu <br> Modyfikuj alarm <br> Usuń alarm <br> Ustawianie stanu alarmu |
| **Uprawnienia** | Uprawnienie Modyfikuj |
| **Biblioteka zawartości** | Dodaj element biblioteki <br> Utwórz bibliotekę lokalną <br> Utwórz subskrybowaną bibliotekę <br> Usuń element biblioteki <br> Usuń bibliotekę lokalną <br> Usuń subskrybowaną bibliotekę <br> Pobieranie plików <br> Wyklucz element biblioteki <br> Wykluczanie subskrybowanej biblioteki <br> Importuj magazyn <br> Informacje o subskrypcji sondy <br> Odczytaj magazyn <br> Synchronizuj element biblioteki <br> Synchronizuj subskrybowaną bibliotekę <br> Introspekcji typu <br> Aktualizowanie ustawień konfiguracji <br> Pliki aktualizacji <br> Biblioteka aktualizacji <br> Aktualizuj element biblioteki <br> Aktualizuj bibliotekę lokalną <br> Aktualizuj subskrybowaną bibliotekę <br> Wyświetl ustawienia konfiguracji |
| **Operacje kryptograficzne** | Dodawanie dysku <br> Klonuj <br> Odszyfrowywanie <br> Bezpośredni dostęp <br> Szyfrowanie <br> Szyfruj nowe <br> Zarządzanie usługą KMS <br> Zarządzanie zasadami szyfrowania <br> Zarządzanie kluczami <br> Migrowanie <br> Recrypt <br> Zarejestruj maszynę wirtualną <br> Zarejestruj hosta |
| **Grupa dvPort** | Create <br> Usuń <br> Modyfikuj <br> Operacja zasad <br> Operacja zakresu |
| **Magazyn danych** | Przydziel miejsce <br> Przeglądaj magazyn danych <br> Konfigurowanie magazynu danych <br> Operacje na plikach niskiego poziomu <br> Przenieś magazyn danych <br> Usuń magazyn danych <br> Usuń plik <br> Zmień nazwę magazynu danych <br> Aktualizowanie plików maszyny wirtualnej <br> Aktualizowanie metadanych maszyny wirtualnej |
| **Menedżer agentów ESX** | Config <br> Modyfikuj <br> Wyświetl |
| **Rozszerzenia** | Zarejestruj rozszerzenie <br> Wyrejestruj rozszerzenie <br> Aktualizacja rozszerzenia |
| **Zewnętrzny dostawca statystyk**| Zarejestruj <br> Unregister <br> Aktualizacja |
| **Folder** | Utwórz folder <br> Usuń folder <br> Przenieś folder <br> Zmień nazwę folderu |
| **Global** | Anuluj zadanie <br> Planowanie pojemności <br> Diagnostyka <br> Wyłącz metody <br> Metody włączania <br> Tag globalny <br> Zdrowie <br> Liczba <br> Rejestruj zdarzenie <br> Zarządzanie atrybutami niestandardowymi <br> Serwer proxy <br> Akcja skryptu <br> Menedżerowie usług <br> Ustaw atrybut niestandardowy <br> Tag systemowy |
| **Dostawca aktualizacji kondycji** | Zarejestruj <br> Unregister <br> Aktualizacja |
| **Konfiguracja > hosta** | Konfiguracja partycji magazynu |
| **Magazyn > hosta** | Modyfikuj klaster |
| **Tagowanie vSphere** | Przypisywanie lub cofanie przypisania tagu vSphere <br> Utwórz tag vSphere <br> Utwórz kategorię tagów vSphere <br> Usuń tag vSphere <br> Usuń kategorię tagu vSphere <br> Edytuj tag vSphere <br> Edytuj kategorię tagów vSphere <br> Modyfikuj pole UsedBy dla kategorii <br> Modyfikuj pole UsedBy dla tagu |
| **Sieć** | Przypisywanie sieci <br> Konfiguracja <br> Przenieś sieć <br> Usuń |
| **Wydajność** | Modyfikuj interwały |
| **Profil hosta** | Wyświetl |
| **Zasób** | Zastosuj zalecenie <br> Przypisywanie vApp do puli zasobów <br> Przypisz maszynę wirtualną do puli zasobów <br> Utwórz pulę zasobów <br> Migrowanie wyłączone z maszyny wirtualnej <br> Migrowanie na maszynie wirtualnej <br> Modyfikuj pulę zasobów <br> Przenieś pulę zasobów <br> VMotion zapytania <br> Usuń pulę zasobów <br> Zmień nazwę puli zasobów |
| **Zaplanowane zadanie** | Tworzenie zadań podrzędnych <br> Modyfikowanie zadania <br> Usuń zadanie <br> Uruchom zadanie |
| **Obrad** | Personifikuj użytkownika <br> Wiadomość <br> Weryfikuj sesję <br> Wyświetlanie i zatrzymywanie sesji |
| **Klaster magazynu danych** | Konfigurowanie klastra magazynu danych |
| **Magazyn oparty na profilach** | Aktualizacja magazynu opartego na profilach <br> Widok magazynu oparty na profilach |
| **Widoki magazynu** | Konfiguruj usługę <br> Wyświetl |
| **Zadania** | Tworzenie zadania <br> Aktualizuj zadanie |
| **Usługa transferu**| Zarządzaj <br> Monitorowanie |
| **vApp** | Dodaj maszynę wirtualną <br> Przypisz pulę zasobów <br> Przypisz vApp <br> Klonuj <br> Create <br> Usuń <br> Eksportuj <br> Import <br> Move <br> Zasilanie wyłączone <br> Włącz <br> Zmiana nazwy <br> Wstrzymywanie <br> Unregister <br> Wyświetl środowisko OVF <br> Konfiguracja aplikacji vApp <br> Konfiguracja wystąpienia vApp <br> Konfiguracja vApp zarządzane <br> Konfiguracja zasobów vApp |
| **VRMPolicy** | VRMPolicy zapytania <br> Aktualizacja VRMPolicy |
| **Konfiguracja > maszyny wirtualnej** | Dodaj istniejący dysk <br> Dodaj nowy dysk <br> Dodaj lub Usuń urządzenie <br> Advanced <br> Zmień liczbę procesorów <br> Zmień zasób <br> Konfigurowanie zarządzane <br> Śledzenie zmian dysku <br> Dzierżawa dysku <br> Wyświetl ustawienia połączenia <br> Zwiększ dysk wirtualny <br> Urządzenie hosta USB <br> Pamięć <br> Modyfikowanie ustawień urządzenia <br> Zgodność z odpornością na uszkodzenia zapytania <br> Wykonywanie zapytań dotyczących plików nienależących do użytkownika <br> Urządzenie RAW <br> Załaduj ponownie ze ścieżki <br> Usuń dysk <br> Zmiana nazwy <br> Resetowanie informacji o gościu <br> Ustaw adnotację <br> Ustawienia <br> Swapfile <br> Przełącz element nadrzędny rozwidlenia <br> Odblokuj maszynę wirtualną <br> Uaktualnij zgodność maszyny wirtualnej |
| **Operacje gościa > maszyny wirtualnej** | Modyfikowanie aliasu operacji gościa <br> Zapytanie aliasu operacji gościa <br> Modyfikacje operacji gościa <br> Wykonywanie programu operacji gościa <br> Zapytania dotyczące operacji gościa |
| **Interakcja > maszyny wirtualnej** | Odpowiedź na pytanie <br> Operacja tworzenia kopii zapasowej na maszynie wirtualnej <br> Konfigurowanie nośnika CD <br> Konfigurowanie dyskietki <br> Interakcja z konsolą <br> Utwórz zrzut ekranu <br> Defragmentacja wszystkich dysków <br> Połączenie z urządzeniem <br> Przeciągnij i upuść <br> Zarządzanie systemem operacyjnym gościa za pomocą interfejsu API VIX <br> Wstawianie kodów skanowania HID USB <br> Wstrzymywanie lub wstrzymywanie wstrzymania <br> Wykonywanie operacji czyszczenia lub zmniejszania <br> Zasilanie wyłączone <br> Włącz <br> Rejestruj sesję na maszynie wirtualnej <br> Odtwórz ponownie sesję na maszynie wirtualnej <br> Reset <br> Wznów odporność na uszkodzenia <br> Wstrzymywanie <br> Wstrzymywanie odporności na uszkodzenia <br> Testowanie pracy w trybie failover <br> Uruchom ponownie pomocniczą maszynę wirtualną <br> Wyłącz odporność na uszkodzenia <br> Włącz odporność na uszkodzenia <br> Instalacja narzędzi VMware |
| **Spis > maszyny wirtualnej** | Utwórz na podstawie istniejącego <br> Tworzenie nowego elementu <br> Move <br> Zarejestruj <br> Usuń <br> Unregister |
| **Inicjowanie obsługi maszyny wirtualnej >** | Zezwalaj na dostęp do dysku <br> Zezwalaj na dostęp do plików <br> Zezwalaj na dostęp do dysku tylko do odczytu <br> Zezwalaj na pobieranie maszyny wirtualnej <br> Zezwalaj na przekazywanie plików maszyny wirtualnej <br> Klonuj szablon <br> Klonowanie maszyny wirtualnej <br> Utwórz szablon z maszyny wirtualnej <br> Dostosowywanie <br> Wdrażanie szablonu <br> Oznacz jako szablon <br> Oznacz jako maszynę wirtualną <br> Modyfikowanie specyfikacji dostosowywania <br> Podwyższanie poziomu dysków <br> Przeczytaj specyfikacje dostosowania |
| **Konfiguracja usługi > maszyny wirtualnej** | Zezwalaj na powiadomienia <br> Zezwalaj na sondowanie globalnych powiadomień o zdarzeniach <br> Zarządzanie konfiguracjami usług <br> Modyfikuj konfigurację usługi <br> Wykonaj zapytania dotyczące konfiguracji usługi <br> Odczytaj konfigurację usługi |
| **Zarządzanie migawką > maszyny wirtualnej** | Tworzenie migawki <br> Usuń migawkę <br> Zmień nazwę migawki <br> Przywróć migawkę |
| **Replikacja vSphere > maszyny wirtualnej** | Konfigurowanie replikacji <br> Zarządzanie replikacją <br> Monitoruj replikację |
| **vService** | Utwórz zależność <br> Zniszcz zależność <br> Skonfiguruj ponownie konfigurację zależności <br> Aktualizuj zależność |

### <a name="cloud-cluster-admin-role"></a>Chmura-klaster-administrator-rola

| **Kategoria** | **Kont** |
|----------|-----------|
| **Magazyn danych** | Przydziel miejsce <br> Przeglądaj magazyn danych <br> Konfigurowanie magazynu danych <br> Operacje na plikach niskiego poziomu <br> Usuń magazyn danych <br> Zmień nazwę magazynu danych <br> Aktualizowanie plików maszyny wirtualnej <br> Aktualizowanie metadanych maszyny wirtualnej |
| **Folder** | Utwórz folder <br> Usuń folder <br> Przenieś folder <br> Zmień nazwę folderu |
| **Konfiguracja > hosta**  | Konfiguracja partycji magazynu |
| **Tagowanie vSphere** | Przypisywanie lub cofanie przypisania tagu vSphere <br> Utwórz tag vSphere <br> Utwórz kategorię tagów vSphere <br> Usuń tag vSphere <br> Usuń kategorię tagu vSphere <br> Edytuj tag vSphere <br> Edytuj kategorię tagów vSphere <br> Modyfikuj pole UsedBy dla kategorii <br> Modyfikuj pole UsedBy dla tagu |
| **Sieć** | Przypisywanie sieci |
| **Zasób** | Zastosuj zalecenie <br> Przypisywanie vApp do puli zasobów <br> Przypisz maszynę wirtualną do puli zasobów <br> Utwórz pulę zasobów <br> Migrowanie wyłączone z maszyny wirtualnej <br> Migrowanie na maszynie wirtualnej <br> Modyfikuj pulę zasobów <br> Przenieś pulę zasobów <br> VMotion zapytania <br> Usuń pulę zasobów <br> Zmień nazwę puli zasobów |
| **vApp** | Dodaj maszynę wirtualną <br> Przypisz pulę zasobów <br> Przypisz vApp <br> Klonuj <br> Create <br> Usuń <br> Eksportuj <br> Import <br> Move <br> Zasilanie wyłączone <br> Włącz <br> Zmiana nazwy <br> Wstrzymywanie <br> Unregister <br> Wyświetl środowisko OVF <br> Konfiguracja aplikacji vApp <br> Konfiguracja wystąpienia vApp <br> Konfiguracja vApp zarządzane <br> Konfiguracja zasobów vApp |
| **VRMPolicy** | VRMPolicy zapytania <br> Aktualizacja VRMPolicy |
| **Konfiguracja > maszyny wirtualnej** | Dodaj istniejący dysk <br> Dodaj nowy dysk <br> Dodaj lub Usuń urządzenie <br> Advanced <br> Zmień liczbę procesorów <br> Zmień zasób <br> Konfigurowanie zarządzane <br> Śledzenie zmian dysku <br> Dzierżawa dysku <br> Wyświetl ustawienia połączenia <br> Zwiększ dysk wirtualny <br> Urządzenie hosta USB <br> Pamięć <br> Modyfikowanie ustawień urządzenia <br> Zgodność z odpornością na uszkodzenia zapytania <br> Wykonywanie zapytań dotyczących plików nienależących do użytkownika <br> Urządzenie RAW <br> Załaduj ponownie ze ścieżki <br> Usuń dysk <br> Zmiana nazwy <br> Resetowanie informacji o gościu <br> Ustaw adnotację <br> Ustawienia <br> Swapfile <br> Przełącz element nadrzędny rozwidlenia <br> Odblokuj maszynę wirtualną <br> Uaktualnij zgodność maszyny wirtualnej |
| **Operacje gościa > maszyny wirtualnej** | Modyfikowanie aliasu operacji gościa <br> Zapytanie aliasu operacji gościa <br> Modyfikacje operacji gościa <br> Wykonywanie programu operacji gościa <br> Zapytania dotyczące operacji gościa |
| **Interakcja > maszyny wirtualnej** | Odpowiedź na pytanie <br> Operacja tworzenia kopii zapasowej na maszynie wirtualnej <br> Konfigurowanie nośnika CD <br> Konfigurowanie dyskietki <br> Interakcja z konsolą <br> Utwórz zrzut ekranu <br> Defragmentacja wszystkich dysków <br> Połączenie z urządzeniem <br> Przeciągnij i upuść <br> Zarządzanie systemem operacyjnym gościa za pomocą interfejsu API VIX <br> Wstawianie kodów skanowania HID USB <br> Wstrzymywanie lub wstrzymywanie wstrzymania <br> Wykonywanie operacji czyszczenia lub zmniejszania <br> Zasilanie wyłączone <br> Włącz <br> Rejestruj sesję na maszynie wirtualnej <br> Odtwórz ponownie sesję na maszynie wirtualnej <br> Reset <br> Wznów odporność na uszkodzenia <br> Wstrzymywanie <br> Wstrzymywanie odporności na uszkodzenia <br> Testowanie pracy w trybie failover <br> Uruchom ponownie pomocniczą maszynę wirtualną <br> Wyłącz odporność na uszkodzenia <br> Włącz odporność na uszkodzenia <br> Instalacja narzędzi VMware
| **Spis > maszyny wirtualnej** | Utwórz na podstawie istniejącego <br> Tworzenie nowego elementu <br> Move <br> Zarejestruj <br> Usuń <br> Unregister |
| **Inicjowanie obsługi maszyny wirtualnej >** | Zezwalaj na dostęp do dysku <br> Zezwalaj na dostęp do plików <br> Zezwalaj na dostęp do dysku tylko do odczytu <br> Zezwalaj na pobieranie maszyny wirtualnej <br> Zezwalaj na przekazywanie plików maszyny wirtualnej <br> Klonuj szablon <br> Klonowanie maszyny wirtualnej <br> Utwórz szablon z maszyny wirtualnej <br> Dostosowywanie <br> Wdrażanie szablonu <br> Oznacz jako szablon <br> Oznacz jako maszynę wirtualną <br> Modyfikowanie specyfikacji dostosowywania <br> Podwyższanie poziomu dysków  <br> Przeczytaj specyfikacje dostosowania |
| **Konfiguracja usługi > maszyny wirtualnej** | Zezwalaj na powiadomienia <br> Zezwalaj na sondowanie globalnych powiadomień o zdarzeniach <br> Zarządzanie konfiguracjami usług <br> Modyfikuj konfigurację usługi <br> Wykonaj zapytania dotyczące konfiguracji usługi <br> Odczytaj konfigurację usługi
| **Zarządzanie migawką > maszyny wirtualnej** | Tworzenie migawki <br> Usuń migawkę <br> Zmień nazwę migawki <br> Przywróć migawkę |
| **Replikacja vSphere > maszyny wirtualnej** | Konfigurowanie replikacji <br> Zarządzanie replikacją <br> Monitoruj replikację |
| **vService** | Utwórz zależność <br> Zniszcz zależność <br> Skonfiguruj ponownie konfigurację zależności <br> Aktualizuj zależność |

### <a name="cloud-storage-admin-role"></a>Cloud-Storage-Administrator-rola

| **Kategoria** | **Kont** |
|----------|-----------|
| **Magazyn danych** | Przydziel miejsce <br> Przeglądaj magazyn danych <br> Konfigurowanie magazynu danych <br> Operacje na plikach niskiego poziomu <br> Usuń magazyn danych <br> Zmień nazwę magazynu danych <br> Aktualizowanie plików maszyny wirtualnej <br> Aktualizowanie metadanych maszyny wirtualnej |
| **Konfiguracja > hosta** | Konfiguracja partycji magazynu |
| **Klaster magazynu danych** | Konfigurowanie klastra magazynu danych |
| **Magazyn oparty na profilach** | Aktualizacja magazynu opartego na profilach <br> Widok magazynu oparty na profilach |
| **Widoki magazynu** | Konfiguruj usługę <br> Wyświetl |

### <a name="cloud-network-admin-role"></a>Chmura-sieć-administrator-rola

| **Kategoria** | **Kont** |
|----------|-----------|
| **Grupa dvPort** | Create <br> Usuń <br> Modyfikuj <br> Operacja zasad <br> Operacja zakresu |
| **Sieć** | Przypisywanie sieci <br> Konfiguracja <br> Przenieś sieć <br> Usuń |
| **Konfiguracja > maszyny wirtualnej** | Modyfikowanie ustawień urządzenia |

### <a name="cloud-vm-admin-role"></a>Chmura-VM-administrator-rola

| **Kategoria** | **Kont** |
|----------|-----------|
| **Magazyn danych** | Przydziel miejsce <br> Przeglądaj magazyn danych |
| **Sieć** | Przypisywanie sieci |
| **Zasób** | Przypisz maszynę wirtualną do puli zasobów <br> Migrowanie wyłączone z maszyny wirtualnej <br> Migrowanie na maszynie wirtualnej
| **vApp** | Eksportuj <br> Import |
| **Konfiguracja > maszyny wirtualnej** | Dodaj istniejący dysk <br> Dodaj nowy dysk <br> Dodaj lub Usuń urządzenie <br> Advanced <br> Zmień liczbę procesorów <br> Zmień zasób <br> Konfigurowanie zarządzane <br> Śledzenie zmian dysku <br> Dzierżawa dysku <br> Wyświetl ustawienia połączenia <br> Zwiększ dysk wirtualny <br> Urządzenie hosta USB <br> Pamięć <br> Modyfikowanie ustawień urządzenia <br> Zgodność z odpornością na uszkodzenia zapytania <br> Wykonywanie zapytań dotyczących plików nienależących do użytkownika <br> Urządzenie RAW <br> Załaduj ponownie ze ścieżki <br> Usuń dysk <br> Zmiana nazwy <br> Resetowanie informacji o gościu <br> Ustaw adnotację <br> Ustawienia <br> Swapfile <br> Przełącz element nadrzędny rozwidlenia <br> Odblokuj maszynę wirtualną <br> Uaktualnij zgodność maszyny wirtualnej |
| **Operacje gościa > maszyny wirtualnej** | Modyfikowanie aliasu operacji gościa <br> Zapytanie aliasu operacji gościa <br> Modyfikacje operacji gościa <br> Wykonywanie programu operacji gościa <br> Zapytania dotyczące operacji gościa    |
| **Interakcja > maszyny wirtualnej** | Odpowiedź na pytanie <br> Operacja tworzenia kopii zapasowej na maszynie wirtualnej <br> Konfigurowanie nośnika CD <br> Konfigurowanie dyskietki <br> Interakcja z konsolą <br> Utwórz zrzut ekranu <br> Defragmentacja wszystkich dysków <br> Połączenie z urządzeniem <br> Przeciągnij i upuść <br> Zarządzanie systemem operacyjnym gościa za pomocą interfejsu API VIX <br> Wstawianie kodów skanowania HID USB <br> Wstrzymywanie lub wstrzymywanie wstrzymania <br> Wykonywanie operacji czyszczenia lub zmniejszania <br> Zasilanie wyłączone <br> Włącz <br> Rejestruj sesję na maszynie wirtualnej <br> Odtwórz ponownie sesję na maszynie wirtualnej <br> Reset <br> Wznów odporność na uszkodzenia <br> Wstrzymywanie <br> Wstrzymywanie odporności na uszkodzenia <br> Testowanie pracy w trybie failover <br> Uruchom ponownie pomocniczą maszynę wirtualną <br> Wyłącz odporność na uszkodzenia <br> Włącz odporność na uszkodzenia <br> Instalacja narzędzi VMware |
| **Spis > maszyny wirtualnej** | Utwórz na podstawie istniejącego <br> Tworzenie nowego elementu <br> Move <br> Zarejestruj <br> Usuń <br> Unregister |
| **Inicjowanie obsługi maszyny wirtualnej >** | Zezwalaj na dostęp do dysku <br> Zezwalaj na dostęp do plików <br> Zezwalaj na dostęp do dysku tylko do odczytu <br> Zezwalaj na pobieranie maszyny wirtualnej <br> Zezwalaj na przekazywanie plików maszyny wirtualnej <br> Klonuj szablon <br> Klonowanie maszyny wirtualnej <br> Utwórz szablon z maszyny wirtualnej <br> Dostosowywanie <br> Wdrażanie szablonu <br> Oznacz jako szablon <br> Oznacz jako maszynę wirtualną <br> Modyfikowanie specyfikacji dostosowywania <br> Podwyższanie poziomu dysków <br> Przeczytaj specyfikacje dostosowania |
| **Konfiguracja usługi > maszyny wirtualnej** | Zezwalaj na powiadomienia <br> Zezwalaj na sondowanie globalnych powiadomień o zdarzeniach <br> Zarządzanie konfiguracjami usług <br> Modyfikuj konfigurację usługi <br> Wykonaj zapytania dotyczące konfiguracji usługi <br> Odczytaj konfigurację usługi
| **Zarządzanie migawką > maszyny wirtualnej** | Tworzenie migawki <br> Usuń migawkę <br> Zmień nazwę migawki <br> Przywróć migawkę |
| **Replikacja vSphere > maszyny wirtualnej** | Konfigurowanie replikacji <br> Zarządzanie replikacją <br> Monitoruj replikację |
| **vService** | Utwórz zależność <br> Zniszcz zależność <br> Skonfiguruj ponownie konfigurację zależności <br> Aktualizuj zależność |
