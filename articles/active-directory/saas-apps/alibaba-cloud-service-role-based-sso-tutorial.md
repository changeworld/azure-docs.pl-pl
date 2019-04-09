---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a79ff869ac57a089664bbd7277ad4ddc9ffea08e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287378"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Samouczek: Integracja usługi Azure Active Directory z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)

W tym samouczku dowiesz się, jak zintegrować firmę Alibaba usługi w chmurze (Role-based SSO) z usługą Azure Active Directory (Azure AD).
Integrowanie firmę Alibaba usługi w chmurze (Role-based SSO) z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach).
* Umożliwia użytkownikom można automatycznie zalogowany do usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Usługa w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) obsługuje **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Dodawanie firmę Alibaba usługi w chmurze (Role-based SSO) z galerii

Aby skonfigurować integrację firmę Alibaba usługi w chmurze (Role-based SSO) w usłudze Azure AD, należy dodać firmę Alibaba usługi w chmurze (Role-based SSO) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać firmę Alibaba usługi w chmurze (Role-based SSO) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **firmę Alibaba usługi w chmurze (Role-based SSO)**, wybierz opcję **firmę Alibaba usługi w chmurze (Role-based SSO)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Usługa w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z usługą w chmurze firmę Alibaba (oparta na rolach SSO) w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika firmę Alibaba w usłudze w chmurze (Role-based SSO) musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) logowanie jednokrotne](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego firmę Alibaba usługi w chmurze (Role-based SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)**  — aby odpowiednikiem Britta Simon firmę Alibaba w usłudze w chmurze (Role-based SSO) połączoną usługę Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach), wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **firmę Alibaba usługi w chmurze (Role-based SSO)** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Domena usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W **identyfikator** tekstu wpisz dowolny adres URL:
    
    | |
    |--|
    | `urn:alibaba:cloudcomputing` |
    | `urn:alibaba:cloudcomputing:international` |

    b. W **adres URL odpowiedzi** tekstu wpisz dowolny adres URL:

    | |
    |--|
    | `https://signin.aliyun.com/saml-role/SSO` |
    | `https://signin.alibabacloud.com/saml-role/SSO` |

5. Aplikacja usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe  **Atrybuty użytkownika** .

    ![image](common/edit-attribute.png)

6. Ponadto powyżej firmę Alibaba usługi w chmurze (Role-based SSO) aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy|
    | ---------------| --------------- |
    | Rola | user.assignedroles |
    | RoleSessionName | user.mail |

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management), aby dowiedzieć się, jak skonfigurować **rolę** w usłudze Azure AD

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

8. Na **Konfigurowanie usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Konfigurowanie chmury firmę Alibaba (oparta na rolach SSO) usługi logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na **firmę Alibaba usługi w chmurze (Role-based SSO)** stronie, musisz wysłać pobrany **XML metadanych Federacji** i skopiować adresy URL z witryny Azure portal do [ Zespołu pomocy technicznej usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)](https://www.aliyun.com/service/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach).

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **firmę Alibaba usługi w chmurze (Role-based SSO)**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **firmę Alibaba usługi w chmurze (Role-based SSO)**.

    ![Link firmę Alibaba usługi w chmurze (Role-based SSO) na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Tworzenie użytkownika testowego firmę Alibaba usługi w chmurze (Role-based logowanie Jednokrotne)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon firmę Alibaba w usłudze w chmurze (Role-based SSO). Praca z [zespołu pomocy technicznej usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)](https://www.aliyun.com/service/) Aby dodać użytkowników na platformie firmę Alibaba usługi w chmurze (Role-based SSO). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka firmę Alibaba usługi w chmurze (Role-based SSO) w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze firmę Alibaba usługę w chmurze (Role-based SSO) dla której skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

