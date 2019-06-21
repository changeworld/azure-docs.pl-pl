---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą E Sales Manager Remix | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "65406948"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integracja usługi Azure Active Directory z Remix kierownikiem ds. sprzedaży E

W tym samouczku dowiesz się, jak zintegrować usługi Azure Active Directory (Azure AD) przy użyciu E Sales Manager Remix.

Integrowanie usługi Azure AD z E Sales Manager Remix, możesz uzyskać następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do E Sales Manager Remix.
- Aby umożliwić użytkownikom uzyskiwanie zalogowany automatycznie Remix Menedżer sprzedaży E, (logowanie jednokrotne, lub logowania jednokrotnego) przy użyciu konta usługi Azure AD.
- Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą E Sales Manager Remix, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcję z obsługą E Sales Manager Remix Usługa rejestracji Jednokrotnej

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, firma Microsoft zaleca wykonanie *nie* za pomocą środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie E Sales Manager Remix z galerii
* Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Dodaj E Sales Manager Remix z galerii
Aby skonfigurować integrację usługi Azure AD z E Sales Manager Remix, Dodaj E Sales Manager Remix z galerii z listą zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

1. Wybierz pozycję **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![W oknie "Aplikacje dla przedsiębiorstw"][2]
    
1. Aby dodać nową aplikację, wybierz **nową aplikację** w górnej części okna.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **E Sales Manager Remix**, wybierz opcję **E Sales Manager Remix** w na liście wyników, a następnie wybierz **Dodaj**.

    ![E Sales Manager Remix na liście wyników](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą E Sales Manager Remix, oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługa Azure AD wymaga do identyfikowania użytkownika E Sales Manager Remix i jego odpowiednika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownika usługi Azure AD i tego samego użytkownika w E Sales Manager Remix.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą E Sales Manager Remix, wykonaj bloki konstrukcyjne w pięć następnych sekcjach:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji E Sales Manager Remix, wykonując następujące czynności:

1. W witrynie Azure portal na **E Sales Manager Remix** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link "Logowanie jednokrotne"][4]

1. W **logowanie jednokrotne** okna w **tryb rejestracji jednokrotnej** wybierz opcję **opartej na SAML logowania jednokrotnego**.
 
    ![W oknie "Logowanie jednokrotne"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. W obszarze **E Sales Manager Remix domena i adresy URL**, wykonaj następujące czynności:

    ![E Sales Manager Remix domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. W **adres URL logowania** wpisz adres URL w następującym formacie: *https://\<Server-na podstawie-URL > /\<domeny podrzędnej > / esales-pc*.

    b. W **identyfikator** wpisz adres URL w następującym formacie: *https://\<Server-na podstawie-URL > /\<domeny podrzędnej > /* .

    c. Uwaga **identyfikator** wartości do późniejszego użycia w ramach tego samouczka.
    
    > [!NOTE] 
    > Podane wyżej wartości nie są rzeczywiste. Zaktualizuj je przy użyciu rzeczywistego adresu logowania i identyfikator. Aby uzyskać wartości, skontaktuj się z pomocą [zespołem pomocy technicznej klienta Remix E Sales Manager](mailto:esupport@softbrain.co.jp).

1. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikat (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Wybierz **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** pole wyboru, a następnie wybierz pozycję **emailaddress** atrybutu.
    
    ![W oknie atrybutów użytkownika](./media/esalesmanagerremix-tutorial/configure1.png)

    **Edytuj atrybut** zostanie otwarte okno.

1. Kopiuj **Namespace** i **nazwa** wartości. Generowanie wartości we wzorcu  *\<Namespace > /\<Name >* i zapisz go do późniejszego użycia w ramach tego samouczka.

    ![W oknie Edytowanie atrybutu](./media/esalesmanagerremix-tutorial/configure2.png)

1. W obszarze **E Sales Manager Remix konfiguracji**, wybierz opcję **skonfigurować Remix Menedżer sprzedaży E**.

    ![Konfiguracja Remix kierownikiem ds. sprzedaży E](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    **Konfigurowanie logowania jednokrotnego** zostanie otwarte okno.

1. W **krótki** sekcji, skopiuj adres URL wylogowania i adres URL usługi rejestracji logowania jednokrotnego SAML.

1. Wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Zaloguj się do aplikacji E Sales Manager Remix jako administrator.

1. W prawym górnym rogu, wybierz **do Menu administratora**.

    ![Polecenie "Menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

1. W okienku po lewej stronie wybierz **ustawień systemowych** > **współpracę z zewnętrznym systemem**.

    !["Ustawienia systemowe" i "Współpracy z systemu zewnętrznego" linki](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. W **współpracę z zewnętrznym systemem** wybierz **SAML**.

    ![W oknie "Współpracy z systemu zewnętrznego"](./media/esalesmanagerremix-tutorial/configure6.png)

1. W obszarze **ustawienia uwierzytelniania SAML**, wykonaj następujące czynności:

    ![W sekcji "Ustawienia uwierzytelniania SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Wybierz **wersji** pole wyboru.
    
    b. W **elementu współpracy** sekcji z listy rozwijanej wybierz **e-mail**.

    c. W **elementu współpracy** pole, Wklej wartość oświadczenia, które wcześniej zostały skopiowane z witryny Azure portal (czyli **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ).

    d. W **wystawcy (identyfikator jednostki)** pole, Wklej wartość identyfikatora, który został wcześniej skopiowany z **E Sales Manager Remix domena i adresy URL** części witryny Azure portal.

    e. Aby przekazać certyfikat pobrany z witryny Azure portal, wybierz pozycję **wybór pliku**.

    f. W **adres URL logowania dostawcy identyfikator** pole, wklej adres URL Usługa rejestracji jednokrotnej SAML, który został skopiowany wcześniej w witrynie Azure portal.

    g. W **adres URL wylogowania dostawcy tożsamości** pole, Wklej wylogowania wartość adresu URL, który został skopiowany wcześniej w witrynie Azure portal.

    h. Wybierz **ustawienie pełne**.

> [!TIP]
> Ponieważ konfigurujesz aplikacji może odczytywać zwięzłe wersję poprzednich instrukcji w [witryny Azure portal](https://portal.azure.com). Po dodaniu aplikacji w **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę, a następnie przejść osadzone w dokumentacji **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji na temat funkcji dokumentacja embedded zobacz [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego Britta Simon w witrynie Azure portal, wykonując następujące czynności:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Połącz usługi Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę bieżących użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. W górnej części **wszyscy użytkownicy** wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Użytkownika** zostanie otwarte okno.

1. W **użytkownika** okna, wykonaj następujące czynności:

    ![W oknie użytkownika](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj tę wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Tworzenie użytkownika testowego E Sales Manager Remix

1. Zaloguj się do aplikacji E Sales Manager Remix jako administrator.

1. Wybierz **do Menu administratora** menu w prawym górnym rogu.

    ![Konfiguracja Remix kierownikiem ds. sprzedaży E](./media/esalesmanagerremix-tutorial/configure4.png)

1. Wybierz **ustawienia firmy** > **konserwacji działów i pracowników**, a następnie wybierz pozycję **pracowników zarejestrowany**.

    ![Na karcie "Zarejestrowano pracowników"](./media/esalesmanagerremix-tutorial/user1.png)

1. W **nowej rejestracji pracowników** sekcji, wykonaj następujące czynności:
    
    !["Rejestracja nowych pracownika" sekcji](./media/esalesmanagerremix-tutorial/user2.png)

    a. W **nazwiska pracownika** wpisz nazwę użytkownika (na przykład **Britta**).

    b. Wykonaj pozostałe wymagane pola.
    
    c. Włączenie protokołu SAML ze strony logowania administratora nie można się zalogować. Udziel uprawnienia administratora, logowania aby użytkownika, wybierając **identyfikator logowania administratora** pole wyboru.

    d. Wybierz **rejestracji**.

1. W przyszłości, aby zalogować się jako administrator, zaloguj się jako użytkownik, który ma uprawnienia administratora, a następnie w prawym górnym rogu, wybierz **do Menu administratora**.

    ![Polecenie "Menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do E Sales Manager Remix. Aby to zrobić, wykonaj następujące czynności: 

![Przypisanie roli użytkownika][200] 

1. W witrynie Azure portal Otwórz **aplikacje** przejdź do widoku **katalogu** wyświetlić, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    !["Aplikacje dla przedsiębiorstw" i "Wszystkie aplikacje" linki][201] 

1. W **aplikacje** listy wybierz **E Sales Manager Remix**.

    ![Link E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Wybierz **Dodaj** a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

1. W **użytkowników i grup** okna w **użytkowników** listy wybierz **Britta Simon**.

1. Wybierz **wybierz** przycisku.

1. W **Dodaj przydziału** wybierz **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka E Sales Manager Remix w panelu dostępu, możesz powinny być zalogowany automatycznie E Sales Manager Remix aplikacji.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

