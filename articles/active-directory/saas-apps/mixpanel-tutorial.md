---
title: 'Samouczek: Integracja usługi Azure Active Directory z Mixpanel | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: ebbd6efaa2acccebcfdafa5ccd7c945b588d763b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046522"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Samouczek: Integracja usługi Azure Active Directory z Mixpanel

W tym samouczku dowiesz się, jak zintegrować Mixpanel w usłudze Azure Active Directory (Azure AD).

Integrowanie Mixpanel z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Mixpanel
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Mixpanel (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Mixpanel, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Mixpanel logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Mixpanel z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-mixpanel-from-the-gallery"></a>Dodawanie Mixpanel z galerii
Aby skonfigurować integrację Mixpanel w usłudze Azure AD, należy dodać Mixpanel z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Mixpanel z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Mixpanel**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mixpanel-tutorial/tutorial_mixpanel_search.png)

5. W panelu wyników wybierz **Mixpanel**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Mixpanel w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika platformie Mixpanel do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika na platformie Mixpanel musi nawiązać.

Platformie Mixpanel, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Mixpanel, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Mixpanel](#creating-a-mixpanel-test-user)**  — aby odpowiednikiem Britta Simon platformie Mixpanel połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Mixpanel.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Mixpanel, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Mixpanel** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

3. Na **Mixpanel domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/mixpanel-tutorial/tutorial_mixpanel_url.png)

     W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://mixpanel.com/login/`

    > [!NOTE] 
    > Zarejestruj w [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) skonfigurować poświadczenia logowania i skontaktuj się z pomocą [zespołem pomocy technicznej Mixpanel](mailto:support@mixpanel.com) umożliwiające ustawień logowania jednokrotnego dla Twojej dzierżawy. Możesz również uzyskać swoją wartość na adres URL logowania w razie potrzeby z zespołem pomocy technicznej Mixpanel. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/mixpanel-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Mixpanel** , kliknij przycisk **skonfigurować Mixpanel** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/mixpanel-tutorial/tutorial_mixpanel_configure.png) 

7. W oknie innej przeglądarki logowanie jednokrotne do aplikacji Mixpanel jako administrator.

8. W dolnej części strony, kliknij polecenie małym **koło zębate** ikony w lewym rogu. 
   
    ![Mixpanel logowanie jednokrotne](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

9. Kliknij przycisk **dostęp zabezpieczeń** kartę, a następnie kliknij przycisk **zmiany ustawień**.
   
    ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

10. Na **zmienić certyfikat** strony okna dialogowego kliknij **Choose file** przekazać certyfikat pobrany, a następnie kliknij przycisk **dalej**.
   
    ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

11.  W polu tekstowym adres URL uwierzytelniania na **zmienić adres URL uwierzytelniania** okna dialogowego strony, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal, a następnie kliknij przycisk **Dalej**.
   
   ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

12. Kliknij przycisk **Gotowe**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mixpanel-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mixpanel-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mixpanel-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mixpanel-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-mixpanel-test-user"></a>Tworzenie użytkownika testowego Mixpanel

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon platformie Mixpanel. 

1. Zaloguj się do witryny firmy Mixpanel jako administrator.

2. W dolnej części strony kliknij mały przycisk z kołem zębatym w lewym rogu, aby otworzyć **ustawienia** okna.

3. Kliknij przycisk **zespołu** kartę.

4. W **członek zespołu** pole tekstowe, wpisz adres e-mail Britty na platformie Azure.
   
    ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Kliknij przycisk **zaprosić**. 

> [!Note]
> Użytkownik otrzyma wiadomość e-mail, aby skonfigurować profil.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Mixpanel.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Mixpanel, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Mixpanel**.

    ![Konfigurowanie logowania jednokrotnego](./media/mixpanel-tutorial/tutorial_mixpanel_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mixpanel w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Mixpanel.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/mixpanel-tutorial/tutorial_general_203.png

