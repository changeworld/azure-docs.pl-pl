---
title: 'Samouczek: Integracji Azure Active Directory z chmurą Creative Adobe | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Adobe Creative chmury.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 808debcee11eb83f7e6afa1f3443a35c9f6767c4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Samouczek: Integracji Azure Active Directory z chmurą Creative Adobe

Z tego samouczka dowiesz się integrowanie Adobe Creative chmurze z usługą Azure Active Directory (Azure AD).

Integrowanie Adobe Creative chmurze z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do programu Adobe Creative chmury.
- Umożliwia użytkownikom automatycznie pobrać zalogowane Adobe Creative chmurą (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z chmurą Creative Adobe, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Adobe Creative chmury logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Adobe Creative chmury z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Dodawanie Adobe Creative chmury z galerii
Aby skonfigurować integrację Adobe Creative chmury do usługi Azure AD, należy dodać Adobe Creative chmury z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Adobe Creative chmury z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **chmury Creative Adobe**, wybierz pozycję **chmury Creative Adobe** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Adobe Creative chmury na liście wyników](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Adobe Creative w chmurze na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w chmurze Creative Adobe jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w chmurze Creative Adobe musi się.

W chmurze Creative Adobe, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z chmurą Creative Adobe, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego chmury Creative Adobe](#create-an-adobe-creative-cloud-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Adobe Creative chmurze, która jest połączona z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji w chmurze Creative Adobe.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z chmurą Creative Adobe, wykonaj następujące czynności:**

1. W portalu Azure na **chmury Creative Adobe** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Na **adresy URL i Adobe Creative domenę chmury** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w trybie IDP inicjowane:

    ![Adresy URL i Adobe Creative domenę chmury pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.okta.com/saml2/service-provider/<token>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej Adobe Creative Cloud Client](https://helpx.adobe.com/in/contact/support.html) uzyskać te wartości. 

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i Adobe Creative domenę chmury pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    W **adres URL logowania** tekstowym, wpisz wartość, jak: `https://adobe.com`

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Aplikacji w chmurze Creative Adobe oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybut użytkownika** aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| ----------------|
    | Imię |user.givenname |
    | Nazwisko |user.surname |
    | Email |User.mail |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.
    
8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. Na **Adobe Creative konfiguracji chmury** kliknij **Konfigurowanie chmury Creative Adobe** otworzyć **konfigurowania rejestracji** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami**.

    ![Konfiguracja chmury Creative Adobe](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. W oknie przeglądarki innej witryny sieci web, zaloguj się do [konsoli administracyjnej programu Adobe](https://adminconsole.adobe.com) jako administrator.

11. Przejdź do **ustawienia** w górnym menu nawigacyjnym pasek, a następnie wybierz pozycję **tożsamości**. Otwiera listy domen. Kliknij przycisk **Konfiguruj** łącze w domenie. Następnie wykonaj następujące czynności na **pojedynczy znak na wymagana konfiguracja** sekcji. Aby uzyskać więcej informacji, zobacz [ustawienia domeny](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Ustawienia](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "ustawienia")
    
    a. Kliknij przycisk **Przeglądaj** można przekazać certyfikatu pobrane z usługi Azure AD do **certyfikatu IDP**.
    
    b. W **wystawcy IDP** pole tekstowe, umieścić wartość elementu **identyfikator jednostki SAML** którego skopiowany z **Konfigurowanie logowania jednokrotnego** sekcji w portalu Azure.
    
    c. W **adres URL logowania IDP** pole tekstowe, umieścić wartość elementu **adres URL usługi logowania jednokrotnego SAML** , które zostały skopiowane z **Konfigurowanie logowania jednokrotnego** sekcji w portalu Azure.
    
    d. Wybierz **HTTP - przekierowania** jako **powiązania IDP**.
    
    e. Wybierz **adres E-mail** jako **ustawienia logowania użytkownika**.
    
    f. Kliknij przycisk **zapisać** przycisku.

12. Pulpit nawigacyjny teraz przedstawi XML **"Pobieranie metadanych"** pliku. Zawiera adres URL EntityDescriptor Adobe i AssertionConsumerService adresu URL. Otwórz plik i skonfigurować je w aplikacji usługi Azure AD.

    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Użyj wartości EntityDescriptor Adobe dostarczył dla **identyfikator** na **Konfigurowanie ustawień aplikacji** okna dialogowego.

    b. Użyj wartości AssertionConsumerService Adobe dostarczył dla **adres URL odpowiedzi** na **Konfigurowanie ustawień aplikacji** okna dialogowego.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Tworzenie użytkownika testowego Adobe Creative chmury

Aby włączyć użytkowników usługi Azure AD zalogować się do chmury Creative Adobe, muszą mieć przydzielone do programu Adobe Creative chmury. W przypadku Adobe Creative chmury Inicjowanie obsługi to zadanie ręczne.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby udostępnić konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się do [konsoli administracyjnej programu Adobe](https://adminconsole.adobe.com) lokacji jako administrator.

2. Dodaj użytkownika w ramach konsoli Adobe jako identyfikator usługi federacyjnej i przypisać je do profilu produktu. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników, zobacz [Dodawanie użytkowników w konsoli administracyjnej programu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. W tym momencie wpisz Twojej adres e-mail/upn do postaci rejestrowanie Adobe, naciśnij klawisz tab, i powinien federacyjną do usługi Azure AD:
    * Dostęp w sieci Web: www.adobe.com > logowania
    * W narzędziu aplikacji komputerowej > logowania
    * W aplikacji > Pomoc > logowania

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Adobe Creative chmury.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Adobe Creative chmury, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **chmury Creative Adobe**.

    ![Łącze Adobe Creative chmury na liście aplikacji](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Adobe Creative chmury w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji w chmurze Creative Adobe.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie domeny (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Konfigurowanie usługi Azure do użycia z logowania jednokrotnego Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

