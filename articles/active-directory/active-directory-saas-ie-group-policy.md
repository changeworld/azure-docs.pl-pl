---
title: Wdrażanie rozszerzenia Panelu dostępu platformy Azure dla programu Internet Explorer przy użyciu obiektu zasad grupy | Dokumentacja firmy Microsoft
description: Jak wdrożyć dodatek programu Internet Explorer dla portalu Moje aplikacje przy użyciu zasad grupy.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d719a31700e8d693dd7edb7de603682daae14ea
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308540"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Wdrażanie rozszerzenia Panel dostępu dla programu Internet Explorer przy użyciu zasad grupy
Ten samouczek przedstawia sposób użycia zasad grupy można zdalnie zainstalować rozszerzenia Panel dostępu dla programu Internet Explorer na komputerach użytkowników. To rozszerzenie jest wymagane dla programu Internet Explorer, użytkownicy muszą logowanie do aplikacji, które są skonfigurowane przy użyciu [opartego na hasłach rejestracji jednokrotnej](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on).

Zaleca się, że administratorzy automatyzacji wdrażania tego rozszerzenia. W przeciwnym razie użytkownicy muszą pobrać i zainstalować rozszerzenie, co jest podatne na błędy użytkowników i musi mieć uprawnienia administratora. Ten samouczek obejmuje jedną metodę automatyzację wdrożenia oprogramowania za pomocą zasad grupy. [Dowiedz się więcej na temat zasad grupy.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozszerzenie panelu dostępu jest również dostępny do [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) i [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), które wymaga uprawnień administratora do zainstalowania.

## <a name="prerequisites"></a>Wymagania wstępne
* Po skonfigurowaniu [usług domenowych w usłudze Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), i dołączeniu komputerów użytkowników do domeny.
* Musi mieć uprawnienie "Edytuj ustawienia" do edycji obiektu zasad grupy (GPO). Domyślnie to uprawnienie mają członków z następujących grup zabezpieczeń: Administratorzy domeny, Administratorzy przedsiębiorstwa oraz Twórcy-właściciele zasad grupy. [Dowiedz się więcej.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1: Tworzenie punktu dystrybucji
Najpierw należy umieścić pakiet Instalatora w lokalizacji sieciowej, które są dostępne dla maszyn, których chcesz zdalnie zainstalować rozszerzenie na. W tym celu wykonaj następujące kroki:

1. Zaloguj się na serwerze jako administrator
2. W **Menedżera serwera** okna, przejdź do **pliki i usługi magazynu**.
   
    ![Otwieranie plików i usługi magazynu](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Przejdź do **udziałów** kartę. Następnie kliknij przycisk **zadania** > **nowy udział...**
   
    ![Otwieranie plików i usługi magazynu](./media/active-directory-saas-ie-group-policy/shares.png)
4. Zakończenie **Kreator nowego udziału** i ustaw uprawnienia, aby upewnić się, że jest dostępny z komputerów użytkowników. [Dowiedz się więcej na temat udziałów.](https://technet.microsoft.com/library/cc753175.aspx)
5. Pobierz następujące pakiet Instalatora systemu Microsoft Windows (plik .msi): [Extension.msi panelu dostępu](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
6. Skopiuj pakiet Instalatora do odpowiedniej lokalizacji w udziale.
   
    ![Skopiuj plik MSI do udziału.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Sprawdź, czy dostęp pakiet Instalatora z udziału z komputerów klienckich. 

## <a name="step-2-create-the-group-policy-object"></a>Krok 2: Tworzenie obiektu zasad grupy
1. Zaloguj się do serwera obsługującego instalacji usług domenowych w usłudze Active Directory (AD DS).
2. W Menedżerze serwera, przejdź do **narzędzia** > **Zarządzanie zasadami grupy**.
   
    ![Przejdź do Narzędzia > zarządzania zasadami grupy](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. W lewym okienku **Zarządzanie zasadami grupy** okna, wyświetlić hierarchii jednostki organizacyjnej (OU) i określić, w których zakresie chcesz zastosować zasady grupy. Na przykład konieczne może być pobranie małych jednostek organizacyjnych do wdrożenia na kilku użytkowników do testowania lub mogą wybierać najwyższego poziomu jednostki Organizacyjnej, aby wdrożyć dla całej organizacji.
   
   > [!NOTE]
   > Jeśli chcesz utworzyć lub edytować jednostkach organizacyjnych (OU), Przełącz wstecz do Menedżera serwera i przejdź do **narzędzia** > **użytkownicy usługi Active Directory i komputery**.
   > 
   > 
4. Po wybraniu jednostki Organizacyjnej, kliknij go prawym przyciskiem myszy i wybierz **Utwórz obiekt GPO w tej domenie i umieść tu łącze...**
   
    ![Utwórz nowy obiekt zasad grupy](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. W **nowy obiekt zasad grupy** wierszu wpisz nazwę nowego obiektu zasad grupy.
   
    ![Nazwa nowego obiektu zasad grupy](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Kliknij prawym przyciskiem myszy obiekt zasad grupy, który został utworzony i wybierz **Edytuj**.
   
    ![Edytuj nowy obiekt zasad grupy](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Krok 3: Przypisz do pakietu instalacyjnego
1. Określić, czy chcesz wdrożyć rozszerzenia na podstawie **Konfiguracja komputera** lub **Konfiguracja użytkownika**. Korzystając z [Konfiguracja komputera](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), rozszerzenie jest zainstalowane na komputerze niezależnie od tego, które użytkownicy logują się go. Z [Konfiguracja użytkownika](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), użytkownicy mają rozszerzenie zainstalowane dla nich niezależnie od tego, które komputery są zalogowani.
2. W lewym okienku **Edytor zarządzania zasadami grupy** okna, przejdź do jednej z następujących ścieżki folderu, w zależności od tego, jakiego typu konfiguracji została wybrana opcja:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Kliknij prawym przyciskiem myszy **instalacji oprogramowania**, a następnie wybierz pozycję **nowy** > **pakietu...**
   
    ![Tworzenie nowego pakietu instalacyjnego oprogramowania](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Przejdź do folderu udostępnionego, który zawiera pakiet Instalatora z [krok 1: Tworzenie punktu dystrybucji](#step-1-create-the-distribution-point), wybierz plik msi i kliknij przycisk **Otwórz**.
   
   > [!IMPORTANT]
   > Jeśli udział znajduje się na tym samym serwerze, sprawdź, czy uzyskują dostęp do pliku msi za pośrednictwem sieci ścieżka pliku, a nie ścieżkę do pliku lokalnego.
   > 
   > 
   
    ![Wybierz pakiet instalacyjny z folderu udostępnionego.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. W **wdrażanie oprogramowania** monitu, wybierz pozycję **przypisane** dla metody wdrażania. Następnie kliknij przycisk **OK**.
   
    ![Wybierz przypisane, a następnie kliknij przycisk OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Rozszerzenie został wdrożony do wybranej jednostki Organizacyjnej. [Dowiedz się, jak Instalacja oprogramowania zasad grupy.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4: Auto Włącz rozszerzenie programu Internet Explorer
Oprócz uruchomiony Instalator, każde rozszerzenie programu Internet Explorer musi być jawnie włączone zanim będzie można go używać. Wykonaj poniższe kroki, aby włączyć rozszerzenie panelu dostępu za pomocą zasad grupy:

1. W **Edytor zarządzania zasadami grupy** okna, przejdź do jednej z następujących ścieżek, w zależności od tego, jakiego typu konfiguracji wybranego w [krok 3: przypisywanie pakietu instalacyjnego](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Kliknij prawym przyciskiem myszy **lista dodatków**i wybierz **Edytuj**.
    ![Edytuj listę dodatek.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. W **lista dodatków** wybierz **włączone**. Następnie w obszarze **opcje** kliknij **Pokaż...** .
   
    ![Kliknij pozycję Włącz, a następnie kliknij przycisk Pokaż...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. W **Pokaż zawartość** okna, wykonaj następujące czynności:
   
   1. Dla pierwszej kolumny ( **Nazwa wartości** pola), skopiuj i wklej następujący identyfikator klasy: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Dla drugiej kolumny ( **wartość** pola), wpisz następujące wartości: `1`
   3. Kliknij przycisk **OK** zamknąć **Pokaż zawartość** okna.
      
      ![Wprowadź wartości określonych powyżej.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Kliknij przycisk **OK** Aby zastosować zmiany i zamknąć **lista dodatków** okna.

Rozszerzenie teraz powinno być włączone dla komputerów w wybranej jednostce Organizacyjnej. [Dowiedz się więcej o używaniu zasad grupy, aby włączyć lub wyłączyć dodatki programu Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (opcjonalny): wyłączanie "Zapamiętaj hasło" wiersza
Gdy użytkownicy logowania do witryn sieci Web przy użyciu rozszerzenia Panelu dostępu, program Internet Explorer mogą być wyświetlane następujący wiersz pytaniem "Czy chcesz zapisać hasło?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Jeśli chcesz uniemożliwić użytkownikom oglądanie ten monit, wykonaj poniższe kroki, aby uniknąć automatycznego zakończenia z zapamiętywanie haseł:

1. W **Edytor zarządzania zasadami grupy** okna, przejdź do ścieżki wymienione poniżej. Taka konfiguracja jest dostępna w obszarze tylko **Konfiguracja użytkownika**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Znajdź ustawienie o nazwie **włączyć funkcję automatycznego zakończenia dla nazwy użytkownika i hasła w formularzach**.
   
   > [!NOTE]
   > Poprzednie wersje usługi Active Directory może wyświetlić to ustawienie o nazwie **nie zezwalaj na automatyczne zakończenia na zapisywanie haseł**. Konfiguracja tego ustawienia różni się od ustawienia opisane w tym samouczku.
   > 
   > 
   
    ![Pamiętaj, aby wyszukać to w obszarze Ustawienia użytkownika.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Powyższe ustawienie kliknij prawym przyciskiem myszy i wybierz **Edytuj**.
4. W oknie zatytułowany **włączyć funkcję automatycznego zakończenia dla nazwy użytkownika i hasła w formularzach**, wybierz pozycję **wyłączone**.
   
    ![Wybierz opcję Wyłącz](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Kliknij przycisk **OK** zastosować te zmiany i zamknąć okno.

Użytkownicy nie będą mogli do przechowywania ich poświadczeń lub użyj automatycznego zakończenia dostęp do poprzednio zapisanych poświadczeń. Jednak te zasady umożliwiają użytkownikom nadal używać automatycznego zakończenia dla innych typów pól formularza, np. pola wyszukiwania.

> [!WARNING]
> Jeśli zasada ta jest włączona po użytkownik zdecydował się do przechowywania niektórych poświadczeń, ta zasada zostanie *nie* Wyczyść poświadczenia, które zostały już zapisane.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Krok 6: Testowanie wdrożenia
Wykonaj poniższe kroki, aby sprawdzić, czy pomyślne wdrożenie rozszerzenie:

1. Jeśli została wdrożona przy użyciu **Konfiguracja komputera**, zaloguj się na komputerze klienckim, który należy do jednostki Organizacyjnej, które wybrano w [krok 2: Tworzenie obiektu zasad grupy](#step-2-create-the-group-policy-object). Jeśli została wdrożona przy użyciu **Konfiguracja użytkownika**, upewnij się, że Zaloguj się jako użytkownik, który należy do tej jednostki Organizacyjnej.
2. Może upłynąć kilka znak ins zasad grupy zmian do w pełni aktualizacji z tego komputera. Aby wymusić aktualizację, należy otworzyć **wiersza polecenia** okno i uruchom następujące polecenie: `gpupdate /force`
3. Należy ponownie uruchomić komputer, aby instalacja została wykonana. Rozruchowego może potrwać znacznie więcej czasu niż zwykle podczas rozszerzenia instaluje.
4. Po ponownym uruchomieniu, otwórz **programu Internet Explorer**. W prawym górnym rogu okna, kliknij polecenie **narzędzia** (koło zębate ikonę), a następnie wybierz **Zarządzanie dodatkami**.
   
    ![Przejdź do Narzędzia > Zarządzanie dodatkami](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. W **Zarządzanie dodatkami** okna, upewnij się, że **rozszerzenia Panel dostępu** został zainstalowany, a jego **stan** została ustawiona jako **włączone**.
   
    ![Sprawdź, czy rozszerzenie panelu dostępu jest zainstalowany i włączony.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Pokrewne artykuły
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md)
* [Rozwiązywanie problemów z rozszerzeniem Panel dostępu dla programu Internet Explorer](active-directory-saas-ie-troubleshooting.md)

