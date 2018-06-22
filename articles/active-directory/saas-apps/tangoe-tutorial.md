---
title: 'Samouczek: Integracji Azure Active Directory z Tangoe polecenia Premium Mobile | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Tangoe polecenie Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 42d1b40e01653e8fb5fa5bf118c65de758832f4d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308612"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Samouczek: Integracji Azure Active Directory z Tangoe polecenia Premium Mobile

Z tego samouczka dowiesz się integrowanie Tangoe polecenia Premium Mobile w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Tangoe polecenia Premium Mobile zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Tangoe polecenia Premium Mobile
- Umożliwia użytkownikom automatycznie pobrać zalogowane Tangoe polecenia Premium powyżej (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Tangoe polecenia Premium Mobile, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Mobile Premium polecenia Tangoe logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodaj Mobile Premium polecenie Tangoe z galerii
2. Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Dodaj Mobile Premium polecenie Tangoe z galerii
Aby skonfigurować integrację Tangoe polecenia Premium Mobile w usłudze Azure Active Directory, należy dodać Mobile Premium polecenie Tangoe z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Mobile Premium polecenie Tangoe z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Tangoe polecenia Premium Mobile**, wybierz pozycję **Tangoe polecenia Premium Mobile** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Dodaj Mobile Premium polecenie Tangoe z galerii ](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Mobile Premium polecenia Tangoe w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w Tangoe polecenia Premium Mobile do użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Tangoe polecenia Premium Mobile.

W Tangoe polecenia Premium urządzeń przenośnych, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Tangoe polecenia Premium Mobile, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Mobile Premium polecenia Tangoe](#create-a-tangoe-command-premium-mobile-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Tangoe polecenia Premium Mobile połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji mobilnych — wersja Premium polecenia Tangoe.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Tangoe polecenia Premium Mobile, wykonaj następujące czynności:**

1. W portalu Azure na **Tangoe polecenia Premium Mobile** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Logowanie na podstawie protokołu SAML](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

3. Na **Tangoe polecenia Premium Mobile domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Polecenie Tangoe Premium przenośnych domeny i adres URL](./media/tangoe-tutorial/tutorial_tangoe_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Mobile Premium polecenia Tangoe](https://www.tangoe.com/contact-us/) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Sekcja certyfikat podpisywania SAML](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Przyciskiem Zapisz](./media/tangoe-tutorial/tutorial_general_400.png)
    
6. Na **Tangoe polecenia Premium Mobile konfiguracji** , kliknij przycisk **skonfigurować Mobile Premium polecenia Tangoe** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Sekcja konfiguracji Mobile Premium polecenia Tangoe](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

7. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, skontaktuj się z z [zespołem pomocy technicznej klienta Mobile Premium polecenia Tangoe](https://www.tangoe.com/contact-us/) i zapewnia następujące korzyści:

   - Plik metadanych pobranych
   - **Identyfikator jednostki SAML**
   - **Adres URL usługi rejestracji jednokrotnej SAML**
   - **Adresu URL wylogowania**

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tangoe-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Użytkownicy i grupy -> Wszyscy użytkownicy](./media/tangoe-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Dodawanie użytkownika](./media/tangoe-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Strony okna dialogowego użytkownika](./media/tangoe-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Tworzenie użytkownika testowego Tangoe polecenia Premium Mobile

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Tangoe polecenia Premium Mobile. 

Aplikacji mobilnych — wersja Premium polecenia Tangoe musi wszystkich użytkowników na potrzeby aprowizacji w aplikacji przed wykonaniem rejestracji jednokrotnej. Pracy, dlatego należy z [zespołem pomocy technicznej klienta Mobile Premium polecenia Tangoe](https://www.tangoe.com/contact-us/) do udostępnienia tych użytkowników do aplikacji. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Tangoe polecenia Premium Mobile.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Tangoe polecenia Premium Mobile, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Tangoe polecenia Premium Mobile**.

    ![Tangoe polecenia Premium Mobile listy aplikacji](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka Tangoe polecenia Premium Mobile w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji mobilnych — wersja Premium polecenia Tangoe. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

