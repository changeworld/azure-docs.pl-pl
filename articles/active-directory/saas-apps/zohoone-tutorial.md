---
title: 'Samouczek: Azure Active Directory integracji z jednego Zoho | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Zoho jeden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 89f8df4e14bb93b1594cbb492a29c16b58d18785
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938544"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Samouczek: Azure Active Directory integracji z jednego Zoho

Z tego samouczka dowiesz się integrowanie Zoho jednego z usługi Azure Active Directory (Azure AD).

Integrowanie Zoho jednego z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do jednej Zoho.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do jednej Zoho (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z jednym Zoho, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Jeden Zoho logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Zoho jednego z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-zoho-one-from-the-gallery"></a>Dodawanie Zoho jednego z galerii
Aby skonfigurować integrację Zoho co w usłudze Azure Active Directory, należy dodać do listy zarządzane aplikacje SaaS Zoho jednego z galerii.

**Aby dodać Zoho jednego z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Zoho jeden**, wybierz pozycję **Zoho jeden** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Zoho jednego z listy wyników](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z jednego Zoho w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w jednym Zoho jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w jednym Zoho musi określone.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z jednego Zoho, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego jeden Zoho](#create-a-zoho-one-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Zoho jednego połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w jednym Zoho aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z jednego Zoho, wykonaj następujące czynności:**

1. W portalu Azure na **Zoho jeden** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

3. Na **Zoho jednej domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i Zoho jedną domenę pojedynczego logowania jednokrotnego informacji](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. W **identyfikator jednostki** tekstowym, wpisz adres URL: `one.zoho.com`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    d. W **stan przekazywania** tekstowym, wpisz adres URL:`https://one.zoho.com`

4. Jeśli chcesz skonfigurować aplikację w **SP** tryb inicjowane, wykonaj następujące kroki:

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Poprzedni **adres URL odpowiedzi** i **adres URL logowania** wartość nie jest prawdziwe. Wartość spowoduje zaktualizowanie o rzeczywisty adres URL odpowiedzi i URL logowania jednokrotnego, który znajduje się w dalszej części tego samouczka. 

5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/zohoone-tutorial/tutorial_general_400.png)
    
7. Na **Zoho jedną konfigurację** kliknij **skonfigurować jeden Zoho** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Zoho jednej konfiguracji](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do jednego Zoho witryny firmy.

9. Na **organizacji** karcie kliknij **Instalator** w obszarze **uwierzytelnianie SAML**.

    ![Zoho jednej organizacji](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

10. W oknie podręcznym strony wykonaj następujące czynności:

    ![Jeden Zoho SIG.](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. W **adres URL logowania** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    b. W **Sign-out URL** pole tekstowe, Wklej wartość **Sign-Out adres URL**, które zostały skopiowane z portalu Azure.

    c. Kliknij przycisk **Przeglądaj** przekazać **certyfikatu (Base64)** którego został pobrany z portalu Azure.

    d. Kliknij pozycję **Zapisz**.

11. Po zapisaniu ustawień uwierzytelnianie SAML, skopiuj **SAML Identfier** wartość, a następnie użyj tej wartości w **adres URL odpowiedzi** w portalu Azure w obszarze **Zoho jednej domeny i adres URL** sekcja.

    ![Jeden Zoho saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

12. Przejdź do **domen** a następnie kliknij pozycję **Dodawanie domeny**.

    ![Zoho jednej domenie](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

13. Na **Dodawanie domeny** wykonaj następujące czynności:

    ![Dodaj jeden Zoho domeny](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. W **nazwy domeny** pole tekstowe, typ domeny, takie jak **contoso.com**.

    b. Kliknij pozycję **Add** (Dodaj).

    >[!Note]
    >Po dodaniu wykonaj domeny [te](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroki, aby zweryfikować domenę. Po domena jest verfified, użyj nazwy domeny w **adres URL logowania** w **Zoho jednej domeny i adres URL** sekcji w portalu Azure.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zohoone-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/zohoone-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zohoone-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zohoone-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-zoho-one-test-user"></a>Tworzenie Zoho jednego użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD zalogować się do jednego Zoho, muszą mieć przydzielone do jednego Zoho. W jednym Zoho Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Zoho jeden jako Administrator zabezpieczeń.

2. Na **użytkowników** karcie, kliknij na **logo użytkownika**.

    ![Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na **Dodaj użytkownika** wykonaj następujące czynności:

    ![Dodaj Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. W **nazwa** tekst Wprowadź nazwę użytkownika, takich jak **Britta Simona**.
    
    b. W **adres E-mail** tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    >[!Note]
    >Wybierz domenę zweryfikowaną, z listy domen.

    c. Kliknij pozycję **Add** (Dodaj).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do jednej Zoho.

![Przypisanie roli użytkownika][200] 

**Aby przypisać jedną Zoho Simona Britta, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Zoho jeden**.

    ![Zoho jednego łącza na liście aplikacji](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu jednego Zoho kafelka w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane Zoho jednej aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

