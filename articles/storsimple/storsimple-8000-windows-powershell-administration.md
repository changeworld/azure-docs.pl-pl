---
title: Program PowerShell do zarządzania urządzeniami StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Windows PowerShell dla usługi StorSimple do zarządzania urządzeniem StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7e488a1b2a63323361a597aaa6a438630fc09a05
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621632"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Użyj programu Windows PowerShell dla usługi StorSimple do administrowania urządzeniem

## <a name="overview"></a>Omówienie

Windows PowerShell dla magazynu StorSimple udostępnia interfejs wiersza polecenia używanej do zarządzania urządzeniem, Microsoft Azure StorSimple. Jak sugeruje nazwa, jest oparte na programie Windows PowerShell, interfejsu wiersza polecenia, która jest wbudowana w ograniczonego obszaru działania. Z punktu widzenia użytkownika w wierszu polecenia ograniczonego obszaru działania zostanie wyświetlony jako ograniczona wersja programu Windows PowerShell. Przy zachowaniu niektórych podstawowych funkcji programu Windows PowerShell, ten interfejs ma dodatkowe dedykowanych poleceń cmdlet, które są przeznaczone dla zarządzania urządzeniem Microsoft Azure StorSimple.

Ten artykuł zawiera opis programu Windows PowerShell dla funkcji usługi StorSimple, w tym, jak połączyć do tego interfejsu i zawiera łącza do procedur krok po kroku lub przepływów pracy, które można wykonać przy użyciu tego interfejsu. Przepływy pracy zawierają jak zarejestrować urządzenie, skonfiguruj interfejs sieciowy na urządzeniu, zainstaluj aktualizacje, które urządzenie musi być w trybie konserwacji, zmiany stanu urządzenia i rozwiązać wszelkie napotkane problemy, które mogą wystąpić.

Po przeczytaniu tego artykułu, będą mieć możliwość:

* Łączenie z urządzeniem StorSimple przy użyciu programu Windows PowerShell dla usługi StorSimple.
* Administrowanie urządzeniem StorSimple przy użyciu programu Windows PowerShell dla usługi StorSimple.
* Uzyskaj pomoc w programie Windows PowerShell dla usługi StorSimple.

> [!NOTE]
> * Programu Windows PowerShell dla poleceń cmdlet usługi StorSimple pozwala na zarządzanie urządzeniem StorSimple z poziomu konsoli szeregowej, lub zdalnie przy użyciu komunikacji zdalnej programu Windows PowerShell. Aby uzyskać więcej informacji na temat każdego z poszczególnych poleceń cmdlet, które mogą być używane w tym interfejsie, przejdź do [Dokumentacja poleceń cmdlet dla programu Windows PowerShell dla usługi StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Polecenia cmdlet programu Azure PowerShell StorSimple są inną kolekcję poleceń cmdlet, dzięki czemu umożliwia automatyzowanie poziomu usługi StorSimple oraz zadania migracji w wierszu polecenia. Aby uzyskać więcej informacji o poleceniach cmdlet programu Azure PowerShell dla usługi StorSimple, przejdź do [informacje o poleceniach cmdlet usługi Azure StorSimple](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Aby uzyskać dostęp programu Windows PowerShell dla usługi StorSimple przy użyciu jednej z następujących metod:

* [Łączenie z konsolą szeregową urządzenia StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Połącz się zdalnie StorSimple przy użyciu programu Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Łączenie do programu Windows PowerShell dla usługi StorSimple za pośrednictwem konsoli szeregowej urządzenia

Możesz [Pobierz program PuTTY](https://www.putty.org/) lub podobne oprogramowania do emulacji terminala połączyć się z programu Windows PowerShell dla usługi StorSimple. Należy skonfigurować program PuTTY specjalnie w celu uzyskania dostępu do urządzenia StorSimple systemu Azure firmy Microsoft. Poniższe tematy zawierają szczegółowe instrukcje dotyczące sposobu konfigurowania programu PuTTy i połączyć się z urządzeniem. Ponadto opisano różne opcje menu w konsoli szeregowej.

### <a name="putty-settings"></a>Ustawienia programu PuTTY

Upewnij się, użyj następujących ustawień programu PuTTY nawiązać połączenia z interfejsu programu Windows PowerShell z konsoli szeregowej.

#### <a name="to-configure-putty"></a>Aby skonfigurować program PuTTY

1. W programie PuTTY **ponownej konfiguracji** dialogowym **kategorii** okienku wybierz **klawiatury**.
2. Upewnij się, że wybrane są następujące opcje (są to domyślne ustawienia w przypadku rozpoczęcia nowej sesji).
   
   | Element klawiatury | Wybierz |
   | --- | --- |
   | Klawisz BACKSPACE |Kontrolka-? (127) |
   | Strona główna i na końcu kluczy |Standardowa (Standard) |
   | Funkcja klucze i klawiatury numerycznej |ESC [n ~ |
   | Początkowy stan klawisze kursora |Normalne |
   | Początkowy stan numerycznej |Normalne |
   | Włączanie funkcji dodatkowej klawiatury |Kontrolka Alt różni się od AltGr |
   
    ![Obsługiwane ustawienia programu Putty](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kliknij przycisk **zastosować**.
4. W **kategorii** okienku wybierz **tłumaczenia**.
5. W **zestaw znaków zdalnego** pola listy, wybierz opcję **UTF-8**.
6. W obszarze **obsługi znaków Rysowanie linii**, wybierz opcję **punkty kodowe Rysowanie linii Unicode użyj**. Poniższy zrzut ekranu przedstawia poprawny wybór programu PuTTY.
   
    ![Ustawienia programu Putty UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kliknij przycisk **zastosować**.

Przy użyciu programu PuTTY można teraz nawiązać połączenie z konsolą szeregową urządzenia, wykonując następujące kroki.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Temat konsoli szeregowej

Gdy uzyskujesz dostęp do programu PowerShell Windows, zostanie wyświetlony interfejs urządzenia StorSimple za pośrednictwem konsoli szeregowej komunikat transparentu, a następnie opcje menu.

Komunikat transparentu zawiera podstawowe informacje o urządzeniu StorSimple takich jak model, nazwę, wersję zainstalowanego oprogramowania i stan kontrolera, które uzyskują dostęp do. Na poniższej ilustracji przedstawiono przykładowy komunikat transparentu.

![Komunikat transparentu szeregowej](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Komunikat transparentu umożliwia ustalić, czy kontroler nawiązano _Active_ lub _pasywnym_.

Na poniższej ilustracji przedstawiono różne opcje obszar działania, które są dostępne w menu konsoli szeregowej.

![Zarejestruj urządzenie 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Możesz wybrać następujące ustawienia:

1. **Zaloguj się przy użyciu pełnego dostępu** ta opcja umożliwia łączenie się z (z właściwymi poświadczeniami) **SSAdminConsole** obszarem działania na lokalnym kontrolerze. (Lokalnego kontrolera jest kontroler, który obecnie uzyskują dostęp do za pośrednictwem konsoli szeregowej urządzenia StorSimple). Tę opcję, można również zezwolić Support firmy Microsoft można uzyskać dostęp bez ograniczeń obszarem działania (sesję pomocy technicznej) rozwiązanie wszelkich problemów dostępnego urządzenia. Zaloguj się przy użyciu opcji 1, możesz zezwolić inżynier firmy Microsoft Support, aby uzyskać dostęp bez ograniczeń obszarem działania, uruchamiając konkretnego polecenia cmdlet. Aby uzyskać szczegółowe informacje, zapoznaj się [rozpocząć sesję pomocy technicznej](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Zaloguj się do kontrolera elementu równorzędnego z pełnym dostępem** ta opcja jest taka sama jak opcja 1, z tą różnicą, że można nawiązać połączenie (z właściwymi poświadczeniami) **SSAdminConsole** obszarem działania na kontrolerze elementów równorzędnych. Ponieważ urządzenie StorSimple jest wysokiej dostępności przy użyciu dwóch kontrolerów w konfiguracji aktywny / pasywny, równorzędnej odnosi się do innego kontrolera na urządzeniu, które chcesz uzyskać dostęp za pośrednictwem konsoli szeregowej).
   Podobnie jak opcja 1, ta opcja może również umożliwia Microsoft Support dostęp bez ograniczeń obszarem działania na kontrolerze elementu równorzędnego do.

3. **Łączenie z ograniczonym dostępem** ta opcja umożliwia dostęp do interfejsu programu Windows PowerShell w trybie ograniczonej. Nie monit o poświadczenia dostępu. Ta opcja umożliwia nawiązywanie bardziej ograniczonym obszarem działania w porównaniu do opcji 1 i 2.  Niektóre zadania, które są dostępne za pośrednictwem opcji 1, **nie* można wykonać w tym obszarze działania są:
   
   * Resetowanie do ustawień fabrycznych
   * Zmień hasło
   * Włącz lub wyłącz dostęp do pomocy technicznej
   * Stosowanie aktualizacji
   * Instalowanie poprawek

     > [!NOTE]
     > Jest to preferowaną opcję, jeśli zapomniane hasło administratora urządzenia i nie można nawiązać połączenia przy użyciu opcji 1 lub 2.

4. **Zmień język** ta opcja umożliwia zmianę języka wyświetlania w interfejsie programu Windows PowerShell. Obsługiwane języki są angielski, japoński, rosyjski, francuski, Południowa —, hiszpański, włoski, niemiecki, chiński i portugalski (Brazylia).

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Połącz się zdalnie StorSimple przy użyciu programu Windows PowerShell dla usługi StorSimple

Komunikacji zdalnej programu Windows PowerShell umożliwia łączenie z urządzeniem StorSimple. Po nawiązaniu połączenia w ten sposób, nie będą widzieć menu. (Zobaczysz menu tylko wtedy, gdy używasz konsoli szeregowej urządzenia do łączenia. Połączenie zdalne przejście bezpośrednio do wielokrotność "opcja 1 — pełny dostęp" na konsoli szeregowej.) Za pomocą komunikacji zdalnej programu Windows PowerShell połączenie z określonym obszarem działania. Można również określić język wyświetlania.

Język wyświetlania jest niezależny od języka, który należy określić przy użyciu **Zmień język** opcji w menu konsoli szeregowej. Ustawienia regionalne urządzenia nawiązujesz połączenie z, jeśli nie zostanie określona automatycznie przejmą zdalnego programu PowerShell.

> [!NOTE]
> Jeśli pracujesz z hostów wirtualnych Microsoft Azure i urządzeń StorSimple w chmurze, można użyć komunikacji zdalnej programu Windows PowerShell i host wirtualny nawiązać połączenia z urządzeniem w chmurze. Jeśli w lokalizacji udziału na host, na którym ma zostać zapisany informacje z sesji środowiska Windows PowerShell, należy pamiętać, że _wszyscy_ jednostki zawiera tylko uwierzytelnionym użytkownikom. W związku z tym jeśli po skonfigurowaniu tego udziału można zezwolić na dostęp przez _wszyscy_ możesz połączyć się bez podawania poświadczeń, nieuwierzytelnione jednostki anonimowego będzie służyć i zostanie wyświetlony błąd. Aby rozwiązać ten problem, w udziale hoście należy włączyć konta gościa i następnie nadaj gościa pełny dostęp do udziału lub należy określić prawidłowe poświadczenia wraz z polecenia cmdlet programu Windows PowerShell.


Do łączenia za pomocą komunikacji zdalnej programu Windows PowerShell, można użyć protokołu HTTP lub HTTPS. Postępuj zgodnie z instrukcjami w ramach następujących samouczków:

* [Nawiązywanie połączenia zdalnego przy użyciu protokołu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Nawiązywanie połączenia zdalnego przy użyciu protokołu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Zagadnienia dotyczące zabezpieczeń połączenia

Wybierając sposób nawiązywania połączeń w programie Windows PowerShell dla usługi StorSimple, należy rozważyć następujące kwestie:

* Bezpośredniego połączenia z konsolą szeregową urządzenia są bezpieczne, ale nie jest połączenie z konsolą szeregową za pośrednictwem przełączników sieciowych. Należy zachować ostrożność zagrożenie bezpieczeństwa, podczas nawiązywania połączenia szeregowego urządzenia za pośrednictwem przełączników sieciowych.
* Łącząc się za pośrednictwem sesji HTTP może zaoferować lepsze zabezpieczenia niż łączenie za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest najbezpieczniejszą metodą, jest dopuszczalne w sieciach zaufanych.
* Łącząc się za pośrednictwem sesji protokołu HTTPS jest najbezpieczniejsza i zalecana opcja.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrowanie urządzeniem StorSimple przy użyciu programu Windows PowerShell dla usługi StorSimple

W poniższej tabeli przedstawiono podsumowanie typowych zadań zarządzania i złożonych przepływów pracy, które mogą być wykonywane w ramach interfejsu programu Windows PowerShell urządzenia StorSimple. Aby uzyskać więcej informacji na temat każdego przepływu pracy kliknij odpowiedni wpis w tabeli.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Program Windows PowerShell dla przepływów pracy usługi StorSimple

| Jeśli chcesz to zrobić... | Użyj tej procedury. |
| --- | --- |
| Zarejestruj urządzenie |[Konfigurowanie i rejestrowanie urządzenia za pomocą programu Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurowanie serwera proxy sieci Web</br>Wyświetl ustawienia serwera proxy sieci web |[Konfigurowanie serwera proxy sieci web dla urządzenia StorSimple](storsimple-8000-configure-web-proxy.md) |
| Zmodyfikuj ustawienia interfejsu sieciowego 0 danych na urządzeniu z systemem |[Modyfikowanie interfejs sieciowy DATA 0 dla urządzenia StorSimple](storsimple-8000-modify-data-0.md) |
| Zatrzymaj kontrolera </br> Ponowne uruchamianie lub zamykanie kontrolera </br> Zamknij urządzenie</br>Resetowanie urządzenia do domyślnych ustawień fabrycznych |[Zarządzaj kontrolerami urządzenia](storsimple-8000-manage-device-controller.md) |
| Zainstaluj aktualizacje trybu konserwacji i poprawki |[Aktualizowanie urządzenia](storsimple-update-device.md) |
| Przejście do trybu konserwacji </br>Wyjdź z trybu konserwacji |[Tryby urządzenia StorSimple](storsimple-8000-device-modes.md) |
| Tworzenie pakietu pomocy technicznej</br>Odszyfrowywanie i edytować pakietu dla pomocy technicznej |[Tworzenie i zarządzanie nimi pakietu dla pomocy technicznej](storsimple-8000-create-manage-support-package.md) |
| Rozpocząć sesję pomocy technicznej</br> |[Rozpocząć sesję pomocy technicznej w programie Windows PowerShell dla usługi StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Uzyskaj pomoc w programie Windows PowerShell dla usługi StorSimple

W programie Windows PowerShell dla usługi StorSimple pomocy polecenia cmdlet jest dostępna. Online, aktualnych wersji Pomocy jest również dostępna, którego można użyć, aby zaktualizować pomoc w Twoim systemie.

Uzyskiwanie pomocy w ten interfejs jest podobny, jak w programie Windows PowerShell, a większość poleceń cmdlet związanych będą działać. Pomoc dla Windows PowerShell można znaleźć w tryb online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Poniżej znajduje się krótki opis typów pomocy dla tego interfejsu programu Windows PowerShell, w tym jak dokonać aktualizacji w Pomocy.

### <a name="to-get-help-for-a-cmdlet"></a>Aby uzyskać pomoc dotyczącą polecenia cmdlet

* Aby uzyskać pomoc dotyczącą polecenia cmdlet dowolnej funkcji, użyj następującego polecenia: `Get-Help <cmdlet-name>`
* Aby uzyskać pomoc online dla każdego polecenia cmdlet, należy użyć poprzedniego polecenia cmdlet z `-Online` parametru: `Get-Help <cmdlet-name> -Online`
* Aby uzyskać pełną pomoc, można użyć `–Full` parametru i przykłady, użyj `–Examples` parametru.

### <a name="to-update-help"></a>Aby zaktualizować pomoc

Pomoc w interfejsie programu Windows PowerShell można łatwo zaktualizować. Wykonaj poniższe kroki, aby zaktualizować pomoc w Twoim systemie.

#### <a name="to-update-cmdlet-help"></a>Aby zaktualizować pomocy polecenia cmdlet
1. Uruchom program Windows PowerShell z **Uruchom jako administrator** opcji.
2. W wierszu polecenia wpisz polecenie:  `Update-Help`
3. Zaktualizowane pliki pomocy zostanie zainstalowana.
4. Po zainstalowaniu plików pomocy, wpisz: `Get-Help Get-Command`. Spowoduje to wyświetlenie listy poleceń cmdlet, dla których jest dostępna Pomoc.

> [!NOTE]
> Aby uzyskać listę wszystkich dostępnych poleceń cmdlet w obszarze działania, zaloguj się do odpowiedniej opcji menu, a następnie uruchom `Get-Command` polecenia cmdlet.


## <a name="next-steps"></a>Kolejne kroki

Jeśli napotkasz jakiekolwiek problemy z urządzeniem StorSimple, wykonując jedną z powyższych przepływów pracy, zapoznaj się [narzędzia do rozwiązywania problemów z wdrożeniami usługi StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

