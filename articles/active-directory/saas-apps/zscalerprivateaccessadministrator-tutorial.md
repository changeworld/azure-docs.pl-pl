---
title: 'Samouczek: Integracji Azure Active Directory przy użyciu prywatnego Zscaler dostępu administratora | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Administrator dostępu prywatnego Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 25517f6f04b43152c72ad1f223e12456aac89a00
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221397"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Samouczek: Integracji Azure Active Directory przy użyciu prywatnego Zscaler dostępu administratora

Z tego samouczka dowiesz sposobu integracji Zscaler prywatnego dostępu administratora w usłudze Azure Active Directory (Azure AD).

Integrowanie Administrator dostępu prywatnego Zscaler z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do Zscaler prywatnego dostępu administratora.
- Umożliwia użytkownikom automatycznie pobrać zalogowane prywatnego Zscaler dostępu administratora (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu prywatnego Zscaler dostępu administratora, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Administrator dostępu prywatnego Zscaler logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Administrator dostępu prywatnego Zscaler z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Dodawanie Administrator dostępu prywatnego Zscaler z galerii
Aby skonfigurować integrację z prywatnej Zscaler dostępu administratora do usługi Azure AD, należy dodać Administrator dostępu prywatnego Zscaler z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Administrator dostępu prywatnego Zscaler z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Administrator dostępu prywatnego Zscaler**, wybierz pozycję **Administrator dostępu prywatnego Zscaler** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Administrator dostępu prywatnego Zscaler na liście wyników](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Zscaler prywatnego Administrator dostępu w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w Zscaler prywatnego dostępu administratora do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w prywatnej Zscaler dostępu administratora musi określone.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu prywatnego Zscaler dostępu administratora, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Administrator dostępu prywatnego Zscaler](#create-a-zscaler-private-access-administrator-test-user)**  — aby odpowiednikiem Simona Britta w prywatnej Zscaler dostępu administratora połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Zscaler prywatnego dostępu administratora.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu prywatnego Zscaler dostępu administratora, wykonaj następujące czynności:**

1. W portalu Azure na **Administrator dostępu prywatnego Zscaler** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Na **Zscaler prywatnego dostępu administratora domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i Administrator dostępu prywatnego Zscaler domeny pojedynczy informacje logowania jednokrotnego](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**

    d. W **RelayState** tekstowym, wpisz wartość: `idpadminsso`

4.  Jeśli chcesz skonfigurować aplikację w **SP** tryb inicjowane, wykonaj następujące czynności:

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL identyfikatora, adres URL odpowiedzi i logowania jednokrotnego, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej Administrator dostępu prywatnego Zscaler](https://help.zscaler.com/zpa-submit-ticket) uzyskać te wartości.
 
5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. W oknie przeglądarki innej witryny sieci web, logowania prywatnego Zscaler dostępu administratora z uprawnieniami administratora.

8. W górnej części, kliknij przycisk **administracji** i przejdź do **uwierzytelniania** kliknij sekcję **konfiguracji IdP**.

    ![Administrator dostępu prywatnego Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. W prawym górnym rogu, kliknij przycisk **Dodawanie konfiguracji IdP**. 

    ![Administrator dostępu prywatnego Zscaler addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. Na **Dodawanie konfiguracji IdP** strony wykonaj następujące czynności:
 
    ![Administrator dostępu prywatnego Zscaler idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kliknij przycisk **wybierz plik** można przekazać pliku metadanych pobranych z usługi Azure AD w **przekazywanie pliku metadanych IdP** pola.

    b. Odczytuje **metadanych IdP** z usługi Azure AD i wypełnienie wszystkich pól informacji, jak pokazano poniżej.

    ![Administrator dostępu prywatnego Zscaler idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Wybierz **jednokrotne** jako **administratora**.

    d. Wybierz domenę z **domen** pola.
    
    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Tworzenie użytkownika testowego Administrator dostępu prywatnego Zscaler

Aby włączyć użytkowników usługi Azure AD zalogować się do prywatnego Zscaler dostępu administratora, one udostępnione do Zscaler prywatnego dostępu administratora. W przypadku administratora dostępu prywatnego Zscaler Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Administrator dostępu prywatnego Zscaler jako administrator.

2. W górnej części, kliknij przycisk **administracji** i przejdź do **uwierzytelniania** kliknij sekcję **konfiguracji IdP**.

    ![Administrator dostępu prywatnego Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Kliknij przycisk **Administratorzy** z lewej części menu.

    ![Administrator dostępu prywatnego Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. W prawym górnym rogu, kliknij przycisk **Dodawanie administratora**:

    ![Administrator dostępu prywatnego Zscaler dodać administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. W **Dodawanie administratora** wykonaj następujące czynności:

    ![Administrator dostępu prywatnego Zscaler użytkownika administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. W **Username** pole tekstowe, wprowadź adres e-mail użytkownika, takich jak **BrittaSimon@contoso.com**.

    b. W **hasło** tekstowym, wpisz hasło.

    c. W **Potwierdź hasło** tekstowym, wpisz hasło.

    d. Wybierz **roli** jako **Administrator dostępu prywatnego Zscaler**.

    e. W **E-mail** pole tekstowe, wprowadź adres e-mail użytkownika, takich jak **BrittaSimon@contoso.com**.

    f. W **Phone** tekstowym, wpisz numer telefonu.

    g. W **strefy czasowej** pole tekstowe, wybierz strefę czasową.

    h. Kliknij pozycję **Zapisz**.  

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Zscaler prywatnego dostępu administratora.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta aby Administrator dostępu prywatnego Zscaler, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Administrator dostępu prywatnego Zscaler**.

    ![Administrator dostępu prywatnego Zscaler łącze na liście aplikacji](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Administrator dostępu prywatnego Zscaler w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji prywatnego Zscaler dostępu administratora.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

