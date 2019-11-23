---
title: 'Samouczek: integracja Azure Active Directory z roztocznym połączeniem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i roztoczą.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160536"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Samouczek: integracja Azure Active Directory z roztocznym połączeniem MiCloud

W tym samouczku dowiesz się, jak zintegrować program MiCloud Connect z usługą Azure Active Directory (Azure AD). Integracja MiCloud z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi MiCloud Connect Apps przy użyciu poświadczeń przedsiębiorstwa.
* Możesz umożliwić użytkownikom na swoim koncie automatyczne logowanie do usługi MiCloud Connect (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.


Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem MiCloud Connect, potrzebne są następujące elementy:

* Subskrypcji usługi Azure AD

  Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Roztoczne konto MiCloud Connect

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD.

* Roztoczne połączenie obsługuje usługę **SP** zainicjowaną przez usługę SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Dodawanie roztoczne połączenia z galerii

Aby skonfigurować integrację nawiązywania połączenia z usługą Azure AD, należy dodać roztoczne połączenie z galerii do listy zarządzanych aplikacji SaaS w Azure Portal.

**Aby dodać roztoczne połączenie z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Kliknij pozycję **aplikacje dla przedsiębiorstw** , a następnie kliknij pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Kliknij pozycję **Nowa aplikacja**.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz tekst **ścięcia** , kliknij pozycję **Rozłącz się** z poziomu panelu wyniki, a następnie kliknij przycisk **Dodaj**.

     ![Roztoczne połączenie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą MiCloud Connect na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie MiCloud Connect.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu połączenia MiCloud, należy wykonać następujące czynności:

1. **[Skonfiguruj MiCloud Connect dla logowania jednokrotnego za pomocą usługi Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** — aby umożliwić użytkownikom korzystanie z tej funkcji oraz Konfigurowanie ustawień logowania jednokrotnego po stronie aplikacji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
4. **[Utwórz test "Roztoczne MiCloud](#create-a-mitel-micloud-connect-test-user)** ", aby uzyskać odpowiednik Britta Simon na koncie MiCloud, które jest połączone z reprezentacją usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurowanie MiCloud Connect dla logowania jednokrotnego za pomocą usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD dla MiCloud Connect w Azure Portal i skonfiguruj konto połączenia MiCloud, aby zezwolić na logowanie jednokrotne przy użyciu usługi Azure AD.

Aby skonfigurować MiCloud łączenie z logowaniem jednokrotnym w usłudze Azure AD, najłatwiej otworzyć Azure Portal i roztoczny Portal konta. Należy skopiować niektóre informacje z Azure Portal do portalu konta roztocznego, a część z portalu konta roztocznego do Azure Portal.


1. Aby otworzyć stronę konfiguracji w [Azure Portal](https://portal.azure.com/), wykonaj następujące czynności:

    a. Na stronie **Rozwiąż połączenie z** integracją aplikacji kliknij pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

    b. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego kliknij pozycję **SAML**.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)
    
    Zostanie wyświetlona strona logowania oparta na protokole SAML.

2. Aby otworzyć okno dialogowe konfiguracji w portalu konta roztocznego, wykonaj następujące czynności:

    a. W menu **system Phone (telefon** ) kliknij pozycję **funkcje dodatków**.

    b. Po prawej stronie **logowania jednokrotnego**kliknij pozycję **Aktywuj** lub **Ustawienia**.
    
    Zostanie wyświetlone okno dialogowe łączenie ustawień logowania jednokrotnego.
    
3. Zaznacz pole wyboru **Włącz logowanie jednokrotne** .
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. W Azure Portal kliknij ikonę **Edytuj** w sekcji **Podstawowa konfiguracja protokołu SAML** .
    ![image](common/edit-urls.png)

    Zostanie wyświetlone okno dialogowe Podstawowa konfiguracja protokołu SAML.

5.  Skopiuj adres URL z pola **Identyfikator Roztoczny (identyfikator jednostki)** w portalu konta roztocznego i wklej go do pola **Identyfikator (identyfikator jednostki)** w Azure Portal.

6. Skopiuj adres URL z pola **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** w portalu roztocza konta i wklej go do pola **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** w Azure Portal.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. W polu tekstowym **adres URL logowania** wpisz jeden z następujących adresów URL:

    * **https://portal.shoretelsky.com** — aby użyć portalu konta roztocznego jako domyślnej aplikacji pod kątem rozłożenia
    * **https://teamwork.shoretel.com** — aby użyć zespołowej jako domyślnej aplikacji ze zgięciem

    **Uwaga**: jest to aplikacja, do której uzyskuje się dostęp, gdy użytkownik kliknie kafelek roztoczne połączenie w panelu dostępu. Jest to również aplikacja, do której można uzyskać dostęp podczas przeprowadzania konfiguracji testowej z usługi Azure AD.

8. Kliknij przycisk **Zapisz** w oknie dialogowym **podstawowe konfiguracje SAML** w Azure Portal.

9. W sekcji **certyfikat podpisywania SAML** na stronie **logowania opartej na protokole SAML** w Azure Portal kliknij pozycję **Pobierz** obok pozycji **certyfikat (base64)** , aby pobrać **certyfikat podpisywania** i zapisać go na komputerze.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Otwórz plik certyfikatu podpisywania w edytorze tekstów, skopiuj wszystkie dane z pliku, a następnie wklej dane w polu **certyfikat podpisywania** w portalu konta roztocznego. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. W sekcji **Ustawienia Roztoczne połączenie** na stronie **logowania opartej na protokole SAML** Azure Portal wykonaj następujące czynności:

    a. Skopiuj adres URL z pola **adres URL logowania** i wklej go w polu **adres URL** logowania w portalu rozłożenia konta.

    b. Skopiuj adres URL z pola **Identyfikator usługi Azure AD** i wklej go do pola **Identyfikator jednostki** w portalu roztocznego konta.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Kliknij przycisk **Zapisz** w oknie dialogowym **łączenie ustawień logowania jednokrotnego** w portalu konta rozłożenia.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W Azure Portal w lewym okienku kliknij pozycję **Azure Active Directory**, kliknij pozycję **Użytkownicy**, a następnie kliknij pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Kliknij pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym właściwości użytkownika wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@\<yourcompanydomain\>.\>rozszerzenia\<.  
Na przykład BrittaSimon@contoso.com.

    d. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do rozłożenia.

1. W Azure Portal kliknij pozycję **aplikacje przedsiębiorstwa**, a następnie kliknij pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje kliknij pozycję **Nawiąż połączenie**.

    ![Link roztoczne połączenie na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Kliknij pozycję **Dodaj użytkownika**, a następnie kliknij pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodaj przypisanie** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, wybierz odpowiednią rolę dla użytkownika z listy w oknie dialogowym **Wybierz rolę** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodaj przypisanie** kliknij przycisk **Przypisz**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Tworzenie MiCloudego użytkownika testowego połączenia

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na koncie MiCloud Connect. Przed skorzystaniem z logowania jednokrotnego należy utworzyć i aktywować użytkowników.

Aby uzyskać szczegółowe informacje o dodawaniu użytkowników w portalu konta rozbudowanego, zapoznaj się z artykułem [Dodawanie użytkownika](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) w bazie wiedzy o zawieszeniu.

Utwórz użytkownika na koncie MiCloud Connect z następującymi szczegółami:

  * **Nazwa:** Britta Simon

* **Służbowy adres e-mail:** `brittasimon@<yourcompanydomain>.<extension>`   
(Przykład: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Nazwa użytkownika:** `brittasimon@<yourcompanydomain>.<extension>`  
(Przykład: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); nazwa użytkownika jest zwykle taka sama jak służbowy adres e-mail użytkownika)

**Uwaga:** Nazwa użytkownika MiCloud kontaktu musi być taka sama jak adres e-mail użytkownika na platformie Azure.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka roztoczne połączenie w panelu dostępu należy automatycznie przekierować do logowania się do aplikacji MiCloud Connect skonfigurowanej jako wartość domyślna w polu **adres URL logowania** . Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
