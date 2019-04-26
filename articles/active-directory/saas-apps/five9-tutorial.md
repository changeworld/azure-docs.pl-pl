---
title: 'Samouczek: Integracja usługi Azure Active Directory Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Five9 oraz karty (CTI, skontaktuj się z Centrum agentów).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: daec6e169805c193b48781dfecbabd9349bdc59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60278665"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Samouczek: Integracja usługi Azure Active Directory Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)

W tym samouczku dowiesz się, jak zintegrować Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z usługą Azure Active Directory (Azure AD).
Integrowanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Five9 oraz karty sieciowej (CTI, skontaktuj się z Centrum agentów).
* Umożliwia użytkownikom można automatycznie zalogowany do Five9 oraz karty sieciowej (CTI, skontaktuj się z Centrum agentów) (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Five9 oraz karta (CTI, skontaktuj się z Centrum agentów) obsługuje **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Dodawanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z galerii

Aby skonfigurować integrację Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) w usłudze Azure AD, należy dodać Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)**, wybierz opcję **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) logowanie jednokrotne](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)](#create-five9-plus-adapter-cti-contact-center-agents-test-user)**  — aby odpowiednikiem Britta Simon Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) połączoną usługę Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Five9 oraz karty (CTI, skontaktuj się z Centrum agentów), wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Domena Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    
    |    Środowisko      |       Adres URL      |
    | :-- | :-- |
    | "Five9 oraz karty dla oprogramowania Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Five9 oraz Adapter dla systemu Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9 oraz Adapter dla agenta Toolkit pulpitu" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    |      Środowisko     |      Adres URL      |
    | :--                  | :--           |
    | "Five9 oraz karty dla oprogramowania Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Five9 oraz Adapter dla systemu Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9 oraz Adapter dla agenta Toolkit pulpitu" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. Na **Konfigurowanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Konfigurowanie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) logowanie jednokrotne

1. Aby skonfigurować logowanie jednokrotne na **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)** stronie, musisz wysłać pobrany **Certificate(Base64)** i odpowiednie skopiowany adresy URL do [Five9 oraz Karta (CTI, skontaktuj się z Centrum agentów), zespół pomocy technicznej](https://www.five9.com/about/contact). Również ponadto dalsze konfigurowania logowania jednokrotnego, wykonaj poniższe kroki, zgodnie z karty:

    a. "Five9 oraz Adapter dla zestawu narzędzi pulpitu agenta" Podręczniku administratora: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 oraz karty dla oprogramowania Microsoft Dynamics CRM" Podręczniku administratora: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 oraz Adapter dla systemu Zendesk" Podręczniku administratora: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Five9 oraz karty sieciowej (CTI, skontaktuj się z Centrum agentów).

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)**.

    ![Łącze Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Tworzenie użytkownika testowego Five9 oraz karty (CTI, skontaktuj się z Centrum agentów)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon Five9 oraz karty (CTI, skontaktuj się z Centrum agentów). Praca z [zespołu pomocy technicznej Five9 oraz kart (CTI, skontaktuj się z Centrum agentów)](https://www.five9.com/about/contact) Aby dodać użytkowników na platformie Five9 oraz karty (CTI, skontaktuj się z Centrum agentów). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu karty oraz Five9 (CTI, skontaktuj się z Centrum agenci kafelka w panelu dostępu, możesz powinna istnieć możliwość automatycznej rejestracji Five9 oraz karty (CTI, skontaktuj się z Centrum agentów) dla której skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

