---
title: 'Samouczek: Integracji Azure Active Directory z Velpic SAML | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 03ef1ef04d80ac9bb83bcce2082b6cc3f617d812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Samouczek: Integracji Azure Active Directory z Velpic SAML

W tym samouczku Dowiedz się jak integrację Velpic SAML w usłudze Azure Active Directory (Azure AD).

Integrację Velpic SAML z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Velpic SAML
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Velpic SAML (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Velpic SAML, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Velpic SAML jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Velpic SAML z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-velpic-saml-from-the-gallery"></a>Dodawanie Velpic SAML z galerii
Aby skonfigurować integrację Velpic SAML do usługi Azure AD, należy dodać Velpic SAML z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Velpic SAML z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Velpic SAML**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. W panelu wyników wybierz **Velpic SAML**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z Velpic SAML w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Velpic SAML jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Velpic SAML.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Velpic SAML.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Velpic SAML, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Velpic SAML](#creating-a-velpic-saml-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SAML Velpic, połączonej z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w aplikacji Velpic SAML.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego Velpic SAML, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **Velpic SAML** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Wprowadź szczegóły w **Velpic SAML domeny i adres URL** sekcji -

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. W **adres URL logowania** tekstowym, wpisz wartość, jak: `https://<sub-domain>.velpicsaml.net`

    b. W **identyfikator** pole tekstowe, Wklej **"Rejestracja jednokrotna w adresie URL"** wartość `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Należy pamiętać, że znaku w adresie URL, które będą udostępniane przez zespół Velpic SAML i wartość identyfikatora będzie dostępna podczas konfigurowania rejestracji Jednokrotnej wtyczki po stronie Velpic SAML. Musisz skopiować tę wartość ze strony aplikacji Velpic SAML i wklej go w tym miejscu.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. W sekcji konfiguracyjnej SAML Velpic kliknij przycisk Konfiguruj Velpic SAML, można otworzyć Konfigurowanie logowania jednokrotnego okna. Skopiuj identyfikator jednostki SAML z sekcji krótkimi opisami.

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Velpic SAML jako administrator.

8. Polecenie **Zarządzaj** karcie i przejdź do **integracji** sekcji, w którym należy kliknąć pozycję **wtyczek** przycisk, aby utworzyć nowej wtyczki dla logowania.

    ![Wtyczki](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. Polecenie **"Dodawanie wtyczki"** przycisku.
    
    ![Wtyczki](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. Polecenie **SAML** kafelka na stronie Dodawanie wtyczki.
    
    ![Wtyczki](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. Wprowadź nazwę nowej wtyczki SAML i kliknij przycisk **"Dodaj"** przycisku.

    ![Wtyczki](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. Wprowadź szczegóły w następujący sposób:

    ![Wtyczki](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. W **nazwa** tekstowym, wpisz nazwę wtyczki SAML.

    b. W **adres URL wystawcy** pole tekstowe, Wklej **identyfikator jednostki SAML** skopiowane z **Konfigurowanie logowania jednokrotnego** okna portalu Azure.

    c. W **dostawcy metadanych konfiguracji** przekazać plik XML metadanych, który został pobrany z portalu Azure.

    d. Można również włączyć tylko w czasie inicjowania obsługi administracyjnej, należy włączyć SAML **"Automatyczne tworzenie nowych użytkowników"** pola wyboru. Jeśli użytkownik nie istnieje w Velpic, ta flaga nie jest włączone logowanie z Azure zakończy się niepowodzeniem. Jeśli włączono flagę użytkownika będą automatycznie udostępniane w Velpic podczas logowania. 

    e. Kopiuj **Rejestracja jednokrotna w adresie URL** z tekstu pola i wklej go w portalu Azure.
    
    f. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Tworzenie użytkownika testowego Velpic SAML

Ten krok zazwyczaj nie jest wymagana aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników. Jeśli nie włączono obsługi automatycznego użytkownika tworzenie ręczne użytkownika można wykonać zgodnie z poniższym opisem.

Zaloguj się do witryny firmy Velpic SAML jako administrator i wykonać następujące kroki:
    
1. Kliknij na karcie zarządzania i przejdź do sekcji użytkownicy, a następnie kliknij przycisk Nowa, aby dodać użytkowników.

    ![Dodaj użytkownika](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. Na **"Tworzenie nowego użytkownika"** okna dialogowego wykonaj następujące kroki.

    ![Użytkownik](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. W **imię** pole tekstowe, nazwa typu pierwszy Simona Britta.

    b. W **nazwisko** tekstowym, wpisz nazwisko Simona Britta.

    c. W **nazwy użytkownika** tekstowym, wpisz nazwę użytkownika Simona Britta.

    d. W **E-mail** tekstowym, wpisz adres e-mail konta Simona Britta.

    e. Pozostała część informacji jest opcjonalne, można go wypełnić, w razie potrzeby.
    
    f. Kliknij przycisk **SAVE** (Zapisz).  

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu jej Velpic SAML.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Velpic SAML, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Velpic SAML**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

1. Po kliknięciu kafelka Velpic SAML w panelu dostępu, należy pobrać strony logowania Velpic SAML aplikacji. Powinny pojawić się **"Zaloguj się za pomocą usługi Azure AD"** przycisk na stronie logowania.

    ![Wtyczki](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. Polecenie **"Zaloguj się za pomocą usługi Azure AD"** przycisk, aby zalogować się do Velpic przy użyciu konta usługi Azure AD.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png

