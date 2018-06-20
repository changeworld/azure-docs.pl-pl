---
title: 'Samouczek: Azure Active Directory integracji z zabezpieczeniami Menlo | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Menlo zabezpieczeń.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9e63fe6b-0ad0-405d-9e41-6a1a40a41df8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: jeedes
ms.openlocfilehash: 1800c6be30af57707ef2a0f06077ea5a0634124d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221567"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>Samouczek: Azure Active Directory integracji z zabezpieczeniami Menlo

Z tego samouczka dowiesz się integrowanie Menlo zabezpieczeń w usłudze Azure Active Directory (Azure AD).

Integrowanie Menlo zabezpieczeń z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Menlo zabezpieczeń
- Umożliwia użytkownikom automatycznie pobrać zalogowane zabezpieczeń Menlo (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz. [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z zabezpieczeniami Menlo, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zabezpieczenia Menlo jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie zabezpieczeń Menlo z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-menlo-security-from-the-gallery"></a>Dodawanie zabezpieczeń Menlo z galerii
Aby skonfigurować integrację Menlo zabezpieczeń w usłudze Azure Active Directory, należy dodać Menlo zabezpieczeń z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Menlo zabezpieczeń z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **zabezpieczeń Menlo**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/menlosecurity-tutorial/tutorial_menlosecurity_search.png)

5. W panelu wyników wybierz **zabezpieczeń Menlo**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/menlosecurity-tutorial/tutorial_menlosecurity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Menlo zabezpieczenia oparte na użytkownika testowego o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w Menlo zabezpieczeń do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi zabezpieczeń Menlo musi określone.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** Menlo zabezpieczeń.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z zabezpieczeniami Menlo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego zabezpieczeń Menlo](#creating-a-menlo-security-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Menlo zabezpieczeń, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Menlo zabezpieczeń.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Menlo zabezpieczeń, wykonaj następujące czynności:**

1. W portalu Azure na **zabezpieczeń Menlo** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/menlosecurity-tutorial/tutorial_menlosecurity_samlbase.png)

3. Na **Menlo zabezpieczeń domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/menlosecurity-tutorial/tutorial_menlosecurity_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.menlosecurity.com/account/login`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.menlosecurity.com/safeview-auth-server/saml/metadata`

    > [!NOTE] 
    > Wartości te nie są rzeczywistych. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta zabezpieczeń Menlo](https://www.menlosecurity.com/menlo-contact) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/menlosecurity-tutorial/tutorial_menlosecurity_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/menlosecurity-tutorial/tutorial_general_400.png)

6. Na **konfiguracji zabezpieczeń Menlo** , kliknij przycisk **Konfigurowanie zabezpieczeń Menlo** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki**, i **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/menlosecurity-tutorial/tutorial_menlosecurity_configure.png) 

7. Aby skonfigurować logowanie jednokrotne w **zabezpieczeń Menlo** strona, zaloguj się do **Menlo zabezpieczeń** witryny sieci Web jako administrator.

8. W obszarze **ustawienia** przejdź do **uwierzytelniania** i wykonywanie następujących czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/menlosecurity-tutorial/menlo_user_setup.png)

    a. Zaznacz pole wyboru **Włącz uwierzytelnianie użytkowników przy użyciu SAML**.

    b. Wybierz **dostęp do zewnętrznych** do **tak**.

    c. W obszarze **SAML dostawcy**, wybierz pozycję **usługi Azure Active Directory**.

    d. **SAML 2.0 Endpoint** : Wklej **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    e. **Identyfikator usługi (Wystawca)** : Wklej **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.

    f. **Certyfikat X.509** : Otwórz **certyfikatu (Base64)** pobrany z portalu Azure w programie Notatnik i wklej go w tym polu.

    g. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/menlosecurity-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/menlosecurity-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/menlosecurity-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/menlosecurity-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-menlo-security-test-user"></a>Tworzenie użytkownika testowego Menlo zabezpieczeń
 
W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta Menlo zabezpieczeń. Praca z [zespołem pomocy technicznej klienta zabezpieczeń Menlo](https://www.menlosecurity.com/menlo-contact) Aby dodać użytkowników do platformy Menlo zabezpieczeń. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Menlo zabezpieczeń.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Menlo zabezpieczeń, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **zabezpieczeń Menlo**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/menlosecurity-tutorial/tutorial_menlosecurity_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego.

Otwórz okno przeglądarki w trybie "Sesję InPrivate" lub "Incognito" Aby wyzwolić nowego uwierzytelniania.  W programie Internet Explorer użyj klawiszy Ctrl + Shift + P.  W przeglądarce Chrome użyj klawiszy Ctrl + Shift + N.  W oknie przeglądania prywatne przejdź do chronionych zasobów i wykonywać logowania usługi Azure AD.  Po pomyślnym logowaniu nastąpi przekierowanie do żądanej witryny w sesji izolacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/menlosecurity-tutorial/tutorial_general_01.png
[2]: ./media/menlosecurity-tutorial/tutorial_general_02.png
[3]: ./media/menlosecurity-tutorial/tutorial_general_03.png
[4]: ./media/menlosecurity-tutorial/tutorial_general_04.png

[100]: ./media/menlosecurity-tutorial/tutorial_general_100.png

[200]: ./media/menlosecurity-tutorial/tutorial_general_200.png
[201]: ./media/menlosecurity-tutorial/tutorial_general_201.png
[202]: ./media/menlosecurity-tutorial/tutorial_general_202.png
[203]: ./media/menlosecurity-tutorial/tutorial_general_203.png

