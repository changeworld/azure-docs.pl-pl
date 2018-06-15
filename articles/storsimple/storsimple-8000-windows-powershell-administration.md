---
title: PowerShell do zarządzania urządzeniami StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Windows PowerShell dla StorSimple do zarządzania urządzeniem StorSimple.
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
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 291de21d3d98bf9eb65adc7d506f3549171ee0a0
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743727"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Administrowanie urządzenia za pomocą programu Windows PowerShell dla urządzenia StorSimple

## <a name="overview"></a>Przegląd

Program Windows PowerShell dla StorSimple udostępnia interfejs wiersza polecenia, który służy do zarządzania urządzeniem Microsoft Azure StorSimple. Jak wynika z nazwy, jest oparty na programie Windows PowerShell, interfejsu wiersza polecenia, który korzysta z wbudowanej w ograniczonego obszaru działania. Z perspektywy użytkownika w wierszu polecenia ograniczonego obszaru działania jest wyświetlany jako ograniczona wersja programu Windows PowerShell. Przy zachowaniu niektóre podstawowe funkcje programu Windows PowerShell, ten interfejs ma dodatkowe dedykowanych poleceń cmdlet, które są przeznaczone dla zarządzania urządzenia Microsoft Azure StorSimple.

W tym artykule opisano programu Windows PowerShell dla funkcji StorSimple, w tym, jak można łączyć do tego interfejsu oraz zawiera łącza do procedury krok po kroku lub przepływów pracy, które można wykonywać przy użyciu tego interfejsu. Przepływy pracy zawierają jak zarejestrować urządzenie, skonfiguruj interfejs sieciowy na urządzeniu, zainstaluj aktualizacje, które urządzenie musi być w trybie konserwacji, zmiany stanu urządzenia i rozwiązać wszelkie napotkane problemy, które mogą wystąpić.

Po przeczytaniu tego artykułu, będą mieć możliwość:

* Połączenie do urządzenia StorSimple przy użyciu programu Windows PowerShell dla urządzenia StorSimple.
* Administrowanie urządzenia StorSimple przy użyciu programu Windows PowerShell dla urządzenia StorSimple.
* Uzyskaj pomoc w programie Windows PowerShell dla StorSimple.

> [!NOTE]
> * Programu Windows PowerShell dla StorSimple poleceń cmdlet pozwala na zarządzanie urządzeniem StorSimple z konsoli szeregowej lub zdalnie za pośrednictwem komunikacji zdalnej programu Windows PowerShell. Aby uzyskać więcej informacji na temat każdego polecenia cmdlet, które mogą być używane w tym interfejsie, przejdź do [referencyjnej poleceń cmdlet środowiska Windows PowerShell dla urządzenia StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Polecenia cmdlet programu Azure PowerShell StorSimple są inną kolekcję poleceń cmdlet, który zezwala na automatyczny StorSimple poziomu usługi i zadania migracji z wiersza polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet programu Azure PowerShell dla urządzenia StorSimple, przejdź do [dokumentacji poleceń cmdlet Azure StorSimple](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple).


Aby dostęp do programu Windows PowerShell dla urządzenia StorSimple przy użyciu jednej z następujących metod:

* [Łączenie z konsolą szeregową urządzenia StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Zdalne nawiązywanie połączenia StorSimple przy użyciu programu Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Łączenie z programu Windows PowerShell dla urządzenia StorSimple za pośrednictwem konsoli szeregowej urządzenia

Możesz [Pobierz program PuTTY](http://www.putty.org/) lub podobnych oprogramowania do emulacji terminala nawiązać połączenia z programu Windows PowerShell dla urządzenia StorSimple. Należy skonfigurować program PuTTY specjalnie w celu uzyskania dostępu do urządzenia Microsoft Azure StorSimple. Poniższe tematy zawierają szczegółowe kroki dotyczące sposobu konfigurowania programu PuTTy i nawiąż połączenie z urządzeniem. Także opisano różne opcje menu konsoli szeregowej.

### <a name="putty-settings"></a>Ustawienia programu PuTTY

Upewnij się, użyj następujących ustawień programu PuTTY do nawiązania połączenia z konsolą szeregową interfejsu programu Windows PowerShell.

#### <a name="to-configure-putty"></a>Aby skonfigurować program PuTTY

1. W PuTTY **ponowna konfiguracja** okna dialogowego, **kategorii** okienku wybierz **klawiatury**.
2. Upewnij się, że wybrane są następujące opcje (są to ustawienia domyślne w przypadku Rozpocznij nową sesję).
   
   | Element klawiatury | Wybierz pozycję |
   | --- | --- |
   | Klawisza |Formant-? (127) |
   | Strona główna i na końcu kluczy |Standardowa (Standard) |
   | Klawisze funkcyjne i klawiatury numerycznej |ESC [n ~ |
   | Stan początkowy kluczy kursora |Normalny |
   | Początkowy stan numerycznej |Normalny |
   | Włącz funkcje dodatkowe klawiatury |Formant Alt różni się od AltGr |
   
    ![Obsługiwane ustawienia programu Putty](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kliknij przycisk **Zastosuj**.
4. W **kategorii** okienku wybierz **tłumaczenia**.
5. W **zestaw znaków zdalnego** pola listy, wybierz opcję **UTF-8**.
6. W obszarze **Obsługa znaków Rysowanie linii**, wybierz pozycję **punktów kodowych Unicode Użyj Rysowanie linii**. Poniższy zrzut ekranu przedstawia poprawne ustawienia programu PuTTY.
   
    ![Ustawienia programu Putty UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kliknij przycisk **Zastosuj**.

Przy użyciu programu PuTTY można teraz nawiązać połączenia z konsolą szeregową urządzenia, wykonując następujące kroki.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Temat konsoli szeregowej

Gdy uzyskujesz dostęp do programu Windows PowerShell są prezentowane interfejsu urządzenia StorSimple za pośrednictwem konsoli szeregowej komunikat transparentu, a następnie opcji menu.

Komunikat transparentu zawiera podstawowe informacje o urządzeniu StorSimple jak modelu, nazwa, wersja zainstalowanego oprogramowania i stan kontrolera, które uzyskują dostęp do. Na poniższej ilustracji przedstawiono przykład komunikat transparentu.

![Komunikat transparentu szeregowe](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Komunikat transparentu umożliwia określenie, czy nie są podłączone do kontrolera _Active_ lub _pasywnym_.

Na poniższej ilustracji przedstawiono różne opcje obszaru działania, które są dostępne w menu konsoli szeregowej.

![Zarejestruj urządzenie 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Możesz wybrać następujące ustawienia:

1. **Zaloguj się przy użyciu pełnego dostępu** ta opcja umożliwia łączenie się z (z właściwymi poświadczeniami) **SSAdminConsole** obszaru działania na lokalnym kontrolerze. (Lokalnego kontrolera jest kontrolera, który obecnie dostęp do za pośrednictwem konsoli szeregowej urządzenia StorSimple). Tej opcji można również umożliwić Microsoft Support dostęp nieograniczony obszaru działania (sesji pomocy technicznej) Aby rozwiązać wszelkie napotkane problemy dostępnego urządzenia do. Zaloguj się przy użyciu opcja 1, można zezwolić pracownikowi Microsoft Support dostęp nieograniczony obszaru działania przez uruchomienie konkretnego polecenia cmdlet. Aby uzyskać więcej informacji, zapoznaj się [rozpocząć sesję pomocy technicznej](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Zaloguj się do kontrolera elementu równorzędnego z pełnym dostępem** ta opcja jest taka sama jak opcja 1, z tą różnicą, że możesz nawiązać połączenie (z właściwymi poświadczeniami) **SSAdminConsole** obszaru działania na kontrolerze elementu równorzędnego. Ponieważ urządzenia StorSimple jest urządzeniem wysokiej dostępności z dwoma kontrolerami w konfiguracji aktywny / pasywny, równorzędnej odwołuje się do kontrolerem inne urządzenie, dla którego chcesz uzyskać dostęp za pośrednictwem konsoli szeregowej).
   Podobnie jak opcja 1, ta opcja może również umożliwia Microsoft Support dostęp nieograniczony obszaru działania na kontrolerze elementu równorzędnego do.

3. **Połącz z ograniczonym dostępem** ta opcja jest używana uzyskać dostępu do interfejsu programu Windows PowerShell w trybie ograniczonym. Nie monit o poświadczenia dostępu. Ta opcja łączy się bardziej ograniczonym obszarem działania w porównaniu do opcji 1 i 2.  Niektóre zadania, które są dostępne za pośrednictwem opcja 1 który **nie* można wykonać w tym obszarze działania są:
   
   * Resetowanie do ustawień fabrycznych
   * Zmień hasło
   * Włącz lub wyłącz dostęp do pomocy technicznej
   * Stosowanie aktualizacji
   * Instalowanie poprawek

    > [!NOTE]
    > Jest to preferowaną opcję, jeśli pamiętasz hasła administratora urządzenia i nie można połączyć za pomocą opcji 1 lub 2.

4. **Zmień język** ta opcja umożliwia zmianę języka wyświetlania interfejsu programu Windows PowerShell. Języki obsługiwane są angielski, japoński, rosyjski, francuski, koreański Południowa, hiszpański, włoski, niemiecki, chiński i portugalski — Brazylia.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Zdalne nawiązywanie połączenia StorSimple przy użyciu programu Windows PowerShell dla urządzenia StorSimple

Komunikacji zdalnej programu Windows PowerShell umożliwia łączenie się z urządzeniem StorSimple. Po podłączeniu w ten sposób, nie będą widzieć menu. (Widzisz menu tylko wtedy, gdy używasz konsoli szeregowej urządzenia do łączenia. Połączenie zdalne przejście bezpośrednio do odpowiednikiem "opcja 1 — pełny dostęp" na konsoli szeregowej.) Przy użyciu komunikacji zdalnej programu Windows PowerShell należy nawiązać określonego obszaru działania. Można również określić język wyświetlania.

Język wyświetlania jest niezależny od języka, który należy określić przy użyciu **Zmień język** opcji z menu konsoli szeregowej. Ustawienia regionalne urządzenia nawiązujesz połączenie z, jeśli nie określono żadnego automatycznie pobierze zdalnego programu PowerShell.

> [!NOTE]
> Podczas pracy z hostów wirtualnych Microsoft Azure i StorSimple chmury urządzenia komunikacji zdalnej programu Windows PowerShell i hostów wirtualnych można użyć do nawiązania połączenia urządzenia chmury. Jeśli w lokalizacji udział na hoście, na którym będą zapisywane informacje z sesji środowiska Windows PowerShell, należy zwrócić uwagę że _wszyscy_ podmiot zabezpieczeń zawiera tylko użytkownicy uwierzytelnieni. W związku z tym jeśli zdefiniowano udział się zezwolić na dostęp przez _wszyscy_ i połączyć bez podania poświadczeń, nieuwierzytelnione anonimowe podmiot zabezpieczeń będą używane i zostanie wyświetlony komunikat o błędzie. Aby rozwiązać ten problem, w udziale hosta, należy włączyć konta gościa i następnie nadaj gościa pełny dostęp do udziału lub należy określić prawidłowe poświadczenia oraz polecenia cmdlet programu Windows PowerShell.


Połączenia za pomocą komunikacji zdalnej programu Windows PowerShell można użyć protokołu HTTP lub HTTPS. Postępuj zgodnie z instrukcjami w następujące samouczki:

* [Połącz zdalnie przy użyciu protokołu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Połącz przy użyciu protokołu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Zagadnienia dotyczące zabezpieczeń połączenia

Wybierając sposób nawiązywania połączenia z programu Windows PowerShell dla urządzenia StorSimple, należy rozważyć następujące kwestie:

* Połączenie bezpośrednio z konsolą szeregową urządzenia jest bezpieczne, ale połączenie z konsolą szeregową za pośrednictwem przełączników sieciowych nie jest. Podczas nawiązywania połączenia szeregowego urządzenia za pośrednictwem przełączników sieciowych należy uważać na zagrożenie bezpieczeństwa.
* Łączących się za pośrednictwem sesji HTTP może oferować lepsze bezpieczeństwo niż łączących się za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest najbezpieczniejszą metodą, jest dopuszczalne w zaufanych sieciach.
* Połączenie za pomocą sesji protokołu HTTPS to najwyższy poziom bezpieczeństwa i zalecana opcja.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrowanie urządzenia StorSimple przy użyciu programu Windows PowerShell dla urządzenia StorSimple

W poniższej tabeli przedstawiono podsumowanie typowych zadań zarządzania i złożonych przepływów pracy, które mogą być wykonywane w ramach interfejsu programu Windows PowerShell urządzenia StorSimple. Aby uzyskać więcej informacji na temat każdego przepływu pracy kliknij odpowiedni wpis w tabeli.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Program Windows PowerShell dla StorSimple przepływy pracy

| Jeśli chcesz to zrobić... | Użyj tej procedury. |
| --- | --- |
| Zarejestruj urządzenie |[Konfigurowanie i rejestrowanie urządzenia za pomocą środowiska Windows PowerShell dla urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurowanie serwera proxy sieci Web</br>Wyświetl ustawienia serwera proxy sieci web |[Skonfiguruj serwer proxy sieci web dla urządzenia StorSimple](storsimple-8000-configure-web-proxy.md) |
| Zmodyfikuj ustawienia interfejsu sieciowego 0 danych na urządzeniu |[Modyfikowanie danych interfejs sieciowy 0 dla urządzenia StorSimple](storsimple-8000-modify-data-0.md) |
| Zatrzymaj kontrolera </br> Ponowne uruchamianie lub zamykanie kontrolera </br> Wyłącz urządzenia</br>Zresetowanie urządzenia do domyślnych ustawień fabrycznych |[Zarządzanie kontrolerami urządzenia](storsimple-8000-manage-device-controller.md) |
| Zainstaluj aktualizacje trybu konserwacji i poprawki |[Aktualizowanie urządzenia](storsimple-update-device.md) |
| Przejście do trybu konserwacji </br>Zamknij tryb konserwacji |[Tryby urządzenia StorSimple](storsimple-8000-device-modes.md) |
| Utwórz pakiet pomocy technicznej</br>Odszyfruj i Edytuj pakiet pomocy technicznej |[Tworzenie i zarządzanie nimi pakietu obsługi](storsimple-8000-create-manage-support-package.md) |
| Rozpocznij sesję pomocy technicznej</br> |[Rozpocznij sesję pomocy technicznej w programie Windows PowerShell dla urządzenia StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Uzyskaj pomoc w programie Windows PowerShell dla urządzenia StorSimple

W programie Windows PowerShell dla urządzenia StorSimple polecenia cmdlet pomoc jest dostępna. Online, aktualnych wersji Pomocy jest również dostępna, którego można użyć, aby zaktualizować pomoc w Twoim systemie.

Uzyskiwanie pomocy w tym interfejsie przebiega podobnie jak w programie Windows PowerShell, a większość poleceń cmdlet związanych z pomocą będą działać. Pomoc dla środowiska Windows PowerShell online można znaleźć w bibliotece TechNet: [skryptów programu Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Poniżej znajduje się krótki opis typów pomocy dla tego interfejsu programu Windows PowerShell, łącznie ze sposobem aktualizacji w Pomocy.

### <a name="to-get-help-for-a-cmdlet"></a>Aby uzyskać pomoc dotyczącą polecenia cmdlet

* Aby uzyskać pomoc dla polecenia cmdlet lub funkcję, użyj następującego polecenia:`Get-Help <cmdlet-name>`
* Aby uzyskać pomoc online dla każdego polecenia cmdlet, należy użyć polecenia cmdlet poprzedniej z `-Online` parametru:`Get-Help <cmdlet-name> -Online`
* Aby uzyskać pełną pomoc, użyj `–Full` parametru i przykłady, użyj `–Examples` parametru.

### <a name="to-update-help"></a>Aby zaktualizować pomoc

Można łatwo aktualizować pomocy w interfejsie programu Windows PowerShell. Wykonaj poniższe kroki, aby zaktualizować pomoc w Twoim systemie.

#### <a name="to-update-cmdlet-help"></a>Aby zaktualizować pomoc polecenia cmdlet
1. Uruchom program Windows PowerShell z **Uruchom jako administrator** opcji.
2. W wierszu polecenia wpisz:`Update-Help`
3. Zaktualizowane pliki pomocy zostanie zainstalowana.
4. Po zainstalowaniu plików pomocy, wpisz: `Get-Help Get-Command`. Spowoduje to wyświetlenie listy poleceń cmdlet, dla których jest dostępna Pomoc.

> [!NOTE]
> Aby uzyskać listę wszystkich poleceń cmdlet dostępnych w obszarze działania, zaloguj się do odpowiedniej opcji menu i uruchom `Get-Command` polecenia cmdlet.


## <a name="next-steps"></a>Kolejne kroki

Jeśli wystąpią problemy z urządzeniem StorSimple podczas wykonywania jednej z powyższych przepływy pracy, zapoznaj się [narzędzia do rozwiązywania problemów z wdrożeniami StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

