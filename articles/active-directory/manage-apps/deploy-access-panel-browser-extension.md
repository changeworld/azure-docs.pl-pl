---
title: Wdrażanie rozszerzenia panelu programu Azure Access dla programu IE przy użyciu obiektu zasad grupy | Dokumenty firmy Microsoft
description: Jak używać zasad grupy do wdrażania dodatku Internet Explorer dla portalu Moje aplikacje.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807681"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Jak: Wdrażanie rozszerzenia Panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy

W tym samouczku pokazano, jak używać zasad grupy do zdalnego instalowania rozszerzenia Panelu dostępu dla programu Internet Explorer na komputerach użytkowników. To rozszerzenie jest wymagane dla użytkowników programu Internet Explorer, którzy muszą logować się do aplikacji skonfigurowanych przy użyciu [logowania jednokrotnego opartego na hasłach](what-is-single-sign-on.md#password-based-sso).

Zaleca się, aby administratorzy zautomatyzowali wdrażanie tego rozszerzenia. W przeciwnym razie użytkownicy muszą pobrać i zainstalować rozszerzenie samodzielnie, co jest podatne na błędy użytkownika i wymaga uprawnień administratora. W tym samouczku opisano jedną metodę automatyzacji wdrożeń oprogramowania przy użyciu zasad grupy. [Dowiedz się więcej o zasadach grupy.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozszerzenie Panelu dostępu jest również dostępne dla [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) i [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), z których żaden nie wymaga uprawnień administratora do zainstalowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Skonfigurowano [Usługi domenowe Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)i przyłączyłeś komputery użytkowników do domeny.
* Aby edytować obiekt zasad grupy, musisz mieć uprawnienie "Edytuj ustawienia". Domyślnie członkowie następujących grup zabezpieczeń mają takie uprawnienia: Administratorzy domeny, Administratorzy przedsiębiorstwa i Właściciele twórców zasad grupy. [Dowiedz się więcej.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1: Tworzenie punktu dystrybucji

Najpierw należy umieścić pakiet instalatora w lokalizacji sieciowej, do której mogą uzyskać dostęp maszyny, na których chcesz zdalnie zainstalować rozszerzenie. W tym celu wykonaj następujące kroki:

1. Zaloguj się na serwerze jako administrator.
1. W oknie **Menedżer serwera** przejdź do strony Pliki i **usługi magazynu**.

    ![Otwieranie plików i usług magazynu](./media/deploy-access-panel-browser-extension/files-services.png)

1. Przejdź do karty **Udziały.** Następnie kliknij **pozycję Zadania** > **Nowy udział...**

    ![Zrzut ekranu pokazuje, gdzie można znaleźć nowy udział na ekranie Zadania](./media/deploy-access-panel-browser-extension/shares.png)

1. Ukończ **Kreatora nowego udostępniania** i ustaw uprawnienia, aby mieć pewność, że dostęp do niego będzie dostępny z komputerów użytkowników. [Dowiedz się więcej o udostępniach.](https://technet.microsoft.com/library/cc753175.aspx)
1. Pobierz następujący pakiet Instalatora Systemu Microsoft Windows (plik msi): [Rozszerzenie panelu dostępu.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Skopiuj pakiet instalatora do żądanej lokalizacji w udziale.

    ![Kopiowanie pliku msi do udziału](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Sprawdź, czy komputery klienckie są w stanie uzyskać dostęp do pakietu instalatora z udziału.

## <a name="step-2-create-the-group-policy-object"></a>Krok 2: Tworzenie obiektu zasad grupy

1. Zaloguj się na serwerze, na którym odbywa się instalacja Usług Domenowych Active Directory (AD DS).
1. W Menedżerze serwera przejdź do pozycji**Zarządzanie zasadami grupy** **narzędzi** > .

    ![Przejdź do narzędzi > zarządzania zasadami grupy](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. W lewym okienku okna **Zarządzanie zasadami grupy** wyświetl hierarchię jednostki organizacyjnej i określ, w jakim zakresie chcesz zastosować zasady grupy. Na przykład może zdecydować się wybrać małą jednostka organizacyjną do wdrożenia dla kilku użytkowników do testowania lub można wybrać najwyższego poziomu OU do wdrożenia w całej organizacji.

   > [!NOTE]
   > Jeśli chcesz utworzyć lub edytować jednostki organizacyjne, przełącz się z powrotem do Menedżera serwera i przejdź do **pozycji Użytkownicy** > i komputery**usługi Tools Active Directory**.

1. Po wybraniu konsoli organizacyjnej kliknij ją prawym przyciskiem myszy i wybierz polecenie **Utwórz obiekt zasad grupy w tej domenie, a następnie połącz ją tutaj...**

    ![Zrzut ekranu przedstawiający opcję Utwórz nowy obiekt zasad grupy](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. W wierszu **Nowy obiekt zasad grupy** wpisz nazwę nowego obiektu zasad grupy.
1. Kliknij prawym przyciskiem myszy utworzony obiekt zasad grupy, a następnie wybierz polecenie **Edytuj**.

## <a name="step-3-assign-the-installation-package"></a>Krok 3: Przypisz pakiet instalacyjny

1. Określ, czy rozszerzenie ma być wdrażane na podstawie **konfiguracji komputera** lub **konfiguracji użytkownika**. Podczas korzystania z [konfiguracji komputera](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)rozszerzenie jest instalowane na komputerze, niezależnie od tego, którzy użytkownicy się do niego logują. W [przypadku konfiguracji użytkownika](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)użytkownicy mają zainstalowane dla nich rozszerzenie, niezależnie od tego, na których komputerach się logują.
1. W lewym okienku okna **Edytor zarządzania zasadami grupy** przejdź do jednej z następujących ścieżek folderów, w zależności od wybranego typu konfiguracji:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Kliknij prawym przyciskiem myszy pozycję **Instalacja oprogramowania**, a następnie wybierz pozycję **Nowy** > **pakiet...**
1. Przejdź do folderu udostępnionego zawierającego pakiet instalacyjny z [kroku 1: Utwórz punkt dystrybucji](#step-1-create-the-distribution-point), wybierz plik msi i kliknij przycisk **Otwórz**.

   > [!IMPORTANT]
   > Jeśli udział znajduje się na tym samym serwerze, sprawdź, czy uzyskujesz dostęp do msi za pośrednictwem ścieżki pliku sieciowego, a nie lokalnej ścieżki pliku.

    ![Wybierz pakiet instalacyjny z folderu udostępnionego](./media/deploy-access-panel-browser-extension/select-package.png)

1. W wierszu **polecenia Wdrażanie oprogramowania** wybierz pozycję **Przypisane** dla metody wdrażania. Następnie kliknij przycisk **OK**.

Rozszerzenie jest teraz wdrażane na wybranej wybrano o.The extension is now deployed to the OU that you selected. [Dowiedz się więcej o instalacji oprogramowania zasad grupy.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4: Automatyczne włączanie rozszerzenia dla programu Internet Explorer

Oprócz uruchamiania instalatora, każde rozszerzenie programu Internet Explorer musi być jawnie włączone, zanim będzie można go używać. Wykonaj poniższe czynności, aby włączyć rozszerzenie panelu dostępu przy użyciu zasad grupy:

1. W oknie **Edytor zarządzania zasadami grupy** przejdź do jednej z następujących ścieżek, w zależności od typu konfiguracji wybranej w [kroku 3: Przypisywanie pakietu instalacyjnego:](#step-3-assign-the-installation-package)

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Kliknij prawym przyciskiem myszy pozycję **Lista dodatków**i wybierz polecenie **Edytuj**.

    ![Kliknij prawym przyciskiem myszy "Lista dodatków" i wybierz "Edytuj"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. W oknie **Lista dodatków** wybierz pozycję **Włączone**. Następnie w sekcji **Opcje** kliknij pozycję **Pokaż...**.

    ![Kliknij pozycję Włącz, a następnie kliknij pozycję Pokaż...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. W oknie **Pokaż zawartość** wykonaj następujące czynności:

   1. Dla pierwszej kolumny (pole **Nazwa wartości)** skopiuj i wklej następujący identyfikator klasy:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. W przypadku drugiej kolumny (pola **Wartość)** wpisz następującą wartość:`1`
   1. Kliknij **przycisk OK,** aby zamknąć okno **Pokaż zawartość.**

      ![Wypełnianie wartości określonych w poprzednim kroku](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Kliknij **przycisk OK,** aby zastosować zmiany i zamknąć okno **Lista dodatków.**

Rozszerzenie powinno być teraz włączone dla maszyn w wybranej o.o. [Dowiedz się więcej o włączaniu lub wyłączeniu dodatków programu Internet Explorer za pomocą zasad grupy.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (opcjonalnie): Wyłącz monit "Zapamiętaj hasło"

Gdy użytkownicy logują się do witryn sieci Web przy użyciu rozszerzenia panelu dostępu, program Internet Explorer może wyświetlić następujący monit z pytaniem "Czy chcesz zapisać hasło?"

![Pokazuje hasło "Czy chcesz zapisać hasło..." Wierszu](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Jeśli chcesz uniemożliwić użytkownikom wyświetlanie tego monitu, wykonaj poniższe czynności, aby zapobiec automatycznemu uzupełnianiu zapamiętywania haseł:

1. W oknie **Edytor zarządzania zasadami grupy** przejdź do ścieżki wymienionej poniżej. To ustawienie konfiguracji jest dostępne tylko w obszarze **Konfiguracja użytkownika**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Znajdź ustawienie o nazwie **Włącz funkcję automatycznego uzupełniania nazw użytkowników i haseł w formularzach**.

   > [!NOTE]
   > Poprzednie wersje usługi Active Directory mogą wymieniać to ustawienie o nazwie **Nie zezwalaj na automatyczne uzupełnianie haseł**. Konfiguracja tego ustawienia różni się od ustawienia opisanego w tym samouczku.

    ![Pamiętaj, aby wyszukać to w ustawieniach użytkownika](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Kliknij prawym przyciskiem myszy powyższe ustawienie, a następnie wybierz polecenie **Edytuj**.
1. W oknie **"Włącz funkcję automatycznego uzupełniania nazw użytkowników i haseł w formularzach"** wybierz pozycję **Wyłączone**.

    ![Wybierz opcję "Wyłączone" dla funkcji automatycznego uzupełniania włączania](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Kliknij **przycisk OK,** aby zastosować te zmiany i zamknąć okno.

Użytkownicy nie będą już mogli przechowywać swoich poświadczeń ani używać funkcji autouzupełnianie w celu uzyskania dostępu do wcześniej przechowywanych poświadczeń. Jednak ta zasada zezwala użytkownikom na dalsze używanie autouzupełniania dla innych typów pól formularzy, takich jak pola wyszukiwania.

> [!WARNING]
> Jeśli ta zasada jest włączona po wybraniu przez użytkowników do przechowywania niektórych poświadczeń, ta zasada *nie* wyczyści poświadczeń, które zostały już zapisane.

## <a name="step-6-testing-the-deployment"></a>Krok 6: Testowanie wdrożenia

Wykonaj poniższe czynności, aby sprawdzić, czy wdrożenie rozszerzenia zakończyło się pomyślnie:

1. Jeśli wdrożono przy użyciu **konfiguracji komputera,** zaloguj się do komputera klienckiego należącego do instalacji organizacyjnej wybranej w [kroku 2: Utwórz obiekt zasad grupy](#step-2-create-the-group-policy-object). Jeśli wdrożono przy użyciu **konfiguracji użytkownika,** upewnij się, że logujesz się jako użytkownik, który należy do tej grupy organizacyjnej.
1. Może upłynąć kilka logów do zmian zasad grupy, aby w pełni zaktualizować z tego komputera. Aby wymusić aktualizację, otwórz okno **wiersza polecenia** i uruchom następujące polecenie:`gpupdate /force`
1. Aby instalacja została prowadzona, należy ponownie uruchomić komputer. Rozruch może zająć znacznie więcej czasu niż zwykle podczas instalacji rozszerzenia.
1. Po ponownym uruchomieniu otwórz **program Internet Explorer**. W prawym górnym rogu okna kliknij pozycję **Narzędzia** (ikona koła zębatego), a następnie wybierz pozycję **Zarządzaj dodatkami**.
1. W oknie **Zarządzanie dodatkami** sprawdź, czy zostało zainstalowane **rozszerzenie panelu dostępu** i czy jego **stan** został ustawiony na **Włączone**.

   ![Sprawdź, czy rozszerzenie panelu dostępu jest zainstalowane i włączone](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Dowiedz się więcej

* [Dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](what-is-single-sign-on.md)
* [Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer](manage-access-panel-browser-extension.md)
