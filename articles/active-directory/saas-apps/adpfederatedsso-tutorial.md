---
title: 'Samouczek: Integracja usługi Azure Active Directory z ADP | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: 75b84c2856373126ceba0fc536e41d270f4d2d05
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048783"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Samouczek: Integracja usługi Azure Active Directory z ADP

W tym samouczku dowiesz się, jak zintegrować ADP w usłudze Azure Active Directory (Azure AD).

Integrowanie ADP z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do ADP.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowane adp (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ADP, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Subskrypcję ADP włączone

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ADP z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-adp-from-the-gallery"></a>Dodawanie ADP z galerii
Aby skonfigurować integrację ADP w usłudze Azure AD, należy dodać ADP z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ADP z galerii, wykonaj następujące czynności:**

1.  Zaloguj się do środowiska dostawcy tożsamości Microsoft Azure jako administrator.

2. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

3. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
4. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

5. W polu wyszukiwania wpisz **ADP**, wybierz opcję **ADP** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ADP na liście wyników](./media/adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą ADP w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ADP do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ADP musi można ustanowić.

W ADP, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą ADP, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego ADP](#create-an-adp-test-user)**  — aby odpowiednikiem Britta Simon w ADP połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ADP.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z ADP, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ADP** strona integracji aplikacji, kliknij pozycję **karta właściwości** i wykonaj następujące czynności: 

    ![Właściwości rejestracji jednokrotnej](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ustaw **włączono dla użytkowników do logowania** pola wartość **tak**.

    b. Kopiowanie **adres URL dostępu użytkownika** i wklej ją w **sekcji adresu URL logowania jednokrotnego Konfiguruj**, co jest opisane w dalszej części tego samouczka.

    c. Ustaw **wymagane przypisanie użytkownika** pola wartość **tak**.

    d. Ustaw **widoczne dla użytkowników** pola wartość **nie**.

2. Kliknij przycisk **logowanie jednokrotne** na **ADP** strony integracji aplikacji.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

3. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. Na **ADP domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![ADP domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/adpfederatedsso-tutorial/tutorial_adp_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL: `https://fed.adp.com` 
    
5. Aplikacja ADP oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego. Nazwa oświadczenia będą zawsze **"PersonImmutableID"** i wartości, które firma Microsoft obecnie **employeeid**. 

    W tym miejscu mapowanie użytkownika z usługi Azure AD ADP będzie odbywać się na **employeeid** , ale można to zamapować na inną wartość, na podstawie własnych ustawień aplikacji. Dlatego prosimy o pracy z [zespołem pomocy technicznej ADP](https://www.adp.com/contact-us/overview.aspx) najpierw na prawidłowy identyfikator użytkownika i zmapować tę wartość z **"PersonImmutableID"** oświadczenia.

    ![Konfigurowanie logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

    > [!NOTE] 
    > Zanim będzie można skonfigurować dla asercji SAML, musisz skontaktować się z Twojego [zespołem pomocy technicznej ADP](https://www.adp.com/contact-us/overview.aspx) i zażądać wartość atrybutu unikatowego identyfikatora dla Twojej dzierżawy. Należy tę wartość, aby skonfigurować oświadczenia niestandardowego w aplikacji. 

7. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Aby skonfigurować logowanie jednokrotne na **ADP** stronie, musisz przekazać pobrany **XML metadanych** na [ADP witryny sieci Web](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ten proces może potrwać kilka dni. 

### <a name="configure-your-adp-services-for-federated-access"></a>Skonfiguruj swoje usługi ADP, aby uzyskać dostęp federacyjny

>[!Important]
> Twoi pracownicy, którzy wymagają dostępu federacyjnego do usług ADP musi być przypisany do aplikacji usługi ADP, a następnie, użytkownicy muszą zostać ponownie przypisane do określonej usługi ADP.
Po otrzymaniu potwierdzenia z przedstawicielem ADP skonfiguruj ADP usług i zarządzanie/przypisywanie użytkowników do sterowania dostępem użytkowników do określonej usługi ADP.

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **ADP**, wybierz opcję **ADP** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ADP na liście wyników](./media/adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. W witrynie Azure portal na Twoje **ADP** strona integracji aplikacji, kliknij pozycję **karta właściwości** i wykonaj następujące czynności:  

    ![Linkedproperties rejestracji jednokrotnej](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ustaw **włączono dla użytkowników do logowania** pola wartość **tak**.

    b.  Ustaw **wymagane przypisanie użytkownika** pola wartość **tak**.

    c.  Ustaw **widoczne dla użytkowników** pola wartość **tak**.

6. Kliknij przycisk **logowanie jednokrotne** na **ADP** strony integracji aplikacji.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

7. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **połączone logowanie jednokrotne**. Aby połączyć aplikację, aby **ADP**.

    ![Logowanie jednokrotne połączonej](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Przejdź do **Konfiguruj adres URL logowania** sekcji, wykonaj następujące czynności:

    ![Prop rejestracji jednokrotnej](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. Wklej **adres URL dostępu użytkownika**, które zostały skopiowane z powyższych **karta właściwości** (od głównej aplikacji ADP).
                                                             
    b. Poniżej przedstawiono 5 aplikacje, które obsługują różne **adresy URL stan przekazywania**. Musisz dołączyć odpowiednie **adres URL przekaźnika stanu** wartość ręcznie do określonej aplikacji **adres URL dostępu użytkownika**.
    
    * **Teraz pracowników ADP**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **Pracowników ADP rozbudowana czasu**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **Firmę ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Zapisz** zmiany.

10. Po otrzymaniu potwierdzenia z przedstawicielem ADP Rozpocznij badanie z jednego lub dwóch użytkowników.

    a. Przypisz kilku użytkowników z usługą ADP aplikacji, aby przetestować dostęp federacyjny.

    b. Test wypadnie pomyślnie, użytkownikom dostępu do aplikacji usługi ADP w galerii i można uzyskać dostępu do usługi ADP.
 
11. Na potwierdzenie pomyślnego testowego należy przypisać usługi federacyjnej ADP do poszczególnych użytkowników lub grup użytkowników, które zostało wyjaśnione w dalszej części tego samouczka i wprowadzane do pracowników. 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/adpfederatedsso-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/adpfederatedsso-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/adpfederatedsso-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/adpfederatedsso-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-adp-test-user"></a>Tworzenie użytkownika testowego ADP

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w ADP. Praca z [zespołem pomocy technicznej ADP](https://www.adp.com/contact-us/overview.aspx) Aby dodać użytkowników w ramach konta ADP.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu ADP za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon adp, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **ADP**.

    ![Link ADP na liście aplikacji](./media/adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ADP w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ADP.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/adpfederatedsso-tutorial/tutorial_general_203.png

