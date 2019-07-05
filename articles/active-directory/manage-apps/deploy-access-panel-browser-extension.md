---
title: Wdrażanie rozszerzenia Panelu dostępu do platformy Azure dla programu Internet Explorer przy użyciu obiektu zasad grupy | Dokumentacja firmy Microsoft
description: Jak używać zasad grupy do wdrożenia dodatku programu Internet Explorer w portalu Moje aplikacje.
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
ms.openlocfilehash: cf665362e2d20f26c17e8a4ae9da29fc30cb47ce
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481289"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Instrukcje: Wdrażanie rozszerzenia Panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy

W tym samouczku pokazano, jak za pomocą zasad grupy do zdalnej instalacji rozszerzenia Panelu dostępu dla programu Internet Explorer na komputerach użytkowników. To rozszerzenie jest wymagane dla programu Internet Explorer, użytkownicy muszą logować się do aplikacji, które są skonfigurowane przy użyciu [opartego na hasłach logowania jednokrotnego](what-is-single-sign-on.md#password-based-sso).

Zaleca się, że administratorzy automatyzację wdrażania tego rozszerzenia. W przeciwnym razie użytkownicy muszą pobrać i zainstalować rozszerzenie, które jest podatne na błędy użytkowników i musi mieć uprawnienia administratora. Ten samouczek obejmuje jedną metodę automatyzację wdrożenia oprogramowania za pomocą zasad grupy. [Dowiedz się więcej o zasadach grupy.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozszerzenie panelu dostępu jest również dostępna dla [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) i [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), które wymagają uprawnień administratora do zainstalowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Po skonfigurowaniu [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), i komputerów użytkowników mają być przyłączone do domeny.
* Musi mieć uprawnienia "Edytuj ustawienia" do edycji obiektu zasad grupy (GPO). Domyślnie członkowie następujące grupy zabezpieczeń mają to uprawnienie: Administratorzy domeny, Administratorzy przedsiębiorstwa i Twórcy-właściciele zasad grupy. [Dowiedz się więcej.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1: Utwórz punkt dystrybucji

Po pierwsze należy umieścić pakiet Instalatora w lokalizacji sieciowej, która może zostać oceniony przez maszyn, które chcesz zdalnie zainstalować rozszerzenie na. W tym celu wykonaj następujące kroki:

1. Zaloguj się serwerze jako administrator.
1. W **Menedżera serwera** okna, przejdź do **plików i magazynowania**.

    ![Otwartych plików i magazynowania](./media/deploy-access-panel-browser-extension/files-services.png)
1. Przejdź do **udziałów** kartę. Następnie kliknij przycisk **zadania** > **nowy udział...**

    ![Otwartych plików i magazynowania](./media/deploy-access-panel-browser-extension/shares.png)
1. Wykonaj **Kreator nowego udziału** i ustawić uprawnienia, aby upewnić się, że jest dostępny z komputerów użytkowników. [Dowiedz się więcej na temat udziałów.](https://technet.microsoft.com/library/cc753175.aspx)
1. Pobierz poniższy pakiet Instalatora systemu Microsoft Windows (plik msi): [Extension.msi panelu dostępu](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Skopiuj pakiet Instalatora do żądanej lokalizacji w udziale.

    ![Skopiuj plik MSI do udziału](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Sprawdź, czy komputery klienckie uzyskiwać dostęp do pakietu Instalatora z tego udziału.

## <a name="step-2-create-the-group-policy-object"></a>Krok 2: Utwórz obiekt zasad grupy

1. Zaloguj się do serwera, który obsługuje instalację usługi Active Directory Domain Services (AD DS).
1. W Menedżerze serwera, przejdź do **narzędzia** > **Zarządzanie zasadami grupy**.

    ![Przejdź do pozycji Narzędzia > Zarządzanie zasadami grupy](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. W okienku po lewej stronie **Zarządzanie zasadami grupy** okna, wyświetlanie hierarchii jednostki organizacyjnej (OU) i określić, w zakresie, które chcesz zastosować zasady grupy. Na przykład konieczne może być pobranie małych jednostek organizacyjnych do wdrożenia na kilku użytkowników do testowania lub może wybrać jednostka Organizacyjna najwyższego poziomu do wdrożenia w całej organizacji.

   > [!NOTE]
   > Jeśli chcesz utworzyć lub edytować jednostkach organizacyjnych (OU), przejdź wstecz do Menedżera serwera i przejdź do **narzędzia** > **użytkownicy usługi Active Directory i komputery**.

1. Po wybraniu jednostki Organizacyjnej, kliknij go prawym przyciskiem myszy i wybierz **Utwórz obiekt GPO w tej domenie i umieść tu łącze...**

    ![Zrzut ekranu przedstawia tworzenie nowych opcji obiektu zasad grupy](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. W **nowego obiektu zasad grupy** wiersza, wpisz nazwę dla nowego obiektu zasad grupy.
1. Kliknij prawym przyciskiem myszy obiekt zasad grupy, który został utworzony i wybierz **Edytuj**.

## <a name="step-3-assign-the-installation-package"></a>Krok 3: Przypisz pakiet instalacyjny

1. Określić, czy chcesz wdrożyć rozszerzenie na podstawie **konfiguracji komputera** lub **Konfiguracja użytkownika**. Korzystając z [konfiguracji komputera](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), rozszerzenie jest zainstalowane na komputerze, niezależnie od tego, w których użytkownicy logują się go. Za pomocą [Konfiguracja użytkownika](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), użytkownicy mają rozszerzenie zainstalowanych dla nich niezależnie od tego, które komputery są zalogowani.
1. W okienku po lewej stronie **Edytor zarządzania zasadami grupy** okna, przejdź do jednej z następujących ścieżek folderów, w zależności od używanego typu konfiguracji została wybrana opcja:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
1. Kliknij prawym przyciskiem myszy **instalacji oprogramowania**, a następnie wybierz **New** > **pakietu...**
1. Przejdź do folderu udostępnionego, który zawiera pakiet Instalatora z [krok 1: Utwórz punkt dystrybucji](#step-1-create-the-distribution-point), wybierz plik msi, a kliknij **Otwórz**.

   > [!IMPORTANT]
   > Jeśli udział znajduje się na tym samym serwerze, należy sprawdzić, czy uzyskują dostęp do pliku .msi za pośrednictwem sieci ścieżka pliku, a nie ścieżkę do pliku lokalnego.

    ![Wybierz pakiet instalacyjny z folderu udostępnionego.](./media/deploy-access-panel-browser-extension/select-package.png)

1. W **wdrażania oprogramowania** monit, wybierz opcję **przypisane** dla metody wdrażania. Następnie kliknij przycisk **OK**.

Rozszerzenie jest teraz wdrożyć do wybranej jednostki Organizacyjnej. [Dowiedz się więcej na temat instalacji oprogramowania zasad grupy.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4: Włącz automatyczne rozszerzenie programu Internet Explorer

Oprócz uruchomiony Instalator, każdy rozszerzenie programu Internet Explorer musi być jawnie włączone zanim będzie można jej używać. Wykonaj poniższe kroki, aby włączyć rozszerzenie panelu dostępu, za pomocą zasad grupy:

1. W **Edytor zarządzania zasadami grupy** okna, przejdź do jednej z następujących ścieżek, w zależności od używanego typu konfiguracji wybranej w ramach [krok 3: Przypisz pakiet instalacyjny](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
1. Kliknij prawym przyciskiem myszy **lista dodatków**i wybierz **Edytuj**.

    ![Kliknij prawym przyciskiem myszy "Dodatek List" i wybierz pozycję "Edytuj"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. W **lista dodatków** wybierz **włączone**. Następnie w obszarze **opcje** kliknij **Pokaż...** .

    ![Kliknij pozycję Włącz, a następnie kliknąć pozycję Pokaż...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. W **Pokaż zawartość** okna, wykonaj następujące czynności:

   1. Dla pierwszej kolumny ( **Nazwa wartości** pola), skopiuj i wklej następujący identyfikator klasy: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Dla drugiej kolumny ( **wartość** pola), wpisz następujące wartości: `1`
   3. Kliknij przycisk **OK** zamknąć **Pokaż zawartość** okna.

      ![Wypełnij wartości, jak to określono w poprzednim kroku](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Kliknij przycisk **OK** Aby zastosować zmiany i zamknąć **lista dodatków** okna.

Rozszerzenie teraz powinna być włączona dla komputerów w wybranej jednostce Organizacyjnej. [Dowiedz się więcej o używaniu zasad grupy, aby włączyć lub wyłączyć dodatki programu Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (opcjonalnie): Wyłącz monit "Zapamiętaj hasło"

Podczas logowania użytkownika do witryn sieci Web przy użyciu rozszerzenia Panelu dostępu, program Internet Explorer mogą być wyświetlane następujący wiersz zapytaniem "Czy chcesz zapisać hasło?"

![Pokazuje "Czy chcesz zapisać hasło..." wiersz](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Jeśli chcesz uniemożliwić użytkownikom wyświetlanie ten monit, postępuj zgodnie z instrukcjami poniżej, aby uniknąć automatycznego uzupełniania z przypominania sobie haseł:

1. W **Edytor zarządzania zasadami grupy** okna, przejdź do ścieżki wymienione poniżej. To ustawienie konfiguracji jest tylko dostępne w obszarze **Konfiguracja użytkownika**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Znajdź ustawienie o nazwie **włączyć funkcję automatycznego uzupełniania dla nazw użytkownika i hasła w formularzach**.

   > [!NOTE]
   > Poprzednie wersje usługi Active Directory może wyświetlać to ustawienie o nazwie **nie zezwalają na automatyczne uzupełnianie na zapisywanie haseł**. Konfiguracja tego ustawienia różni się od ustawień opisanych w tym samouczku.

    ![Pamiętaj, aby wyszukać to w obszarze Ustawienia użytkownika](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Powyższe ustawienie kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Edytuj**.
1. W oknie o nazwie **włączyć funkcję automatycznego uzupełniania dla nazw użytkownika i hasła w formularzach**, wybierz opcję **wyłączone**.

    ![Wybierz opcję "Wyłączone" Włącz funkcję automatycznego ukończenia](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Kliknij przycisk **OK** zastosować te zmiany i zamknąć okno.

Użytkownicy nie będą mogli do przechowywania ich poświadczeń lub użyj automatycznego uzupełniania, wcześniej zapisane poświadczenia dostępu do. Jednak te zasady umożliwiają użytkownikom w dalszym ciągu używać automatycznego uzupełniania dla innych typów pól formularza, takie jak pola wyszukiwania.

> [!WARNING]
> Jeśli zasada ta jest włączona po użytkownik zdecydował się do przechowywania niektórych poświadczeń spowoduje zasad *nie* Wyczyść poświadczenia, które zostały już zapisane.

## <a name="step-6-testing-the-deployment"></a>Krok 6: Testowanie wdrożenia

Wykonaj poniższe kroki, aby sprawdzić, jeśli została pomyślnie wdrożona rozszerzenia:

1. Jeśli została wdrożona przy użyciu **konfiguracji komputera**, zaloguj się na komputerze klienckim, który należy do jednostki Organizacyjnej, które wybrano w [krok 2: Utwórz obiekt zasad grupy](#step-2-create-the-group-policy-object). Jeśli została wdrożona przy użyciu **Konfiguracja użytkownika**, pamiętaj zalogować się jako użytkownik, który należy do tej jednostki Organizacyjnej.
1. Może upłynąć kilka logowania zmian zasad grupy do pełnej aktualizacji z tą maszyną. Aby wymusić aktualizację, należy otworzyć **polecenia** okna i uruchom następujące polecenie: `gpupdate /force`
1. Należy ponownie uruchomić komputer, instalacja została wykonana. Rozruchu może potrwać znacznie więcej czasu niż zwykle podczas rozszerzenia instaluje.
1. Po ponownym uruchomieniu komputera, należy otworzyć **programu Internet Explorer**. W prawym górnym rogu okna, kliknij polecenie **narzędzia** (ikonę koła zębatego), a następnie wybierz pozycję **zarządzać dodatkami**.
1. W **Zarządzanie dodatkami** okna, upewnij się, że **rozszerzenia Panelu dostępu do** został zainstalowany i czy jego **stan** został ustawiony na **włączone**.

   ![Sprawdź, czy rozszerzenie panelu dostępu jest zainstalowane i włączone.](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Dowiedz się więcej

* [Dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](what-is-single-sign-on.md)
* [Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer](manage-access-panel-browser-extension.md)
