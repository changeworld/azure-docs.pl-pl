---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą PageDNA | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54c3ae22b9cc2e447960b9e3527bbbb0afae3e54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095099"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą PageDNA

W tym samouczku dowiesz się, jak zintegrować PageDNA w usłudze Azure Active Directory (Azure AD).

Integrowanie PageDNA z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do PageDNA.
* Aby umożliwić użytkownikom automatyczne logowanie do PageDNA (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać szczegółowe informacje na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą PageDNA, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja PageDNA przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka konfigurowania i testowania usługi Azure AD logowanie jednokrotne w środowisku testowym, a integracja PageDNA z usługą Azure AD.

PageDNA obsługuje następujące funkcje:

* Zainicjowane przez Dostawcę logowanie jednokrotne (SSO).

* Inicjowanie obsługi użytkowników just in time.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Dodaj PageDNA z witryny Azure Marketplace

Aby skonfigurować integrację PageDNA w usłudze Azure AD, należy dodać PageDNA w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** u góry okienka.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **PageDNA**. W wynikach wyszukiwania wybierz **PageDNA**, a następnie wybierz pozycję **Dodaj** umożliwiające dodanie aplikacji.

    ![PageDNA na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą PageDNA w oparciu o nazwie użytkownika testowego **Britta Simon**. Dla logowania jednokrotnego do pracy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w PageDNA.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą PageDNA, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Konfigurowanie PageDNA logowania jednokrotnego](#configure-pagedna-single-sign-on)**  do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Tworzenie użytkownika testowego PageDNA](#create-a-pagedna-test-user)**  tak, że istnieje użytkownik o nazwie Britta Simon w PageDNA, który jest połączony z użytkownikiem usługi Azure AD o nazwie Britta Simon.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z PageDNA, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **PageDNA** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. W **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawową konfigurację protokołu SAML** okienko, wykonaj następujące czynności:

    ![PageDNA domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/sp-identifier.png)

    1. W **adres URL logowania** wprowadź adres URL przy użyciu jednej z następujących wzorców:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. W **identyfikator jednostki** wprowadź adres URL przy użyciu jednej z następujących wzorców:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z pomocą [zespołem pomocy technicznej PageDNA](mailto:success@pagedna.com). Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okienko w witrynie Azure portal.

1. W **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **certyfikatu (Raw)** z podanych opcji, a następnie zapisz go na komputerze.

    ![Opcja (nieprzetworzone) Pobierz certyfikat](common/certificateraw.png)

1. W **Konfigurowanie PageDNA** sekcji, skopiuj adres URL lub adresy URL, które są potrzebne:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowania**

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurowanie PageDNA logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na stronie PageDNA, wysyłanie pobranego certyfikatu (Raw) i odpowiednie adresy URL skopiowany z portalu Azure w celu [zespołem pomocy technicznej PageDNA](mailto:success@pagedna.com). Zespół PageDNA będzie upewnij się, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal w okienku po lewej wybierz kolejno pozycje **Azure Active Directory**   > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i "All users" Opcje](common/users.png)

1. W górnej części ekranu wybierz **+ nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W **użytkownika** okienko, wykonaj następujące czynności:

    ![W okienku użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **BrittaSimon\@\<domenatwojejfirmy >.\< Rozszerzenia >** . Na przykład **BrittaSimon\@contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej PageDNA.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **PageDNA**.

    ![Okienko aplikacje przedsiębiorstwa](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **PageDNA**.

    ![PageDNA na liście aplikacji](common/all-applications.png)

1. W okienku po lewej stronie w obszarze **ZARZĄDZAJ**, wybierz opcję **użytkowników i grup**.

    ![Opcji "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **+ Dodaj użytkownika**, a następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okienka.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** w **użytkowników** , a następnie wybierz **wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartość roli dla asercji SAML, następnie w **wybierz rolę** okienku, wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-a-pagedna-test-user"></a>Tworzenie użytkownika testowego PageDNA

Użytkownik o nazwie Britta Simon został utworzony w PageDNA. Nie trzeba podejmować żadnych działań do utworzenia tego użytkownika. PageDNA obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. Jeśli jeszcze nie istnieje użytkownik o nazwie Britta Simon w PageDNA, nowy katalog jest tworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po wybraniu **PageDNA** w portalu Moje aplikacje powinny być automatycznie zarejestrowaniu w usłudze PageDNA subskrypcji, dla którego możesz skonfigurować logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)