---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą adaptacyjne Insights | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i adaptacyjne szczegółowe informacje.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 74f38ef3043cfa0e6df40c264b72cc5eae38544e
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430293"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą adaptacyjne usługi Insights

W tym samouczku dowiesz się, jak zintegrować adaptacyjne szczegółowych informacji z usługi Azure Active Directory (Azure AD).

Integrowanie adaptacyjne szczegółowych informacji z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do adaptacyjnego szczegółowych informacji.
- Użytkowników, aby automatycznie uzyskać zalogowanych do adaptacyjnego Insights (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD za pomocą usługi Insights adaptacyjne, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Adaptacyjne Insights logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie adaptacyjne szczegółowych informacji z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-adaptive-insights-from-the-gallery"></a>Dodawanie adaptacyjne szczegółowych informacji z galerii
Aby skonfigurować integrację adaptacyjne wglądu w szczegółowe dane w usłudze Azure AD, należy dodać adaptacyjne szczegółowych informacji z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać funkcje adaptacyjnego sterowania szczegółowych informacji z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **adaptacyjne szczegółowych informacji**, wybierz opcję **Insights funkcje adaptacyjnego sterowania** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Insights adaptacyjne w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w adaptacyjne szczegółowych informacji do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w adaptacyjne szczegółowych informacji musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą adaptacyjne Insights, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego adaptacyjne szczegółowych informacji](#create-an-adaptive-insights-test-user)**  — aby odpowiednikiem Britta Simon w adaptacyjne szczegółowe informacje, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji adaptacyjne szczegółowych informacji.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą usługi Insights adaptacyjne, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **Insights adaptacyjne** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** tryb intiated:

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Funkcje adaptacyjnego sterowania usługi insights można uzyskać wartości adresu URL odpowiedzi i identyfikator jednostki **ustawień logowania jednokrotnego SAML** strony.
 
5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)** i zapisz go na komputerze.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. Na **Konfigurowanie adaptacyjne usługi Insights** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    Należy zwrócić uwagę na to, czy adres URL może wskazywać następujące czynności:

    a. Adres URL logowania

    b. Identyfikator usługi Azure Ad

    c. Adres URL wylogowywania

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy adaptacyjne szczegółowych informacji.

8. Przejdź do **administratora**.

    ![Administrator](./media/adaptivesuite-tutorial/IC805644.png "administratora")

9. W **użytkownikami i rolami** kliknij **Zarządzanie ustawieniami logowania jednokrotnego SAML**.

    ![Zarządzanie ustawieniami logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/IC805645.png "Zarządzanie ustawieniami logowania jednokrotnego SAML")

10. Na **ustawień logowania jednokrotnego SAML** strony, wykonaj następujące czynności:

    ![Ustawienia logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/IC805646.png "ustawień logowania jednokrotnego SAML")

    a. W **nazwę dostawcy tożsamości** polu tekstowym wpisz nazwę dla danej konfiguracji.

    b. Wklej **usługi Azure Ad identyfikator** wartość kopiowana z witryny Azure portal do **dostawcy tożsamości identyfikator jednostki** pola tekstowego.

    c. Wklej **adres URL logowania** wartość kopiowana z witryny Azure portal do **dostawcy tożsamości adres URL logowania jednokrotnego** pola tekstowego.

    d. Wklej **adres URL wylogowania** wartość kopiowana z witryny Azure portal do **adres URL wylogowania niestandardowe** pola tekstowego.

    e. Aby przekazać certyfikat pobrany, kliknij przycisk **Choose file**.

    f. Wybierz następujące polecenie, aby uzyskać:

    * **Identyfikator użytkownika SAML**, wybierz opcję **nazwy użytkownika adaptacyjne Insights**.

    * **Lokalizacja identyfikator użytkownika SAML**, wybierz opcję **identyfikatora użytkownika w NameID podmiotu**.

    * **Format identyfikatora SAML NameID**, wybierz opcję **adres E-mail**.

    * **Włącz SAML**, wybierz opcję **Zezwalaj logowania jednokrotnego SAML i bezpośrednie logowanie adaptacyjne Insights**.

    g. Kopiuj **adaptacyjne adres URL logowania jednokrotnego szczegółowych informacji** i Wklej do **identyfikator jednostki** i **adres URL odpowiedzi** pola tekstowe w **adaptacyjne domena szczegółowych informacji i adresy URL** sekcji w witrynie Azure portal.

    h. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Tworzenie użytkownika testowego adaptacyjne szczegółowych informacji

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do adaptacyjnego szczegółowe informacje, musi być obsługiwana w adaptacyjne szczegółowe informacje. W przypadku adaptacyjne Insights aprowizacji to zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:** 

1. Zaloguj się do Twojej **Insights adaptacyjne** witryny firmy jako administrator.
2. Przejdź do **administratora**.

   ![Administrator](./media/adaptivesuite-tutorial/IC805644.png "administratora")

3. W **użytkownikami i rolami** kliknij **Dodaj użytkownika**.

   ![Dodaj użytkownika](./media/adaptivesuite-tutorial/IC805648.png "Dodaj użytkownika")
   
4. W **nowego użytkownika** sekcji, wykonaj następujące czynności:

   ![Prześlij](./media/adaptivesuite-tutorial/IC805649.png "przesyłania")

   a. Typ **nazwa**, **logowania**, **wiadomości E-mail**, **hasło** z prawidłowym użytkownikiem usługi Azure Active Directory chcesz aprowizować do powiązane pola tekstowe.

   b. Wybierz **roli**.

   c. Kliknij przycisk **przesłać**.

>[!NOTE]
>Można użyć jakichkolwiek innych adaptacyjne szczegółowych informacji użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez adaptacyjne wgląd do aprowizacji kont użytkowników usługi AAD.
>

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do adaptacyjnego szczegółowych informacji.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **Insights adaptacyjne**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka adaptacyjne wgląd w panelu dostępu, możesz powinien uzyskać automatycznie zalogowanych do aplikacji funkcje adaptacyjnego sterowania szczegółowych informacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
