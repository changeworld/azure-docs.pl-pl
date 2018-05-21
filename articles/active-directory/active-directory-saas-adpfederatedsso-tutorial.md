---
title: 'Samouczek: Integracji Azure Active Directory z ADP | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: 64ae39e630897c69155ee85347c90421aca394a3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Samouczek: Integracji Azure Active Directory z ADP

Z tego samouczka dowiesz się integrowanie ADP w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD ADP zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do ADP.
- Umożliwia użytkownikom automatycznie pobrać zalogowane adp (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ADP, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Subskrypcja ADP włączone

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie ADP z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-adp-from-the-gallery"></a>Dodawanie ADP z galerii
Aby skonfigurować integrację usługi Azure AD ADP, należy dodać ADP z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać ADP z galerii, wykonaj następujące czynności:**

1.  Zaloguj się do środowiska dostawcy tożsamości Microsoft Azure jako administrator.

2. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

3. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
4. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

5. W polu wyszukiwania wpisz **ADP**, wybierz pozycję **ADP** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ADP na liście wyników](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ADP w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w ADP jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w ADP musi się.

W ADP, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ADP, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego ADP](#create-an-adp-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta ADP połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji ADP.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z ADP, wykonaj następujące czynności:**

1. W portalu Azure na **ADP** strona integracji aplikacji, kliknij polecenie **karta właściwości** i wykonaj następujące czynności: 

    ![Właściwości rejestracji jednokrotnej](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ustaw **dostępny dla użytkowników do logowania** wartość do pola **tak**.

    b. Kopia **adres URL dostępu użytkownika** i wklej go w **sekcji Konfigurowanie logowania jednokrotnego adresu URL**, który znajduje się w dalszej części tego samouczka.

    c. Ustaw **przypisanie użytkownika wymagane** wartość do pola **tak**.

    d. Ustaw **widoczny dla użytkowników** wartość do pola **nr**.

2. Kliknij przycisk **logowanie jednokrotne** na **ADP** strony integracja aplikacji.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

3. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. Na **ADP domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny ADP pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_url.png)

    W **identyfikator** tekstowym, wpisz adres URL: `https://fed.adp.com` 
    
5. Aplikacja ADP oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do konfiguracji atrybuty tokenu SAML. Poniższy zrzut ekranu przedstawia przykład tego. Nazwa oświadczenia będą zawsze miały **"PersonImmutableID"** i których wartość możemy mapować do **identyfikator pracownika**. 

    W tym miejscu będzie można przeprowadzić mapowanie użytkownika z usługi Azure AD ADP **identyfikator pracownika** , ale możesz mapować to na inną wartość, na podstawie własnych ustawień aplikacji. Pracy, dlatego należy z [zespołem pomocy technicznej ADP](https://www.adp.com/contact-us/overview.aspx) najpierw do używania prawidłowy identyfikator użytkownika i zmapować tę wartość z **"PersonImmutableID"** oświadczeń.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

    > [!NOTE] 
    > Aby można było skonfigurować potwierdzenia języka SAML, należy skontaktować się z [ADP zespołem pomocy technicznej](https://www.adp.com/contact-us/overview.aspx) i zażądać wartość atrybutu Unikatowy identyfikator dla dzierżawy. Należy tę wartość, aby skonfigurować niestandardowe oświadczeń dla aplikacji. 

7. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Aby skonfigurować logowanie jednokrotne w **ADP** stronie, musisz przekazać pobrany **XML metadanych** na [ADP witryny sieci Web](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ten proces może potrwać kilka dni. 

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurowanie usług z ADP uzyskać dostęp federacyjny

>[!Important]
> Pracownicy, którzy potrzebują dostępu federacyjnego do usług ADP musi być przypisany do usługi app Service ADP, a następnie, użytkownicy muszą być przypisane do określonej usługi ADP.
Po otrzymaniu potwierdzenia z przedstawicielem ADP skonfigurować użytkowników usług i przypisz lub zarządzać ADP, aby kontrolować dostęp użytkownika do określonej usługi ADP.

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **ADP**, wybierz pozycję **ADP** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ADP na liście wyników](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. W portalu Azure w sieci **ADP** strona integracji aplikacji, kliknij przycisk **karta właściwości** i wykonaj następujące czynności:  

    ![Linkedproperties rejestracji jednokrotnej](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ustaw **dostępny dla użytkowników do logowania** wartość do pola **tak**.

    b.  Ustaw **przypisanie użytkownika wymagane** wartość do pola **tak**.

    c.  Ustaw **widoczny dla użytkowników** wartość do pola **tak**.

6. Kliknij przycisk **logowanie jednokrotne** na **ADP** strony integracja aplikacji.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

7. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **połączonej logowania jednokrotnego**. Aby połączyć aplikację **ADP**.

    ![Logowanie jednokrotne połączone](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Przejdź do **Konfiguruj adres URL logowania** sekcji, wykonaj następujące czynności:

    ![Prop rejestracji jednokrotnej](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. Wklej **adres URL dostępu użytkownika**, które zostały skopiowane z powyższych **karta właściwości** (z poziomu głównego aplikacji ADP).
                                                             
    b. Poniżej przedstawiono 5 aplikacji, które obsługują różne **adresy URL stanu przekazywania**. Należy dołączyć odpowiednie **adres URL stanu przekazywania** wartości dla określonej aplikacji ręcznie do **adres URL dostępu użytkownika**.
    
    * **Teraz ADP pracowników**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **Pracowników ADP wzbogaconych czasu**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP firmę Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Zapisz** zmiany.

10. Po otrzymaniu potwierdzenia z przedstawicielem ADP należy rozpocząć test z jednego lub dwóch użytkowników.

    a. W przypadku kilku użytkowników należy przypisać do usługi ADP aplikacji do testowania dostępu federacyjnego.

    b. Test zakończy się pomyślnie, gdy użytkownicy dostęp do aplikacji usługi ADP w galerii i uzyskać dostęp do swoich ADP usługi.
 
11. Na potwierdzenie pomyślnego testu należy przypisać usługi federacyjnej ADP do indywidualnych użytkowników lub grup użytkowników, które znajduje się w dalszej części tego samouczka i wdrażanie jej do pracowników. 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-adp-test-user"></a>Tworzenie użytkownika testowego ADP

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w ADP. Praca z [zespołem pomocy technicznej ADP](https://www.adp.com/contact-us/overview.aspx) Aby dodać użytkowników w ramach konta ADP.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu ADP.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta adp, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **ADP**.

    ![Łącze ADP na liście aplikacji](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka ADP w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji ADP.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png

