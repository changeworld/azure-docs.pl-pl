---
title: Rozwiązanie Azure VMware by CloudSimple — model uprawnień w chmurze prywatnej
description: Zawiera opis modelu uprawnień, grup i kategorii cloudsimple private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014950"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple Private Cloud model uprawnień VMware vCenter

CloudSimple zachowuje pełny dostęp administracyjny do środowiska private cloud. Każdy klient CloudSimple otrzymuje wystarczające uprawnienia administracyjne, aby móc wdrażać maszyny wirtualne i zarządzać nimi w ich środowisku.  W razie potrzeby można tymczasowo eskalować uprawnienia do wykonywania funkcji administracyjnych.

## <a name="cloud-owner"></a>Właściciel chmury

Podczas tworzenia chmury prywatnej użytkownik **CloudOwner** jest tworzony w domenie rejestracji jednokrotnej vCenter z **dostępem** do roli właściciela chmury do zarządzania obiektami w chmurze prywatnej. Ten użytkownik może również skonfigurować dodatkowe [źródła tożsamości vCenter](set-vcenter-identity.md)i innych użytkowników do private cloud vCenter.

> [!NOTE]
> Domyślnym użytkownikiem usługi CloudSimple Private cloudowner@cloudsimple.local Cloud vCenter jest utworzenie chmury prywatnej.

## <a name="user-groups"></a>Grupy użytkowników

Grupa o nazwie **Cloud-Owner-Group** jest tworzona podczas wdrażania chmury prywatnej. Użytkownicy w tej grupie mogą administrować różnymi częściami środowiska vSphere w chmurze prywatnej. Ta grupa jest automatycznie nadawane **cloud-owner-role** uprawnienia, a użytkownik **CloudOwner** jest dodawany jako członek tej grupy.  CloudSimple tworzy dodatkowe grupy z ograniczonymi uprawnieniami dla ułatwienia zarządzania.  Do tych wstępnie utworzonych grup można dodać dowolnego użytkownika, a poniższe uprawnienia są automatycznie przypisywane do użytkowników w grupach.

### <a name="pre-created-groups"></a>Wstępnie utworzone grupy

| Nazwa grupy | Przeznaczenie | Rola |
| -------- | ------- | ------ |
| Grupa-Właściciel chmury | Członkowie tej grupy mają uprawnienia administracyjne do centrum vCenter private cloud | [Rola właściciela chmury](#cloud-owner-role) |
| Chmura-globalna-klaster-administrator-grupa | Członkowie tej grupy mają uprawnienia administracyjne w klastrze vcenter private cloud | [Rola administratora klastra w chmurze](#cloud-cluster-admin-role) |
| Chmura-globalna-storage-admin-grupa | Członkowie tej grupy mogą zarządzać magazynem w centrum vCenter private cloud | [Rola administratora magazynu w chmurze](#cloud-storage-admin-role) |
| Chmura-globalna-sieć-admin-grupa | Członkowie tej grupy mogą zarządzać grupami portów sieciowych i rozproszonych w centrum vCenter private cloud | [Rola administratora sieci w chmurze](#cloud-network-admin-role) |
| Chmura-Globalna-VM-Admin-Group | Członkowie tej grupy mogą zarządzać maszynami wirtualnymi w centrum vCenter private cloud | [Rola administratora maszyny Wirtualnej w chmurze](#cloud-vm-admin-role) |

Aby przyznać poszczególnym użytkownikom uprawnienia do zarządzania chmurą prywatną, utwórz konta użytkowników dodawaj do odpowiednich grup.

> [!CAUTION]
> Nowi użytkownicy muszą być dodani tylko do *grupy Cloud-Owner,* *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* lub *Cloud-Global-VM-Admin-Group*.  Użytkownicy dodani do grupy *Administratorzy* zostaną automatycznie usunięci.  Tylko konta usług muszą być dodawane do grupy *administratorów* i kont usług nie mogą być używane do logowania się do interfejsu użytkownika sieci web vSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista uprawnień vCenter dla ról domyślnych

### <a name="cloud-owner-role"></a>Rola właściciela chmury

| **Kategoria** | **Privilege** |
|----------|-----------|
| **Alarmy** | Potwierdzanie alarmu <br> Tworzenie alarmu <br> Wyłączanie akcji alarmu <br> Modyfikowanie alarmu <br> Usuń alarm <br> Ustawianie stanu alarmu |
| **Uprawnienia** | Modyfikowanie uprawnień |
| **Biblioteka zawartości** | Dodawanie elementu biblioteki <br> Tworzenie biblioteki lokalnej <br> Tworzenie subskrybowanych bibliotek <br> Usuwanie elementu biblioteki <br> Usuwanie biblioteki lokalnej <br> Usuwanie subskrybowanych bibliotek <br> Pobieranie plików <br> Eksmisjaj element biblioteki <br> Eksmitować subskrybowane biblioteki <br> Importowanie magazynu <br> Sondowanie informacji o subskrypcji <br> Odczyt pamięci masowej <br> Element biblioteki synchronizacji <br> Synchronizacja subskrybowanych biblioteki <br> Wpisz introspekcję <br> Aktualizowanie ustawień konfiguracyjnych <br> Aktualizowanie plików <br> Aktualizowanie biblioteki <br> Aktualizowanie elementu biblioteki <br> Aktualizowanie biblioteki lokalnej <br> Aktualizowanie subskrybowanych bibliotek <br> Wyświetlanie ustawień konfiguracji |
| **Operacje kryptograficzne** | Dodawanie dysku <br> Klonowanie <br> Odszyfrowywanie <br> Funkcja DirectAccess <br> Szyfrowanie <br> Szyfruj nowe <br> Zarządzanie usługiami zarządzania kluczami <br> Zarządzanie zasadami szyfrowania <br> Zarządzanie kluczami <br> Migrate (Migracja) <br> Recrypt <br> Zarejestruj maszynę wirtualną <br> Zarejestruj hosta |
| **Grupa dvPort** | Utwórz <br> Usuń <br> Modyfikowanie <br> Operacja zasad <br> Operacja zakresu |
| **Magazyn danych** | Przydzielanie miejsca <br> Przeglądanie magazynu danych <br> Konfigurowanie magazynu danych <br> Operacje na plikach niskiego poziomu <br> Przenoszenie magazynu danych <br> Usuwanie magazynu danych <br> Usuń plik <br> Zmienianie nazwy magazynu danych <br> Aktualizowanie plików maszyn wirtualnych <br> Aktualizowanie metadanych maszyny wirtualnej |
| **Menedżer agenta ESX** | Config <br> Modyfikowanie <br> Widok |
| **Wewnętrzny** | Rozszerzenie rejestru <br> Wyrejestruj rozszerzenie <br> Rozszerzenie aktualizacji |
| **Zewnętrzny dostawca statystyk**| Zarejestruj <br> Unregister <br> Aktualizacja |
| **Folder** | Tworzenie folderu <br> Usuń folder <br> Przenieś folder <br> Zmienianie nazwy folderu |
| **Globalny** | Anuluj zadanie <br> Planowanie pojemności <br> Diagnostyka <br> Wyłączanie metod <br> Włącz metody <br> Tag globalny <br> Health <br> Licencje <br> Rejestrowanie zdarzenia <br> Zarządzanie atrybutami niestandardowymi <br> Serwer proxy <br> Akcja skryptu <br> Menedżerowie usług <br> Ustawianie atrybutu niestandardowego <br> Znacznik systemu |
| **Dostawca aktualizacji kondycji** | Zarejestruj <br> Unregister <br> Aktualizacja |
| **Konfiguracja > hosta** | Konfiguracja partycji magazynu |
| **Zapasy > hosta** | Modyfikowanie klastra |
| **Tagowanie vSphere** | Przypisywanie lub przydzielania tagu vSphere <br> Tworzenie znacznika vSphere <br> Tworzenie kategorii znaczników vSphere <br> Usuń znacznik vSphere <br> Usuń kategorię znaczników vSphere <br> Edytuj tag vSphere <br> Edytuj kategorię znaczników vSphere <br> Zmodyfikuj pole UsedBy dla kategorii <br> Zmodyfikuj pole UsedBy dla znacznika |
| **Sieć** | Przypisywanie sieci <br> Konfigurowanie <br> Przenoszenie sieci <br> Remove |
| **Wydajność** | Modyfikowanie interwałów |
| **Profil hosta** | Widok |
| **Zasobów** | Zastosuj zalecenie <br> Przypisywanie aplikacji vApp do puli zasobów <br> Przypisywanie maszyny wirtualnej do puli zasobów <br> Tworzenie puli zasobów <br> Migrowanie wyłączonej maszyny wirtualnej <br> Migrowanie na maszynie wirtualnej <br> Modyfikowanie puli zasobów <br> Przenoszenie puli zasobów <br> Kwerenda vMotion <br> Usuwanie puli zasobów <br> Zmienianie nazwy puli zasobów |
| **Zaplanowane zadanie** | Tworzenie zadań podrzędnych <br> Modyfikowanie zadania <br> Usuń zadanie <br> Uruchom zadanie |
| **Sesje** | Podszywanie się pod użytkownika <br> Komunikat <br> Sprawdzanie poprawności sesji <br> Wyświetlanie i zatrzymywania sesji |
| **Klaster magazynu danych** | Konfigurowanie klastra magazynu danych |
| **Pamięć masowa oparta na profilu** | Aktualizacja magazynu oparta na profilu <br> Widok magazynu oparty na profilu |
| **Widoki magazynu** | Konfigurowanie usługi <br> Widok |
| **Zadania** | Tworzenie zadania <br> Zadanie aktualizacji |
| **Usługa transferu**| Zarządzaj <br> Monitorowanie |
| **vApp (Aplikacji)** | Dodawanie maszyny wirtualnej <br> Przypisywanie puli zasobów <br> Przypisywanie aplikacji vApp <br> Klonowanie <br> Utwórz <br> Usuń <br> Eksportowanie <br> Import <br> Move <br> Wyłączanie <br> Włączanie zasilania <br> Zmiana nazwy <br> Wstrzymanie <br> Unregister <br> Zobacz środowisko OVF <br> Konfiguracja aplikacji vApp <br> Konfiguracja wystąpienia aplikacji vApp <br> konfiguracja vApp managedBy <br> Konfiguracja zasobów vApp |
| **VRMPolicy (VRMPolicy)** | Zapytanie VRMPolicy <br> Aktualizacja VRMPolicy |
| **Konfiguracja > maszyny wirtualnej** | Dodawanie istniejącego dysku <br> Dodawanie nowego dysku <br> Dodawanie lub usuwanie urządzenia <br> Zaawansowane <br> Zmienianie liczby procesorów <br> Zmienianie zasobu <br> Konfigurowanie managedBy <br> Śledzenie zmian dysku <br> Dzierżawa dysku <br> Wyświetlanie ustawień połączenia <br> Rozszerzanie dysku wirtualnego <br> Host urządzenia USB <br> Memory (Pamięć) <br> Modyfikowanie ustawień urządzenia <br> Zgodność z odpornością na uszkodzenia kwerend <br> Kwerenda nieobjętych plikami <br> Urządzenie nieprzetworzone <br> Przeładuj ze ścieżki <br> Usuwanie dysku <br> Zmiana nazwy <br> Resetowanie informacji o gościu <br> Ustawianie adnotacji <br> Ustawienia <br> Umieszczenie pliku wymiany <br> Przełącz element nadrzędny wideł <br> Odblokowywanie maszyny wirtualnej <br> Uaktualnianie zgodności z maszynami wirtualnymi |
| **Operacje > maszyny wirtualnej** | Modyfikacja aliasu operacji gościa <br> Kwerenda aliasu operacji gościa <br> Modyfikacje operacji gościa <br> Wykonanie programu operacji gościa <br> Kwerendy operacji gościa |
| **Maszyna wirtualna > interakcji** | Odpowiedź na pytanie <br> Operacja tworzenia kopii zapasowej na maszynie wirtualnej <br> Konfigurowanie nośników CD <br> Konfigurowanie nośników dyskietek <br> Interakcja z konsolą <br> Utwórz zrzut ekranu <br> Defragmentacja wszystkich dysków <br> Połączenie urządzenia <br> Przeciąganie i upuszczanie <br> Zarządzanie systemem operacyjnym dla gości przez VIX API <br> Wstrzyknąć kody skanowania USB HID <br> Wstrzymanie lub unpause <br> Wykonywanie operacji czyszczenia lub zmniejszania <br> Wyłączanie <br> Włączanie zasilania <br> Rejestrowanie sesji na maszynie wirtualnej <br> Sesja odtwarzania na maszynie wirtualnej <br> Reset <br> Wznów odporność na uszkodzenia <br> Wstrzymanie <br> Wstrzymanie odporności na uszkodzenia <br> Testowanie pracy w trybie failover <br> Uruchom ponownie pomocniczą maszynę wirtualną <br> Wyłącz odporność na uszkodzenia <br> Włączanie odporności na uszkodzenia <br> Instalacja narzędzi VMware |
| **> zapasy maszyny wirtualnej** | Tworzenie na podstawie istniejących <br> Tworzenie nowego elementu <br> Move <br> Zarejestruj <br> Remove <br> Unregister |
| **Maszyna wirtualna > inicjowania obsługi administracyjnej** | Zezwalaj na dostęp do dysku <br> Zezwalaj na dostęp do plików <br> Zezwalaj na dostęp do dysku tylko do odczytu <br> Zezwalaj na pobieranie maszyny wirtualnej <br> Zezwalaj na przekazywanie plików maszyn wirtualnych <br> Szablon klonowania <br> Klonuj maszynę wirtualną <br> Tworzenie szablonu z maszyny wirtualnej <br> Dostosowywanie <br> Wdrażanie szablonu <br> Oznacz jako szablon <br> Oznacz jako maszynę wirtualną <br> Modyfikowanie specyfikacji dostosowywania <br> Promuj dyski <br> Przeczytaj specyfikacje dostosowywania |
| **Konfiguracja > usługi maszyny wirtualnej** | Zezwalaj na powiadomienia <br> Zezwalaj na sondowanie powiadomień o zdarzeniach globalnych <br> Zarządzanie konfiguracjami usług <br> Modyfikowanie konfiguracji usługi <br> Konfiguracje usługi kwerend <br> Odczyt konfiguracji usługi |
| **Zarządzanie migawkami maszyny wirtualnej >** | Tworzenie migawki <br> Usuwanie migawki <br> Zmienianie nazwy migawki <br> Powrót do migawki |
| **Maszyna wirtualna > replikacji vSphere** | Konfigurowanie replikacji <br> Zarządzanie replikacją <br> Monitorowanie replikacji |
| **usługi vService** | Tworzenie zależności <br> Zniszcz zależność <br> Ponowna konfiguracja zależności <br> Aktualizuj zależność |

### <a name="cloud-cluster-admin-role"></a>Rola administratora klastra w chmurze

| **Kategoria** | **Privilege** |
|----------|-----------|
| **Magazyn danych** | Przydzielanie miejsca <br> Przeglądanie magazynu danych <br> Konfigurowanie magazynu danych <br> Operacje na plikach niskiego poziomu <br> Usuwanie magazynu danych <br> Zmienianie nazwy magazynu danych <br> Aktualizowanie plików maszyn wirtualnych <br> Aktualizowanie metadanych maszyny wirtualnej |
| **Folder** | Tworzenie folderu <br> Usuń folder <br> Przenieś folder <br> Zmienianie nazwy folderu |
| **Konfiguracja > hosta**  | Konfiguracja partycji magazynu |
| **Tagowanie vSphere** | Przypisywanie lub przydzielania tagu vSphere <br> Tworzenie znacznika vSphere <br> Tworzenie kategorii znaczników vSphere <br> Usuń znacznik vSphere <br> Usuń kategorię znaczników vSphere <br> Edytuj tag vSphere <br> Edytuj kategorię znaczników vSphere <br> Zmodyfikuj pole UsedBy dla kategorii <br> Zmodyfikuj pole UsedBy dla znacznika |
| **Sieć** | Przypisywanie sieci |
| **Zasobów** | Zastosuj zalecenie <br> Przypisywanie aplikacji vApp do puli zasobów <br> Przypisywanie maszyny wirtualnej do puli zasobów <br> Tworzenie puli zasobów <br> Migrowanie wyłączonej maszyny wirtualnej <br> Migrowanie na maszynie wirtualnej <br> Modyfikowanie puli zasobów <br> Przenoszenie puli zasobów <br> Kwerenda vMotion <br> Usuwanie puli zasobów <br> Zmienianie nazwy puli zasobów |
| **vApp (Aplikacji)** | Dodawanie maszyny wirtualnej <br> Przypisywanie puli zasobów <br> Przypisywanie aplikacji vApp <br> Klonowanie <br> Utwórz <br> Usuń <br> Eksportowanie <br> Import <br> Move <br> Wyłączanie <br> Włączanie zasilania <br> Zmiana nazwy <br> Wstrzymanie <br> Unregister <br> Zobacz środowisko OVF <br> Konfiguracja aplikacji vApp <br> Konfiguracja wystąpienia aplikacji vApp <br> konfiguracja vApp managedBy <br> Konfiguracja zasobów vApp |
| **VRMPolicy (VRMPolicy)** | Zapytanie VRMPolicy <br> Aktualizacja VRMPolicy |
| **Konfiguracja > maszyny wirtualnej** | Dodawanie istniejącego dysku <br> Dodawanie nowego dysku <br> Dodawanie lub usuwanie urządzenia <br> Zaawansowane <br> Zmienianie liczby procesorów <br> Zmienianie zasobu <br> Konfigurowanie managedBy <br> Śledzenie zmian dysku <br> Dzierżawa dysku <br> Wyświetlanie ustawień połączenia <br> Rozszerzanie dysku wirtualnego <br> Host urządzenia USB <br> Memory (Pamięć) <br> Modyfikowanie ustawień urządzenia <br> Zgodność z odpornością na uszkodzenia kwerend <br> Kwerenda nieobjętych plikami <br> Urządzenie nieprzetworzone <br> Przeładuj ze ścieżki <br> Usuwanie dysku <br> Zmiana nazwy <br> Resetowanie informacji o gościu <br> Ustawianie adnotacji <br> Ustawienia <br> Umieszczenie pliku wymiany <br> Przełącz element nadrzędny wideł <br> Odblokowywanie maszyny wirtualnej <br> Uaktualnianie zgodności z maszynami wirtualnymi |
| **Operacje > maszyny wirtualnej** | Modyfikacja aliasu operacji gościa <br> Kwerenda aliasu operacji gościa <br> Modyfikacje operacji gościa <br> Wykonanie programu operacji gościa <br> Kwerendy operacji gościa |
| **Maszyna wirtualna > interakcji** | Odpowiedź na pytanie <br> Operacja tworzenia kopii zapasowej na maszynie wirtualnej <br> Konfigurowanie nośników CD <br> Konfigurowanie nośników dyskietek <br> Interakcja z konsolą <br> Utwórz zrzut ekranu <br> Defragmentacja wszystkich dysków <br> Połączenie urządzenia <br> Przeciąganie i upuszczanie <br> Zarządzanie systemem operacyjnym dla gości przez VIX API <br> Wstrzyknąć kody skanowania USB HID <br> Wstrzymanie lub unpause <br> Wykonywanie operacji czyszczenia lub zmniejszania <br> Wyłączanie <br> Włączanie zasilania <br> Rejestrowanie sesji na maszynie wirtualnej <br> Sesja odtwarzania na maszynie wirtualnej <br> Reset <br> Wznów odporność na uszkodzenia <br> Wstrzymanie <br> Wstrzymanie odporności na uszkodzenia <br> Testowanie pracy w trybie failover <br> Uruchom ponownie pomocniczą maszynę wirtualną <br> Wyłącz odporność na uszkodzenia <br> Włączanie odporności na uszkodzenia <br> Instalacja narzędzi VMware
| **> zapasy maszyny wirtualnej** | Tworzenie na podstawie istniejących <br> Tworzenie nowego elementu <br> Move <br> Zarejestruj <br> Remove <br> Unregister |
| **Maszyna wirtualna > inicjowania obsługi administracyjnej** | Zezwalaj na dostęp do dysku <br> Zezwalaj na dostęp do plików <br> Zezwalaj na dostęp do dysku tylko do odczytu <br> Zezwalaj na pobieranie maszyny wirtualnej <br> Zezwalaj na przekazywanie plików maszyn wirtualnych <br> Szablon klonowania <br> Klonuj maszynę wirtualną <br> Tworzenie szablonu z maszyny wirtualnej <br> Dostosowywanie <br> Wdrażanie szablonu <br> Oznacz jako szablon <br> Oznacz jako maszynę wirtualną <br> Modyfikowanie specyfikacji dostosowywania <br> Promuj dyski  <br> Przeczytaj specyfikacje dostosowywania |
| **Konfiguracja > usługi maszyny wirtualnej** | Zezwalaj na powiadomienia <br> Zezwalaj na sondowanie powiadomień o zdarzeniach globalnych <br> Zarządzanie konfiguracjami usług <br> Modyfikowanie konfiguracji usługi <br> Konfiguracje usługi kwerend <br> Odczyt konfiguracji usługi
| **Zarządzanie migawkami maszyny wirtualnej >** | Tworzenie migawki <br> Usuwanie migawki <br> Zmienianie nazwy migawki <br> Powrót do migawki |
| **Maszyna wirtualna > replikacji vSphere** | Konfigurowanie replikacji <br> Zarządzanie replikacją <br> Monitorowanie replikacji |
| **usługi vService** | Tworzenie zależności <br> Zniszcz zależność <br> Ponowna konfiguracja zależności <br> Aktualizuj zależność |

### <a name="cloud-storage-admin-role"></a>Rola administratora magazynu w chmurze

| **Kategoria** | **Privilege** |
|----------|-----------|
| **Magazyn danych** | Przydzielanie miejsca <br> Przeglądanie magazynu danych <br> Konfigurowanie magazynu danych <br> Operacje na plikach niskiego poziomu <br> Usuwanie magazynu danych <br> Zmienianie nazwy magazynu danych <br> Aktualizowanie plików maszyn wirtualnych <br> Aktualizowanie metadanych maszyny wirtualnej |
| **Konfiguracja > hosta** | Konfiguracja partycji magazynu |
| **Klaster magazynu danych** | Konfigurowanie klastra magazynu danych |
| **Pamięć masowa oparta na profilu** | Aktualizacja magazynu oparta na profilu <br> Widok magazynu oparty na profilu |
| **Widoki magazynu** | Konfigurowanie usługi <br> Widok |

### <a name="cloud-network-admin-role"></a>Rola administratora sieci w chmurze

| **Kategoria** | **Privilege** |
|----------|-----------|
| **Grupa dvPort** | Utwórz <br> Usuń <br> Modyfikowanie <br> Operacja zasad <br> Operacja zakresu |
| **Sieć** | Przypisywanie sieci <br> Konfigurowanie <br> Przenoszenie sieci <br> Remove |
| **Konfiguracja > maszyny wirtualnej** | Modyfikowanie ustawień urządzenia |

### <a name="cloud-vm-admin-role"></a>Rola administratora maszyny Wirtualnej w chmurze

| **Kategoria** | **Privilege** |
|----------|-----------|
| **Magazyn danych** | Przydzielanie miejsca <br> Przeglądanie magazynu danych |
| **Sieć** | Przypisywanie sieci |
| **Zasobów** | Przypisywanie maszyny wirtualnej do puli zasobów <br> Migrowanie wyłączonej maszyny wirtualnej <br> Migrowanie na maszynie wirtualnej
| **vApp (Aplikacji)** | Eksportowanie <br> Import |
| **Konfiguracja > maszyny wirtualnej** | Dodawanie istniejącego dysku <br> Dodawanie nowego dysku <br> Dodawanie lub usuwanie urządzenia <br> Zaawansowane <br> Zmienianie liczby procesorów <br> Zmienianie zasobu <br> Konfigurowanie managedBy <br> Śledzenie zmian dysku <br> Dzierżawa dysku <br> Wyświetlanie ustawień połączenia <br> Rozszerzanie dysku wirtualnego <br> Host urządzenia USB <br> Memory (Pamięć) <br> Modyfikowanie ustawień urządzenia <br> Zgodność z odpornością na uszkodzenia kwerend <br> Kwerenda nieobjętych plikami <br> Urządzenie nieprzetworzone <br> Przeładuj ze ścieżki <br> Usuwanie dysku <br> Zmiana nazwy <br> Resetowanie informacji o gościu <br> Ustawianie adnotacji <br> Ustawienia <br> Umieszczenie pliku wymiany <br> Przełącz element nadrzędny wideł <br> Odblokowywanie maszyny wirtualnej <br> Uaktualnianie zgodności z maszynami wirtualnymi |
| **Operacje >maszyny wirtualnej gościa** | Modyfikacja aliasu operacji gościa <br> Kwerenda aliasu operacji gościa <br> Modyfikacje operacji gościa <br> Wykonanie programu operacji gościa <br> Kwerendy operacji gościa    |
| **Maszyna wirtualna >interakcji** | Odpowiedź na pytanie <br> Operacja tworzenia kopii zapasowej na maszynie wirtualnej <br> Konfigurowanie nośników CD <br> Konfigurowanie nośników dyskietek <br> Interakcja z konsolą <br> Utwórz zrzut ekranu <br> Defragmentacja wszystkich dysków <br> Połączenie urządzenia <br> Przeciąganie i upuszczanie <br> Zarządzanie systemem operacyjnym dla gości przez VIX API <br> Wstrzyknąć kody skanowania USB HID <br> Wstrzymanie lub unpause <br> Wykonywanie operacji czyszczenia lub zmniejszania <br> Wyłączanie <br> Włączanie zasilania <br> Rejestrowanie sesji na maszynie wirtualnej <br> Sesja odtwarzania na maszynie wirtualnej <br> Reset <br> Wznów odporność na uszkodzenia <br> Wstrzymanie <br> Wstrzymanie odporności na uszkodzenia <br> Testowanie pracy w trybie failover <br> Uruchom ponownie pomocniczą maszynę wirtualną <br> Wyłącz odporność na uszkodzenia <br> Włączanie odporności na uszkodzenia <br> Instalacja narzędzi VMware |
| **>zapasy maszyny wirtualnej** | Tworzenie na podstawie istniejących <br> Tworzenie nowego elementu <br> Move <br> Zarejestruj <br> Remove <br> Unregister |
| **Maszyna wirtualna >inicjowania obsługi administracyjnej** | Zezwalaj na dostęp do dysku <br> Zezwalaj na dostęp do plików <br> Zezwalaj na dostęp do dysku tylko do odczytu <br> Zezwalaj na pobieranie maszyny wirtualnej <br> Zezwalaj na przekazywanie plików maszyn wirtualnych <br> Szablon klonowania <br> Klonuj maszynę wirtualną <br> Tworzenie szablonu z maszyny wirtualnej <br> Dostosowywanie <br> Wdrażanie szablonu <br> Oznacz jako szablon <br> Oznacz jako maszynę wirtualną <br> Modyfikowanie specyfikacji dostosowywania <br> Promuj dyski <br> Przeczytaj specyfikacje dostosowywania |
| **Konfiguracja >usługi maszyny wirtualnej** | Zezwalaj na powiadomienia <br> Zezwalaj na sondowanie powiadomień o zdarzeniach globalnych <br> Zarządzanie konfiguracjami usług <br> Modyfikowanie konfiguracji usługi <br> Konfiguracje usługi kwerend <br> Odczyt konfiguracji usługi
| **Zarządzanie migawkami maszyny wirtualnej >** | Tworzenie migawki <br> Usuwanie migawki <br> Zmienianie nazwy migawki <br> Powrót do migawki |
| **Maszyna wirtualna >replikacji vSphere** | Konfigurowanie replikacji <br> Zarządzanie replikacją <br> Monitorowanie replikacji |
| **usługi vService** | Tworzenie zależności <br> Zniszcz zależność <br> Ponowna konfiguracja zależności <br> Aktualizuj zależność |
