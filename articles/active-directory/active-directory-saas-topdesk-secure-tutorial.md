---
title: 'Samouczek: Integracji Azure Active Directory z TOPdesk - bezpiecznego | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i TOPdesk - bezpieczny.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 34296313753eb795bee5d93a8d8f1c8ae5efac9f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Samouczek: Integracji Azure Active Directory z TOPdesk - bezpieczne

W tym samouczku opisano sposób integracji TOPdesk - zabezpieczyć za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie TOPdesk - bezpiecznego z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do TOPdesk - bezpieczny.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do TOPdesk - bezpiecznego (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z TOPdesk - bezpieczny, potrzebne następujące elementy:

- Subskrypcję usługi Azure AD
- A TOPdesk - bezpieczne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie TOPdesk - bezpieczny z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-topdesk---secure-from-the-gallery"></a>Dodawanie TOPdesk - bezpieczny z galerii
Aby skonfigurować integrację TOPdesk - bezpieczny do usługi Azure AD, należy dodać TOPdesk — bezpieczny z galerii do listy SaaS zarządzanych aplikacji.

**Aby dodać TOPdesk - bezpieczny z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **TOPdesk - bezpiecznego**, wybierz pozycję **TOPdesk - bezpiecznego** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TOPdesk - Secure na liście wyników](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TOPdesk — bezpieczne w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w TOPdesk - bezpieczny do użytkownika w usłudze Azure AD. Innymi słowy link relację między użytkownika usługi Azure AD i danemu użytkownikowi w TOPdesk - bezpiecznego powinien być określony.

W TOPdesk - bezpieczny, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TOPdesk - bezpieczny, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Utwórz TOPdesk - użytkownika testowego bezpiecznego](#create-a-topdesk---secure-test-user)**  — aby odpowiednikiem Simona Britta w TOPdesk - bezpiecznego połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci TOPdesk - zabezpieczonej aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z TOPdesk - bezpieczny, wykonaj następujące czynności:**

1. W portalu Azure na **TOPdesk - bezpiecznego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. Na **TOPdesk - Secure domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Informacje logowania z jednym TOPdesk - bezpiecznej domeny i adres URL](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Adres URL odpowiedzi znajduje się w dalszej części samouczka. Skontaktuj się z [TOPdesk - zespołem pomocy technicznej klienta Secure](http://www.topdesk.com/us/support) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_400.png)

6. Na **TOPdesk - Secure konfiguracji** , kliknij przycisk **TOPdesk skonfigurować - bezpiecznego** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![TOPdesk - bezpiecznej konfiguracji](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. Zaloguj się na Twojej **TOPdesk - bezpiecznego** witryny firmy jako administrator.

8. W **TOPdesk** menu, kliknij przycisk **ustawienia**.

    ![Ustawienia](./media/active-directory-saas-topdesk-secure-tutorial/ic790598.png "ustawienia")

9. Kliknij przycisk **ustawienia logowania**.

    ![Ustawienia logowania](./media/active-directory-saas-topdesk-secure-tutorial/ic790599.png "ustawienia logowania")

10. Rozwiń węzeł **ustawienia logowania** menu, a następnie kliknij przycisk **ogólne**.

    ![Ogólne](./media/active-directory-saas-topdesk-secure-tutorial/ic790600.png "ogólne")

11. W **bezpiecznego** sekcji **logowania SAML** konfiguracji sekcji, wykonaj następujące czynności:

    ![Ustawienia techniczne](./media/active-directory-saas-topdesk-secure-tutorial/ic790855.png "ustawienia techniczne")
   
    a. Kliknij przycisk **Pobierz** można pobrać pliku metadanych publicznego, a następnie zapisz go lokalnie na komputerze.
   
    b. Otwórz plik metadanych, a następnie zlokalizuj **AssertionConsumerService** węzła.
    
    ![Usługa konsumenta potwierdzenia](./media/active-directory-saas-topdesk-secure-tutorial/ic790856.png "potwierdzenia klienta usługi")
   
    c. Kopiuj **AssertionConsumerService** wartość, wklej tę wartość w polu tekstowym adres URL odpowiedzi w **TOPdesk - Secure domeny i adres URL** sekcji.

12. Aby utworzyć plik certyfikatu, wykonaj następujące czynności:
    
    ![Certyfikat](./media/active-directory-saas-topdesk-secure-tutorial/ic790606.png "certyfikatu")
    
    a. Otwórz plik metadanych pobranych z portalu Azure.

    b. Rozwiń węzeł **RoleDescriptor** węzła, który ma **xsi: type** z **przekazywani: ApplicationServiceType**.

    c. Skopiuj wartość **X509Certificate** węzła.

    d. Zapisz skopiowanych **X509Certificate** wartość lokalnie na komputerze w pliku.

13. W **publicznego** kliknij **Dodaj**.
    
    ![Dodaj](./media/active-directory-saas-topdesk-secure-tutorial/ic790607.png "Dodaj")

14. Na **Asystenta konfiguracji SAML** okna dialogowego strony, należy wykonać następujące czynności:
    
    ![Asystent konfiguracji SAML](./media/active-directory-saas-topdesk-secure-tutorial/ic790608.png "Asystenta konfiguracji SAML")
    
    a. Aby przesłać plik metadanych pobranych z portalu Azure, w obszarze **metadanych Federacji**, kliknij przycisk **Przeglądaj**.

    b. Aby przesłać plik certyfikatu, w obszarze **certyfikatu (RSA)**, kliknij przycisk **Przeglądaj**.

    c. Aby przekazać plik logo uzyskano z zespołem pomocy technicznej TOPdesk, w obszarze **ikona Logo**, kliknij przycisk **Przeglądaj**.

    d. W **atrybutu nazwy użytkownika** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. W **Nazwa wyświetlana** tekstowym, wpisz nazwę dla danej konfiguracji.

    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-topdesk---secure-test-user"></a>Utwórz TOPdesk - użytkownika testowego bezpieczne

Aby włączyć użytkowników usługi Azure AD zalogować się do TOPdesk - bezpieczny, ich muszą mieć przydzielone do TOPdesk - bezpieczny.  
W przypadku TOPdesk - bezpieczny, inicjowanie obsługi to zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:
1. Zaloguj się na Twojej **TOPdesk - bezpiecznego** witryny firmy jako administrator.
2. W menu u góry kliknij **TOPdesk \> nowy \> pliki obsługi \> Operator**.
   
    ![Operator](./media/active-directory-saas-topdesk-secure-tutorial/ic790610.png "— Operator")

3. Na **operatora New** okna dialogowego, wykonaj następujące czynności:
   
    ![New Operator](./media/active-directory-saas-topdesk-secure-tutorial/ic790611.png "New — Operator")
   
    a. Kliknij przycisk **ogólne** kartę.
   
    b. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak **Simona**.
   
    c. Wybierz **lokacji** dla konta w **lokalizacji** sekcji.
   
    d. W **nazwa logowania** pole tekstowe z **logowania TOPdesk** wpisz nazwę logowania dla użytkownika.
   
    e. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Można użyć dowolnego innego TOPdesk — narzędzia do tworzenia konta użytkownika bezpiecznego lub interfejsów API dostarczonych przez TOPdesk - bezpieczny do udostępnienia konta użytkownika usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu TOPdesk - bezpieczny.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta TOPdesk - bezpieczny, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **TOPdesk - bezpiecznego**.

    ![TOPdesk - bezpieczne połączenie na liście aplikacji](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu TOPdesk - bezpiecznego kafelka w panelu dostępu należy należy pobrać automatycznie zalogowane do Twojej TOPdesk - zabezpieczonej aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_203.png

