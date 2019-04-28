---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą TurboRater | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 0c279a224a7692a64621e24cc8fe2213b78b54ce
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62735622"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą TurboRater

W tym samouczku dowiesz się, jak zintegrować TurboRater w usłudze Azure Active Directory (Azure AD).

Integrowanie TurboRater z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do TurboRater.
* Aby umożliwić użytkownikom automatyczne logowanie do TurboRater (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać szczegółowe informacje na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TurboRater, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja TurboRater przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

TurboRater obsługuje inicjowane przez dostawcę tożsamości logowania jednokrotnego (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Dodaj TurboRater z witryny Azure Marketplace

Aby skonfigurować integrację TurboRater w usłudze Azure AD, należy dodać TurboRater w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Opcja aplikacje przedsiębiorstwa](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** u góry okienka.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **TurboRater**. W wynikach wyszukiwania wybierz **TurboRater**, a następnie wybierz pozycję **Dodaj** umożliwiające dodanie aplikacji.

    ![TurboRater na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą TurboRater w oparciu o nazwie użytkownika testowego **Britta Simon**. Dla logowania jednokrotnego do pracy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w TurboRater.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TurboRater, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Konfigurowanie TurboRater logowania jednokrotnego](#configure-turborater-single-sign-on)**  do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Tworzenie użytkownika testowego TurboRater](#create-a-turborater-test-user)**  tak, że istnieje użytkownik o nazwie Britta Simon w TurboRater, który jest połączony z użytkownikiem usługi Azure AD o nazwie Britta Simon.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z TurboRater, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **TurboRater** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. Na **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawową konfigurację protokołu SAML** okienko, wykonaj następujące czynności:

    ![TurboRater domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    1. W **identyfikator jednostki** wprowadź adres URL:

       `https://www.itcdataservices.com`

    1. W **adres URL odpowiedzi (adres URL usługi Assertion konsumenta)** wprowadź adres URL przy użyciu następującego wzorca:

       | Środowisko | Adres URL |
       | ---------------| --------------- |
       | Testowanie  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Na żywo  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z pomocą [zespołem pomocy technicznej TurboRater](https://www.getitc.com/support). Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okienko w witrynie Azure portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **XML metadanych Federacji**  z podanych opcji, a następnie zapisz go na komputerze.

    ![Opcja pobierania XML metadanych Federacji](common/metadataxml.png)

1. W **Konfigurowanie TurboRater** sekcji, skopiuj adres URL lub adresy URL, które są potrzebne:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowania**

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurowanie TurboRater logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na stronie TurboRater, musisz wysłać pobrany XML metadanych Federacji i odpowiednie adresy URL skopiowany z witryny Azure portal, aby [zespołem pomocy technicznej TurboRater](https://www.getitc.com/support). Zespół TurboRater będzie upewnij się, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal w okienku po lewej wybierz kolejno pozycje **Azure Active Directory**   > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i "All users" Opcje](common/users.png)

1. W górnej części ekranu wybierz **+ nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W **użytkownika** okienko, wykonaj następujące czynności:

    ![W okienku użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **BrittaSimon\@\<domenatwojejfirmy >.\< Rozszerzenia >**. Na przykład **BrittaSimon\@contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej TurboRater.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **TurboRater**.

    ![Okienko aplikacje przedsiębiorstwa](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **TurboRater**.

    ![TurboRater na liście aplikacji](common/all-applications.png)

1. W okienku po lewej stronie w obszarze **ZARZĄDZAJ**, wybierz opcję **użytkowników i grup**.

    ![Opcji "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **+ Dodaj użytkownika**, a następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okienka.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** w **użytkowników** , a następnie wybierz **wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartość roli dla asercji SAML, następnie w **wybierz rolę** okienku, wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-a-turborater-test-user"></a>Tworzenie użytkownika testowego TurboRater

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w TurboRater. Praca z [zespołem pomocy technicznej TurboRater](https://www.getitc.com/support) dodać Britta Simon jako użytkownik w TurboRater. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po wybraniu **TurboRater** w portalu Moje aplikacje powinny być automatycznie zarejestrowaniu w usłudze TurboRater subskrypcji, dla którego możesz skonfigurować logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)