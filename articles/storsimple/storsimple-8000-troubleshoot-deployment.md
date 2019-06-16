---
title: Rozwiązywanie problemów z wdrażaniem serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak diagnozować i rozwiązywać błędy występujące przy pierwszym wdrożeniu usługi StorSimple.
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
ms.openlocfilehash: f2b454e812db1eea686f82e92841163f1129b6c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715217"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia urządzenia StorSimple
## <a name="overview"></a>Omówienie
Ten artykuł zawiera przydatne wskazówki dotyczące rozwiązywania problemów dla danego wdrożenia programu Microsoft Azure StorSimple. Opisano w nim typowe problemy, możliwe przyczyny i zalecane kroki, aby pomóc w rozwiązywaniu problemów, które mogą wystąpić podczas konfigurowania usługi StorSimple. 

Te informacje dotyczą zarówno w przypadku urządzenia fizycznego StorSimple 8000 series, jak i urządzenia StorSimple w chmurze.

> [!NOTE]
> Związane z konfiguracją problemów z urządzeniami, które mogą się spodziewać, może wystąpić podczas wdrażania urządzenia po raz pierwszy lub mogą one wystąpić później, gdy urządzenie będzie działać. Ten artykuł koncentruje się na temat rozwiązywania problemów z wdrażaniem po raz pierwszy. Aby rozwiązać problemy z działającego urządzenia, przejdź do [Rozwiązywanie problemów z działającego urządzenia za pomocą narzędzia diagnostyczne](storsimple-8000-diagnostics.md).

W tym artykule również w tym artykule opisano narzędzia do rozwiązywania problemów z wdrożeniami usługi StorSimple i stanowi przykład krok po kroku dotyczące rozwiązywania problemów.

## <a name="first-time-deployment-issues"></a>Problemy z wdrażaniem po raz pierwszy
Jeśli napotkasz problem podczas wdrażania urządzenia po raz pierwszy, należy wziąć pod uwagę następujące informacje:

* Są rozwiązywania problemu z urządzeniem fizycznym, upewnij się, że sprzęt został zainstalowany i skonfigurowany zgodnie z opisem w [instalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
* Sprawdź wymagania wstępne dotyczące wdrażania. Upewnij się, że wszystkie informacje, które są opisane w [Lista kontrolna wdrażania konfiguracji](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Przejrzyj informacje o wersji usługi StorSimple, jeśli ma opis problemu. Informacje o wersji obejmują obejścia problemów z instalacją znane. 

Podczas wdrażania urządzenia najbardziej typowych problemów przez użytkowników wystąpić rozpoznawania twarzy po uruchomieniu Kreatora instalacji i po ich rejestrowanie urządzenia za pomocą programu Windows PowerShell dla usługi StorSimple. (Umożliwia programu Windows PowerShell dla usługi StorSimple zarejestrowaniu i skonfigurowaniu urządzenia StorSimple. Aby uzyskać więcej informacji dotyczących rejestracji urządzeń, zobacz [krok 3: Konfigurowanie i rejestrowanie urządzenia za pomocą programu Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Poniższe sekcje może pomóc rozwiązać problemy, które występują podczas konfigurowania urządzenia StorSimple po raz pierwszy.

## <a name="first-time-setup-wizard-process"></a>Proces Kreatora instalacji po raz pierwszy
Poniższe instrukcje stanowią podsumowanie procesu Kreatora instalacji. Szczegółowe informacje, zobacz [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).

1. Uruchom [polecenie Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) polecenia cmdlet, aby uruchomić Kreatora instalacji, który przeprowadzi Cię przez pozostałe kroki. 
2. Skonfiguruj sieć: Kreator instalacji umożliwia skonfigurowanie ustawień sieci dla interfejs sieciowy DATA 0 na urządzeniu StorSimple. Te ustawienia obejmują następujące czynności:
   * Wirtualnego adresu IP (VIP), maskę podsieci i bramę — [HcsNetInterface zestaw](https://technet.microsoft.com/library/dn688161.aspx) polecenia cmdlet jest wykonywane w tle. Konfiguruje adres IP, maskę podsieci i bramę interfejs sieciowy DATA 0 na urządzeniu StorSimple.
   * Podstawowy serwer DNS — [HcsDnsClientServerAddress zestaw](https://technet.microsoft.com/library/dn688172.aspx) polecenia cmdlet jest wykonywane w tle. Konfiguruje ustawienia DNS dla rozwiązania StorSimple.
   * Serwer NTP — [HcsNtpClientServerAddress zestaw](https://technet.microsoft.com/library/dn688138.aspx) polecenia cmdlet jest wykonywane w tle. Konfiguruje ustawienia serwera NTP dla rozwiązania StorSimple.
   * Serwer proxy opcjonalny sieci web — [HcsWebProxy zestaw](https://technet.microsoft.com/library/dn688154.aspx) polecenia cmdlet jest wykonywane w tle. Ona zestawy i umożliwia konfigurację serwera proxy sieci web dla rozwiązania StorSimple.
3. Skonfiguruj hasło: następnym krokiem jest ustawienie hasła administratora urządzenia.
   Hasło administratora urządzenia jest używana do logowania się na urządzeniu. Domyślne hasło urządzenia to **Password1**.
        
     > [!IMPORTANT]
     > Hasła są zbierane przed rejestracją, ale stosowane tylko wtedy, gdy zarejestrujesz urządzenie. W przypadku awarii do zastosowania hasła monit zostanie ponownie podać hasło, dopóki nie są zbierane wymagane hasła (spełniające wymagania co do złożoności).
     
4. Rejestrowanie urządzenia: ostatni krok polega na zarejestrowanie urządzenia w usłudze usługi Menedżer urządzeń StorSimple, działające na platformie Microsoft Azure. Rejestracja wymaga [Pobierz klucz rejestracji usługi](storsimple-8000-manage-service.md#get-the-service-registration-key) w witrynie Azure portal, a podać go w Kreatorze instalacji. **Po pomyślnym zarejestrowaniu urządzenia klucz szyfrowania danych usługi jest udostępniany. Pamiętaj ten klucz szyfrowania należy przechowywać w bezpiecznym miejscu, ponieważ będą musieli zarejestrować wszystkich kolejnych urządzeń w usłudze.**

## <a name="common-errors-during-device-deployment"></a>Typowe błędy występujące podczas wdrażania urządzenia
W poniższej tabeli wymieniono typowe błędy, mogą wystąpić po użytkownik:

* Skonfiguruj wymagane ustawienia sieci.
* Skonfiguruj ustawienia serwera proxy sieci web opcjonalne.
* Skonfiguruj hasło administratora urządzenia.
* Rejestrowanie urządzenia.

## <a name="errors-during-the-required-network-settings"></a>Błędy podczas wymagane ustawienia sieci
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Polecenie Invoke-HcsSetupWizard: To polecenie może działać tylko na aktywnym kontrolerze. |Konfiguracja wykonywana na kontrolerze pasywnym. |Uruchom to polecenie z aktywnym kontrolerem. Aby uzyskać więcej informacji, zobacz [identyfikowanie aktywnego kontrolera na urządzeniu z systemem](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Polecenie Invoke-HcsSetupWizard: Urządzenie nie jest gotowy. |Występują problemy z łącznością sieciową na interfejs DATA 0. |Sprawdź łączność sieci fizycznej na interfejs DATA 0. |
| 3 |Polecenie Invoke-HcsSetupWizard: Występuje konflikt adresu IP z innego systemu w sieci (wyjątek od HRESULT: 0x80070263). |Adres IP dla interfejsu dane 0 był już używany przez inny system. |Podaj nowy adres IP, który nie jest używany. |
| 4 |Polecenie Invoke-HcsSetupWizard: Zasób klastra nie powiodło się. (Wyjątek od HRESULT: 0x800713AE). |Duplicate VIP. Podany adres IP jest już używana. |Podaj nowy adres IP, który nie jest używany. |
| 5 |Polecenie Invoke-HcsSetupWizard: Nieprawidłowy adres IPv4. |Adres IP znajduje się w niepoprawnym formacie. |Sprawdź format, a następnie ponownie podać swój adres IP. Aby uzyskać więcej informacji, zobacz [adresów Ipv4][1]. |
| 6 |Polecenie Invoke-HcsSetupWizard: Nieprawidłowy adres IPv6. |Adres IP znajduje się w niepoprawnym formacie. |Sprawdź format, a następnie ponownie podać swój adres IP. Aby uzyskać więcej informacji, zobacz [adresowanie Ipv6][2]. |
| 7 |Polecenie Invoke-HcsSetupWizard: Nie dodatkowe punkty końcowe są dostępne z mapowania punktów końcowych. (Wyjątek od HRESULT: 0x800706D9) |Funkcje klastra nie działa. |[Skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md), aby uzyskać informacje o kolejnych krokach. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Błędy podczas web opcjonalne ustawienia serwera proxy
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Polecenie Invoke-HcsSetupWizard: Nieprawidłowy parametr (wyjątek od HRESULT: 0x80070057) |Jeden z parametrów podany dla ustawienia serwera proxy jest nieprawidłowy. |Nie podano identyfikatora URI w poprawnym formacie. Użyj następującego formatu: http:// *\<IP adres lub nazwę FQDN serwera proxy sieci web >* : *\<numer portu TCP >* |
| 2 |Polecenie Invoke-HcsSetupWizard: Serwer RPC jest niedostępny (wyjątek od HRESULT: 0x800706ba) |Główną przyczyną jest jedną z następujących czynności:<ol><li>Klaster nie jest nawet.</li><li>Kontrolera pasywnego nie mogą komunikować się z aktywnym kontrolerem, a polecenie jest wykonywane z kontrolera pasywnego.</li></ol> |W zależności od przyczyny:<ol><li>[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) aby upewnić się, że klaster działa.</li><li>Uruchom polecenie z aktywnym kontrolerem. Jeśli chcesz uruchomić polecenie z kontrolera pasywnego, należy upewnić się, że kontrolera pasywnego może komunikować się z aktywnym kontrolerem. Konieczne będzie [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) Jeśli to połączenie zostanie przerwane.</li></ol> |
| 3 |Polecenie Invoke-HcsSetupWizard: Wywołania RPC nie powiodło się (wyjątek od HRESULT: 0x800706be) |Klaster nie działa. |[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) aby upewnić się, że klaster działa. |
| 4 |Polecenie Invoke-HcsSetupWizard: Nie można odnaleźć zasobu klastra (wyjątek od HRESULT: 0x8007138f) |Nie można odnaleźć zasobu klastra. Może to nastąpić, jeśli instalacja nie jest prawidłowy. |Może być konieczne zresetowanie urządzenia do domyślnych ustawień fabrycznych. [Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) do utworzenia zasobu klastra. |
| 5 |Polecenie Invoke-HcsSetupWizard: Zasób nie online klastra (wyjątek od HRESULT: 0x8007138c) |Zasoby klastra nie są w trybie online. |[Skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md), aby uzyskać informacje o kolejnych krokach. |

## <a name="errors-related-to-device-administrator-password"></a>Błędy związane z hasła administratora urządzenia
Domyślne hasło administratora urządzenia jest **Password1**. To hasło wygasa po upływie pierwszego logowania; w związku z tym należy je zmienić za pomocą Kreatora instalacji. Po zarejestrowaniu urządzenia po raz pierwszy, musisz podać nowe hasło administratora urządzenia. 

Upewnij się, że hasła spełniać następujące wymagania:

* Hasło administratora urządzenia może zawierać od 8 do 15 znaków.
* Hasła powinna zawierać 3 z następujących typów znaków 4: małe litery, wielkie litery, liczbowych i specjalnych. 
* Hasło nie może być taka sama jak ostatnie 24 haseł.

Ponadto należy pamiętać o tym, które hasła wygasa co roku, a także można zmienić tylko po zarejestrowaniu urządzenia. Jeśli rejestracja nie powiedzie się z jakiegokolwiek powodu, nie można zmienić hasła.

Aby uzyskać więcej informacji o hasło administratora urządzenia, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple, aby zmienić hasło usługi StorSimple](storsimple-8000-change-passwords.md).

Co najmniej jeden z następujących błędów mogą wystąpić podczas konfigurowania administratora urządzenia i hasła programu StorSimple Snapshot Manager.

| Nie. | Komunikat o błędzie | Zalecana akcja |
| --- | --- | --- |
| 1 |Hasło przekracza maksymalną długość. |Hasło administratora urządzenia musi być od 8 do 15 znaków. |
| 2 |Hasło nie spełnia wymaganą długość. |Hasło administratora urządzenia musi być od 8 do 15 znaków.|
| 3 |Hasło musi zawierać małe litery. |Hasła muszą zawierać 3 z następujących typów znaków 4: małe litery, wielkie litery, liczbowych i specjalnych. Upewnij się, że hasło spełnia te wymagania. |
| 4 |Hasło musi zawierać tylko znaki numeryczne. |Hasła muszą zawierać 3 z następujących typów znaków 4: małe litery, wielkie litery, liczbowych i specjalnych. Upewnij się, że hasło spełnia te wymagania. |
| 5 |Hasło musi zawierać znaki specjalne. |Hasła muszą zawierać 3 z następujących typów znaków 4: małe litery, wielkie litery, liczbowych i specjalnych. Upewnij się, że hasło spełnia te wymagania. |
| 6 |Hasło musi zawierać 3 z następujących typów znaków 4: wielkie litery, małe litery, cyfry i specjalnych. |Hasło nie zawiera wymaganych typów znaków. Upewnij się, że hasło spełnia te wymagania. |
| 7 |Parametr jest niezgodny z potwierdzeniem. |Upewnij się, że hasło spełnia wszystkie wymagania i czy został wprowadzony poprawnie. |
| 8 |Hasło nie może być zgodna wartość domyślna. |Domyślne hasło jest *Password1*. Należy zmienić to hasło po zalogowaniu się po raz pierwszy. |
| 9 |Zostały wprowadzone hasło jest niezgodne z hasłem urządzenia. Wpisz ponownie hasło. |Sprawdź hasło, a następnie wpisz je ponownie. |

Hasła są zbierane, zanim urządzenie jest zarejestrowane, ale są stosowane dopiero po pomyślnej rejestracji. Przepływ pracy odzyskiwania hasła wymaga urządzenia do zarejestrowania.

> [!IMPORTANT]
> Ogólnie rzecz biorąc Jeśli próba zastosowania hasła nie powiedzie się, następnie oprogramowania wielokrotnie próbuje zebrać hasło przed pomyślnym zakończeniem. W rzadkich przypadkach nie można zastosować hasło. W takiej sytuacji można zarejestrować urządzenia i kontynuować, jednak nie będzie można zmienić hasła. Po zarejestrowaniu w witrynie Azure portal, możesz zmienić hasło administratora urządzenia.


Możesz zresetować hasło w witrynie Azure portal, za pomocą usługi Menedżer urządzeń StorSimple. Aby uzyskać więcej informacji, przejdź do [zmienić hasło administratora urządzenia](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Błędy podczas rejestracji urządzenia
Usługa Menedżer urządzeń StorSimple, działające na platformie Microsoft Azure umożliwia zarejestrowanie urządzenia. Z tym może wystąpić co najmniej jeden z następujących problemów podczas rejestracji urządzenia.

| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Błąd 350027: Nie można zarejestrować urządzenia przy użyciu Menedżera urządzeń StorSimple. | |Poczekaj kilka minut, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Błąd 350013: Wystąpił błąd podczas rejestrowania urządzenia. Może to być spowodowane nieprawidłowy klucz rejestracji usługi. | |Zarejestruj urządzenie ponownie przy użyciu prawidłowego klucza rejestracji. Aby uzyskać więcej informacji, zobacz [Pobierz klucz rejestracji usługi.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Błąd 350063: Uwierzytelnianie usługi Menedżer urządzeń StorSimple przekazane, ale rejestracji nie powiodło się. Spróbuj ponownie wykonać operację po pewnym czasie. |Ten błąd wskazuje, że uwierzytelniania za pomocą usługi ACS został przekazany, ale wywołanie rejestru do usługi nie powiodło się. Może to być wynikiem błąd sporadyczne sieci. |Jeśli problem będzie się powtarzać, skontaktuj [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Błąd 350049: Nie można było skontaktować się z usługą podczas rejestracji. |Po nawiązaniu połączenia z usługą Odebrano wyjątek sieci web. W niektórych przypadkach może to rozwiązania, ponowieniem próby wykonania operacji później. |Sprawdź, czy adres IP i nazwy DNS, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, [skontaktuj się z Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Błąd 350031: Urządzenia został już zarejestrowany. | |Trzeba podejmować żadnych działań. |
| 6 |Błąd 350016: Rejestracja urządzenia nie powiodła się. | |Upewnij się, że klucz rejestracji jest prawidłowy. |
| 7 |Polecenie Invoke-HcsSetupWizard: Wystąpił błąd podczas rejestrowania urządzenia; może to być spowodowane niepoprawnym adresem IP lub nazwy DNS. Sprawdź ustawienia sieci i spróbuj ponownie. Jeśli problem będzie się powtarzać, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Błąd 350050) |Upewnij się, że urządzenie może wysyłać polecenia ping siecią zewnętrzną. Jeśli nie masz łączności z siecią zewnętrzną, rejestracja może zakończyć się niepowodzeniem z powodu następującego błędu. Ten błąd może być kombinacją co najmniej jeden z następujących czynności:<ul><li>Niepoprawne IP</li><li>Nieprawidłowa podsieć</li><li>Nieprawidłowa brama</li><li>Nieprawidłowe ustawienia DNS</li></ul> |Zobacz kroki w [przykład rozwiązywania problemów krok po kroku](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Polecenie Invoke-HcsSetupWizard: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FBE2]. Spróbuj wykonać ponownie operację za jakiś czas. Jeśli problem będzie się powtarzać, skontaktuj się z Microsoft Support. |Jest to błąd rodzajowy, generowany dla wszystkich użytkowników, niewidoczne błędów z usługi lub agenta. Najbardziej typową przyczyną może być o niepowodzeniu uwierzytelniania usługi ACS. Możliwe przyczyny błędu jest, czy nie występują problemy z konfiguracją serwera NTP i czasu na urządzeniu nie jest poprawnie ustawiony. |Poprawić czas (w przypadku problemów), a następnie ponów próbę wykonania operacji rejestrowania. Jeśli używasz polecenia Set-HcsSystem - Timezone Dostosuj strefę czasową, własne w strefie czasowej (na przykład "Pacific Standard Time").  Jeśli ten problem będzie się powtarzać, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. |
| 9 |Ostrzeżenie: Nie można aktywować urządzenia. Administrator urządzenia i hasła programu StorSimple Snapshot Manager nie został zmieniony. |Jeśli rejestracja zakończy się niepowodzeniem, administrator urządzenia i hasła programu StorSimple Snapshot Manager nie są zmieniane. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Narzędzia do rozwiązywania problemów z wdrożeniami usługi StorSimple
Usługa StorSimple zawiera kilka narzędzi, które umożliwiają rozwiązywanie problemów z rozwiązania StorSimple. Należą do nich:

* Obsługuje pakietów i dzienników urządzenia.
* Polecenia cmdlet została zaprojektowana na potrzeby rozwiązywania problemów.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Obsługuje pakiety i dostępnych dzienników urządzeń do rozwiązywania problemów
Pakiet dla pomocy technicznej zawiera wszystkie odpowiednie dzienniki, które mogą pomóc zespołowi Microsoft Support rozwiązywania problemów z urządzenia. Aby wygenerować pakiet zaszyfrowanych pomocy technicznej, który można następnie udostępnić personel pomocy technicznej, można użyć środowiska Windows PowerShell dla usługi StorSimple.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Aby wyświetlić dzienniki lub zawartości pakietu dla pomocy technicznej
1. Użyj programu Windows PowerShell dla usługi StorSimple, aby wygenerować pakiet dla pomocy technicznej zgodnie z opisem w [tworzenie i zarządzanie nimi pakietu dla pomocy technicznej](storsimple-8000-create-manage-support-package.md).
2. Pobierz [skryptu odszyfrowywania](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na komputerze klienckim.
3. Użyj tego [procedury krok po kroku](storsimple-8000-create-manage-support-package.md#edit-a-support-package) do otwierania i odszyfrować pakietu dla pomocy technicznej.
4. Dzienniki pakiet odszyfrowany pomocy technicznej są w formacie etw/etvx. Można wykonać poniższe kroki, aby wyświetlić te pliki w Podglądzie zdarzeń Windows:
   
   1. Uruchom **eventvwr** polecenia na komputerze klienckim Windows. Spowoduje to uruchomienie podglądu zdarzeń.
   2. W **akcje** okienku kliknij **Otwórz zapisany dziennik** i wskazać pliki dziennika w formacie etvx/etw (pakietu dla pomocy technicznej). Można teraz wyświetlić plik. Po otwarciu pliku możesz kliknij prawym przyciskiem myszy, a następnie zapisz plik jako tekst.
      
      > [!IMPORTANT]
      > Można również użyć **polecenia Get-WinEvent** polecenia cmdlet, aby otworzyć te pliki w programie Windows PowerShell. Aby uzyskać więcej informacji, zobacz [polecenia Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) w Dokumentacja poleceń cmdlet programu Windows PowerShell.
     
5. Po otwarciu dzienniki w Podglądzie zdarzeń, wyszukaj następujące dzienniki zawierające problemy związane z konfiguracją urządzenia:
   
   * hcs_pfconfig/Operational dziennika
   * hcs_pfconfig/Config
6. W plikach dziennika umożliwia wyszukiwanie ciągów związanych z poleceniami cmdlet wywoływane przez Kreatora instalacji. Zobacz [procesu Kreatora instalacji po raz pierwszy](#first-time-setup-wizard-process) listę tych poleceń cmdlet.
7. Jeśli nie jest możliwe ustalić przyczynę problemu, możesz to zrobić [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. Użyj kroków w [Utwórz żądanie obsługi](storsimple-8000-contact-microsoft-support.md#create-a-support-request) kiedy się Support firmy Microsoft, aby uzyskać pomoc.

## <a name="cmdlets-available-for-troubleshooting"></a>Polecenia cmdlet dostępne do rozwiązywania problemów
Użyj następujących poleceń cmdlet programu Windows PowerShell, aby wykryć błędy związane z łącznością.

* `Get-NetAdapter`: Użyj tego polecenia cmdlet, aby wykryć kondycję interfejsów sieciowych.
* `Test-Connection`: Użyj tego polecenia cmdlet, aby sprawdzić łączność w sieci i spoza sieci.
* `Test-HcsmConnection`: Użyj tego polecenia cmdlet, aby sprawdzić łączność pomyślnie zarejestrowanego urządzenia.
* `Sync-HcsTime`: Użyj tego polecenia cmdlet, aby wyświetlić czas urządzenia i wymusić synchronizację czasu z serwerem NTP.
* `Enable-HcsPing` i `Disable-HcsPing`: Używać tych poleceń cmdlet, aby umożliwić hostom na polecenie ping interfejsów sieciowych na urządzeniu StorSimple. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping.
* `Trace-HcsRoute`: Użyj tego polecenia cmdlet jako narzędzie śledzenia trasy. Wysyła pakiety do każdego routera na trasie do miejsca docelowego w okresie czasu, a następnie oblicza wyniki na podstawie pakietów zwróconych z każdego przeskoku. Ponieważ `Trace-HcsRoute` pokazuje stopień utraty pakietów na określonym routerze lub łącza, można wyznaczyć routerów, które lub łącza mogą być przyczyną problemów z siecią.
* `Get-HcsRoutingTable`: Aby wyświetlić lokalnej tabeli routingu IP, należy użyć tego polecenia cmdlet.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Rozwiązywanie problemów przy użyciu polecenia cmdlet Get-NetAdapter
Podczas konfigurowania interfejsów sieciowych do wdrożenia po raz pierwszy, urządzenia, stan sprzętu nie jest dostępna w usłudze Menedżer urządzeń StorSimple interfejsu użytkownika, ponieważ urządzenie nie zostało jeszcze zarejestrowane w usłudze. Ponadto **kondycja sprzętu** bloku mogą zawsze poprawnie odzwierciedla stan urządzenia, zwłaszcza, jeśli występują problemy, które wpływają na usługę synchronizacji. W takich sytuacjach można używać `Get-NetAdapter` polecenia cmdlet, aby określić kondycję i stan interfejsów sieciowych.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Aby wyświetlić listę wszystkich kart sieciowych zainstalowanych na urządzeniu z systemem
1. Uruchom program Windows PowerShell dla usługi StorSimple, a następnie wpisz `Get-NetAdapter`. 
2. Użyj danych wyjściowych `Get-NetAdapter` polecenia cmdlet i poniższe wskazówki, aby poznać stan interfejsu sieciowego.
   
   * Jeśli interfejs jest w dobrej kondycji i włączony, **ifIndex** stan jest wyświetlany jako **się**.
   * Jeśli interfejs jest w dobrej kondycji, ale nie jest fizycznie podłączony (przy użyciu kabla sieciowego), **ifIndex** jest przedstawiana w postaci **wyłączone**.
   * Jeśli interfejs jest w dobrej kondycji, ale nie włączono **ifIndex** stan jest wyświetlany jako **NotPresent**.
   * Jeśli interfejs nie istnieje, nie ma na tej liście. Interfejs użytkownika usługi Menedżer urządzeń StorSimple w dalszym ciągu będzie ten interfejs w stanie niepowodzenia.

Aby uzyskać więcej informacji na temat sposobu użycia tego polecenia cmdlet, przejdź do [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) w Dokumentacja poleceń cmdlet programu Windows PowerShell.

W poniższych sekcjach przedstawiono przykłady dane wyjściowe z `Get-NetAdapter` polecenia cmdlet.

 W tych przykładach kontrolera 0 został kontrolera pasywnego i był skonfigurowany następująco:

* Interfejs DATA 0, dane 1, 2 danych i DATA 3 sieci, z którą interfejsów istniała na urządzeniu.
* DANE 4 i 5 dane karty sieciowe nie był obecny; w związku z tym jakiej nie występują w danych wyjściowych.
* Interfejs DATA 0 został włączony.

Kontrolera 1 aktywny kontroler został i był skonfigurowany następująco:

* Interfejs DATA 0, dane 1, 2 danych, dane 3, 4 dane i dane 5 sieci, z którą interfejsów istniała na urządzeniu.
* Interfejs DATA 0 został włączony.

**Przykładowe dane wyjściowe — kontrolera 0**

Poniżej znajduje się dane wyjściowe z poziomu kontrolera 0 (kontrolera pasywnego). DANE 1, dane 2 i DATA 3 nie są połączone. DATA 4 i 5 dane nie są wyświetlane, ponieważ nie są obecne na urządzeniu.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Przykładowe dane wyjściowe — kontrolera 1**

Poniżej znajduje się dane wyjściowe z kontrolera 1 (aktywny kontroler). Tylko dane 0 skonfigurowano interfejsu sieciowego na urządzeniu i pracy.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Rozwiązywanie problemów przy użyciu polecenia cmdlet Test-Connection
Możesz użyć `Test-Connection` polecenia cmdlet, aby określić, czy urządzenie StorSimple można połączyć z siecią zewnętrzną. Jeśli wszystkie parametry sieci, takich jak DNS, zostały skonfigurowane poprawnie, w Kreatorze instalacji, możesz użyć `Test-Connection` polecenia cmdlet, aby wykonać polecenie ping znanego adresu spoza sieci, takich jak outlook.com.

Należy włączyć polecenia ping, aby rozwiązać problemy z łącznością z tym poleceniem cmdlet wyłączenie ping.

Zobacz poniższe przykłady dane wyjściowe z `Test-Connection` polecenia cmdlet.

> [!NOTE]
> W tym przykładzie pierwsze urządzenie jest skonfigurowane przy użyciu niepoprawna konfiguracja usługi DNS. W drugim przykładzie DNS jest poprawna.

**Przykładowe dane wyjściowe — niepoprawna konfiguracja usługi DNS**

W poniższym przykładzie istnieje żadne dane wyjściowe dla adresów IPV4 i IPV6, co oznacza, że DNS nie zostanie rozwiązany. Oznacza to, że nie ma łączności z siecią zewnętrzną i poprawne DNS musi podać.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Przykładowe dane wyjściowe — właściwego systemu DNS**

W poniższym przykładzie DNS zwraca adres IPV4, wskazujący, że DNS jest prawidłowo skonfigurowany. Będzie to potwierdzenie, że istnieje łączność z siecią zewnętrzną.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Rozwiązywanie problemów przy użyciu polecenia cmdlet Test-HcsmConnection
Użyj `Test-HcsmConnection` polecenia cmdlet dla urządzenia, która jest już podłączony do rejestrowania przy użyciu usługi Menedżer urządzeń StorSimple. To polecenie cmdlet pomaga Sprawdź łączność między zarejestrowanego urządzenia i odpowiednie usługi Menedżer urządzeń StorSimple. To polecenie można uruchomić w programie Windows PowerShell dla usługi StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Aby uruchomić polecenie cmdlet Test-HcsmConnection
1. Upewnij się, że urządzenie jest zarejestrowane.
2. Sprawdź stan urządzenia. Jeśli dezaktywacji urządzenia w trybie konserwacji lub w trybie offline, może pojawić się jeden z następujących błędów:
   
   * ErrorCode.CiSDeviceDecommissioned — oznacza to, że dezaktywacji urządzenia.
   * ErrorCode.DeviceNotReady — wskazuje, czy urządzenie jest w trybie konserwacji.
   * ErrorCode.DeviceNotReady — oznacza to, że urządzenie nie jest w trybie online.
3. Sprawdź, czy jest uruchomiona usługa Menedżer urządzeń StorSimple (Użyj [Get ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) polecenia cmdlet). Jeśli usługa nie jest uruchomiona, można napotkać następujące błędy:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError — oznacza to, że wystąpił wyjątek po uruchomieniu polecenia Get-ClusterResource.
4. Sprawdź token usługi Access Control Service (ACS). Jeśli go zgłasza wyjątek sieci web, może być wynikiem problemu bramy, brak uwierzytelniania serwera proxy, niepoprawna konfiguracja usługi DNS lub wystąpił błąd uwierzytelniania. Możesz zobaczyć następujące błędy:
   
   * ErrorCode.CiSApplianceGateway — oznacza to, wyjątek HttpStatusCode.BadGateway: z usługi rozpoznawania nazw nie można rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceProxy — oznacza to, wyjątek HttpStatusCode.ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić przy użyciu serwera proxy.
   * ErrorCode.CiSApplianceDNSError — oznacza to, wyjątek WebExceptionStatus.NameResolutionFailure: z usługi rozpoznawania nazw nie można rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceACSError — oznacza to, że Usługa zwróciła błąd uwierzytelniania, ale ma łączności.
     
     Jeśli nie zostanie zgłoszony wyjątek sieci web, sprawdź, czy ErrorCode.CiSApplianceFailure. Oznacza to, że urządzenie nie powiodło się.
5. Sprawdź łączność usługi w chmurze. Jeśli usługa zgłasza wyjątek sieci web, można napotkać następujące błędy:
   
   * ErrorCode.CiSApplianceGateway — oznacza to, wyjątek HttpStatusCode.BadGateway: pośredniczącego serwera proxy otrzymał nieprawidłowe żądanie z innego serwera proxy lub z oryginalnego serwera.
   * ErrorCode.CiSApplianceProxy — oznacza to, wyjątek HttpStatusCode.ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić przy użyciu serwera proxy.
   * ErrorCode.CiSApplianceDNSError — oznacza to, wyjątek WebExceptionStatus.NameResolutionFailure: z usługi rozpoznawania nazw nie można rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceACSError — oznacza to, że Usługa zwróciła błąd uwierzytelniania, ale ma łączności.
     
     Jeśli nie zostanie zgłoszony wyjątek sieci web, sprawdź, czy ErrorCode.CiSApplianceSaasServiceError. Oznacza to problem z usługą Menedżera urządzeń StorSimple.
6. Sprawdź łączność usługi Azure Service Bus. ErrorCode.CiSApplianceServiceBusError wskazuje, czy urządzenie nie można połączyć z usługą Service Bus.

CiSCommandletLog0Curr.errlog plików dziennika, a następnie CiSAgentsvc0Curr.errlog będzie zawierał więcej informacji, np. Szczegóły wyjątku.

Aby uzyskać więcej informacji o sposobie używania polecenia cmdlet, przejdź do [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) dokumentację referencyjną w programie Windows PowerShell.

> [!IMPORTANT]
> Możesz uruchomić to polecenie cmdlet służące do aktywnych i kontrolera pasywnego.

Zobacz poniższe przykłady dane wyjściowe z `Test-HcsmConnection` polecenia cmdlet.

**Przykładowe dane wyjściowe — pomyślnie zarejestrowane urządzenia z systemem StorSimple Update 3**

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

**Przykładowe dane wyjściowe — w urządzeniu w trybie offline** 

W tym przykładzie pochodzi z urządzenia, który ma stan **Offline** w witrynie Azure portal.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Urządzenie nie może połączyć za pomocą bieżącej konfiguracji serwera proxy sieci web. Może to być problem z konfiguracji serwera proxy sieci web lub problem z łącznością sieciową. W takim przypadku należy pamiętać, że ustawienia serwera proxy sieci web są prawidłowe, oraz serwery proxy sieci web są online i jest osiągalny.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Rozwiązywanie problemów przy użyciu polecenia cmdlet Sync-HcsTime
Użyj następującego polecenia cmdlet, aby wyświetlić czas urządzenia. Jeśli czas urządzenia zawiera przesunięcie z serwerem NTP, następnie służy to polecenie cmdlet do wymuszenia synchronizacji czasu z serwerem NTP.
- Jeśli przesunięcie między urządzeniem a serwerem NTP jest większa niż 5 minut, zostanie wyświetlone ostrzeżenie. 
- Jeśli przesunięcie przekracza 15 minut, następnie urządzenie będzie przejdą w tryb offline. Można nadal używać tego polecenia cmdlet, aby wymusić synchronizację czasu. 
- Jednak jeśli przesunięcie przekracza 15 godzin, następnie nie będzie możliwe do wymuszenia synchronizacji czasu i komunikat o błędzie zostanie wyświetlony.

**Przykładowe dane wyjściowe — synchronizacji wymuszony przy użyciu Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Rozwiązywanie problemów przy użyciu poleceń cmdlet Enable HcsPing i Disable HcsPing
Użyj tych poleceń cmdlet, aby upewnić się, że interfejsy sieciowe na urządzeniu będą odpowiadać na żądania ping protokołu ICMP. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping. Za pomocą tego polecenia cmdlet jest najprostszym sposobem, aby dowiedzieć się, czy urządzenie jest w trybie online i jest osiągalny.

**Przykładowe dane wyjściowe — Enable HcsPing i Disable HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Rozwiązywanie problemów przy użyciu polecenia cmdlet HcsRoute śledzenia
Użyj tego polecenia cmdlet jako narzędzie śledzenia trasy. Wysyła pakiety do każdego routera na trasie do miejsca docelowego w okresie czasu, a następnie oblicza wyniki na podstawie pakietów zwróconych z każdego przeskoku. Ponieważ polecenie cmdlet wyświetla stopień utraty pakietów na określonym routerze lub łącza, można wyznaczyć routerów, które lub łącza mogą być przyczyną problemów z siecią.

**Przykładowe dane wyjściowe, przedstawiający sposób śledzenia trasy pakiet z HcsRoute śledzenia**

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

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Rozwiązywanie problemów przy użyciu polecenia cmdlet Get-HcsRoutingTable
Użyj tego polecenia cmdlet, aby wyświetlić tabelę routingu dla urządzenia StorSimple. Tabela routingu jest zestaw reguł, które mogą pomóc ustalić, gdzie będą kierowane pakiety danych przesyłanych przez sieć protokołu internetowego (IP).

Tabela routingu pokazuje interfejsy i bramy, która kieruje je do określonych sieciach. Daje ona również metryki routingu, który jest podejmująca decyzję, aby uzyskać ścieżkę do miejsca docelowego określonego. Dolna metryki routingu, tym wyższy priorytet.

Na przykład w przypadku 2 interfejsy sieciowe DATA 2 i DATA 3 połączenie z Internetem. Jeśli metryka routingu DATA 2 i DATA 3 są odpowiednio 15 i 261, 2 danych z niższym metryki routingu jest preferowany interfejs używany do uzyskiwania dostępu do Internetu.

Jeśli aktualizacja Update 1 są uruchomione na urządzeniu StorSimple, usługi interfejs sieciowy DATA 0 ma najwyższy preferencji dla ruchu w chmurze. Oznacza to, że nawet w przypadku innych interfejsów z włączoną obsługę chmury, ruchu w chmurze będą kierowane za pośrednictwem interfejsu dane 0.

Jeśli uruchamiasz `Get-HcsRoutingTable` polecenia cmdlet bez określania parametrów (jak w poniższym przykładzie przedstawiono), polecenia cmdlet zwróci tabelach routingu IPv4 i IPv6. Alternatywnie, można określić `Get-HcsRoutingTable -IPv4` lub `Get-HcsRoutingTable -IPv6` można pobrać odpowiedni tabeli routingu.

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

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Przykład rozwiązywania problemów krok po kroku StorSimple
Poniższy przykład pokazuje, rozwiązywania problemów krok po kroku wdrożenia usługi StorSimple. W przykładowym scenariuszu urządzenia, rejestracja kończy się niepowodzeniem komunikat o błędzie wskazujący, że ustawienia sieciowej lub nazwę DNS jest niepoprawny.

Jest zwracany komunikat o błędzie:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Ten błąd może być spowodowane przez jedną z następujących czynności:

* Sprzęt niepoprawna instalacja
* Interfejsy sieciowe awaryjnym
* Nieprawidłowy adres IP, maskę podsieci, bramy, podstawowy serwer DNS lub serwer proxy sieci web
* Klucz rejestracji niepoprawne
* Ustawienia zapory niepoprawne

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Aby zlokalizować i rozwiązać problem rejestracji urządzenia
1. Sprawdź konfigurację urządzenia: Uruchom na aktywnym kontrolerze `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > Kreator instalacji należy uruchomić na aktywnym kontrolerze. Aby sprawdzić, czy masz połączenie z aktywnym kontrolerem, Przyjrzyj się Baner znajdujące się w konsoli szeregowej. Transparent wskazuje, czy nawiązano połączenie z kontrolerem 0 lub kontrolera 1 i tego, czy kontroler jest aktywnych lub pasywnych. Aby uzyskać więcej informacji, przejdź do [identyfikowanie aktywnego kontrolera na urządzeniu z systemem](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Upewnij się, że urządzenie jest prawidłowo rozpakowane: Sprawdź okablowania ponownie płaszczyzną urządzenia sieciowego. Okablowanie zależy od modelu urządzenia. Aby uzyskać więcej informacji, przejdź do [instalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Jeśli używasz portów sieciowych 10 GbE należy korzystać z podanych kart QSFP SFP i kable SFP. Aby uzyskać więcej informacji, zobacz [listy kable, przełączniki i odbiorcze zalecane dla portów 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Sprawdzanie kondycji interfejsu sieciowego:
   
   * Użyj polecenia cmdlet Get-NetAdapter do wykrywania kondycji interfejsy sieciowe DATA 0. 
   * Jeśli link nie działa, **ifindex** stan wskaże, że interfejs nie działa. Następnie należy sprawdzić połączenia sieciowego port do urządzenia i z przełącznikiem. Należy również wykluczenia zły kable. 
   * Jeśli podejrzewasz, że DATA 0, port na aktywnym kontrolerze nie powiodło się, można to potwierdzić, nawiązując połączenie z danymi 0 port kontrolera 1. Można to potwierdzić, odłącz kabel sieciowy z tyłu urządzenia z poziomu kontrolera 0, podłącz kabel do kontrolera 1, a następnie ponownie uruchom polecenie cmdlet Get-NetAdapter.
     Jeśli DATA 0 jest port na kontrolerze zakończy się niepowodzeniem, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. Konieczne może być wymiana kontrolera w Twoim systemie.
4. Sprawdź połączenia z przełącznikiem:
   
   * Upewnij się, że interfejsy sieciowe 0 dane na kontrolerze 0 i kontrolera 1 w Twojej podstawowej obudowie znajdują się w tej samej podsieci. 
   * Sprawdź Centrum lub router. Zazwyczaj należy połączyć oba kontrolery na tym samym Centrum lub router. 
   * Upewnij się, że przełączników, których używasz dla połączenia znajdują się dane 0 dla obu kontrolerów, w tej samej sieci vLAN.
5. Wyeliminuj błędy użytkownika:
   
   * Uruchom ponownie Kreatora instalacji (uruchamianie **polecenie Invoke-HcsSetupWizard**), a następnie wprowadź wartości ponownie, aby upewnić się, że nie ma żadnych błędów. 
   * Weryfikuj rejestrację klucz używany. Ten sam klucz rejestracji może służyć do łączenia z wielu urządzeń do usługi Menedżer urządzeń StorSimple. Procedura [Pobierz klucz rejestracji usługi](storsimple-8000-manage-service.md#get-the-service-registration-key) aby upewnić się, że używasz klucza rejestracji poprawne.
     
     > [!IMPORTANT]
     > Jeśli masz wiele usług, uruchamianie, należy upewnić się, że klucz rejestracji usługi odpowiednie służy do rejestrowania urządzenia. Jeśli urządzenia zostały zarejestrowane w usłudze Menedżer urządzeń StorSimple problem, konieczne będzie [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) dalsze czynności. Być może trzeba wykonać w celu resetowania do ustawień fabrycznych urządzenia (co może skutkować utratą danych), a następnie połączyć je z usługą zamierzone.
     > 
     > 
6. Aby sprawdzić, czy masz połączenie z siecią zewnętrzną, należy użyć polecenia cmdlet Test-Connection. Aby uzyskać więcej informacji, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Sprawdź, czy zakłócenia zapory. Jeśli sprawdzono, ustawień wirtualnego adresu IP (VIP), podsieci, bramy i DNS są poprawne i nadal widzisz problemy z łącznością, a następnie jest możliwe, że Zapora blokuje komunikację między urządzeniem i siecią zewnętrzną. Należy upewnić się, że porty 80 i 443 są dostępne na urządzeniu StorSimple dla komunikacji wychodzącej. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące sieci dla urządzenia StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Sprawdź dzienniki. Przejdź do [obsługuje pakiety i dostępnych dzienników urządzeń do rozwiązywania problemów](#support-packages-and-device-logs-available-for-troubleshooting).
9. Jeśli poprzednie kroki nie rozwiązują problemu, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) uzyskać pomoc.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak rozwiązywanie problemów z urządzeniem StorSimple za pomocą narzędzia do diagnostyki](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
