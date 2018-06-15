---
title: Rozwiązywanie problemów dotyczących wdrożenia serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje sposób diagnozowanie i usuwanie błędów występujących podczas wdrażania najpierw StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: d6642231bf9d012b0015902c82d2bfde3b86285c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927859"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia urządzenia StorSimple
## <a name="overview"></a>Omówienie
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów przydatne dla danego wdrożenia programu Microsoft Azure StorSimple. Opisuje typowych problemów, możliwe przyczyny i zalecane kroki w celu ułatwienia rozwiązywania problemów, które mogą wystąpić podczas konfigurowania StorSimple. 

Te informacje dotyczą zarówno urządzeń fizycznych serii StorSimple 8000, jak i urządzenia StorSimple w chmurze.

> [!NOTE]
> Związane z konfiguracją problemy urządzenia, które mogą się spodziewać mogą wystąpić podczas wdrażania urządzenia po raz pierwszy lub mogą one występować później, gdy urządzenie jest operacyjnej. Ten artykuł skupia się na temat rozwiązywania problemów dotyczących wdrożenia po raz pierwszy. Aby rozwiązać działającego urządzenia, przejdź do [Rozwiązywanie problemów z działającego urządzenia za pomocą narzędzia diagnostyczne](storsimple-8000-diagnostics.md).

Również w tym artykule opisano narzędzia do rozwiązywania problemów z wdrożeniami StorSimple i zawiera krok przykład rozwiązywania problemów.

## <a name="first-time-deployment-issues"></a>Problemy z wdrażaniem po raz pierwszy
Jeśli napotkasz problem podczas wdrażania urządzenia po raz pierwszy, należy rozważyć następujące kwestie:

* Są rozwiązywania problemu z urządzenia fizycznego, upewnij się, że sprzęt został zainstalowany i skonfigurowany zgodnie z opisem w [zainstalować do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
* Sprawdź wymagania wstępne dotyczące wdrażania. Upewnij się, że wszystkie informacje opisane w [Lista kontrolna dotycząca konfiguracji wdrożenia](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Przejrzyj informacje o wersji StorSimple, jeśli ma opis problemu. Informacje o wersji obejmują rozwiązania problemów z instalacją znane. 

Podczas wdrażania urządzenia najbardziej typowe problemy przez użytkowników krój wystąpić po uruchomieniu Kreatora instalacji i po ich rejestracji urządzenie za pomocą środowiska Windows PowerShell dla urządzenia StorSimple. (Możesz użyć programu Windows PowerShell dla StorSimple do rejestracji i konfiguracji urządzenia StorSimple. Aby uzyskać więcej informacji o rejestracji urządzenia, zobacz [krok 3: Konfigurowanie i rejestrowanie urządzenia za pośrednictwem programu Windows PowerShell dla StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Poniższe sekcje może pomóc rozwiązać problemy, które wystąpią podczas konfigurowania urządzenia StorSimple po raz pierwszy.

## <a name="first-time-setup-wizard-process"></a>Proces Kreatora instalacji po raz pierwszy
Poniższe instrukcje stanowią podsumowanie procesu Kreatora instalacji. Szczegółowe informacje, zobacz [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

1. Uruchom [Invoke HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) polecenia cmdlet, aby uruchomić Kreatora instalacji, który poprowadzi Cię przez pozostałe kroki. 
2. Skonfiguruj sieć: Kreator instalacji umożliwia skonfigurowanie ustawień sieci dla interfejsu sieciowego 0 danych na urządzeniu StorSimple. Te ustawienia obejmują następujące czynności:
   * Wirtualnego adresu IP (VIP), maskę podsieci i bramę — [HcsNetInterface zestaw](https://technet.microsoft.com/library/dn688161.aspx) polecenia cmdlet są wykonywane w tle. Konfiguruje adres IP, maski podsieci i bramy dla interfejsu sieciowego 0 danych na urządzeniu StorSimple.
   * Podstawowy serwer DNS — [HcsDnsClientServerAddress zestaw](https://technet.microsoft.com/library/dn688172.aspx) polecenia cmdlet są wykonywane w tle. Konfiguruje ustawienia DNS dla rozwiązania StorSimple.
   * Serwer NTP — [HcsNtpClientServerAddress zestaw](https://technet.microsoft.com/library/dn688138.aspx) polecenia cmdlet są wykonywane w tle. Konfiguruje ustawienia serwera NTP dla rozwiązania StorSimple.
   * Serwer proxy sieci web opcjonalne — [HcsWebProxy zestaw](https://technet.microsoft.com/library/dn688154.aspx) polecenia cmdlet są wykonywane w tle. Ustawia, a umożliwia konfigurację serwera proxy sieci web dla rozwiązania StorSimple.
3. Ustawianie hasła: następnym krokiem jest ustanowienie hasło administratora urządzenia.
   Hasło administratora urządzenia jest używana do logowania się do urządzenia. Domyślne hasło urządzenia to **Password1**.
        
     > [!IMPORTANT]
     > Hasła są zbierane przed rejestracją, ale stosowane tylko po zarejestrowaniu urządzenia. W przypadku awarii, aby zastosować hasła, wyświetli się monit o podanie hasła ponownie, dopóki wymagane hasła (które spełniają wymagania dotyczące złożoności) są zbierane.
     
4. Rejestrowanie urządzenia: ostatnim krokiem jest rejestrowanie urządzenia w usłudze Menedżer StorSimple urządzenia działają na platformie Microsoft Azure. Rejestracja wymaga [pobieranie klucza rejestracji usługi](storsimple-8000-manage-service.md#get-the-service-registration-key) z portalu Azure i przekazują je w Kreatorze instalacji. **Po pomyślnym zarejestrowaniu urządzenia, klucz szyfrowania danych usługi są dostępne. Pamiętaj zachować ten klucz szyfrowania w bezpiecznej lokalizacji, ponieważ będzie on musiał zarejestrować wszystkich kolejnych urządzeń w usłudze.**

## <a name="common-errors-during-device-deployment"></a>Typowe błędy podczas wdrażania urządzenia
W poniższych tabelach przedstawiono typowe błędy, że można napotkać podczas możesz:

* Skonfiguruj wymagane ustawienia sieciowe.
* Skonfiguruj ustawienia serwera proxy sieci web opcjonalne.
* Ustawianie hasła administratora urządzenia.
* Rejestrowanie urządzenia.

## <a name="errors-during-the-required-network-settings"></a>Błędy podczas wymagane ustawienia sieciowe
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecane działanie |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: To polecenie można uruchomić tylko na aktywnym kontrolerze. |Konfiguracja wykonywana na kontrolerze pasywnych. |Uruchom to polecenie z aktywnym kontrolerze. Aby uzyskać więcej informacji, zobacz [Zidentyfikuj active kontroler na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Urządzenie nie jest gotowy. |Występują problemy z łącznością sieciową na dane 0. |Sprawdź łączność sieci fizycznej na dane 0. |
| 3 |Invoke-HcsSetupWizard: Istnieje konflikt adresów IP z innym systemem w sieci (wyjątek od HRESULT: 0x80070263). |Adres IP podany dla danych 0 już był używany przez inny system. |Podaj nowego adresu IP, który nie jest używany. |
| 4 |Invoke-HcsSetupWizard: Zasób klastra nie powiodło się. (Wyjątek od HRESULT: 0x800713AE). |Zduplikowany adres VIP. Podany adres IP jest już używana. |Podaj nowego adresu IP, który nie jest używany. |
| 5 |Wywołanie HcsSetupWizard: Adres IPv4 nieprawidłowe. |Adres IP znajduje się w niepoprawnym formacie. |Sprawdź format i ponownie podaj adres IP. Aby uzyskać więcej informacji, zobacz [adresowanie Ipv4][1]. |
| 6 |Wywołanie HcsSetupWizard: Adres IPv6 nieprawidłowe. |Adres IP znajduje się w niepoprawnym formacie. |Sprawdź format i ponownie podaj adres IP. Aby uzyskać więcej informacji, zobacz [adresowanie Ipv6][2]. |
| 7 |Invoke-HcsSetupWizard: Nie dostępnych więcej punktów końcowych z mapowania punktów końcowych. (Wyjątek od HRESULT: 0x800706D9) |Funkcje klastra nie działa. |[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Błędy podczas ustawienia serwera proxy sieci web opcjonalne
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecane działanie |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Nieprawidłowy parametr (wyjątek od HRESULT: 0x80070057) |Jeden z parametrów podanych ustawień serwera proxy jest nieprawidłowy. |Nie podano identyfikatora URI w poprawnym formacie. Użyj następującego formatu: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: Serwer RPC jest niedostępny (wyjątek od HRESULT: 0x800706ba) |Główną przyczyną problemu jest jedną z następujących czynności:<ol><li>Klaster nie jest w górę.</li><li>Pasywne kontrolera nie może komunikować się z aktywnym kontrolerze i jest wykonywane z pasywnym kontrolera.</li></ol> |W zależności od przyczyny:<ol><li>[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) aby upewnić się, że klaster jest uruchomiony.</li><li>Uruchom polecenie z aktywnym kontrolerze. Jeśli chcesz uruchomić polecenie z kontrolera pasywnym, konieczne będzie upewnij się, że pasywnym kontroler może komunikować się z aktywnym kontrolerze. Konieczne będzie [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) Jeżeli to połączenie zostanie przerwane.</li></ol> |
| 3 |Invoke-HcsSetupWizard: Wywołanie RPC nie powiodło się (wyjątek od HRESULT: 0x800706be) |Klaster jest wyłączony. |[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) aby upewnić się, że klaster jest uruchomiony. |
| 4 |Invoke-HcsSetupWizard: Nie można odnaleźć zasobu klastra (wyjątek od HRESULT: 0x8007138f) |Nie można odnaleźć zasobu klastra. Może to nastąpić, jeśli instalacja nie jest prawidłowa. |Może być konieczne zresetowanie urządzenia do domyślnych ustawień fabrycznych. [Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) można utworzyć zasobu klastra. |
| 5 |Invoke-HcsSetupWizard: Zasób nie online klastra (wyjątek od HRESULT: 0x8007138c) |Zasoby klastra nie są w trybie online. |[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. |

## <a name="errors-related-to-device-administrator-password"></a>Błędy związane z hasło administratora urządzenia
Domyślne hasło administratora urządzenia jest **Password1**. To hasło wygaśnie po pierwszym zalogowaniu; Dlatego należy ją zmienić za pomocą Kreatora konfiguracji. Należy podać nowe hasło administratora urządzenia podczas rejestrowania urządzenia po raz pierwszy. 

Upewnij się, że hasła spełniać następujące wymagania:

* Hasło administratora urządzenia może zawierać od 8 do 15 znaków.
* Hasła powinna zawierać 3 następujących typów znaków 4: małe litery, wielkie litery liczbowych i specjalnych. 
* Hasło nie może być taka sama jak ostatnie 24 haseł.

Ponadto należy pamiętać, hasła wygasa co roku, którą można zmienić tylko po zarejestrowaniu urządzenia. W przypadku niepowodzenia rejestracji jakiegoś powodu nie można zmienić hasła.

Aby uzyskać więcej informacji o hasło administratora urządzenia, przejdź do [zmienić hasło StorSimple przy użyciu usługi Menedżer urządzeń StorSimple](storsimple-8000-change-passwords.md).

Co najmniej jeden z następujących błędów mogą wystąpić podczas konfigurowania administratora urządzenia i hasło programu StorSimple Snapshot Manager.

| Nie. | Komunikat o błędzie | Zalecane działanie |
| --- | --- | --- |
| 1 |Hasło przekracza maksymalną długość. |Hasło administratora urządzenia musi należeć do zakresu od 8 do 15 znaków. |
| 2 |Hasło nie spełnia wymaganą długość. |Hasło administratora urządzenia musi należeć do zakresu od 8 do 15 znaków.|
| 3 |Hasło musi zawierać tylko małe litery. |Hasła muszą zawierać 3 następujących typów znaków 4: małe litery, wielkie litery liczbowych i specjalnych. Upewnij się, że hasło spełnia te wymagania. |
| 4 |Hasło musi zawierać cyfry. |Hasła muszą zawierać 3 następujących typów znaków 4: małe litery, wielkie litery liczbowych i specjalnych. Upewnij się, że hasło spełnia te wymagania. |
| 5 |Hasło musi zawierać znaki specjalne. |Hasła muszą zawierać 3 następujących typów znaków 4: małe litery, wielkie litery liczbowych i specjalnych. Upewnij się, że hasło spełnia te wymagania. |
| 6 |Hasło musi zawierać 3 następujących typów znaków 4: wielkie litery, małe litery, liczbowych i specjalnych. |Hasło nie zawiera wymaganych typów znaków. Upewnij się, że hasło spełnia te wymagania. |
| 7 |Parametr jest niezgodny z potwierdzeniem. |Upewnij się, że hasło spełnia wszystkie wymagania i że został wprowadzony poprawnie. |
| 8 |Hasło nie może dopasować wartość domyślna. |Domyślne hasło jest *Password1*. Musisz zmienić hasło po zalogowaniu się po raz pierwszy. |
| 9 |Wprowadzone hasło jest niezgodne z hasłem urządzenia. Wpisz ponownie hasło. |Sprawdź hasło, a następnie wpisz je ponownie. |

Hasła są zbierane, zanim urządzenie jest zarejestrowane, ale są stosowane tylko po pomyślnej rejestracji. Przepływ pracy odzyskiwania hasła wymaga urządzenia do zarejestrowania.

> [!IMPORTANT]
> Ogólnie rzecz biorąc Jeśli próba stosowanie hasła nie powiedzie się, następnie oprogramowania wielokrotnie próbuje zebrać hasła przed pomyślnym zakończeniem. W rzadkich przypadkach nie można zastosować hasło. W takiej sytuacji można zarejestrować urządzenia i kontynuować, jednak nie będzie można zmienić hasła. Po rejestracji z portalu Azure, można zmienić hasło administratora urządzenia.


Możesz resetować hasła w portalu Azure za pomocą usługi Menedżera urządzeń StorSimple. Aby uzyskać więcej informacji, przejdź do [Zmień hasło administratora urządzenia](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Błędy podczas rejestracji urządzenia
Usługę Menedżer StorSimple urządzenia działają na platformie Microsoft Azure jest używany do rejestrowania urządzenia. Co najmniej jeden z następujących problemów mogą napotkać podczas rejestracji urządzenia.

| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecane działanie |
| --- | --- | --- | --- |
| 1 |Błąd 350027: Nie można zarejestrować urządzenia przy użyciu Menedżera urządzeń StorSimple. | |Poczekaj kilka minut, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Błąd 350013: Wystąpił błąd w rejestracji urządzenia. Może to być spowodowane klucz rejestracji usługi niepoprawne. | |Zarejestruj urządzenie ponownie przy użyciu prawidłowego klucza rejestracji. Aby uzyskać więcej informacji, zobacz [pobieranie klucza rejestracji usługi.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Błąd 350063: Uwierzytelnianie usługi Menedżer StorSimple urządzenia przekazany, ale rejestracji nie powiodło się. Spróbuj ponownie wykonać operację po pewnym czasie. |Ten błąd wskazuje, że minął uwierzytelniania w usługach ACS, ale wywołanie register do usługi nie powiodło się. Może to być wynikiem usterkę sporadyczne sieci. |Jeśli problem będzie się powtarzać, skontaktuj [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Błąd 350049: Nie można osiągnąć usługi podczas rejestracji. |Po nawiązaniu połączenia z usługą zostało odebrane wyjątek sieci web. W niektórych przypadkach może pobrać rozwiązany przez ponowną próbą wykonania tej operacji później. |Sprawdź nazwę DNS i adres IP, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, [skontaktuj się z Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Błąd 350031: Urządzenia został już zarejestrowany. | |Nie trzeba nic robić. |
| 6 |Błąd 350016: Rejestracja urządzenia nie powiodła się. | |Upewnij się, że klucz rejestracji jest poprawna. |
| 7 |Invoke-HcsSetupWizard: Wystąpił błąd podczas rejestrowania urządzenia; może to być spowodowane niepoprawnym adresem IP lub nazwę DNS. Sprawdź ustawienia sieciowe i spróbuj ponownie. Jeśli problem będzie się powtarzać, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Błąd 350050) |Upewnij się, że urządzenie może wysyłać polecenia ping siecią zewnętrzną. Jeśli nie ma łączności z siecią zewnętrzną, rejestracja może zakończyć się niepowodzeniem z powodu następującego błędu. Ten błąd może być kombinacją co najmniej jeden z następujących czynności:<ul><li>Niepoprawne IP</li><li>Niepoprawne podsieci</li><li>Niepoprawne bramy</li><li>Nieprawidłowe ustawienia DNS</li></ul> |Zobacz kroki w [przykład rozwiązywania problemów krok po kroku](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FBE2]. Spróbuj wykonać ponownie operację za jakiś czas. Jeśli problem będzie się powtarzać, skontaktuj się z Microsoft Support. |Jest to rodzajowy błąd zgłoszony dla wszystkich użytkowników, niewidoczne błędów z usługi lub agenta. Najczęstszą przyczyną może być niepowodzeniu uwierzytelniania usług ACS. Możliwą przyczyną błędu jest czy występują problemy z konfiguracją serwera NTP i godzina na urządzeniu nie została poprawnie ustawiona. |Popraw czas (w przypadku problemów), a następnie ponów operację rejestracji. Jeśli używasz polecenia Set-HcsSystem - strefy czasowej dostosowanie strefę czasową własne w strefie czasowej (na przykład "czas pacyficzny").  Jeśli ten problem będzie się powtarzać, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. |
| 9 |Ostrzeżenie: Nie można aktywować urządzenia. Nie zmieniono administratora urządzenia i hasło programu StorSimple Snapshot Manager. |W przypadku niepowodzenia rejestracji administratora urządzenia i hasło programu StorSimple Snapshot Manager nie są zmieniane. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Narzędzia do rozwiązywania problemów z wdrożeniami StorSimple
StorSimple zawiera kilka narzędzi, które umożliwiają rozwiązywanie problemów z rozwiązania StorSimple. Należą do nich:

* Obsługuje pakiety i dzienniki urządzenia.
* Polecenia cmdlet zaprojektowane specjalnie w celu rozwiązania problemu.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Obsługuje pakiety i urządzenia dzienników dostępnych dla rozwiązywania problemów
Pakiet obsługi zawiera wszystkie dzienniki odpowiednich wspomagające zespół Microsoft Support o rozwiązywania problemów z urządzeniami. Programu Windows PowerShell dla StorSimple umożliwia generowanie pakietu zaszyfrowanych pomocy technicznej, który można następnie udostępnić pomocy technicznej.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Aby wyświetlić dzienniki lub zawartość pakietu obsługi
1. Generowanie pakietu obsługi zgodnie z opisem w za pomocą programu Windows PowerShell dla urządzenia StorSimple [tworzenie i zarządzanie nimi pakietu obsługi](storsimple-8000-create-manage-support-package.md).
2. Pobierz [skryptu odszyfrowywania](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na komputerze klienckim.
3. Użyj tej [procedury krok po kroku](storsimple-8000-create-manage-support-package.md#edit-a-support-package) do otwierania i odszyfrować pakiet pomocy technicznej.
4. Dzienniki pakietu odszyfrowane pomocy technicznej są w formacie etw/etvx. Można wykonać następujące kroki, aby wyświetlić w Podglądzie zdarzeń systemu Windows:
   
   1. Uruchom **eventvwr** na kliencie systemu Windows. Spowoduje to uruchomienie podglądu zdarzeń.
   2. W **akcje** okienku, kliknij przycisk **Otwórz zapisany dziennik** i wskaż plik dziennika w formacie etvx/etw (pakiet pomocy technicznej). Można teraz wyświetlić plik. Po otwarciu pliku, można kliknąć prawym przyciskiem myszy i Zapisz plik jako tekst.
      
      > [!IMPORTANT]
      > Można również użyć **Get-WinEvent** polecenia cmdlet, aby otworzyć tych plików w programie Windows PowerShell. Aby uzyskać więcej informacji, zobacz [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) w dokumentacji referencyjnej polecenia cmdlet programu Windows PowerShell.
     
5. Po otwarciu dzienniki w Podglądzie zdarzeń, wyszukaj następujące dzienniki zawierające problemy związane z konfiguracją urządzenia:
   
   * hcs_pfconfig/Operational dziennika
   * hcs_pfconfig/Config
6. W plikach dziennika wyszukiwanie ciągów związanych z poleceniami cmdlet wywoływane przez Kreatora instalacji. Zobacz [procesu Kreatora instalacji po raz pierwszy](#first-time-setup-wizard-process) listę tych poleceń cmdlet.
7. Jeśli nie jest możliwe ustalić przyczynę problemu, możesz [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. Wykonaj kroki w [Utwórz żądanie obsługi](storsimple-8000-contact-microsoft-support.md#create-a-support-request) po się Support firmy Microsoft, aby uzyskać pomoc.

## <a name="cmdlets-available-for-troubleshooting"></a>Polecenia cmdlet dostępne do rozwiązywania problemów
Użyj następujących poleceń cmdlet programu Windows PowerShell do wykrywania błędów łączności.

* `Get-NetAdapter`: Użyj tego polecenia cmdlet, aby wykryć kondycji interfejsów sieciowych.
* `Test-Connection`: Użyj tego polecenia cmdlet, aby sprawdzić łączność sieciową w i poza siecią.
* `Test-HcsmConnection`: Użyj tego polecenia cmdlet, aby sprawdzić łączność pomyślnie zarejestrowane urządzenie.
* `Sync-HcsTime`: Użyj tego polecenia cmdlet do wyświetlania godzina na urządzeniu i wymusić synchronizacji czasu serwera NTP.
* `Enable-HcsPing`i `Disable-HcsPing`: używać tych poleceń cmdlet, aby umożliwić hostom na polecenie ping interfejsy sieciowe na urządzeniu StorSimple. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping.
* `Trace-HcsRoute`: Użyj tego polecenia cmdlet jako narzędziem do śledzenia trasy. Przez pewien czas wysyła pakiety do każdego routera w drodze do miejsca docelowego, a następnie oblicza wyniki na podstawie pakietów zwrócony z każdym przeskoku. Ponieważ `Trace-HcsRoute` pokazuje stopień utraty pakietów na określonym routerze lub łącza, można wyznaczyć które routery lub łącza mogą być przyczyną problemów z siecią.
* `Get-HcsRoutingTable`: Użyj tego polecenia cmdlet, aby wyświetlić lokalnej tabeli routingu IP.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter
Podczas konfigurowania interfejsów sieciowych dla wdrożenia urządzenia po raz pierwszy, stan sprzętu nie jest dostępna w usłudze Menedżer StorSimple urządzenia interfejsu użytkownika, ponieważ urządzenie nie jest jeszcze zarejestrowane w usłudze. Ponadto **kondycji sprzętu** bloku może nie zawsze poprawnie odzwierciedlał stanu urządzenia, zwłaszcza, jeśli występują problemy, które mają wpływ na usługi synchronizacji. W takich przypadkach można użyć `Get-NetAdapter` polecenia cmdlet, aby ustalić kondycję i stan interfejsów sieciowych.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Aby wyświetlić listę wszystkich kart sieciowych zainstalowanych na urządzeniu
1. Uruchom program Windows PowerShell dla urządzenia StorSimple, a następnie wpisz `Get-NetAdapter`. 
2. Użyj danych wyjściowych `Get-NetAdapter` polecenia cmdlet i następujących wytycznych zrozumieć stan interfejsu sieciowego.
   
   * Jeśli interfejs jest w dobrej kondycji i włączona, **ifIndex** stan jest wyświetlany jako **się**.
   * Jeśli interfejs jest w dobrej kondycji, ale nie jest fizycznie podłączony (za pomocą kabla sieciowego), **ifIndex** jest wyświetlany jako **wyłączone**.
   * Jeśli interfejs jest w dobrej kondycji, ale nie jest włączony, **ifIndex** stan jest wyświetlany jako **NotPresent**.
   * Jeśli interfejs nie istnieje, nie ma na liście. Usługę Menedżer StorSimple urządzenia interfejsu użytkownika będzie widoczna w stanie niepowodzenia tego interfejsu.

Aby uzyskać więcej informacji na temat korzystania z tego polecenia cmdlet, przejdź do [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) w dokumentacji poleceń cmdlet programu Windows PowerShell.

W poniższych sekcjach przedstawiono przykłady, danych wyjściowych z `Get-NetAdapter` polecenia cmdlet.

 Te przykłady kontrolera 0 został pasywnym kontrolera i był skonfigurowany następująco:

* DANE 0, 1 danych dane 2 i sieci dane 3 interfejsy znajdował się na urządzeniu.
* DANE 4 i 5 danych karty sieciowe nie były widoczne; nie są one w związku z tym wymienione w danych wyjściowych.
* DATA 0 został włączony.

Kontrolera 1 została active kontrolera i był skonfigurowany następująco:

* DANE 0, danych 1, 2 danych, dane 3, 4 danych i sieci danych 5 interfejsy znajdował się na urządzeniu.
* DATA 0 został włączony.

**Przykładowe dane wyjściowe — kontrolera 0**

Poniżej przedstawiono dane wyjściowe z kontrolera 0 (kontroler pasywnym). DANE 1, dane 2 i dane 3 nie są połączone. DANE 4 i 5 dane nie są wyświetlane, ponieważ nie są obecne na urządzeniu.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Przykładowe dane wyjściowe — kontrolera 1**

Poniżej przedstawiono dane wyjściowe z kontrolera 1 (kontroler active). Tylko dane 0 skonfigurowano interfejsu sieciowego na urządzeniu i działa.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection
Można użyć `Test-Connection` polecenia cmdlet, aby ustalić, czy urządzenia StorSimple można połączyć się z siecią zewnętrzną. Jeśli wszystkie parametry sieci, w tym DNS są skonfigurowane poprawnie, w Kreatorze instalacji, możesz użyć `Test-Connection` polecenia cmdlet na polecenie ping znanego adresu spoza sieci, takich jak outlook.com.

Należy włączyć polecenia ping, aby rozwiązać problemy z łącznością z tego polecenia cmdlet wyłączenie ping.

Zobacz poniższe przykłady, danych wyjściowych z `Test-Connection` polecenia cmdlet.

> [!NOTE]
> W pierwszym przykładzie urządzenie jest skonfigurowane z niepoprawne DNS. W drugim przykładzie DNS jest poprawna.

**Przykładowe dane wyjściowe — nieprawidłowa DNS**

W poniższym przykładzie nie ma żadnych danych wyjściowych dla adresów IPV4 i IPV6, co oznacza, że DNS nie zostanie rozwiązany. Oznacza to, że nie ma łączności z siecią zewnętrzną i poprawne DNS musi być dostarczane.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Przykładowe dane wyjściowe — właściwego systemu DNS**

W poniższym przykładzie DNS zwraca adres IPV4, wskazującą, czy usługa DNS jest prawidłowo skonfigurowany. Potwierdza to, że istnieje łączność z siecią zewnętrzną.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection
Użyj `Test-HcsmConnection` polecenia cmdlet dla urządzenia, które jest już połączony i zarejestrowana przy użyciu usługi Menedżer StorSimple urządzenia. To polecenie cmdlet pomaga Sprawdź łączność między zarejestrowane urządzenie i odpowiedniej usługi Menedżer StorSimple urządzenia. To polecenie można uruchomić środowisko Windows PowerShell dla urządzenia StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Aby uruchomić polecenie cmdlet Test-HcsmConnection
1. Upewnij się, że urządzenie jest zarejestrowane.
2. Sprawdź stan urządzenia. Jeśli urządzenie jest dezaktywowana w trybie konserwacji, lub w trybie offline, może pojawić się jeden z następujących błędów:
   
   * ErrorCode.CiSDeviceDecommissioned — wskazuje, że urządzenie jest dezaktywowana.
   * ErrorCode.DeviceNotReady — wskazuje, czy urządzenie jest w trybie konserwacji.
   * ErrorCode.DeviceNotReady — wskazuje, że urządzenie nie jest w trybie online.
3. Sprawdź, czy działa usługa Menedżera urządzenia StorSimple (Użyj [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) polecenia cmdlet). Jeśli usługa nie jest uruchomiona, można napotkać następujące błędy:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError — wskazuje, że wystąpił wyjątek po uruchomieniu Get ClusterResource.
4. Sprawdź token usługi kontroli dostępu (ACS). Zgłasza wyjątek sieci web, może być spowodowany przez problem bramy, brak uwierzytelnianie serwera proxy, niepoprawny DNS lub wystąpił błąd uwierzytelniania. Można napotkać następujące błędy:
   
   * ErrorCode.CiSApplianceGateway — wskazuje wyjątek HttpStatusCode.BadGateway: usługa rozpoznawania nazw nie można rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceProxy — wskazuje wyjątek HttpStatusCode.ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić przy użyciu serwera proxy.
   * ErrorCode.CiSApplianceDNSError — wskazuje wyjątek WebExceptionStatus.NameResolutionFailure: usługa rozpoznawania nazw nie można rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceACSError — to oznacza, że Usługa zwróciła błąd uwierzytelniania, ale ma łączności.
     
     Jeśli nie zgłasza wyjątek sieci web, sprawdź ErrorCode.CiSApplianceFailure. Oznacza to, że urządzenia nie powiodło się.
5. Sprawdź łączność usługi w chmurze. Jeśli usługa zgłasza wyjątek sieci web, można napotkać następujące błędy:
   
   * ErrorCode.CiSApplianceGateway — wskazuje wyjątek HttpStatusCode.BadGateway: pośredniczącego serwera proxy otrzymał nieprawidłowe żądanie z innego serwera proxy lub z oryginalnego serwera.
   * ErrorCode.CiSApplianceProxy — wskazuje wyjątek HttpStatusCode.ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić przy użyciu serwera proxy.
   * ErrorCode.CiSApplianceDNSError — wskazuje wyjątek WebExceptionStatus.NameResolutionFailure: usługa rozpoznawania nazw nie można rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceACSError — to oznacza, że Usługa zwróciła błąd uwierzytelniania, ale ma łączności.
     
     Jeśli nie zgłasza wyjątek sieci web, sprawdź ErrorCode.CiSApplianceSaasServiceError. Oznacza to problem z usługą Menedżera urządzeń StorSimple.
6. Sprawdź łączność usługi Azure Service Bus. ErrorCode.CiSApplianceServiceBusError wskazuje, że urządzenie nie może połączyć się usługi Service Bus.

Pliki dziennika CiSCommandletLog0Curr.errlog i CiSAgentsvc0Curr.errlog będzie mieć więcej informacji, takich jak Szczegóły wyjątku.

Aby uzyskać więcej informacji na temat używania polecenia cmdlet, przejdź do [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) dokumentacji odwoływać się w programie Windows PowerShell.

> [!IMPORTANT]
> Możesz uruchomić to polecenie cmdlet dla aktywnych i pasywnych kontrolera.

Zobacz poniższe przykłady, danych wyjściowych z `Test-HcsmConnection` polecenia cmdlet.

**Przykładowe dane wyjściowe — pomyślnie zarejestrowane urządzenie z systemem StorSimple Update 3**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Przykładowe dane wyjściowe — urządzenia w trybie offline** 

Ten przykład jest z urządzenia, które ma stan **Offline** w portalu Azure.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Urządzenie nie może połączyć, używając bieżącej konfiguracji serwera proxy sieci web. Może to być problem z konfiguracji serwera proxy sieci web lub problem z łącznością sieciową. W takim przypadku należy się upewnić, że ustawienia serwera proxy sieci web są poprawne i serwerów proxy sieci web są w trybie online i jest dostępny.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet HcsTime synchronizacji
Użyj następującego polecenia cmdlet, aby wyświetlić godzinę urządzenia. Jeśli godzina na urządzeniu ma przesunięcie z serwera NTP, następnie umożliwia to polecenie cmdlet życie synchronizacji czasu serwera NTP.
- Jeśli przesunięcie między urządzeniem a serwerem NTP jest większa niż 5 minut, zostanie wyświetlone ostrzeżenie. 
- Jeśli przesunięcie przekracza 15 minut, następnie urządzenie przejdzie w trybie offline. Do wymusza synchronizację czasu można nadal używać tego polecenia cmdlet. 
- Jednak jeśli przesunięcie przekracza 15 godzin, następnie nie będzie mógł życie synchronizacji czasu i komunikat o błędzie będzie wyświetlane.

**Przykładowe dane wyjściowe — synchronizacja czasu wymuszony przy użyciu HcsTime synchronizacji**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Enable-HcsPing i Wyłącz HcsPing
Używać tych poleceń cmdlet, aby upewnić się, że interfejsy sieciowe na urządzeniu odpowiadają na żądania ping protokołu ICMP. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping. Za pomocą tego polecenia cmdlet jest najprostszym sposobem, aby dowiedzieć się, czy urządzenie jest w trybie online i jest dostępny.

**Przykładowe dane wyjściowe — HcsPing Włączanie i wyłączanie HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet HcsRoute śledzenia
Użyj następującego polecenia cmdlet jako narzędziem do śledzenia trasy. Przez pewien czas wysyła pakiety do każdego routera w drodze do miejsca docelowego, a następnie oblicza wyniki na podstawie pakietów zwrócony z każdym przeskoku. Ponieważ polecenie cmdlet wyświetla stopień utraty pakietów na danego routera lub łącza, można wyznaczyć które routery lub łącza mogą być przyczyną problemów z siecią.

**Przykładowe dane wyjściowe przedstawiająca sposób śledzenia trasy pakiet z HcsRoute śledzenia**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Get-HcsRoutingTable
Użyj następującego polecenia cmdlet, aby wyświetlić tabelę routingu dla urządzenia StorSimple. Tabela routingu jest zestaw reguł, które mogą pomóc ustalić, który zostanie skierowany pakiety danych przesyłanych przez sieć protokołu internetowego (IP).

Tabela routingu zawiera interfejsy i bramy, która przekierowuje dane do określonych sieci. Udostępnia także metrykę routingu, który jest podejmującą dla ścieżki do miejsca docelowego określonego. Niższe metryki routingu, tym wyższy priorytet.

Na przykład jeśli masz 2 interfejsy sieciowe dane 2 i dane 3 połączony z Internetem. Jeśli routingu metryki dane 2 i dane 3 są odpowiednio 15 i 261, dane 2 o niższej metryce routingu jest wybranego interfejsu używane do dostępu do Internetu.

Jeśli używasz Update 1 na urządzeniu StorSimple, interfejsu dane 0 sieci ma najwyższy priorytet ruchu w chmurze. Oznacza to, że nawet jeśli istnieją inne interfejsy włączoną obsługę chmury, ruch chmurze będzie kierowany przez dane 0.

Po uruchomieniu `Get-HcsRoutingTable` polecenia cmdlet bez określania parametrów (jak w poniższym przykładzie przedstawiono), polecenia cmdlet dane wyjściowe obejmują tabelach routingu IPv4 i IPv6. Alternatywnie można określić `Get-HcsRoutingTable -IPv4` lub `Get-HcsRoutingTable -IPv6` można uzyskać odpowiedniej tabeli routingu.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Krok po kroku StorSimple przykład rozwiązywania problemów
W poniższym przykładzie przedstawiono krok po kroku rozwiązywania problemów z wdrożenia StorSimple. W przykładowym scenariuszu rejestracji urządzeń kończy się niepowodzeniem z komunikat o błędzie wskazujący, że ustawienia sieci lub nazwę DNS jest niepoprawny.

Jest zwracany komunikat o błędzie:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Ten błąd może być spowodowane jedną z następujących czynności:

* Instalacja niepoprawne sprzętu
* Interfejs sieciowy uszkodzony
* Nieprawidłowy adres IP, maski podsieci, bramy, podstawowy serwer DNS lub serwer proxy sieci web
* Klucz rejestracji niepoprawne
* Ustawienia zapory niepoprawne

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Aby zlokalizować i rozwiązać problem rejestracji urządzenia
1. Sprawdź konfigurację urządzenia: na aktywnym kontrolerze, uruchom `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > Kreator instalacji należy uruchomić na aktywnym kontrolerze. Aby sprawdzić, czy masz połączenie z aktywnym kontrolerem, spójrz na banerze przedstawionych w konsoli szeregowej. Transparent wskazuje, czy komputer jest podłączony do kontrolera 0 i kontrolera 1 i czy kontroler jest aktywnych lub pasywnych. Aby uzyskać więcej informacji, przejdź do [Zidentyfikuj active kontroler na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Upewnij się, że urządzenie jest poprawnie kablem: Sprawdź okablowanie na urządzeniu wstecz płaszczyzny sieci. Okablowanie jest specyficzne dla modelu urządzenia. Aby uzyskać więcej informacji, przejdź do [zainstalować do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Jeśli używasz portów sieciowych 10 GbE, należy użyć podanego karty QSFP SFP i kable SFP. Aby uzyskać więcej informacji, zobacz [lista kabli i przełączników oraz nadawczo-odbiorczych zalecane dla portów 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Sprawdź kondycję interfejsu sieciowego:
   
   * Za pomocą polecenia cmdlet Get-NetAdapter wykrywania kondycji interfejsy sieciowe dane 0. 
   * Jeśli łącze nie działa, **ifindex** stan wskaże, że interfejs nie działa. Następnie należy sprawdź połączenie sieciowe portu urządzenia, a do tego przełącznika. Należy również wykluczyć zły kable. 
   * Jeśli podejrzewasz, że dane 0 portu na aktywnym kontrolerze nie powiodło się, można to potwierdzić, łącząc się dane 0 portu kontrolera 1. Aby to potwierdzić, odłącz kabel sieciowy z tyłu urządzenia z kontrolera 0, podłącz kabel do kontrolera 1, a następnie ponownie uruchom polecenie cmdlet Get-NetAdapter.
     Jeśli port DATA 0 na kontrolerze zakończy się niepowodzeniem, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. Konieczne może być Zastąp kontrolera, w tym systemie.
4. Sprawdź łączność z przełącznikiem:
   
   * Upewnij się, że dane interfejsów sieciowych 0 na kontrolera 0 i kontrolera 1 w sieci podstawowej obudowa są w tej samej podsieci. 
   * Sprawdź Centrum lub router. Zazwyczaj należy łączyć zarówno kontrolery do tego samego Centrum lub router. 
   * Upewnij się, że przełączniki używanych dla połączenia jest DATA 0 dla obu kontrolerów w tej samej sieci vLAN.
5. Wyeliminować błędy użytkownika:
   
   * Uruchom ponownie Kreatora instalacji (Uruchom **Invoke HcsSetupWizard**), a następnie wprowadź wartości ponownie, aby upewnić się, że nie ma żadnych błędów. 
   * Sprawdź rejestrację klucz używany. Ten sam klucz rejestracji umożliwia podłączenie wielu urządzeń do usługi Menedżer StorSimple urządzenia. Użyj procedury [pobieranie klucza rejestracji usługi](storsimple-8000-manage-service.md#get-the-service-registration-key) aby upewnić się, że używasz prawidłowej rejestracji klucza.
     
     > [!IMPORTANT]
     > Jeśli masz wiele usług uruchomionych należy upewnić się, że klucz rejestracji, odpowiednie usługi służy do rejestrowania urządzenia. Jeśli urządzenia zostały zarejestrowane w usłudze Menedżer StorSimple urządzenia niewłaściwy, konieczne będzie [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. Być może trzeba wykonać resetowania do ustawień fabrycznych urządzenia (co może doprowadzić do utraty danych) do, a następnie połącz go z usługą zamierzone.
     > 
     > 
6. Aby sprawdzić, czy istnieje połączenie z siecią zewnętrzną, należy użyć polecenia cmdlet Test-Connection. Aby uzyskać więcej informacji, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Sprawdź, czy Zapora zakłóceń. Jeśli po sprawdzeniu, że ustawień wirtualnego adresu IP (VIP), podsieć bramy i DNS są wszystkie prawidłowe i również widzieć problemy z łącznością, a następnie jest możliwe, że Zapora blokuje komunikację między urządzeniem i siecią zewnętrzną. Należy się upewnić, że porty 80 i 443 są dostępne na urządzeniu StorSimple dla komunikacji wychodzącej. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące sieci dla urządzenia StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Sprawdź dzienniki. Przejdź do [obsługuje pakiety i urządzenia dzienników dostępnych dla rozwiązywania problemów](#support-packages-and-device-logs-available-for-troubleshooting).
9. Jeśli poprzednie kroki nie rozwiązują problemu, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) Aby uzyskać pomoc.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak używać narzędzia diagnostycznego do Rozwiązywanie problemów z urządzeniem StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
