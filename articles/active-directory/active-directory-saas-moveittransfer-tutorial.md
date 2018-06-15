---
title: 'Samouczek: Integracji Azure Active Directory z MOVEit Transfer — integracji z usługą Azure AD | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i MOVEit Transfer — integracji z usługą Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: f9bfd3ad884ad36026d56af514391c5f75a97e51
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347826"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Samouczek: Integracji Azure Active Directory z MOVEit Transfer — integracji z usługą Azure AD

Z tego samouczka dowiesz się integrowanie MOVEit Transfer — integracji z usługą Azure AD z usługą Azure Active Directory (Azure AD).

Integrowanie MOVEit Transfer — integracji z usługą Azure AD z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do transferu MOVEit - integracji z usługą Azure AD.
- Umożliwia użytkownikom automatycznie pobrać zalogowane transferem MOVEit - integracji usługi Azure AD (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z MOVEit Transfer — integracji z usługą Azure AD, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- MOVEit Transfer — usługi Azure AD integracji jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie MOVEit Transfer — integracji z usługą Azure AD z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Dodawanie MOVEit Transfer — integracji z usługą Azure AD z galerii
Aby skonfigurować integrację MOVEit Transfer — integracji usługi Azure AD do usługi Azure AD, należy dodać MOVEit Transfer — integracji usługi Azure AD z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać MOVEit Transfer — integracji z usługą Azure AD z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **MOVEit Transfer — integracji z usługą Azure AD**, wybierz pozycję **MOVEit Transfer — integracji z usługą Azure AD** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![MOVEit Transfer — integracji z usługą Azure AD na liście wyników](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z MOVEit Transfer — integracji z usługą Azure AD w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w MOVEit Transfer — integracji z usługą Azure AD jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w MOVEit Transfer — integracji z usługą Azure AD musi się.

W MOVEit Transfer — integracji z usługą Azure AD, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z MOVEit Transfer — integracji z usługą Azure AD, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie przeniesienia MOVEit - użytkownika testowego integracji usługi Azure AD](#create-a-moveit-transfer---azure-ad-integration-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta MOVEit Transfer — integracji z usługą Azure AD połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w transferu MOVEit - aplikacji integracji usługi Azure AD.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z MOVEit Transfer — integracji z usługą Azure AD, należy wykonać następujące czynności:**

1. W portalu Azure na **MOVEit Transfer — integracji z usługą Azure AD** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. Na **MOVEit Transfer — adresy URL i integracji z usługą Azure AD domeny** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://contoso.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://contoso.com/<tenatid>`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Może się odwoływać te wartości później w **adres URL metadanych dostawcy usługi** sekcji lub skontaktuj się z [MOVEit Transfer — zespołem pomocy technicznej klienta integracji usługi Azure AD](https://community.ipswitch.com/s/support) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Zaloguj się do dzierżawy MOVEit Transfer jako administrator.

7. W lewym okienku nawigacji, kliknij polecenie **ustawienia**.

    ![Strona aplikacji w sekcji Ustawienia](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Kliknij przycisk **pojedynczego logować** łącza, która znajduje się w **zasad zabezpieczeń -> uwierzytelniania użytkowników**.

    ![Strony aplikacji na zasady zabezpieczeń](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Kliknij łącze adres URL metadanych, aby pobrać dokumentu metadanych.

    ![Adres URL metadanych dostawcy usługi](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Sprawdź **entityID** odpowiada **identyfikator** w **MOVEit Transfer — integracji z usługą Azure AD domeny i adres URL** sekcji.
    * Sprawdź **AssertionConsumerService** odpowiada adresu URL lokalizacji **adres URL odpowiedzi** w **MOVEit Transfer — adresy URL i integracji z usługą Azure AD domeny** sekcji.
    
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Kliknij przycisk **Dodawanie dostawcy tożsamości** przycisk, aby dodać nowego dostawcę tożsamości federacyjnych.

    ![Dodaj dostawcę tożsamości](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Kliknij przycisk **Przeglądaj...**  i wybierz plik metadanych, który został pobrany z portalu Azure, następnie kliknij przycisk **Dodawanie dostawcy tożsamości** można przekazać pobranego pliku.

    ![Dostawca tożsamości SAML](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Wybierz opcję "**tak**" jako **włączone** w **ustawień dostawcy tożsamości federacyjnych edycji...**  i kliknij przycisk **zapisać**.

    ![Ustawienia dostawcy tożsamości federacyjnych](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. W **Edycja ustawień tożsamości federacyjnych dostawca użytkownika** wykonaj następujące czynności:
    
    ![Edytuj ustawienia dostawcy tożsamości federacyjnych](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Wybierz **SAML NameID** jako **nazwa logowania**.
    
    b. Wybierz **innych** jako **imię i nazwisko** i **nazwa atrybutu** pole tekstowe Podaj wartość: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Wybierz **innych** jako **E-mail** i **nazwa atrybutu** pole tekstowe Podaj wartość: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Wybierz **tak** jako **automatyczne tworzenie konta logowaniu**.
    
    e. Kliknij przycisk **zapisać** przycisku.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Tworzenie przeniesienia MOVEit - użytkownika testowego integracji usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w MOVEit Transfer — integracji z usługą Azure AD. MOVEit Transfer — integracji z usługą Azure AD obsługę just in time, które zostało włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu MOVEit Transfer — integracji usługi Azure AD, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [MOVEit Transfer — zespołem pomocy technicznej klienta integracji usługi Azure AD](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do transferu MOVEit - integracji z usługą Azure AD.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta MOVEit Transfer — integracji z usługą Azure AD, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **MOVEit Transfer — integracji z usługą Azure AD**.

    ![MOVEit Transfer — integracji z usługą Azure AD łącza na liście aplikacji](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu MOVEit Transfer — integracji z usługą Azure AD kafelka w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do transferu MOVEit - aplikacji integracji usługi Azure AD. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

