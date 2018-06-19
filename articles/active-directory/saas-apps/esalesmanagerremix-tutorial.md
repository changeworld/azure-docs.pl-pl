---
title: 'Samouczek: Integracji Azure Active Directory z E sprzedaży Menedżera Remix | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i E sprzedaży Menedżera Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 5592a0f6c465d4057ad5e3ee217d7a93f75ef88f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941098"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrowanie usługi Azure Active Directory z Remix kierownik E

Z tego samouczka dowiesz Integrowanie usługi Azure Active Directory (Azure AD) z E sprzedaży Menedżera Remix.

Integrowanie usługi Azure AD z Remix Menedżera sprzedaży E, można uzyskać następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do E sprzedaży Menedżera Remix.
- Można umożliwić użytkownikom uzyskać zalogowany automatycznie Remix Menedżera sprzedaży E, (logowanie jednokrotne lub logowania jednokrotnego) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji, portalu Azure.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Remix Menedżera sprzedaży E, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Włączone E sprzedaży Menedżera Remix SSO subskrypcji

> [!NOTE]
> Podczas testowania czynności w tym samouczku, zaleca się wykonanie *nie* za pomocą środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

* Dodawanie E sprzedaży Menedżera Remix z galerii
* Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Dodaj E sprzedaży Menedżera Remix z galerii
Aby skonfigurować integrację usługi Azure AD z Remix Menedżera sprzedaży E, Dodaj E sprzedaży Menedżera Remix z galerii do listy zarządzane aplikacje SaaS w następujący sposób:

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Okno "Aplikacje przedsiębiorstwa"][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** w górnej części okna.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **E sprzedaży Menedżera Remix**, wybierz pozycję **E sprzedaży Menedżera Remix** w lista wyników, a następnie wybierz **Dodaj**.

    ![E sprzedaży Menedżera Remix na liście wyników](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z E sprzedaży Menedżera Remix, na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi zidentyfikować E sprzedaży Menedżera Remix użytkownik i jego odpowiednik w usłudze Azure AD. Innymi słowy można ustanowić relacji łącza między użytkownika usługi Azure AD i tego samego użytkownika w E sprzedaży Menedżera Remix.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z Remix Menedżera sprzedaży E, wykonaj bloków konstrukcyjnych w następnych pięciu sekcjach:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

Włączanie usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji E sprzedaży Menedżera Remix w następujący sposób:

1. W portalu Azure na **E sprzedaży Menedżera Remix** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Łącze "Logowanie jednokrotne"][4]

2. W **logowanie jednokrotne** okna w **tryb rejestracji jednokrotnej** wybierz opcję **na języku SAML logowania jednokrotnego**.
 
    ![Okno "Logowanie jednokrotne"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. W obszarze **E sprzedaży Menedżera Remix domeny i adres URL**, wykonaj następujące czynności:

    ![Adresy URL i E sprzedaży Menedżera Remix domeny pojedynczy informacje logowania jednokrotnego](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. W **adres URL logowania** wpisz adres URL w następującym formacie: *https://\<— na podstawie — adres URL serwera > /\<domeny podrzędnej > / esales pc*.

    b. W **identyfikator** wpisz adres URL w następującym formacie: *https://\<— na podstawie — adres URL serwera > /\<domeny podrzędnej > /*.

    c. Uwaga **identyfikator** wartości do użycia w tym samouczku.
    
    > [!NOTE] 
    > Poprzednie wartości nie są prawdziwe. Zaktualizuj je z rzeczywistego logowania URL i identyfikator. Aby uzyskać wartości, skontaktuj się z [zespołem pomocy technicznej klienta Remix E sprzedaży Menedżera](mailto:esupport@softbrain.co.jp).

4. W obszarze **SAML certyfikat podpisywania**, wybierz pozycję **certyfikatu (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Łącze pobierania certyfikatu (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Wybierz **widoku i edytować wszystkie atrybuty użytkowników** pole wyboru, a następnie wybierz **emailaddress** atrybutu.
    
    ![Okno atrybutów użytkownika](./media/esalesmanagerremix-tutorial/configure1.png)

    **Atrybutu Edytuj** zostanie otwarte okno.

6. Kopiuj **Namespace** i **nazwa** wartości. Generowanie wartości we wzorcu  *\<Namespace > /\<Name >* i zapisz go do użycia w tym samouczku.

    ![Okno edycji atrybucie](./media/esalesmanagerremix-tutorial/configure2.png)

7. W obszarze **E sprzedaży Menedżera konfiguracji Remix**, wybierz pozycję **skonfigurować Remix Menedżera sprzedaży E**.

    ![Konfiguracja Remix kierownik E](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    **Konfigurowanie logowania jednokrotnego** zostanie otwarte okno.

8. W **krótkimi opisami** sekcji, skopiuj adres URL wylogowania i adres URL usługi rejestracji jednokrotnej SAML.

9. Wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

10. Zaloguj się do aplikacji E sprzedaży Menedżera Remix jako administrator.

11. U góry po prawej stronie, zaznacz pole wyboru **do Menu administratora**.

    ![Polecenie "Menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

12. W okienku po lewej stronie wybierz **ustawienia systemu** > **współpracy z systemu zewnętrznego**.

    !["System ustawienia" i "Współpracy z systemu zewnętrznego" łącza](./media/esalesmanagerremix-tutorial/configure5.png)
    
13. W **współpracy z systemu zewnętrznego** wybierz **SAML**.

    ![Okno "Współpracy z systemu zewnętrznego"](./media/esalesmanagerremix-tutorial/configure6.png)

14. W obszarze **ustawienia uwierzytelniania SAML**, wykonaj następujące czynności:

    ![W sekcji "Ustawienia uwierzytelniania SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Wybierz **wersji** pole wyboru.
    
    b. W **elementu współpracy** sekcji, na liście rozwijanej wybierz **e-mail**.

    c. W **elementu współpracy** Wklej wartość oświadczenia, które wcześniej zostały skopiowane z portalu Azure (czyli **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. W **wystawcy (identyfikator podmiot)** Wklej wartość identyfikatora, które wcześniej zostały skopiowane z **E sprzedaży Menedżera Remix domeny i adres URL** części portalu Azure.

    e. Aby przekazać certyfikat pobrany z portalu Azure, wybierz **pliku wybór**.

    f. W **adres URL logowania dostawcy identyfikator** Wklej adres URL usługi rejestracji jednokrotnej SAML skopiowanego wcześniej w portalu Azure.

    g. W **adres URL wylogowania dostawcy tożsamości** Wklej wylogowania wartość adresu URL skopiowanego wcześniej w portalu Azure.

    h. Wybierz **ustawienie pełną**.

> [!TIP]
> Jak podczas konfigurowania aplikacji, możesz przeczytać zwięzły wersji poprzednich instrukcji w [portalu Azure](https://portal.azure.com). Po dodaniu aplikacji w **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie, a następnie przejść osadzone w dokumentacji **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji o funkcji osadzonych dokumentacji, zobacz [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji możesz tworzyć użytkownika testowego Simona Britta w portalu Azure w następujący sposób:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory**.

    ![Łącze usługi Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę bieżących użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. W górnej części **wszyscy użytkownicy** wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Użytkownika** zostanie otwarte okno.

4. W **użytkownika** okna, wykonaj następujące czynności:

    ![Okno użytkownika](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Tworzenie użytkownika testowego Remix Menedżera sprzedaży E

1. Zaloguj się do aplikacji E sprzedaży Menedżera Remix jako administrator.

2. Wybierz **do Menu administratora** w menu u góry po prawej.

    ![Konfiguracja Remix kierownik E](./media/esalesmanagerremix-tutorial/configure4.png)

3. Wybierz **ustawienia firmy** > **konserwacji działów i pracownicy**, a następnie wybierz **pracowników w zarejestrowany**.

    ![Na karcie "Zarejestrowany pracowników"](./media/esalesmanagerremix-tutorial/user1.png)

4. W **nowej rejestracji pracownika** sekcji, wykonaj następujące czynności:
    
    !["Rejestracja nowych pracowników" sekcji](./media/esalesmanagerremix-tutorial/user2.png)

    a. W **nazwa pracownika** wpisz nazwę użytkownika (na przykład **Britta**).

    b. Wykonaj pozostałe wymagane pola.
    
    c. Jeśli włączysz SAML, administrator nie można zalogować się na stronie logowania. Uprawnienia logowania administratora GRANT dla użytkownika, wybierając **logowania administratora** pole wyboru.

    d. Wybierz **rejestracji**.

5. W przyszłości, aby zalogować się jako administrator, zaloguj się jako użytkownik, który ma uprawnienia administratora, a następnie u góry po prawej, wybierz **do Menu administratora**.

    ![Polecenie "Menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji możesz umożliwić użytkownikowi Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do E sprzedaży Menedżera Remix. Aby to zrobić, wykonaj następujące czynności: 

![Przypisanie roli użytkownika][200] 

1. W portalu Azure Otwórz **aplikacji** widok, przejdź do **katalogu** wyświetlić, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    !["Aplikacje przedsiębiorstwa" i "Wszystkie aplikacje" łącza][201] 

2. W **aplikacji** listy, wybierz **E sprzedaży Menedżera Remix**.

    ![Łącze Remix Menedżera sprzedaży E](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** , a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okna w **użytkowników** listy, wybierz **Simona Britta**.

6. Wybierz **wybierz** przycisku.

7. W **Dodaj przydziału** wybierz **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka E sprzedaży Menedżera Remix w panelu dostępu należy powinny być podpisane automatycznie do aplikacji E sprzedaży Menedżera Remix.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczki dotyczące integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

