---
title: Rozwiązywanie problemów z wdrażaniem serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób diagnozowania i naprawiania błędów, które występują podczas pierwszego wdrażania StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267627"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Rozwiązywanie problemów z wdrażaniem urządzenia StorSimple
## <a name="overview"></a>Omówienie
Ten artykuł zawiera przydatne wskazówki dotyczące rozwiązywania problemów dotyczących wdrożenia usługi Microsoft Azure StorSimple. Opisano w nim typowe problemy, możliwe przyczyny i zalecane kroki ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas konfigurowania storsimple. 

Te informacje dotyczą zarówno urządzenia fizycznego storsimple serii 8000, jak i urządzenia StorSimple Cloud Appliance.

> [!NOTE]
> Problemy związane z konfiguracją urządzenia, które mogą wystąpić podczas wdrażania urządzenia po raz pierwszy lub mogą wystąpić później, gdy urządzenie działa. W tym artykule skupiono się na rozwiązywaniu problemów z wdrażaniem po raz pierwszy. Aby rozwiązać problem z urządzeniem operacyjnym, przejdź do strony [Użyj narzędzia Diagnostyka, aby rozwiązać problem z urządzeniem operacyjnym](storsimple-8000-diagnostics.md).

W tym artykule opisano również narzędzia do rozwiązywania problemów z wdrożeniami StorSimple i zawiera przykład rozwiązywania problemów krok po kroku.

## <a name="first-time-deployment-issues"></a>Problemy z wdrażaniem po raz pierwszy
Jeśli problem występuje podczas pierwszego wdrażania urządzenia, należy wziąć pod uwagę następujące kwestie:

* Jeśli rozwiązujesz problemy z urządzeniem fizycznym, upewnij się, że sprzęt został zainstalowany i skonfigurowany zgodnie z opisem w [temacie Instalowanie urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [Instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
* Sprawdź wymagania wstępne dotyczące wdrażania. Upewnij się, że masz wszystkie informacje opisane na [liście kontrolnej konfiguracji wdrożenia](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Zapoznaj się z storsimple release notes, aby sprawdzić, czy problem jest opisany. Informacje o wersji zawierają obejścia znanych problemów z instalacją. 

Podczas wdrażania urządzenia najczęstsze problemy, z którymi borykają się użytkownicy, występują podczas uruchamiania kreatora konfiguracji i rejestrowania urządzenia za pośrednictwem programu Windows PowerShell for StorSimple. (Program Windows PowerShell for StorSimple służy do rejestrowania i konfigurowania urządzenia StorSimple. Aby uzyskać więcej informacji na temat rejestracji urządzenia, zobacz [Krok 3: Konfigurowanie i rejestrowanie urządzenia za pośrednictwem programu Windows PowerShell for StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Poniższe sekcje mogą pomóc w rozwiązaniu problemów, które występują podczas konfigurowania urządzenia StorSimple po raz pierwszy.

## <a name="first-time-setup-wizard-process"></a>Proces kreatora konfiguracji po raz pierwszy
Poniższe kroki podsumowują proces kreatora instalacji. Aby uzyskać szczegółowe informacje o konfiguracji, zobacz [Wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

1. Uruchom polecenie cmdlet [Invoke-HcsSetupWizard,](https://technet.microsoft.com/library/dn688135.aspx) aby uruchomić kreatora konfiguracji, który poprowadzi Cię przez pozostałe kroki. 
2. Konfigurowanie sieci: kreator konfiguracji umożliwia skonfigurowanie ustawień sieciowych interfejsu sieciowego DATA 0 na urządzeniu StorSimple. Są to m.in. następujące ustawienia:
   * Wirtualny adres IP (VIP), maska podsieci i brama — polecenie cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) jest wykonywane w tle. Konfiguruje adres IP, maskę podsieci i bramę dla interfejsu sieciowego DATA 0 na urządzeniu StorSimple.
   * Podstawowy serwer DNS — polecenie cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) jest wykonywane w tle. Konfiguruje ustawienia DNS dla rozwiązania StorSimple.
   * Serwer NTP — polecenie cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) jest wykonywane w tle. Konfiguruje ustawienia serwera NTP dla rozwiązania StorSimple.
   * Opcjonalny serwer proxy sieci Web — polecenie cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) jest wykonywane w tle. Ustawia i włącza konfigurację serwera proxy sieci Web dla rozwiązania StorSimple.
3. Skonfiguruj hasło: następnym krokiem jest skonfigurowanie hasła administratora urządzenia.
   Hasło administratora urządzenia służy do logowania się do urządzenia. Domyślne hasło urządzenia to **Password1**.
        
     > [!IMPORTANT]
     > Hasła są zbierane przed rejestracją, ale stosowane dopiero po pomyślnym zarejestrowaniu urządzenia. Jeśli nie można zastosować hasła, zostanie wyświetlony monit o ponowne podanie hasła do momentu zebrania wymaganych haseł (spełniania wymagań dotyczących złożoności).
     
4. Zarejestruj urządzenie: ostatnim krokiem jest zarejestrowanie urządzenia w usłudze StorSimple Device Manager uruchomionej na platformie Microsoft Azure. Rejestracja wymaga [uzyskania klucza rejestracji usługi](storsimple-8000-manage-service.md#get-the-service-registration-key) z witryny Azure portal i udostępnić go w kreatorze instalacji. **Po pomyślnym zarejestrowaniu urządzenia zostanie podany klucz szyfrowania danych usługi. Pamiętaj, aby zachować ten klucz szyfrowania w bezpiecznej lokalizacji, ponieważ będzie on wymagany do zarejestrowania wszystkich kolejnych urządzeń w usłudze.**

## <a name="common-errors-during-device-deployment"></a>Typowe błędy podczas wdrażania urządzenia
W poniższych tabelach przedstawiono typowe błędy, które mogą wystąpić podczas:

* Skonfiguruj wymagane ustawienia sieciowe.
* Skonfiguruj opcjonalne ustawienia serwera proxy sieci Web.
* Skonfiguruj hasło administratora urządzenia.
* Zarejestruj urządzenie.

## <a name="errors-during-the-required-network-settings"></a>Błędy podczas wymaganych ustawień sieciowych
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: To polecenie można uruchomić tylko na aktywnym kontrolerze. |Konfiguracja była wykonywana na kontrolerze pasywnym. |Uruchom to polecenie z aktywnego kontrolera. Aby uzyskać więcej informacji, zobacz [Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Urządzenie nie jest gotowe. |Występują problemy z łącznością sieciową na danych 0. |Sprawdź fizyczną łączność sieciową na danych 0. |
| 3 |Invoke-HcsSetupWizard: Występuje konflikt adresu IP z innym systemem w sieci (Wyjątek od HRESULT: 0x80070263). |Ip dostarczone dla DANYCH 0 było już używane przez inny system. |Podaj nowy adres IP, który nie jest używany. |
| 4 |Invoke-HcsSetupWizard: Zasób klastra nie powiódł się. (Wyjątek od HRESULT: 0x800713AE). |Zduplikowany adres VIP. Dostarczony adres IP jest już używany. |Podaj nowy adres IP, który nie jest używany. |
| 5 |Invoke-HcsSetupWizard: Nieprawidłowy adres IPv4. |Adres IP jest podany w niepoprawnym formacie. |Sprawdź format i ponownie podaj swój adres IP. Aby uzyskać więcej informacji, zobacz [Adresowanie Ipv4][1]. |
| 6 |Invoke-HcsSetupWizard: Nieprawidłowy adres IPv6. |Adres IP jest podany w niepoprawnym formacie. |Sprawdź format i ponownie podaj swój adres IP. Aby uzyskać więcej informacji, zobacz [Adresowanie Ipv6][2]. |
| 7 |Invoke-HcsSetupWizard: Nie ma więcej punktów końcowych dostępnych z mapera punktu końcowego. (Wyjątek od HRESULT: 0x800706D9) |Funkcja klastra nie działa. |Aby uzyskać następujące kroki, [skontaktuj się z pomocą techniczną firmy Microsoft.](storsimple-8000-contact-microsoft-support.md) |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Błędy podczas opcjonalnych ustawień serwera proxy sieci Web
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Nieprawidłowy parametr (wyjątek od HRESULT: 0x80070057) |Jeden z parametrów podanych dla ustawień serwera proxy jest nieprawidłowy. |Identyfikator URI nie jest podany w poprawnym formacie. Użyj następującego formatu: http://*\<adres IP lub nazwa FQDN serwera proxy www>*:*\<Numer portu TCP>* |
| 2 |Invoke-HcsSetupWizard: Serwer RPC jest niedostępny (wyjątek od HRESULT: 0x800706ba) |Główną przyczyną jest jedna z następujących przyczyn:<ol><li>Klaster nie jest w górę.</li><li>Kontroler pasywny nie może komunikować się z aktywnym kontrolerem, a polecenie jest uruchamiane z kontrolera pasywnego.</li></ol> |W zależności od głównej przyczyny:<ol><li>[Skontaktuj się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby upewnić się, że klaster jest w górę.</li><li>Uruchom polecenie z aktywnego kontrolera. Jeśli chcesz uruchomić polecenie z kontrolera pasywnego, musisz upewnić się, że kontroler pasywny może komunikować się z aktywnym kontrolerem. Jeśli ta łączność jest uszkodzona, należy [skontaktować się z pomocą techniczną firmy Microsoft.](storsimple-8000-contact-microsoft-support.md)</li></ol> |
| 3 |Invoke-HcsSetupWizard: Wywołanie RPC nie powiodło się (wyjątek od HRESULT: 0x800706be) |Klaster jest w dół. |[Skontaktuj się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby upewnić się, że klaster jest w górę. |
| 4 |Invoke-HcsSetupWizard: Nie znaleziono zasobu klastra (wyjątek od HRESULT: 0x8007138f) |Nie znaleziono zasobu klastra. Może się to zdarzyć, gdy instalacja nie była poprawna. |Może być konieczne zresetowanie urządzenia do ustawień fabrycznych. [Skontaktuj się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby utworzyć zasób klastra. |
| 5 |Invoke-HcsSetupWizard: Zasób klastra nie jest w trybie online (wyjątek od HRESULT: 0x8007138c) |Zasoby klastra nie są w trybie online. |Aby uzyskać następujące kroki, [skontaktuj się z pomocą techniczną firmy Microsoft.](storsimple-8000-contact-microsoft-support.md) |

## <a name="errors-related-to-device-administrator-password"></a>Błędy związane z hasłem administratora urządzenia
Domyślnym hasłem administratora urządzenia jest **Password1**. To hasło wygasa po pierwszym zalogowaniu; w związku z tym należy użyć kreatora konfiguracji, aby go zmienić. Podczas pierwszej rejestracji urządzenia należy podać nowe hasło administratora urządzenia. 

Upewnij się, że hasła spełniają następujące wymagania:

* Hasło administratora urządzenia powinno mieć od 8 do 15 znaków.
* Hasła powinny zawierać 3 z następujących 4 typów znaków: małe, wielkie, numeryczne i specjalne. 
* Hasło nie może być takie samo jak ostatnie 24 hasła.

Ponadto należy pamiętać, że hasła wygasają co roku i mogą być zmieniane dopiero po pomyślnym zarejestrowaniu urządzenia. Jeśli rejestracja nie powiedzie się z jakiegokolwiek powodu, hasła nie zostaną zmienione.

Aby uzyskać więcej informacji na temat hasła administratora urządzenia, przejdź do [usługi StorSimple Device Manager w celu zmiany hasła StorSimple](storsimple-8000-change-passwords.md).

Podczas konfigurowania hasła administratora urządzenia i menedżera migawek StorSimple może wystąpić co najmniej jeden z następujących błędów.

| Nie. | Komunikat o błędzie | Zalecana akcja |
| --- | --- | --- |
| 1 |Hasło przekracza maksymalną długość. |Hasło administratora urządzenia musi mieć od 8 do 15 znaków. |
| 2 |Hasło nie spełnia wymaganej długości. |Hasło administratora urządzenia musi mieć od 8 do 15 znaków.|
| 3 |Hasło musi zawierać małe litery. |Hasła muszą zawierać 3 z następujących 4 typów znaków: małe, wielkie, numeryczne i specjalne. Upewnij się, że hasło spełnia te wymagania. |
| 4 |Hasło musi zawierać znaki liczbowe. |Hasła muszą zawierać 3 z następujących 4 typów znaków: małe, wielkie, numeryczne i specjalne. Upewnij się, że hasło spełnia te wymagania. |
| 5 |Hasło musi zawierać znaki specjalne. |Hasła muszą zawierać 3 z następujących 4 typów znaków: małe, wielkie, numeryczne i specjalne. Upewnij się, że hasło spełnia te wymagania. |
| 6 |Hasło musi zawierać 3 z następujących 4 typów znaków: wielkie, małe litery, numeryczne i specjalne. |Hasło nie zawiera wymaganych typów znaków. Upewnij się, że hasło spełnia te wymagania. |
| 7 |Parametr nie pasuje do potwierdzenia. |Upewnij się, że hasło spełnia wszystkie wymagania i że zostało wprowadzone poprawnie. |
| 8 |Hasło nie może być zgodne z wartością domyślną. |Domyślnym hasłem jest *Password1*. Musisz zmienić to hasło po pierwszym zalogowaniu. |
| 9 |Wprowadzone hasło nie jest zgodne z hasłem urządzenia. Wpisz ponownie hasło. |Sprawdź hasło i wpisz go ponownie. |

Hasła są zbierane przed rejestracją urządzenia, ale są stosowane tylko po pomyślnej rejestracji. Przepływ pracy odzyskiwania hasła wymaga zarejestrowania urządzenia.

> [!IMPORTANT]
> Ogólnie rzecz biorąc, jeśli próba zastosowania hasła nie powiedzie się, oprogramowanie wielokrotnie próbuje zebrać hasło, dopóki nie zakończy się pomyślnie. W rzadkich przypadkach nie można zastosować hasła. W tej sytuacji można zarejestrować urządzenie i kontynuować, jednak hasła nie zostaną zmienione. Hasło administratora urządzenia można zmienić po rejestracji w witrynie Azure portal.


Hasło można zresetować w witrynie Azure portal za pośrednictwem usługi StorSimple Device Manager. Aby uzyskać więcej informacji, przejdź do [strony Zmienianie hasła administratora urządzenia](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Błędy podczas rejestracji urządzenia
Aby zarejestrować urządzenie, należy użyć usługi StorSimple Device Manager uruchomionej na platformie Microsoft Azure. Podczas rejestracji urządzenia można napotkać co najmniej jeden z następujących problemów.

| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Błąd 350027: Nie można zarejestrować urządzenia w Menedżerze urządzeń StorSimple. | |Poczekaj kilka minut, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzał, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 2 |Błąd 350013: Wystąpił błąd podczas rejestrowania urządzenia. Może to być spowodowane niepoprawnym kluczem rejestracji usługi. | |Zarejestruj urządzenie ponownie przy tym przy tym przy tym przy tym przy tym przy tym przy tym przy tym przy tym nawuszcze informacje o poprawnym numerze rejestracyjnym usługi. Aby uzyskać więcej informacji, zobacz [Pobierz klucz rejestracji usługi.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Błąd 350063: Uwierzytelnianie usługi StorSimple Device Manager przebiegło, ale rejestracja nie powiodła się. Po pewnym czasie ponów próbę wykonania operacji. |Ten błąd wskazuje, że uwierzytelnianie za pomocą usługi ACS minęło, ale wywołanie rejestru wykonane do usługi nie powiodło się. Może to być wynikiem sporadycznego błędu sieci. |Jeśli problem będzie się powtarzał, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 4 |Błąd 350049: Nie można uzyskać połączenia z usługą podczas rejestracji. |Po nawoływania do usługi odbierany jest wyjątek sieci Web. W niektórych przypadkach może to zostać naprawione przez ponowne ponowienie wykonania operacji później. |Sprawdź swój adres IP i nazwę DNS, a następnie ponów próbę wykonania operacji. Jeśli problem będzie się powtarzał, [skontaktuj się z pomocą techniczną firmy Microsoft.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Błąd 350031: Urządzenie zostało już zarejestrowane. | |Nie trzeba podejmować żadnych działań. |
| 6 |Błąd 350016: Rejestracja urządzenia nie powiodła się. | |Upewnij się, że klucz rejestracyjny jest poprawny. |
| 7 |Invoke-HcsSetupWizard: Wystąpił błąd podczas rejestrowania urządzenia; może to być spowodowane nieprawidłowym adresem IP lub nazwą DNS. Sprawdź ustawienia sieciowe i spróbuj ponownie. Jeśli problem będzie się powtarzał, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md). (Błąd 350050) |Upewnij się, że urządzenie może pingować sieć zewnętrzną. Jeśli nie masz łączności z siecią zewnętrzną, rejestracja może zakończyć się niepowodzeniem z tym błędem. Ten błąd może być kombinacją co najmniej jednej z następujących czynności:<ul><li>Nieprawidłowy adres IP</li><li>Niepoprawna podsieć</li><li>Nieprawidłowa brama</li><li>Nieprawidłowe ustawienia DNS</li></ul> |Zobacz kroki opisane w [przykładzie rozwiązywania problemów krok po kroku](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FBE2]. Ponów próbę wykonania operacji po pewnym czasie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. |Jest to ogólny błąd generowany dla wszystkich niewidocznych błędów użytkownika z usługi lub agenta. Najczęstszą przyczyną może być niepowodzenie uwierzytelniania ACS. Możliwą przyczyną błędu jest to, że występują problemy z konfiguracją serwera NTP i czas na urządzeniu nie jest ustawiony poprawnie. |Popraw czas (jeśli występują problemy), a następnie ponów próbę wykonania operacji rejestracji. Jeśli dopasowywanie strefy czasowej jest używane polecenie Set-HcsSystem -Timezone, należy użyć polecenia "Ustaw-system -Timezone", skapitalizuj każdy wyraz w strefie czasowej (na przykład "Pacyficzny czas standardowy").  Jeśli ten problem będzie się powtarzał, [skontaktuj się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby uzyskać następujące kroki. |
| 9 |Ostrzeżenie: Nie można aktywować urządzenia. Hasła administratora urządzenia i Menedżera migawek StorSimple nie zostały zmienione. |Jeśli rejestracja nie powiedzie się, hasło administratora urządzenia i Menedżera migawek StorSimple nie zostaną zmienione. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Narzędzia do rozwiązywania problemów z wdrożeniami usługi StorSimple
StorSimple zawiera kilka narzędzi, których można użyć do rozwiązywania problemów z rozwiązaniem StorSimple. Należą do nich:

* Pakiety pomocy technicznej i dzienniki urządzeń.
* Polecenia cmdlet zaprojektowane specjalnie do rozwiązywania problemów.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Dostępne pakiety pomocy technicznej i dzienniki urządzeń do rozwiązywania problemów
Pakiet pomocy technicznej zawiera wszystkie odpowiednie dzienniki, które mogą pomóc zespołowi pomocy technicznej firmy Microsoft w rozwiązywaniu problemów z urządzeniem. Za pomocą programu Windows PowerShell for StorSimple można wygenerować zaszyfrowany pakiet pomocy technicznej, który można następnie udostępnić personelowi pomocy technicznej.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Aby wyświetlić dzienniki lub zawartość pakietu pomocy technicznej
1. Użyj programu Windows PowerShell for StorSimple, aby wygenerować pakiet pomocy technicznej zgodnie z opisem w [temat Tworzenie i zarządzanie pakietem pomocy technicznej](storsimple-8000-create-manage-support-package.md).
2. Pobierz [skrypt deszyfrowania](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na komputerze klienckim.
3. Ta [procedura krok po kroku](storsimple-8000-create-manage-support-package.md#edit-a-support-package) służy do otwierania i odszyfrowywania pakietu pomocy technicznej.
4. Odszyfrowane dzienniki pakietów pomocy technicznej są w formacie etw/etvx. Aby wyświetlić te pliki w Podglądzie zdarzeń systemu Windows, można wykonać następujące czynności:
   
   1. Uruchom polecenie **eventvwr** na kliencie systemu Windows. Spowoduje to uruchomienie Podglądu zdarzeń.
   2. W okienku **Akcje** kliknij pozycję **Otwórz zapisany dziennik** i wskaż pliki dziennika w formacie etvx/etw (pakiet wsparcia). Teraz można wyświetlić plik. Po otwarciu pliku można kliknąć plik prawym przyciskiem myszy i zapisać go jako tekst.
      
      > [!IMPORTANT]
      > Można również użyć polecenia cmdlet **Get-WinEvent,** aby otworzyć te pliki w programie Windows PowerShell. Aby uzyskać więcej informacji, zobacz [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) w dokumentacji odwołania do polecenia polecenia cmdlet programu Windows PowerShell.
     
5. Po otwarciu dzienników w Podglądzie zdarzeń poszukaj następujących dzienników, które zawierają problemy związane z konfiguracją urządzenia:
   
   * dziennik hcs_pfconfig/operacyjny
   * hcs_pfconfig/Config
6. W plikach dziennika wyszukaj ciągi powiązane z poleceniami cmdlet wywoływane przez kreatora instalacji. Zobacz [Pierwszy proces konfiguracji, aby](#first-time-setup-wizard-process) wyświetlić listę tych poleceń cmdlet.
7. Jeśli nie możesz ustalić przyczyny problemu, możesz [skontaktować się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby uzyskać następujące kroki. Czynności opisane w programie [Utwórz żądanie pomocy technicznej](storsimple-8000-contact-microsoft-support.md#create-a-support-request) umożliwiają skontaktowanie się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

## <a name="cmdlets-available-for-troubleshooting"></a>Polecenia cmdlet dostępne do rozwiązywania problemów
Aby wykryć błędy łączności, użyj następujących poleceń cmdlet programu Windows PowerShell.

* `Get-NetAdapter`: Użyj tego polecenia cmdlet, aby wykryć kondycję interfejsów sieciowych.
* `Test-Connection`: Użyj tego polecenia cmdlet, aby sprawdzić łączność sieciową wewnątrz i na zewnątrz sieci.
* `Test-HcsmConnection`: Użyj tego polecenia cmdlet, aby sprawdzić łączność pomyślnie zarejestrowanego urządzenia.
* `Sync-HcsTime`: Użyj tego polecenia cmdlet, aby wyświetlić czas urządzenia i wymusić synchronizację czasu z serwerem NTP.
* `Enable-HcsPing`oraz `Disable-HcsPing`: Użyj tych poleceń cmdlet, aby umożliwić hostom ping interfejsów sieciowych na urządzeniu StorSimple. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping.
* `Trace-HcsRoute`: Użyj tego polecenia cmdlet jako narzędzia do śledzenia trasy. Wysyła pakiety do każdego routera w drodze do miejsca docelowego w okresie czasu, a następnie oblicza wyniki na podstawie pakietów zwróconych z każdego przeskoku. Ponieważ `Trace-HcsRoute` pokazuje stopień utraty pakietów w danym routerze lub łączu, można wskazać, które routery lub łącza mogą powodować problemy z siecią.
* `Get-HcsRoutingTable`: Użyj tego polecenia cmdlet, aby wyświetlić lokalną tabelę routingu IP.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Rozwiązywanie problemów z poleceniem cmdlet Get-NetAdapter
Podczas konfigurowania interfejsów sieciowych dla pierwszego wdrożenia urządzenia stan sprzętu nie jest dostępny w interfejsie użytkownika usługi StorSimple Device Manager, ponieważ urządzenie nie jest jeszcze zarejestrowane w usłudze. Ponadto blok **kondycji sprzętu** może nie zawsze poprawnie odzwierciedlać stan urządzenia, zwłaszcza jeśli występują problemy, które wpływają na synchronizację usługi. W takich sytuacjach można `Get-NetAdapter` użyć polecenia cmdlet do określenia kondycji i stanu interfejsów sieciowych.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Aby wyświetlić listę wszystkich kart sieciowych na urządzeniu
1. Uruchom program Windows PowerShell dla storproszami, a następnie wpisz . `Get-NetAdapter` 
2. Użyj danych wyjściowych polecenia `Get-NetAdapter` cmdlet i poniższych wskazówek, aby zrozumieć stan interfejsu sieciowego.
   
   * Jeśli interfejs jest w dobrej kondycji i włączony, stan **ifIndex** jest wyświetlany jako **W górę**.
   * Jeśli interfejs jest w dobrej kondycji, ale nie jest fizycznie podłączony (za pomocą kabla sieciowego), **ifIndex** jest wyświetlany jako **Wyłączony**.
   * Jeśli interfejs jest w dobrej kondycji, ale nie jest włączony, stan **ifIndex** jest wyświetlany jako **NotPresent**.
   * Jeśli interfejs nie istnieje, nie pojawia się na tej liście. Interfejs użytkownika usługi Menedżer urządzeń StorSimple nadal będzie wyświetlał ten interfejs w stanie awarii.

Aby uzyskać więcej informacji na temat używania tego polecenia cmdlet, przejdź do polecenia polecenia cmdlet [programu Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) w odwołaniu do polecenia cmdlet programu Windows PowerShell.

W poniższych sekcjach przedstawiono `Get-NetAdapter` próbki danych wyjściowych z polecenia cmdlet.

 W tych przykładach kontroler 0 był kontrolerem pasywnym i został skonfigurowany w następujący sposób:

* Interfejsy sieciowe DATA 0, DATA 1, DATA 2 i DATA 3 istniały na urządzeniu.
* Karty interfejsu sieciowego DATA 4 i DATA 5 nie były obecne; w związku z tym nie są one wymienione w danych wyjściowych.
* DATA 0 została włączona.

Kontroler 1 był aktywnym kontrolerem i został skonfigurowany w następujący sposób:

* W urządzeniu istniały interfejsy sieciowe DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 i DATA 5.
* DATA 0 została włączona.

**Wyjście próbki – sterownik 0**

Poniżej przedstawiono dane wyjściowe ze sterownika 0 (kontroler pasywny). DANE 1, DANE 2 i DANE 3 nie są połączone. Dane 4 i DATA 5 nie są wymienione, ponieważ nie są obecne na urządzeniu.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Wyjście próbki – sterownik 1**

Poniżej przedstawiono dane wyjściowe ze sterownika 1 (aktywny kontroler). Tylko interfejs sieciowy DATA 0 na urządzeniu jest skonfigurowany i działa.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Rozwiązywanie problemów z poleceniem cmdlet Test-Connection
Za pomocą `Test-Connection` polecenia cmdlet można określić, czy urządzenie StorSimple może łączyć się z siecią zewnętrzną. Jeśli wszystkie parametry sieci, w tym DNS, są poprawnie skonfigurowane w `Test-Connection` kreatorze instalacji, można użyć polecenia cmdlet do pingowania znanego adresu poza siecią, takiego jak outlook.com.

Jeśli ping jest wyłączony, należy włączyć polecenie Ping, aby rozwiązać problemy z łącznością z tym poleceniem cmdlet.

Zobacz następujące przykłady danych `Test-Connection` wyjściowych z polecenia cmdlet.

> [!NOTE]
> W pierwszym przykładzie urządzenie jest skonfigurowane z niepoprawnym systemem DNS. W drugim przykładzie dns jest poprawny.

**Przykładowe dane wyjściowe — niepoprawny system DNS**

W poniższym przykładzie nie ma żadnych danych wyjściowych dla adresów IPV4 i IPV6, co wskazuje, że system DNS nie został rozwiązany. Oznacza to, że nie ma łączności z siecią zewnętrzną i należy podać poprawny system DNS.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Przykładowe dane wyjściowe — poprawny DNS**

W poniższym przykładzie dns zwraca adres IPV4, wskazując, że dns jest poprawnie skonfigurowany. Potwierdza to, że istnieje łączność z siecią zewnętrzną.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Rozwiązywanie problemów z poleceniem cmdlet Test-HcsmConnection
Użyj `Test-HcsmConnection` polecenia cmdlet dla urządzenia, które jest już podłączone do usługi StorSimple Device Manager i zarejestrowane w usłudze StorSimple Device Manager. To polecenie cmdlet pomaga zweryfikować łączność między zarejestrowanym urządzeniem a odpowiednią usługą StorSimple Device Manager. To polecenie można uruchomić w programie Windows PowerShell for StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Aby uruchomić polecenie cmdlet Test-HcsmConnection
1. Upewnij się, że urządzenie jest zarejestrowane.
2. Sprawdź stan urządzenia. Jeśli urządzenie jest dezaktywowane w trybie konserwacji lub w trybie offline, może zostać wyświetlony jeden z następujących błędów:
   
   * ErrorCode.CiSDeviceDecommissioned — oznacza to, że urządzenie jest dezaktywowane.
   * ErrorCode.DeviceNotReady — oznacza to, że urządzenie jest w trybie konserwacji.
   * ErrorCode.DeviceNotReady — oznacza to, że urządzenie nie jest w trybie online.
3. Sprawdź, czy usługa Menedżer urządzeń StorSimple jest uruchomiona (użyj polecenia cmdlet [Get-ClusterResource).](https://technet.microsoft.com/library/ee461004.aspx) Jeśli usługa nie jest uruchomiona, mogą zostać wyświetlenia następujących błędów:
   
   * ErrorCode.cisApplianceagentNotOnline
   * ErrorCode.CisPowershellScriptHcsError — oznacza to, że wystąpił wyjątek podczas uruchomienia Get-ClusterResource.
4. Sprawdź token usługi kontroli dostępu (ACS). Jeśli zgłosi wyjątek sieci Web, może to być wynikiem problemu z bramą, brakującego uwierzytelniania serwera proxy, niepoprawnego systemu DNS lub niepowodzenia uwierzytelniania. Mogą pojawić się następujące błędy:
   
   * ErrorCode.CiSApplianceGateway — oznacza wyjątek HttpStatusCode.BadGateway: usługa rozpoznawania nazw nie może rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceProxy — oznacza to wymagany wyjątek httpStatusCode.ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić się na serwerze proxy.
   * ErrorCode.CiSApplianceDNSError — oznacza to wyjątek WebExceptionStatus.NameResolutionFailure: usługa rozpoznawania nazw nie może rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceACSError — oznacza to, że usługa zwróciła błąd uwierzytelniania, ale istnieje łączność.
     
     Jeśli nie zgłasza wyjątek sieci web, sprawdź, czy ErrorCode.CiSApplianceFailure. Oznacza to, że urządzenie nie powiodło się.
5. Sprawdź łączność usługi w chmurze. Jeśli usługa zgłasza wyjątek sieci web, mogą pojawić się następujące błędy:
   
   * ErrorCode.CiSApplianceGateway — oznacza wyjątek HttpStatusCode.BadGateway: pośredni serwer proxy odebrał złe żądanie od innego serwera proxy lub z oryginalnego serwera.
   * ErrorCode.CiSApplianceProxy — oznacza to wymagany wyjątek httpStatusCode.ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić się na serwerze proxy.
   * ErrorCode.CiSApplianceDNSError — oznacza to wyjątek WebExceptionStatus.NameResolutionFailure: usługa rozpoznawania nazw nie może rozpoznać nazwy hosta.
   * ErrorCode.CiSApplianceACSError — oznacza to, że usługa zwróciła błąd uwierzytelniania, ale istnieje łączność.
     
     Jeśli nie zgłasza wyjątek sieci web, sprawdź, czy ErrorCode.CiSApplianceSaasServiceError. Oznacza to problem z usługą StorSimple Device Manager.
6. Sprawdź łączność usługi Azure Service Bus. ErrorCode.CiSApplianceServiceBusError wskazuje, że urządzenie nie może połączyć się z magistralą usług.

Pliki dziennika CiSCommandletLog0Curr.errlog i CiSAgentsvc0Curr.errlog będą miały więcej informacji, takich jak szczegóły wyjątku.

Aby uzyskać więcej informacji na temat korzystania z polecenia cmdlet, przejdź do [programu Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) w dokumentacji referencyjnej programu Windows PowerShell.

> [!IMPORTANT]
> Można uruchomić to polecenie cmdlet zarówno dla aktywnego, jak i pasywnego kontrolera.

Zobacz następujące przykłady danych `Test-HcsmConnection` wyjściowych z polecenia cmdlet.

**Przykładowe dane wyjściowe – pomyślnie zarejestrowane urządzenie z aktualizacją StorSimple 3**

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

**Przykładowe wyjście – urządzenie offline** 

Ten przykład pochodzi z urządzenia, które ma stan **offline** w witrynie Azure portal.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Urządzenie nie może połączyć się przy użyciu bieżącej konfiguracji serwera proxy sieci Web. Może to być problem z konfiguracją serwera proxy sieci web lub problem z łącznością sieciową. W takim przypadku należy upewnić się, że ustawienia serwera proxy sieci web są poprawne, a serwery proxy sieci Web są w trybie online i osiągalne.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Rozwiązywanie problemów z poleceniem cmdlet Sync-HcsTime
Użyj tego polecenia cmdlet, aby wyświetlić czas urządzenia. Jeśli czas urządzenia ma przesunięcie z serwerem NTP, można użyć tego polecenia cmdlet, aby wymusić synchronizację czasu z serwerem NTP.
- Jeśli przesunięcie między urządzeniem a serwerem NTP jest większe niż 5 minut, zostanie wyświetlone ostrzeżenie. 
- Jeśli przesunięcie przekracza 15 minut, urządzenie przejdzie do trybu offline. Nadal można użyć tego polecenia cmdlet, aby wymusić synchronizację czasu. 
- Jeśli jednak przesunięcie przekracza 15 godzin, nie będzie można wymusić synchronizacji czasu i zostanie wyświetlony komunikat o błędzie.

**Przykładowe wyjście – wymuszona synchronizacja czasu za pomocą Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Rozwiązywanie problemów za pomocą poleceń poleceń cmdlet Enable-HcsPing i Disable-HcsPing
Użyj tych poleceń cmdlet, aby upewnić się, że interfejsy sieciowe urządzenia reagują na żądania ping ICMP. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping. Korzystanie z tego polecenia cmdlet jest najprostszym sposobem, aby wiedzieć, czy urządzenie jest w trybie online i osiągalne.

**Dane wyjściowe próbki — Enable-HcsPing i Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Rozwiązywanie problemów z poleceniem cmdlet Trace-HcsRoute
Użyj tego polecenia cmdlet jako narzędzia do śledzenia trasy. Wysyła pakiety do każdego routera w drodze do miejsca docelowego w okresie czasu, a następnie oblicza wyniki na podstawie pakietów zwróconych z każdego przeskoku. Ponieważ polecenie cmdlet pokazuje stopień utraty pakietów w danym routerze lub łączu, można określić, które routery lub łącza mogą powodować problemy z siecią.

**Przykładowe dane wyjściowe pokazujące sposób śledzenia trasy pakietu za pomocą funkcji Trace-HcsRoute**

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

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Rozwiązywanie problemów z poleceniem cmdlet Get-HcsRoutingTable
Użyj tego polecenia cmdlet, aby wyświetlić tabelę routingu dla urządzenia StorSimple. Tabela routingu to zestaw reguł, które mogą pomóc w określeniu, gdzie będą kierowane pakiety danych przesyłane przez sieć protokołu internetowego (IP).

Tabela routingu pokazuje interfejsy i bramę, która kieruje dane do określonych sieci. Daje również metryki routingu, który jest twórcą decyzji dla ścieżki obranego, aby dotrzeć do określonego miejsca docelowego. Im niższa metryka routingu, tym wyższe preferencje.

Na przykład, jeśli masz 2 interfejsy sieciowe, DANE 2 i DATA 3, podłączone do Internetu. Jeśli metryki routingu dla danych 2 i DATA 3 są odpowiednio 15 i 261, wówczas data 2 z niższą metryką routingu jest preferowanym interfejsem używanym do dotarcia do Internetu.

Jeśli korzystasz z aktualizacji 1 na urządzeniu StorSimple, interfejs sieciowy DATA 0 ma najwyższe preferencje dla ruchu w chmurze. Oznacza to, że nawet jeśli istnieją inne interfejsy z obsługą chmury, ruch w chmurze będzie kierowany za pośrednictwem danych 0.

Jeśli uruchomisz `Get-HcsRoutingTable` polecenie cmdlet bez określania żadnych parametrów (jak pokazano w poniższym przykładzie), polecenie cmdlet spowoduje wyświetlenie tabel routingu IPv4 i IPv6. Alternatywnie można `Get-HcsRoutingTable -IPv4` określić `Get-HcsRoutingTable -IPv6` lub uzyskać odpowiednią tabelę routingu.

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

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Przykład rozwiązywania problemów z storsimple krok po kroku
W poniższym przykładzie przedstawiono rozwiązywanie problemów krok po kroku wdrożenia StorSimple. W przykładowym scenariuszu rejestracja urządzenia kończy się niepowodzeniem z komunikatem o błędzie wskazującym, że ustawienia sieciowe lub nazwa DNS są niepoprawne.

Zwrócony komunikat o błędzie to:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Błąd może być spowodowany przez dowolną z następujących czynności:

* Nieprawidłowa instalacja sprzętu
* Wadliwe interfejsy sieciowe
* Niepoprawny adres IP, maska podsieci, brama, podstawowy serwer DNS lub serwer proxy sieci Web
* Niepoprawny klucz rejestracji
* Nieprawidłowe ustawienia zapory

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Aby zlokalizować i rozwiązać problem z rejestracją urządzenia
1. Sprawdź konfigurację urządzenia: na aktywnym kontrolerze uruchom `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > Kreator instalacji musi działać na aktywnym kontrolerze. Aby sprawdzić, czy masz połączenie z aktywnym kontrolerem, spójrz na baner prezentowany w konsoli szeregowej. Baner wskazuje, czy masz połączenie z kontrolerem 0 czy kontrolerem 1 oraz czy kontroler jest aktywny czy pasywny. Aby uzyskać więcej informacji, przejdź do [strony Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Upewnij się, że urządzenie jest prawidłowo okablowane: sprawdź okablowanie sieciowe na tylnej płaszczyźnie urządzenia. Okablowanie jest specyficzne dla modelu urządzenia. Aby uzyskać więcej informacji, przejdź do [witryny Instalowanie urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [Instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Jeśli używasz portów sieciowych 10 GbE, musisz użyć dostarczonych adapterów QSFP-SFP i kabli SFP. Aby uzyskać więcej informacji, zobacz [listę kabli, przełączników i nadajników nadawczo-odbiorczych zalecanych dla portów 10 GbE.](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)
  
3. Sprawdź kondycję interfejsu sieciowego:
   
   * Polecenie cmdlet Get-NetAdapter służy do wykrywania kondycji interfejsów sieciowych dla danych 0. 
   * Jeśli łącze nie działa, stan **ifindex** wskaże, że interfejs jest wyłączony. Następnie należy sprawdzić połączenie sieciowe portu z urządzeniem i przełącznikiem. Trzeba będzie również wykluczyć złe kable. 
   * Jeśli podejrzewasz, że port DATA 0 na aktywnym kontrolerze nie powiódł się, możesz to potwierdzić, łącząc się z portem DATA 0 na kontrolerze 1. Aby to potwierdzić, odłącz kabel sieciowy od tylnej części urządzenia od kontrolera 0, podłącz kabel do kontrolera 1, a następnie ponownie uruchom polecenie cmdlet Get-NetAdapter.
     Jeśli port DATA 0 na kontrolerze nie powiedzie się, [skontaktuj się z pomocą techniczną firmy Microsoft, aby](storsimple-8000-contact-microsoft-support.md) uzyskać następujące kroki. Może być konieczna wymiana kontrolera w systemie.
4. Sprawdź łączność z przełącznikiem:
   
   * Upewnij się, że interfejsy sieciowe DATA 0 na kontrolerze 0 i kontrolerze 1 w obudowy podstawowej znajdują się w tej samej podsieci. 
   * Sprawdź koncentrator lub router. Zazwyczaj należy podłączyć oba kontrolery do tego samego koncentratora lub routera. 
   * Upewnij się, że przełączniki używane dla połączenia mają dane 0 dla obu kontrolerów w tej samej sieci vLAN.
5. Wyeliminuj wszelkie błędy użytkownika:
   
   * Uruchom kreatora konfiguracji ponownie (uruchom **Invoke-HcsSetupWizard)** i wprowadź wartości ponownie, aby upewnić się, że nie ma żadnych błędów. 
   * Sprawdź używany klucz rejestracji. Ten sam klucz rejestracji może służyć do łączenia wielu urządzeń z usługą StorSimple Device Manager. Użyj procedury w [Pobierz klucz rejestracji usługi,](storsimple-8000-manage-service.md#get-the-service-registration-key) aby upewnić się, że używasz poprawnego klucza rejestracji.
     
     > [!IMPORTANT]
     > Jeśli masz wiele uruchomionych usług, należy upewnić się, że klucz rejestracji dla odpowiedniej usługi jest używany do rejestracji urządzenia. Jeśli urządzenie zostało zarejestrowane w niewłaściwej usłudze StorSimple Device Manager, należy [skontaktować się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu uzyskania kolejnych kroków. Może być konieczne przywrócenie ustawień fabrycznych urządzenia (co może spowodować utratę danych), aby następnie podłączyć je do zamierzonej usługi.
     > 
     > 
6. Użyj polecenia cmdlet Test-Connection, aby sprawdzić, czy masz łączność z siecią zewnętrzną. Aby uzyskać więcej informacji, przejdź do [rozwiązywania problemów za pomocą polecenia cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Sprawdź, czy nie ma zakłóceń zapory. Jeśli użytkownik potwierdził, że ustawienia wirtualnego adresu IP (VIP), podsieci, bramy i systemu DNS są poprawne i nadal występują problemy z łącznością, możliwe jest, że zapora blokuje komunikację między urządzeniem a siecią zewnętrzną. Należy upewnić się, że porty 80 i 443 są dostępne na urządzeniu StorSimple do komunikacji wychodzącej. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące sieci urządzenia StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Spójrz na dzienniki. Przejdź do [pomocy technicznej pakietów i dzienników urządzeń dostępnych do rozwiązywania problemów](#support-packages-and-device-logs-available-for-troubleshooting).
9. Jeśli powyższe kroki nie rozwiążą problemu, [skontaktuj się z pomocą techniczną firmy Microsoft, aby](storsimple-8000-contact-microsoft-support.md) uzyskać pomoc.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak używać narzędzia Diagnostyka do rozwiązywania problemów z urządzeniem StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
