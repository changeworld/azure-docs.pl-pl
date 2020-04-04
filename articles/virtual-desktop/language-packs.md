---
title: Instalowanie pakietów językowych na maszynach wirtualnych z systemem Windows 10 na pulpicie wirtualnym systemu Windows — Azure
description: Jak zainstalować pakiety językowe dla wielosesyjnych maszyn wirtualnych systemu Windows 10 na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634037"
---
# <a name="install-language-packs"></a>Instalowanie pakietów językowych

Podczas konfigurowania wdrożeń pulpitu wirtualnego systemu Windows na całym świecie warto upewnić się, że wdrożenie obsługuje wiele języków. Pakiety językowe można zainstalować na obrazie maszyny wirtualnej systemu Windows 10 Enterprise, aby obsługiwać dowolną liczbę języków, ile potrzebuje twoja organizacja. W tym artykule dowiesz się, jak zainstalować pakiety językowe i przechwytywać obrazy, które pozwalają użytkownikom wybrać własne języki wyświetlania.

Dowiedz się więcej o wdrażaniu maszyny wirtualnej na platformie Azure w [witrynie Tworzenie maszyny wirtualnej systemu Windows w strefie dostępności za pomocą portalu Azure.](../virtual-machines/windows/create-portal-availability-zone.md)

>[!NOTE]
>Ten artykuł dotyczy wielosesyjnych maszyn wirtualnych systemu Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Instalowanie pakietu językowego

Aby utworzyć obraz maszyny Wirtualnej z pakietami językowymi, należy najpierw zainstalować pakiety językowe na komputerze i przechwycić jego obraz.

Aby zainstalować pakiety językowe:

1. Zaloguj się jako administrator.
2. Upewnij się, że zainstalowano wszystkie najnowsze aktualizacje systemu Windows i Sklepu Windows.
3. Przejdź do **ustawienia** > **czas & regionu języka** > **Region**.
4. W obszarze **Kraj lub region**wybierz preferowany kraj lub region z menu rozwijanego.
    W tym przykładzie wybierzemy **Francję**, jak pokazano na poniższym zrzucie ekranu:

    ![Zrzut ekranu strony Region. Regionem obecnie wybranym jest Francja.](media/region-page-france.png)

5. Następnie wybierz **pozycję Język**, a następnie wybierz pozycję **Dodaj język**. Wybierz język, który chcesz zainstalować z listy, a następnie wybierz pozycję **Dalej**.
6. Po otwarciu okna **Zainstaluj funkcje języka** zaznacz pole wyboru z etykietą **Zainstaluj język i ustaw jako język wyświetlania systemu Windows**.
7. Wybierz pozycję **Zainstaluj**.
8. Aby dodać wiele języków jednocześnie, wybierz **pozycję Dodaj język**, a następnie powtórz proces, aby dodać język w krokach 5 i 6. Powtórz ten proces dla każdego języka, który chcesz zainstalować. Można jednak ustawić tylko jeden język jako język wyświetlania naraz.

    Przejdźmy przez szybką demonstrację wizualną. Poniższe obrazy pokazują, jak zainstalować pakiety języka francuskiego i holenderskiego, a następnie ustawić francuski jako język wyświetlania.

    ![Zrzut ekranu strony Język na początku procesu. Wybranym językiem wyświetlania systemu Windows jest język angielski.](media/language-page-default.png)

    ![Zrzut ekranu okna wyboru języka. Użytkownik wprowadził "francuski" do paska wyszukiwania, aby znaleźć pakiety w języku francuskim.](media/select-language-french.png)

    ![Zrzut ekranu przedstawiający stronę Funkcje języka instalacji. Język francuski jest wybierany jako preferowany język. Wybrane opcje to "Ustaw mój język wyświetlania", "Zainstaluj pakiet językowy", "Rozpoznawanie mowy" i "Pismo ręczne".](media/install-language-features.png)

    Po zainstalowaniu pakietów językowych na liście języków powinny pojawić się nazwy pakietów językowych.

    ![Zrzut ekranu strony języka z zainstalowanymi nowymi pakietami językowymi. Pakiety językowe języka francuskiego i niderlandzkiego są wymienione w "preferowanych językach".](media/language-page-complete.png)

9. Jeśli pojawi się okno z prośbą o wylogowanie się z sesji. Wyloguj się, a następnie zaloguj się ponownie. Język wyświetlania powinien być teraz wybranym językiem.

10.  Przejdź do **panelu sterowania** > **Zegar i Region regionu** > **Region**.

11.  Po otwarciu okna **Region** wybierz kartę **Administracja,** a następnie wybierz pozycję **Kopiuj ustawienia**.

12.  Zaznacz pola wyboru oznaczone jako **Ekran powitalny i konta systemowe oraz** **Nowe konta użytkowników**.

13.  Kliknij przycisk **OK**.

14.  Otworzy się okno z poleceniem ponownego uruchomienia sesji. Wybierz **pozycję Uruchom ponownie teraz**.

15.  Po zalogowaniu się wróć do panelu **sterowania** > **Zegar i** > **Region**.

16.  Wybierz kartę **Administracja.**

17.  Wybierz **pozycję Zmień ustawienia regionalne systemu**.

18. W menu rozwijanym w obszarze **Bieżące ustawienia regionalne systemu**wybierz język ustawień regionalnych, którego chcesz użyć. Następnie wybierz **przycisk OK**.

19. Wybierz **ponownie uruchom teraz,** aby ponownie uruchomić sesję.

Gratulacje, zainstalowałeś pakiety językowe!

Przed kontynuowaniem upewnij się, że w systemie są zainstalowane najnowsze wersje systemu Windows i sklepu Windows.

## <a name="sysprep"></a>Sysprep

Następnie należy sysprep komputera, aby przygotować go do procesu przechwytywania obrazu.

Aby sysprep urządzenia:

1. Uruchom program Windows PowerShell jako administrator.
2. Uruchom następujące polecenie cmdlet, aby przejść do właściwego katalogu:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Następnie uruchom następujące polecenie cmdlet:
    
    ```powershell
    .\sysprep.exe
    ```

4. Po otwarciu okna Narzędzie przygotowania systemu zaznacz pole wyboru o nazwie **Generalize**, a następnie przejdź do **opcji zamykania** systemu i wybierz polecenie **Zamknij** z menu rozwijanego.

>[!NOTE]
>Proces syprep potrwa kilka minut. Po zamknięciu maszyny Wirtualnej sesja zdalna zostanie odłączona.

### <a name="resolve-sysprep-errors"></a>Rozwiązywanie problemów z programem sysprep

Jeśli podczas procesu sysprep zostanie wyświetlony komunikat o błędzie, oto co należy zrobić:

1. Otwórz **dysk C** i przejdź do Systemu **Windows** > **System32 Sysprep** > **Panther**, a następnie otwórz plik **setuperr.**

   Tekst w pliku błędu powie Ci, że musisz odinstalować określony pakiet językowy, jak pokazano na poniższej ilustracji. Skopiuj nazwę pakietu językowego dla następnego kroku.

   ![Zrzut ekranu pliku setuperra. Tekst z nazwą pakietu jest wyróżniony na ciemnoniebieski.](media/setuperr-package-name.png)

2. Otwórz nowe okno programu PowerShell i uruchom następujące polecenie cmdlet o nazwie pakietu skopiowanej w kroku 2, aby usunąć pakiet językowy:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Sprawdź, czy pakiet został usunięty, ponownie `Remove-AppxPackage` uruchamiając polecenie cmdlet. Jeśli pakiet został pomyślnie usunięty, powinien zostać wyświetlony komunikat informujący, że pakiet, który próbujesz usunąć, nie istnieje.

4. Ponownie `sysprep.exe` uruchom polecenie cmdlet.

## <a name="capture-the-image"></a>Przechwytywanie obrazu

Teraz, gdy system jest gotowy, można przechwycić obraz, dzięki czemu inni użytkownicy mogą rozpocząć korzystanie z maszyn wirtualnych na podstawie systemu bez konieczności powtarzania procesu konfiguracji.

Aby przechwycić obraz:

1. Przejdź do witryny Azure portal i wybierz nazwę komputera skonfigurowanego w [aplikacji Zainstaluj pakiet językowy](#install-a-language-pack) i [narzędzie sysprep](#sysprep).

2. Wybierz **pozycję Przechwytywanie**.

3. Wprowadź nazwę obrazu w polu **Nazwa** i przypisz ją do grupy zasobów za pomocą menu rozwijanego **Grupa zasobów,** jak pokazano na poniższej ilustracji.

   ![Zrzut ekranu okna Utwórz obraz. Nazwa nadana przez użytkownika temu obrazowi testowego to "vmwvd-image-fr" i przypisała ją do grupy zasobów "testwvdimagerg".](media/create-image.png)

4. Wybierz **pozycję Utwórz**.

5. Poczekaj kilka minut na zakończenie procesu przechwytywania. Gdy obraz jest gotowy, powinien zostać wyświetlony komunikat w Centrum powiadomień informujący o przechwyceniu obrazu.

Teraz można wdrożyć maszynę wirtualną przy użyciu nowego obrazu. Podczas wdrażania maszyny Wirtualnej należy postępować zgodnie z instrukcjami w [obszarze Tworzenie maszyny wirtualnej systemu Windows w strefie dostępności za pomocą portalu Azure.](../virtual-machines/windows/create-portal-availability-zone.md)

### <a name="how-to-change-display-language-for-standard-users"></a>Jak zmienić język wyświetlania dla użytkowników standardowych

Użytkownicy standardowi mogą zmieniać język wyświetlania na swoich maszynach wirtualnych.

Aby zmienić język wyświetlania:

1. Przejdź do **ustawień języka**. Jeśli nie wiesz, gdzie to jest, możesz wprowadzić **język** w pasku wyszukiwania w menu Start.

2. W menu rozwijanym Język wyświetlania systemu Windows wybierz język, którego chcesz używać jako języka wyświetlania.

3. Wyloguj się z sesji, a następnie zaloguj się z powrotem. Język wyświetlania powinien być teraz wybrany w kroku 2.
