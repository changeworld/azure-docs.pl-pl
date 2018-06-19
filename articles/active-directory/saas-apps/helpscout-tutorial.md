---
title: 'Samouczek: Integracji Azure Active Directory z pomocy Scout | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Scout pomocy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: 28611619340a0cf624c6ab841c6376a97325b2f8
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942764"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Samouczek: Integracji Azure Active Directory z Scout pomocy

Z tego samouczka dowiesz się integrowanie pomocy Scout w usłudze Azure Active Directory (Azure AD).

Integrowanie pomocy Scout z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do pomocy Scout.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do pomocy Scout (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocy Scout, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pomoc Scout jednokrotnego włączone subskrypcji

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pomocy Scout z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-help-scout-from-the-gallery"></a>Dodawanie pomocy Scout z galerii
Aby skonfigurować integrację usługi Azure AD Scout pomocy, należy dodać Scout pomocy z poziomu galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Scout pomocy z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **pomocy Scout**, wybierz pozycję **Scout pomocy** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Scout pomocy na liście wyników](./media/helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Scout pomocy, na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w pomocy Scout jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w pomocy Scout musi się.

Scout pomocy używa adresów e-mail dla nazwy logowania, więc do ustanawiania relacji łącza, używać tego samego **adres e-mail** jako **nazwy użytkownika** w usłudze Azure AD.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Scout pomocy, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego pomocy Scout](#create-a-help-scout-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta pomocy Scout połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Scout pomocy.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pomocy Scout, wykonaj następujące czynności:**

1. W portalu Azure na **pomocy Scout** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Na **pomocy domeny Scout i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Scout pojedynczego logowania jednokrotnego informacje pomocy](./media/helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identyfikator** jest **"Odbiorców identyfikatora URI (identyfikator jednostki dostawcy usługi)"** z pomocy Scout rozpoczyna się od `urn:`

    b. **Adres URL odpowiedzi** jest **"Po wstecz URL (adres URL usługi potwierdzenia klienta)"** z pomocy Scout rozpoczyna się od `https://` 

    > [!NOTE] 
    > Wartości te adresy URL są do pokazania tylko. Musisz zaktualizować te wartości z rzeczywisty adres URL odpowiedzi i identyfikator. Możesz uzyskać te wartości z **rejestracji jednokrotnej** kartę sekcji uwierzytelniania, który znajduje się w dalszej części tego samouczka.

4. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane trybie wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![Adresy URL i domeny Scout pojedynczego logowania jednokrotnego informacje pomocy](./media/helpscout-tutorial/tutorial_helpscout_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL jako: `https://secure.helpscout.net/members/login/`
     
5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/helpscout-tutorial/tutorial_general_400.png)


7. Na **Pomoc konfiguracji Scout** , kliknij przycisk **skonfigurować Scout pomocy** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/helpscout-tutorial/config.png) 

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Scout pomocy.

9. Gdy użytkownik jest zalogowany w kliknij **"Manage"** z menu u góry, a następnie wybierz **"Firmy"** z menu rozwijanego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/helpscout-tutorial/settings1.png) 
 
10. Wybierz **"Uwierzytelnianie"** z menu po lewej stronie. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/helpscout-tutorial/settings2.png) 

11. Przejście do sekcji Ustawienia SAML i wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/helpscout-tutorial/settings3.png) 
 
    a. Kopiuj **ogłaszania adresu URL (adres URL usługi potwierdzenia klienta)** i Wklej wartość w **adres URL odpowiedzi służący** pole w portalu Azure, w obszarze Scout pomocy **domeny i adres URL** sekcji.
    
    b. Kopia **identyfikatora URI odbiorców (identyfikator jednostki dostawcy usługi)** i Wklej wartość w **identyfikator** pole w portalu Azure, w obszarze Pomoc Scout **domeny i adres URL** sekcji.

12. Przełącz **Włącz SAML** na i wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/helpscout-tutorial/settings4.png) 
 
    a. W **URL rejestracji jednokrotnej** pole tekstowe, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
    
    b. Kliknij przycisk **Przekaż certyfikat** przekazać **Certificate(Base64)** pobrany z portalu Azure.

    c. Wprowadź organizacji e-mail domenach e.x. - `contoso.com` w **domen poczty E-mail** pola tekstowego. Wiele domen można rozdzielić przecinkami. W każdej chwili pomocy Scout użytkownika lub administratora, który wchodzi tej konkretnej domeny [pomocy Scout logowania strony](https://secure.helpscout.net/members/login/) będą kierowane do dostawcy tożsamości do uwierzytelniania przy użyciu poświadczeń.

    d. Ponadto można przełączyć **życie SAML logowania jednokrotnego** Jeśli użytkownicy mają tylko zalogować się do pomocy Scout za pośrednictwem za pomocą tej metody. Jeśli chcesz nadal pozostaw tę opcję, aby zalogować się przy użyciu swoich poświadczeń Scout pomocy, można pozostawić go wyłączony. Nawet jeśli ta opcja jest włączona, właścicielem konta zawsze będzie mógł zalogować się do pomocy Scout z hasła do konta.

    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/helpscout-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/helpscout-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/helpscout-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/helpscout-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-help-scout-test-user"></a>Tworzenie użytkownika testowego Scout pomocy

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Scout pomocy. Pomoc Scout obsługę w czasie, który jest domyślnie włączone.

Nie ma elementu akcji można w tej sekcji. Jeśli użytkownik nie istnieje w pomocy Scout, nowy jest tworzony podczas próby dostępu Scout pomocy.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do pomocy Scout.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Scout pomocy, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **pomocy Scout**.

    ![Łącza pomocy Scout na liście aplikacji](./media/helpscout-tutorial/tutorial_helpscout_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Scout pomoc w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji Scout pomocy.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/helpscout-tutorial/tutorial_general_01.png
[2]: ./media/helpscout-tutorial/tutorial_general_02.png
[3]: ./media/helpscout-tutorial/tutorial_general_03.png
[4]: ./media/helpscout-tutorial/tutorial_general_04.png

[100]: ./media/helpscout-tutorial/tutorial_general_100.png

[200]: ./media/helpscout-tutorial/tutorial_general_200.png
[201]: ./media/helpscout-tutorial/tutorial_general_201.png
[202]: ./media/helpscout-tutorial/tutorial_general_202.png
[203]: ./media/helpscout-tutorial/tutorial_general_203.png

