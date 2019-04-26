---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą EverBridge | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1bb62c9a11971f72a6c96c4652b136c19812cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60279510"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą EverBridge

W tym samouczku dowiesz się, jak zintegrować EverBridge w usłudze Azure Active Directory (Azure AD).

Integrowanie EverBridge z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do EverBridge.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do EverBridge (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą EverBridge, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- EverBridge logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie EverBridge z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-everbridge-from-the-gallery"></a>Dodawanie EverBridge z galerii

Aby skonfigurować integrację EverBridge w usłudze Azure AD, należy dodać EverBridge z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać EverBridge z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **EverBridge**, wybierz opcję **EverBridge** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![EverBridge na liście wyników](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą EverBridge w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w EverBridge do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w EverBridge musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą EverBridge, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego EverBridge](#creating-an-everbridge-test-user)**  — aby odpowiednikiem Britta Simon w EverBridge połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji EverBridge.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z EverBridge, wykonaj następujące czynności:**

1. W witrynie Azure portal na **EverBridge** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

    >[!NOTE]
    >Konieczne konfiguracje aplikacji jako Manager Portal lub elementu członkowskiego portalu na obu końcach, tj. w witrynie Azure Portal i portalu Everbridge.

4. Aby skonfigurować **EverBridge** jako **portalu EverBridge Manager**na **podstawową konfigurację protokołu SAML** sekcji należy wykonać następujące czynności:

    ![EverBridge domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://sso.everbridge.net/<API_Name>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com) do uzyskania tych wartości.

5. Aby skonfigurować **EverBridge** jako **EverBridge elementu członkowskiego portalu**na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

   * Jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

       ![EverBridge domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

       * W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

       * W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

       ![EverBridge domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

       * W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com) do uzyskania tych wartości.

6. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link do pobierania certyfikatu](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Na **Konfigurowanie EverBridge** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja EverBridge](common/configuresection.png)

8. Aby uzyskać logowanie Jednokrotne skonfigurowane dla **EverBridge** jako **portalu EverBridge Manager** aplikacji, wykonaj następujące czynności: 
 
9. W oknie przeglądarki internetowej innej, zaloguj się do EverBridge jako Administrator.

9. W menu u góry kliknij **ustawienia** kartę, a następnie wybierz pozycję **logowania jednokrotnego** w obszarze **zabezpieczeń**.
   
     ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. W **nazwa** polu tekstowym wpisz nazwę identyfikatora dostawcy (na przykład: Nazwa firmy).
   
     b. W **Nazwa interfejsu API** polu tekstowym wpisz nazwę interfejsu API.
   
     c. Kliknij przycisk **wybierz plik** przycisk, aby przekazać plik metadanych, który został pobrany z witryny Azure portal.
   
     d. W tym miejscu tożsamości SAML, wybierz **tożsamość jest w elemencie NameIdentifier instrukcji podmiotu**.
   
     e. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.
   
     f. W dostawcy inicjowane żądania powiązania usługi, wybierz **przekierowania HTTP**.

     g. Kliknij pozycję **Zapisz**

10. Aby skonfigurować logowanie jednokrotne na **EverBridge** jako **EverBridge elementu członkowskiego portalu**, musisz wysłać pobrany **XML metadanych Federacji** do [ Zespół pomocy technicznej Everbridge](mailto:support@everbridge.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
  
### <a name="creating-an-everbridge-test-user"></a>Tworzenie użytkownika testowego EverBridge

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Everbridge. Praca z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com) Aby dodać użytkowników na platformie Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do EverBridge.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **EverBridge**.

    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka EverBridge w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji EverBridge.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
