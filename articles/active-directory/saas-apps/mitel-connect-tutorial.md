---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Mitel Connect | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i połącz Mitel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: b656bbb7c5924f14b300f0352551530bb47f2a53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097118"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Mitel MiCloud Connect

W tym samouczku dowiesz się, jak zintegrować Mitel MiCloud łączenie z usługą Azure Active Directory (Azure AD). Integrowanie MiCloud łączenie z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do MiCloud połączyć aplikacje przy użyciu swoich poświadczeń w organizacji.
* Możesz umożliwić użytkownikom na Twoim koncie, aby być zalogowany automatycznie połączyć MiCloud (logowanie jednokrotne), za pomocą kont usługi Azure AD.


Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą MiCloud Connect, potrzebne są następujące elementy:

* Subskrypcji usługi Azure AD

  Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Konto połączenia MiCloud Mitel

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD logowania jednokrotnego (SSO).

* Obsługuje łączenie Mitel **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-mitel-connect-from-the-gallery"></a>Dodawanie Mitel nawiązać połączenie z galerii

Aby skonfigurować integrację Mitel Connect w usłudze Azure AD, należy dodać Mitel połączyć z galerii z listą zarządzanych aplikacji SaaS w witrynie Azure portal.

**Aby dodać Mitel połączyć z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Kliknij przycisk **aplikacje dla przedsiębiorstw** a następnie kliknij przycisk **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Kliknij przycisk **nową aplikację**.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. Typ **Mitel połączyć** kliknij w polu wyszukiwania **Mitel połączyć** panel wyników i kliknij **Dodaj**.

     ![Mitel Connect na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne za pomocą MiCloud Connect w oparciu o nazwie użytkownika testowego **Britta Simon**. Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w MiCloud połączyć musi zostać nawiązane.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą MiCloud Connect, musisz wykonać następujące czynności:

1. **[Konfigurowanie połączenia MiCloud logowania jednokrotnego z usługą Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)**  — aby umożliwić użytkownikom korzystać z tej funkcji i konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
4. **[Tworzenie użytkownika testowego połączenia MiCloud Mitel](#create-a-mitel-micloud-connect-test-user)**  — aby mają odpowiednika w pozycji Britta simon na Twoim koncie MiCloud Connect, połączonego z usługi Azure AD reprezentacja użytkownika.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurowanie MiCloud połączyć z logowania jednokrotnego z usługą Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego dla połączenia MiCloud w witrynie Azure portal i skonfiguruj swoje konto MiCloud Połącz, aby umożliwić logowanie Jednokrotne przy użyciu usługi Azure AD.

Aby skonfigurować MiCloud połączyć przy użyciu logowania jednokrotnego do usługi Azure AD, najłatwiej jest otworzyć portalu Azure i portal konta Mitel obok siebie. Należy skopiować pewnych informacji w witrynie Azure portal do portalu konta Mitel, a niektóre w portalu konta usługi Mitel do witryny Azure portal.


1. Aby otworzyć stronę konfiguracji w [witryny Azure portal](https://portal.azure.com/), wykonaj następujące czynności:

    a. Na **połączyć Mitel** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

    b. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **SAML**.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)
    
    Opartej na SAML logowania zostanie wyświetlona strona.

2. Aby otworzyć okno dialogowe konfiguracji w portalu konta Mitel, wykonaj następujące czynności:

    a. Na **systemu telefonicznego** menu, kliknij przycisk **funkcje dodatku**.

    b. Po prawej stronie **logowania jednokrotnego**, kliknij przycisk **Aktywuj** lub **ustawienia**.
    
    Zostanie wyświetlone okno dialogowe łączenia jednej ustawień logowania jednokrotnego.
    
3. Wybierz **włączyć logowanie jednokrotne** pole wyboru.
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. W witrynie Azure portal kliknij pozycję **Edytuj** ikonę **podstawową konfigurację protokołu SAML** sekcji.
    ![image](common/edit-urls.png)

    Zostanie wyświetlone okno dialogowe podstawową konfigurację protokołu SAML.

5.  Skopiuj adres URL z **Mitel identyfikator jednostki** pola w portalu konta Mitel, a następnie wklej go do **identyfikator jednostki** pola w witrynie Azure portal.

6. Skopiuj adres URL z **adres URL odpowiedzi (adres URL usługi Assertion konsumenta)** pola w portalu konta Mitel, a następnie wklej go do **adres URL odpowiedzi (adres URL usługi Assertion konsumenta)** pola w witrynie Azure portal.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. W **adres URL logowania** pole tekstowe, wpisz jedno z następujących adresów URL:

    * **https://portal.shoretelsky.com** — do użycia w portalu konta Mitel jako domyślnej aplikacji Mitel
    * **https://teamwork.shoretel.com** — do użycia wszystkich członków zespołu jako domyślnej aplikacji Mitel

    **UWAGA**: Domyślna aplikacja Mitel jest aplikacji dostępne, gdy użytkownik kliknie na kafelku Mitel połączenia w panelu dostępu. Dotyczy to również aplikacji dostępne w trakcie konfiguracji testu z usługi Azure AD.

8. Kliknij przycisk **Zapisz** w **podstawową konfigurację protokołu SAML** okno dialogowe, w witrynie Azure portal.

9. W **certyfikat podpisywania SAML** sekcji na **opartej na SAML logowania jednokrotnego** stronie w witrynie Azure portal, kliknij przycisk **Pobierz** obok **certyfikat (Base64)** można pobrać **certyfikat podpisywania** i zapisz go na komputerze.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Otwórz plik certyfikatu podpisywania w edytorze tekstów, skopiuj wszystkie dane w pliku, a następnie wklej je w **certyfikat podpisywania** pole w portalu konta Mitel. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. W **Instalator Mitel połączyć** sekcji na **opartej na SAML logowania jednokrotnego** strony w witrynie Azure Portal, wykonaj następujące czynności:

    a. Skopiuj adres URL z **adres URL logowania** pola, a następnie wklej go do **adres URL logowania** pole w portalu konta Mitel.

    b. Skopiuj adres URL z **usługi Azure AD identyfikator** pola, a następnie wklej go do **identyfikator jednostki** pole w portalu konta Mitel.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Kliknij przycisk **Zapisz** na **łączenia jednej ustawień logowania jednokrotnego** okno dialogowe, w portalu konta Mitel.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testu o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory**, kliknij przycisk **użytkowników**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](common/users.png)

2. Kliknij przycisk **nowego użytkownika** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym właściwości użytkownika wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W **nazwa** wpisz **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz brittasimon @\<domenatwojejfirmy\>.\< rozszerzenie\>.  
Na przykład BrittaSimon@contoso.com.

    c. Wybierz **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do udzielania dostępu do łączenia z Mitel za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal kliknij pozycję **aplikacje dla przedsiębiorstw**, a następnie kliknij przycisk **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji kliknij **połączyć Mitel**.

    ![Połącz Mitel łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, następnie kliknij przycisk **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** w **użytkowników** , a następnie kliknij przycisk **wybierz** w dolnej części ekranu.

6. Jeśli dowolna wartość roli są oczekiwane w potwierdzenie SAML, wybierz odpowiednią rolę dla użytkownika z listy w **wybierz rolę** okna dialogowego, a następnie kliknij **wybierz** w dolnej części ekranu.

7. W **Dodaj przydziału** okno dialogowe, kliknij przycisk **przypisać**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Tworzenie użytkownika testowego Mitel MiCloud Connect

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na Twoim koncie MiCloud połączenia. Użytkownicy muszą być tworzone i aktywowana przed rozpoczęciem korzystania z logowania jednokrotnego.

Aby uzyskać szczegółowe informacje na temat dodawania użytkowników w portalu konta Mitel, zobacz [dodanie użytkownika](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) artykułu w bazie wiedzy Mitel.

Tworzenie użytkownika na koncie MiCloud połączyć z następującymi szczegółami:

  * **Nazwa:** Britta Simon

* **Służbowy adres E-mail:** `brittasimon@<yourcompanydomain>.<extension>`   
(Przykład: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com))

* **Nazwa użytkownika:** `brittasimon@<yourcompanydomain>.<extension> `  
(Przykład: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com); nazwa użytkownika jest zazwyczaj taki sam jak adres e-mail firmy użytkownika)

**UWAGA:** Połącz MiCloud użytkownika musi być taka sama jak adres e-mail użytkownika na platformie Azure.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji będziesz testu konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mitel połączenia w panelu dostępu, powinno nastąpić automatyczne przekierowanie do logowania do aplikacji łączenie MiCloud, skonfigurowany jako domyślnej w **adres URL logowania** pola. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
