---
title: 'Samouczek: Integracji Azure Active Directory z TOPdesk - publicznego | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i TOPdesk - publicznego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 76b75ffde280047f8ed6c2d4173e905df1d7a658
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352076"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Samouczek: Integracji Azure Active Directory z TOPdesk - publicznego

Z tego samouczka dowiesz się integrowanie TOPdesk - publicznego w usłudze Azure Active Directory (Azure AD).

Integrowanie TOPdesk - publicznego z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do TOPdesk - publicznego.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do TOPdesk — publiczny (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z TOPdesk - publiczny, potrzebne są następujące zasoby:

- Subskrypcję usługi Azure AD
- TOPdesk — publiczny jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie TOPdesk - publicznego z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-topdesk---public-from-the-gallery"></a>Dodawanie TOPdesk - publicznego z galerii
Aby skonfigurować integrację TOPdesk - publicznych do usługi Azure AD, należy dodać TOPdesk - publicznego z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać TOPdesk - publicznego z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **TOPdesk - publicznego**, wybierz pozycję **TOPdesk - publicznego** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TOPdesk - publiczny, na liście wyników](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TOPdesk — publiczny w oparciu o nazwie "Britta Simona" użytkownika testowego.

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, jaki użytkownik odpowiednika w TOPdesk — publiczny jest użytkownikiem w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w TOPdesk - publicznego musi się.

W TOPdesk - publiczny, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TOPdesk - publiczny, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Utwórz TOPdesk - użytkownika testowego publicznego](#create-a-topdesk---public-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta TOPdesk - publicznego połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci TOPdesk - publicznych aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z TOPdesk - publiczny, należy wykonać następujące czynności:**

1. W portalu Azure na **TOPdesk - publicznego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

3. Na **TOPdesk - domeny publicznej i adres URL** sekcji, wykonaj następujące czynności:

    ![TOPdesk - domeny publicznej i adresów URL jednym informacje logowania jednokrotnego](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net`
    
    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Adres URL odpowiedzi jest explaned później w samouczku. Skontaktuj się z [TOPdesk - zespołem pomocy technicznej klienta publicznego](https://help.topdesk.com/saas/enterprise/user/) uzyskać te wartości.  

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_400.png)
    
6. Na **TOPdesk - publicznej konfiguracji** , kliknij przycisk **skonfigurować TOPdesk - publicznego** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![TOPdesk - publicznej konfiguracji](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

7. Zaloguj się na Twojej **TOPdesk - publicznego** witryny firmy jako administrator.

8. W **TOPdesk** menu, kliknij przycisk **ustawienia**.
   
    ![Ustawienia](./media/active-directory-saas-topdesk-public-tutorial/ic790598.png "ustawienia")

9. Kliknij przycisk **ustawienia logowania**.
   
    ![Ustawienia logowania](./media/active-directory-saas-topdesk-public-tutorial/ic790599.png "ustawienia logowania")

10. Rozwiń węzeł **ustawienia logowania** menu, a następnie kliknij przycisk **ogólne**.
   
    ![Ogólne](./media/active-directory-saas-topdesk-public-tutorial/ic790600.png "ogólne")

11. W **publicznego** sekcji **logowania SAML** konfiguracji sekcji, wykonaj następujące czynności:
   
    ![Ustawienia techniczne](./media/active-directory-saas-topdesk-public-tutorial/ic790601.png "ustawienia techniczne")
   
    a. Kliknij przycisk **Pobierz** można pobrać pliku metadanych publicznego, a następnie zapisz go lokalnie na komputerze.
   
    b. Otwórz plik metadanych pobranych, a następnie zlokalizuj **AssertionConsumerService** węzła.

    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiuj **AssertionConsumerService** wartość, wklej tę wartość w **adres URL odpowiedzi służący** textbox w **TOPdesk - domeny publicznej i adres URL** sekcji.      
   
12. Aby utworzyć plik certyfikatu, wykonaj następujące czynności:
    
    ![Certyfikat](./media/active-directory-saas-topdesk-public-tutorial/ic790606.png "certyfikatu")
    
    a. Otwórz plik metadanych pobranych z portalu Azure.
    
    b. Rozwiń węzeł **RoleDescriptor** węzła, który ma **xsi: type** z **przekazywani: ApplicationServiceType**.
    
    c. Skopiuj wartość **X509Certificate** węzła.
    
    d. Zapisz skopiowanych **X509Certificate** wartość lokalnie na komputerze w pliku.

13. W **publicznego** kliknij **Dodaj**.
    
    ![Logowania SAML](./media/active-directory-saas-topdesk-public-tutorial/ic790625.png "logowania SAML")

14. Na **Asystenta konfiguracji SAML** okna dialogowego strony, należy wykonać następujące czynności:
    
    ![Asystent konfiguracji SAML](./media/active-directory-saas-topdesk-public-tutorial/ic790608.png "Asystenta konfiguracji SAML")
    
    a. Aby przesłać plik metadanych pobranych z portalu Azure, w obszarze **metadanych Federacji**, kliknij przycisk **Przeglądaj**.

    b. Aby przesłać plik certyfikatu, w obszarze **certyfikatu (RSA)**, kliknij przycisk **Przeglądaj**.

    c. Aby przekazać plik logo uzyskano z zespołem pomocy technicznej TOPdesk, w obszarze **ikona Logo**, kliknij przycisk **Przeglądaj**.

    d. W **atrybutu nazwy użytkownika** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. W **Nazwa wyświetlana** tekstowym, wpisz nazwę dla danej konfiguracji.

    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-topdesk---public-test-user"></a>Utwórz TOPdesk - użytkownika testowego publiczny

Aby włączyć użytkowników usługi Azure AD zalogować się do TOPdesk - publiczny, muszą mieć przydzielone do TOPdesk - publicznego.  
W przypadku TOPdesk - publiczny, inicjowanie obsługi to zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:
1. Zaloguj się na Twojej **TOPdesk - publicznego** witryny firmy jako administrator.

2. W menu u góry kliknij **TOPdesk \> nowy \> pliki obsługi \> osoby**.
   
    ![Osoba](./media/active-directory-saas-topdesk-public-tutorial/ic790628.png "osoby")

3. W oknie dialogowym nowej osoby wykonaj następujące czynności:
   
    ![Nowej osoby](./media/active-directory-saas-topdesk-public-tutorial/ic790629.png "nowej osoby")
   
    a. Kliknij kartę Ogólne.

    b. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak Simona
 
    c. Wybierz **lokacji** dla konta.
 
    d. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Można użyć dowolnego innego TOPdesk — narzędzia do tworzenia konta użytkownika publicznego lub interfejsów API dostarczonych przez TOPdesk - publicznego do udostępnienia konta użytkownika usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu TOPdesk - publicznego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta TOPdesk - publiczny, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **TOPdesk - publicznego**.

    ![TOPdesk — publiczny łącza na liście aplikacji](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu TOPdesk - publicznego kafelka w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowane do Twojej TOPdesk - publicznych aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_203.png

