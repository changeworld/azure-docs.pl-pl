---
title: 'Samouczek: Azure Active Directory integracji z łuk publikowania - logowania jednokrotnego | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i łuk publikowania - logowania jednokrotnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 26e80153438eb9894753c74b81d6622c7b4c7b14
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34337915"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Samouczek: Azure Active Directory integracji z łuk publikowania — Usługa rejestracji Jednokrotnej

Z tego samouczka dowiesz się integrowanie łuk publikowania — Usługa rejestracji Jednokrotnej w usłudze Azure Active Directory (Azure AD).

Integrowanie publikowanie łuk — logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do łuk publikowania - logowania jednokrotnego.
- Umożliwia użytkownikom automatycznie pobrać zalogowane na łuk publikowania - SSO (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z łuk publikowania — logowanie Jednokrotne, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Publikowanie łuk — logowanie Jednokrotne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie publikowanie łuk — Usługa rejestracji Jednokrotnej z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Dodawanie publikowanie łuk — Usługa rejestracji Jednokrotnej z galerii
Aby skonfigurować integrację łuk publikowania - logowania jednokrotnego do usługi Azure AD, należy dodać łuk publikowania - rejestracji Jednokrotnej z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać łuk publikowania — Usługa rejestracji Jednokrotnej z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **łuk publikowania - logowania jednokrotnego**, wybierz pozycję **łuk publikowania - logowania jednokrotnego** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Publikowanie łuk - rejestracji Jednokrotnej z listy wyników](./media/active-directory-saas-arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z łuk publikowania — Usługa rejestracji Jednokrotnej w oparciu o nazwie "Britta Simona" użytkownika testowego.

Logowanie jednokrotne do pracy usługi Azure AD musi znać użytkownika odpowiednika w publikacji łuk — Usługa rejestracji Jednokrotnej jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w publikacji łuk — logowania jednokrotnego musi się.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z publikowania łuk — logowanie Jednokrotne, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Utwórz publikowania łuk — użytkownik testowy logowania jednokrotnego](#create-an-arc-publishing---sso-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta łuk publikowania - logowania jednokrotnego połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci łuk publikowania — Usługa rejestracji Jednokrotnej w aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z łuk publikowania — logowanie Jednokrotne, należy wykonać następujące czynności:**

1. W portalu Azure na **łuk publikowania - logowania jednokrotnego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-arc-tutorial/tutorial_arc_samlbase.png)

3. Na **łuk publikowania — domena logowania jednokrotnego i adresy URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Publikowanie łuk — domena logowania jednokrotnego i adresów URL jednym logowania jednokrotnego informacji](./media/active-directory-saas-arc-tutorial/tutorial_arc_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Publikowanie łuk — domena logowania jednokrotnego i adresów URL jednym logowania jednokrotnego informacji](./media/active-directory-saas-arc-tutorial/tutorial_arc_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [łuk publikowania - zespołem pomocy technicznej klienta logowania jednokrotnego](mailto:inf@washpost.com) uzyskać te wartości. 

5. Publikowanie łuk — aplikacja rejestracji Jednokrotnej oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-arc-tutorial/tutorial_arc_attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | Imię | user.givenname |
    | Nazwisko | user.surname |
    | wyślij wiadomość e-mail | User.mail |
    | grupy | User.assignedroles |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

     ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-arc-tutorial/tutorial_attribute_04.png)

     ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-arc-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    d. Kliknij przycisk **Ok**

    > [!NOTE]
    > W tym miejscu **grup** atrybutu jest zamapowana z **user.assignedroles**. Są to role niestandardowe utworzone w usłudze Azure AD do mapowania nazwy grupy w aplikacji. Można znaleźć więcej wskazówek [tutaj](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) na temat tworzenia niestandardowych ról w usłudze Azure AD. 

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-arc-tutorial/tutorial_arc_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-arc-tutorial/tutorial_general_400.png)
    
9. Na **łuk publikowania — Konfiguracja logowania jednokrotnego** , kliknij przycisk **skonfigurować łuk publikowania - logowania jednokrotnego** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Publikowanie łuk — Konfiguracja logowania jednokrotnego](./media/active-directory-saas-arc-tutorial/tutorial_arc_configure.png) 

10. Skonfigurować logowanie jednokrotne w **łuk publikowania - logowania jednokrotnego** stronie, musisz wysłać pobrany **certyfikatu (Base64), adres URL Sign-Out identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [łuk Publikowanie - zespołem pomocy technicznej usługi logowania jednokrotnego](mailto:inf@washpost.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-arc-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-arc-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-arc-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-arc-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Utwórz łuk publikowania - logowania jednokrotnego użytkownika testowego

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w łuk publikowania - logowania jednokrotnego. Publikowanie łuk — logowania jednokrotnego obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu łuk publikowania - logowania jednokrotnego, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [łuk publikowania - zespołem pomocy technicznej usługi logowania jednokrotnego](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu łuk publikowania - logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta łuk publikowania — logowanie Jednokrotne, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **łuk publikowania - logowania jednokrotnego**.

    ![Publikowanie łuk - logowania jednokrotnego łącza na liście aplikacji](./media/active-directory-saas-arc-tutorial/tutorial_arc_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu przycisku łuk publikowania - kafelka logowania jednokrotnego w panelu dostępu należy należy pobrać automatycznie zalogowane do Twojej łuk publikowania — Usługa rejestracji Jednokrotnej w aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arc-tutorial/tutorial_general_203.png

