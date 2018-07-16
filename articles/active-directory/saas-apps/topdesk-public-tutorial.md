---
title: 'Samouczek: Integracja usługi Azure Active Directory z TOPdesk - Public | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TOPdesk — publiczny.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 7f8bedda36e4008bdb9c3aeefc4d5acdf6ba253e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042133"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Samouczek: Integracja usługi Azure Active Directory z TOPdesk — publiczny

W tym samouczku dowiesz się, jak zintegrować TOPdesk - publicznej za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie TOPdesk - publicznej z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do TOPdesk - publicznego.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do TOPdesk — publiczny (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z TOPdesk - publiczny, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- TOPdesk — publiczny logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie TOPdesk - publicznego z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-topdesk---public-from-the-gallery"></a>Dodawanie TOPdesk - publicznego z galerii
Aby skonfigurować integrację TOPdesk - publicznego w usłudze Azure AD, należy dodać TOPdesk - publicznego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TOPdesk - publicznego z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **TOPdesk — publiczny**, wybierz opcję **TOPdesk — publiczny** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TOPdesk - publiczny, na liście wyników](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą TOPdesk — publiczny w oparciu o nazwie "Britta Simon" użytkownika testowego.

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w TOPdesk — publiczny jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TOPdesk — publiczny musi można ustanowić.

W TOPdesk — publiczna, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą TOPdesk - publiczny, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Utwórz TOPdesk - użytkownika testowego publicznych](#create-a-topdesk---public-test-user)**  — aby odpowiednikiem Britta Simon w TOPdesk - publiczny, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w sieci TOPdesk - publicznych aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TOPdesk — publiczna, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TOPdesk - Public** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

3. Na **TOPdesk — domeny publicznej i adresów URL** sekcji, wykonaj następujące czynności:

    ![TOPdesk — domeny publicznej i adresów URL pojedynczy informacje logowania jednokrotnego](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.topdesk.net`
    
    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Adres URL odpowiedzi to explaned w dalszej części samouczka. Skontaktuj się z pomocą [TOPdesk - zespołem pomocy technicznej publicznych klienta](https://help.topdesk.com/saas/enterprise/user/) do uzyskania tych wartości.  

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
6. Na **TOPdesk — Konfiguracja publiczna** , kliknij przycisk **skonfigurować TOPdesk - Public** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![TOPdesk — Konfiguracja publiczna](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

7. Zaloguj się na swoje **TOPdesk - Public** witryny firmy jako administrator.

8. W **TOPdesk** menu, kliknij przycisk **ustawienia**.
   
    ![Ustawienia](./media/topdesk-public-tutorial/ic790598.png "ustawienia")

9. Kliknij przycisk **ustawienia logowania**.
   
    ![Ustawienia logowania](./media/topdesk-public-tutorial/ic790599.png "ustawienia logowania")

10. Rozwiń **ustawienia logowania** menu, a następnie kliknij przycisk **ogólne**.
   
    ![Ogólne](./media/topdesk-public-tutorial/ic790600.png "ogólne")

11. W **publicznych** części **logowania języka SAML** konfiguracji sekcji, wykonaj następujące czynności:
   
    ![Ustawień technicznych](./media/topdesk-public-tutorial/ic790601.png "ustawień technicznych")
   
    a. Kliknij przycisk **Pobierz** można pobrać pliku metadanych publiczne, a następnie zapisz go lokalnie na komputerze.
   
    b. Otwórz plik metadanych pobrany, a następnie zlokalizuj **AssertionConsumerService** węzła.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiuj **AssertionConsumerService** wartość, wklej tę wartość w **adres URL odpowiedzi** polu tekstowym w **TOPdesk — domeny publicznej i adresów URL** sekcji.      
   
12. Aby utworzyć plik certyfikatu, wykonaj następujące czynności:
    
    ![Certyfikat](./media/topdesk-public-tutorial/ic790606.png "certyfikatu")
    
    a. Otwórz plik metadanych pobranych z witryny Azure portal.
    
    b. Rozwiń **deskryptora roli** węzeł, który ma **xsi: type** z **przekazywani: ApplicationServiceType**.
    
    c. Skopiuj wartość **X509Certificate** węzła.
    
    d. Zapisz skopiowany **X509Certificate** wartość lokalnie na komputerze użytkownika w pliku.

13. W **publicznych** kliknij **Dodaj**.
    
    ![Logowania języka SAML](./media/topdesk-public-tutorial/ic790625.png "logowania języka SAML")

14. Na **Asystent konfiguracji SAML** okna dialogowego strony, wykonaj następujące czynności:
    
    ![Asystent konfiguracji SAML](./media/topdesk-public-tutorial/ic790608.png "Asystent konfiguracji SAML")
    
    a. Można przekazać pliku metadanych pobranych z witryny Azure portal, w obszarze **metadanych Federacji**, kliknij przycisk **Przeglądaj**.

    b. Aby przesłać plik certyfikatu, w obszarze **certyfikatu (RSA)**, kliknij przycisk **Przeglądaj**.

    c. Aby przekazać plik logo uzyskana z zespołem pomocy technicznej TOPdesk, w obszarze **ikona Logo**, kliknij przycisk **Przeglądaj**.

    d. W **atrybut nazwy użytkownika** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. W **nazwę wyświetlaną** polu tekstowym wpisz nazwę dla danej konfiguracji.

    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/topdesk-public-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/topdesk-public-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/topdesk-public-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-topdesk---public-test-user"></a>Utwórz TOPdesk - użytkownika testowego publiczne

Aby umożliwić użytkownikom usługi Azure AD zalogować się do TOPdesk - publiczny, musi być obsługiwana w TOPdesk - publicznego.  
W przypadku TOPdesk - publiczny, inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:
1. Zaloguj się na swoje **TOPdesk - Public** witryny firmy jako administrator.

2. W menu u góry kliknij **TOPdesk \> New \> pliki obsługi \> osoby**.
   
    ![Osoby](./media/topdesk-public-tutorial/ic790628.png "osoby")

3. W oknie dialogowym nowej osoby wykonaj następujące czynności:
   
    ![Nową osobę](./media/topdesk-public-tutorial/ic790629.png "nowej osoby")
   
    a. Kliknij kartę Ogólne.

    b. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takich jak Simon
 
    c. Wybierz **witryny** dla konta.
 
    d. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Możesz użyć wszelkie inne TOPdesk — narzędzia do tworzenia konta użytkownika publicznego lub interfejsów API dostarczonych przez TOPdesk — publiczny można uaktywniać ich konta usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do TOPdesk - publicznego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon TOPdesk - publiczny, należy wykonać następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **TOPdesk - Public**.

    ![TOPdesk - publicznego linku na liście aplikacji](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu TOPdesk - Public kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do Twojej TOPdesk - publicznych aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png

