---
title: Zarządzanie urządzeniami w programie PowerShell for StorSimple
description: Dowiedz się, jak zarządzać urządzeniem StorSimple za pomocą programu Windows PowerShell for StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7d59f00d655bc7b2395c46713a56f52c61ffa42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277105"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Używanie programu Windows PowerShell dla usługi StorSimple do administrowania urządzeniem

## <a name="overview"></a>Omówienie

Program Windows PowerShell for StorSimple udostępnia interfejs wiersza polecenia, którego można używać do zarządzania urządzeniem Microsoft Azure StorSimple. Jak sama nazwa wskazuje, jest to oparty na programie Windows PowerShell interfejs wiersza polecenia, który jest zbudowany w ograniczonej przestrzeni runspace. Z punktu widzenia użytkownika w wierszu polecenia ograniczony obszar runspace jest wyświetlany jako ograniczona wersja programu Windows PowerShell. Zachowując niektóre z podstawowych możliwości programu Windows PowerShell, ten interfejs ma dodatkowe dedykowane polecenia cmdlet, które są ukierunkowane na zarządzanie urządzeniem Microsoft Azure StorSimple.

W tym artykule opisano funkcje programu Windows PowerShell for StorSimple, w tym sposób łączenia się z tym interfejsem, oraz łącza do procedur krok po kroku lub przepływów pracy, które można wykonać za pomocą tego interfejsu. Przepływy pracy obejmują sposób rejestrowania urządzenia, konfigurowania interfejsu sieciowego na urządzeniu, instalowania aktualizacji, które wymagają, aby urządzenie było w trybie konserwacji, zmiany stanu urządzenia i rozwiązywania problemów, które mogą wystąpić.

Po przeczytaniu tego artykułu, będziesz mógł:

* Połącz się z urządzeniem StorSimple przy użyciu programu Windows PowerShell for StorSimple.
* Administrowanie urządzeniem StorSimple przy użyciu programu Windows PowerShell for StorSimple.
* Uzyskaj pomoc w programie Windows PowerShell for StorSimple.

> [!NOTE]
> * Polecenia cmdlet programu Windows PowerShell for StorSimple umożliwiają zarządzanie urządzeniem StorSimple z konsoli szeregowej lub zdalnie za pośrednictwem komunikacji zdalnej programu Windows PowerShell. Aby uzyskać więcej informacji na temat poszczególnych poleceń cmdlet, które mogą być używane w tym interfejsie, przejdź do [odwołania polecenia cmdlet dla programu Windows PowerShell dla StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Polecenia cmdlet usługi Azure PowerShell StorSimple to inna kolekcja poleceń cmdlet, które umożliwiają automatyzację zadań na poziomie usługi i migracji StorSimple z wiersza polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet programu Azure PowerShell dla StorSimple, przejdź do [odwołania do polecenia cmdlet usługi Azure StorSimple.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure)


Dostęp do programu Windows PowerShell for StorSimple można uzyskać przy użyciu jednej z następujących metod:

* [Połącz się z konsolą szeregową urządzenia StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Zdalne łączenie się z usługą StorSimple przy użyciu programu Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Łączenie się z programem Windows PowerShell for StorSimple za pośrednictwem konsoli szeregowej urządzenia

Możesz pobrać oprogramowanie do emulacji [putty](https://www.putty.org/) lub podobnej emulacji terminali, aby połączyć się z programem Windows PowerShell for StorSimple. Musisz skonfigurować PuTTY specjalnie, aby uzyskać dostęp do urządzenia Microsoft Azure StorSimple. Poniższe tematy zawierają szczegółowe kroki dotyczące konfigurowania PuTTy i łączenia się z urządzeniem. Wyjaśniono również różne opcje menu w konsoli szeregowej.

### <a name="putty-settings"></a>Ustawienia programu PuTTY

Upewnij się, że używasz następujących ustawień PuTTY, aby połączyć się z interfejsem programu Windows PowerShell z konsoli szeregowej.

#### <a name="to-configure-putty"></a>Aby skonfigurować putty

1. W oknie dialogowym **Rekonfiguracja** PuTTY w okienku **Kategoria** wybierz pozycję **Klawiatura**.
2. Upewnij się, że są zaznaczone następujące opcje (są to ustawienia domyślne podczas rozpoczynania nowej sesji).
   
   | Element klawiatury | Wybierz pozycję |
   | --- | --- |
   | Backspace |Kontrola? (127) |
   | Klucze do domu i końca |Standardowa |
   | Klawisze funkcyjne i klawiatura |ESC[n~ |
   | Początkowy stan klawiszy kursora |Normalne |
   | Początkowy stan klawiatury numerycznej |Normalne |
   | Włączanie dodatkowych funkcji klawiatury |Control-Alt różni się od AltGr |
   
    ![Obsługiwane ustawienia kitu](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kliknij przycisk **Zastosuj**.
4. W okienku **Kategoria** wybierz pozycję **Tłumaczenie**.
5. W polu listy **Zestaw znaków zdalny** wybierz pozycję **UTF-8**.
6. W obszarze **Obsługa znaków rysowania linii**wybierz pozycję Użyj punktów kodu **rysunku linii Unicode**. Poniższy zrzut ekranu przedstawia poprawne wybory PuTTY.
   
    ![Ustawienia kitu UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kliknij przycisk **Zastosuj**.

Teraz można użyć PuTTY, aby połączyć się z konsoli szeregowej urządzenia, wykonując następujące kroki.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Informacje o konsoli szeregowej

Po dołączeniu do interfejsu programu Windows PowerShell urządzenia StorSimple za pośrednictwem konsoli szeregowej wyświetlany jest komunikat banera, a następnie opcje menu.

Komunikat banera zawiera podstawowe informacje o urządzeniu StorSimple, takie jak model, nazwa, zainstalowana wersja oprogramowania i stan kontrolera, do którego uzyskujesz dostęp. Na poniższej ilustracji przedstawiono przykład wiadomości banerowej.

![Seryjny komunikat banera](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Za pomocą transparentu można określić, czy kontroler, z którego masz połączenie, jest _aktywny_ czy _pasywny._

Na poniższej ilustracji przedstawiono różne opcje obszaru uruchomień, które są dostępne w menu konsoli szeregowej.

![Zarejestruj swoje urządzenie 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Możesz wybrać jeden z następujących ustawień:

1. **Zaloguj się z pełnym dostępem** Ta opcja umożliwia połączenie (przy użyciu odpowiednich poświadczeń) z obszarem uruchomień **SSAdminConsole** na kontrolerze lokalnym. (Kontroler lokalny jest kontrolerem, do którego aktualnie uzyskujesz dostęp za pośrednictwem konsoli szeregowej urządzenia StorSimple). Ta opcja może również służyć do umożliwienia pomocy technicznej firmy Microsoft dostępu do nieograniczonego obszaru runspace (sesji pomocy technicznej) w celu rozwiązania wszelkich możliwych problemów z urządzeniem. Po użyciu opcji 1 do logowania można zezwolić inżynierowi pomocy technicznej firmy Microsoft na dostęp do nieograniczonego obszaru uruchomień, uruchamiając określone polecenie cmdlet. Szczegółowe informacje można znaleźć w [1.](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
   
2. **Zaloguj się do kontrolera równorzędnego z pełnym dostępem** Ta opcja jest taka sama jak opcja 1, z tą różnicą, że można połączyć (z odpowiednimi poświadczeniami) do obszaru runspace **SSAdminConsole** na kontrolerze równorzędnym. Ponieważ urządzenie StorSimple jest urządzeniem o wysokiej dostępności z dwoma kontrolerami w konfiguracji aktywny-pasywny, peer odwołuje się do innego kontrolera w urządzeniu, do którego uzyskujesz dostęp za pośrednictwem konsoli szeregowej).
   Podobnie jak w przypadku opcji 1, ta opcja może również służyć do umożliwienia pomocy technicznej firmy Microsoft dostępu do nieograniczonego obszaru runspace na kontrolerze równorzędnym.

3. **Połącz się z ograniczonym dostępem** Ta opcja służy do uzyskiwania dostępu do interfejsu programu Windows PowerShell w trybie ograniczonym. Nie jest wyświetlany monit o podanie poświadczeń dostępu. Ta opcja łączy się z bardziej ograniczonym obszarem startowym w porównaniu z opcjami 1 i 2.  Niektóre zadania, które są dostępne za pośrednictwem opcji 1, które **nie mogą* być wykonywane w tym obszarze runspace są:
   
   * Resetowanie ustawień fabrycznych
   * Zmienianie hasła
   * Włączanie lub wyłączanie dostępu do pomocy technicznej
   * Stosowanie aktualizacji
   * Instalowanie poprawek

     > [!NOTE]
     > Jest to preferowana opcja, jeśli nie pamiętasz hasła administratora urządzenia i nie możesz połączyć się za pomocą opcji 1 lub 2.

4. **Zmienianie języka** Ta opcja umożliwia zmianę języka wyświetlania w interfejsie programu Windows PowerShell. Obsługiwane języki to angielski, japoński, rosyjski, francuski, południowokoreański, hiszpański, włoski, niemiecki, chiński i brazylijski portugalski.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Łączenie się zdalnie z storsimple przy użyciu programu Windows PowerShell for StorSimple

Komunikacji zdalnej programu Windows PowerShell można używać do łączenia się z urządzeniem StorSimple. Po podłączeniu w ten sposób nie zobaczysz menu. (Menu jest wyświetlane tylko wtedy, gdy do nawiązania połączenia jest używana konsola szeregowa na urządzeniu. Zdalne podłączanie prowadzi bezpośrednio do odpowiednika "opcja 1 – pełny dostęp" na konsoli szeregowej.) W programie Windows PowerShell komunikacji zdalnej można połączyć się z określonym obszarem run. Można również określić język wyświetlania.

Język wyświetlania jest niezależny od języka ustawionego za pomocą opcji **Zmień język** w menu konsoli szeregowej. Remote PowerShell automatycznie odbierze ustawienia regionalne urządzenia, z którego nawiązujesz połączenie, jeśli nie zostanie określony.

> [!NOTE]
> Jeśli pracujesz z wirtualnymi hostami platformy Microsoft Azure i urządzeniami storsimple w chmurze, możesz użyć komunikacji zdalnej programu Windows PowerShell i hosta wirtualnego, aby połączyć się z urządzeniem w chmurze. Jeśli na hoście skonfigurowana jest lokalizacja udziału, na której mają być zapisywane informacje z sesji programu Windows PowerShell, należy pamiętać, że podmiot zabezpieczeń _Wszyscy_ zawiera tylko uwierzytelnionych użytkowników. W związku z tym jeśli skonfigurowano udział, aby umożliwić dostęp przez _wszystkich_ i połączyć bez określania poświadczeń, nieuwierzytyna anonimowy podmiot zabezpieczeń będzie używany i pojawi się błąd. Aby rozwiązać ten problem, na hoście udziału należy włączyć konto gość, a następnie dać kontu gościa pełny dostęp do udziału lub należy określić prawidłowe poświadczenia wraz z poleceniem cmdlet programu Windows PowerShell.


Za pomocą protokołu HTTP lub HTTPS można łączyć się za pośrednictwem komunikacji zdalnej programu Windows PowerShell. Skorzystaj z instrukcji w następujących samouczkach:

* [Łączenie zdalne przy użyciu protokołu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Łączenie zdalne przy użyciu protokołu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Zagadnienia dotyczące zabezpieczeń połączeń

Podejmując decyzję o nawiązaniu połączenia z programem Windows PowerShell for StorSimple, należy wziąć pod uwagę następujące kwestie:

* Podłączanie bezpośrednio do konsoli szeregowej urządzenia jest bezpieczne, ale łączenie się z konsolą szeregową za pośrednictwem przełączników sieciowych nie jest. Należy zachować ostrożność podczas łączenia się z urządzeniem szeregowym za pośrednictwem przełączników sieciowych.
* Łączenie się za pośrednictwem sesji HTTP może zapewnić większe bezpieczeństwo niż łączenie się za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest to najbezpieczniejsza metoda, jest dopuszczalna w zaufanych sieciach.
* Łączenie się za pośrednictwem sesji HTTPS jest najbezpieczniejszą i zalecaną opcją.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrowanie urządzeniem StorSimple przy użyciu programu Windows PowerShell for StorSimple

W poniższej tabeli przedstawiono podsumowanie wszystkich typowych zadań zarządzania i złożonych przepływów pracy, które można wykonać w interfejsie programu Windows PowerShell urządzenia StorSimple. Aby uzyskać więcej informacji o każdym przepływie pracy, kliknij odpowiedni wpis w tabeli.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Przepływy pracy programu Windows PowerShell for StorSimple

| Jeśli chcesz to zrobić ... | Użyj tej procedury. |
| --- | --- |
| Rejestrowanie własnego urządzenia |[Konfigurowanie i rejestrowanie urządzenia przy użyciu programu Windows PowerShell for StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurowanie serwera proxy sieci Web</br>Wyświetlanie ustawień serwera proxy w sieci Web |[Konfigurowanie serwera proxy sieci Web dla urządzenia StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modyfikowanie ustawień interfejsu sieciowego DATA 0 na urządzeniu |[Modyfikowanie interfejsu sieciowego DATA 0 dla urządzenia StorSimple](storsimple-8000-modify-data-0.md) |
| Zatrzymywać kontroler </br> Ponowne uruchamianie lub zamykanie kontrolera </br> Zamykanie urządzenia</br>Resetowanie urządzenia do domyślnych ustawień fabrycznych |[Zarządzanie kontrolerami urządzeń](storsimple-8000-manage-device-controller.md) |
| Instalowanie aktualizacji trybu konserwacji i poprawek |[Aktualizowanie urządzenia](storsimple-update-device.md) |
| Wejdź w tryb konserwacji </br>Wyjście z trybu konserwacji |[Tryby urządzenia StorSimple](storsimple-8000-device-modes.md) |
| Tworzenie pakietu pomocy technicznej</br>Odszyfrowywanie i edytowanie pakietu pomocy technicznej |[Tworzenie pakietu pomocy technicznej i zarządzanie nim](storsimple-8000-create-manage-support-package.md) |
| Rozpoczynanie sesji pomocy technicznej</br> |[Rozpoczynanie sesji pomocy technicznej w programie Windows PowerShell dla storsimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Uzyskaj pomoc w programie Windows PowerShell dla storsimple

W programie Windows PowerShell for StorSimple dostępna jest Pomoc polecenia cmdlet. Dostępna jest również aktualna wersja tej Pomocy w trybie online, za pomocą której można zaktualizować Pomoc w systemie.

Uzyskiwanie Pomocy w tym interfejsie jest podobne do tej w programie Windows PowerShell, a większość poleceń cmdlet związanych z Pomocą będzie działać. Pomoc dotyczącą programu Windows PowerShell w trybie online można znaleźć w trybie online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Poniżej znajduje się krótki opis typów Pomocy dla tego interfejsu programu Windows PowerShell, w tym jak zaktualizować Pomoc.

### <a name="to-get-help-for-a-cmdlet"></a>Aby uzyskać pomoc dotyczącą polecenia cmdlet

* Aby uzyskać Pomoc dotyczącą dowolnego polecenia cmdlet lub funkcji, użyj następującego polecenia:`Get-Help <cmdlet-name>`
* Aby uzyskać Pomoc online dla dowolnego polecenia cmdlet, `-Online` użyj poprzedniego polecenia cmdlet z parametrem:`Get-Help <cmdlet-name> -Online`
* Aby uzyskać pełną Pomoc, `–Full` można użyć parametru, `–Examples` a na przykład użyć parametru.

### <a name="to-update-help"></a>Aby zaktualizować Pomoc

Pomoc można łatwo zaktualizować w interfejsie programu Windows PowerShell. Wykonaj następujące kroki, aby zaktualizować Pomoc w systemie.

#### <a name="to-update-cmdlet-help"></a>Aby zaktualizować Pomoc polecenia cmdlet
1. Uruchom program Windows PowerShell z opcją **Uruchom jako administrator.**
2. W wierszu polecenia wpisz:`Update-Help`
3. Zostaną zainstalowane zaktualizowane pliki Pomocy.
4. Po zainstalowaniu plików Pomocy `Get-Help Get-Command`wpisz: . Spowoduje to wyświetlenie listy poleceń cmdlet, dla których Pomoc jest dostępna.

> [!NOTE]
> Aby uzyskać listę wszystkich dostępnych poleceń cmdlet w obszarze uruchomień, zaloguj `Get-Command` się do odpowiedniej opcji menu i uruchom polecenie cmdlet.


## <a name="next-steps"></a>Następne kroki

Jeśli podczas wykonywania jednego z powyższych przepływów pracy wystąpią jakiekolwiek problemy z urządzeniem StorSimple, zapoznaj się z witryną Narzędzia do rozwiązywania problemów z [wdrożeniami StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

