---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Uberflip | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: fb55840a3423f32d2d6d6d2531628ae4361a0cc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088205"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Uberflip

W tym samouczku dowiesz się, jak zintegrować Uberflip w usłudze Azure Active Directory (Azure AD).

Integrowanie Uberflip z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Uberflip.
* Aby umożliwić użytkownikom automatyczne logowanie do Uberflip (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać szczegółowe informacje na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Uberflip, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja Uberflip przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

Uberflip obsługuje następujące funkcje:

* Zainicjowane przez Dostawcę i inicjowane przez dostawcę tożsamości rejestracji jednokrotnej (SSO).
* Inicjowanie obsługi użytkowników just in time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Dodaj Uberflip z witryny Azure Marketplace

Aby skonfigurować integrację Uberflip w usłudze Azure AD, należy dodać Uberflip w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

   ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

   ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** u góry okienka.

   ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **Uberflip**. W wynikach wyszukiwania wybierz **Uberflip**, a następnie wybierz pozycję **Dodaj** umożliwiające dodanie aplikacji.

   ![Uberflip na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Uberflip w oparciu o nazwie użytkownika testowego **B Simon**. Dla logowania jednokrotnego do pracy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w Uberflip.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Uberflip, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Konfigurowanie Uberflip logowania jednokrotnego](#configure-uberflip-single-sign-on)**  do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Tworzenie użytkownika testowego Uberflip](#create-an-uberflip-test-user)**  tak, że istnieje użytkownik o nazwie Simon B. w Uberflip, który jest połączony z użytkownikiem usługi Azure AD o nazwie B. Simon.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Uberflip, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Uberflip** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. W **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** okienko, wykonaj jedną z następujących czynności, w zależności od tego, który tryb logowania jednokrotnego, którą chcesz skonfigurować:

   * Aby skonfigurować aplikację w trybie logowanie Jednokrotne zainicjowane przez dostawcę tożsamości w **adres URL odpowiedzi (adres URL usługi Assertion konsumenta)** wprowadź adres URL przy użyciu następującego wzorca:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/both-replyurl.png)

     > [!NOTE]
     > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu adresu URL odpowiedzi rzeczywistych. Aby uzyskać rzeczywiste wartości, skontaktuj się z pomocą [zespołem pomocy technicznej Uberflip](mailto:support@uberflip.com). Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okienko w witrynie Azure portal.

   * Aby skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług rejestracji Jednokrotnej, wybierz pozycję **Ustaw dodatkowe adresy URL**, a następnie w **adres URL logowania** wprowadź ten adres URL:

     `https://app.uberflip.com/users/login`

     ![Uberflip domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/both-signonurl.png)

1. Na **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **XML metadanych Federacji**  z podanych opcji, a następnie zapisz go na komputerze.

   ![Opcja pobierania XML metadanych Federacji](common/metadataxml.png)

1. W **Konfigurowanie Uberflip** okienko, skopiuj adres URL lub adresy URL, które są potrzebne:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowania**

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurowanie Uberflip logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na stronie Uberflip, musisz wysłać pobrany XML metadanych Federacji i odpowiednie adresy URL skopiowany z witryny Azure portal, aby [zespołem pomocy technicznej Uberflip](mailto:support@uberflip.com). Zespół Uberflip będzie upewnij się, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testu o nazwie B. Simon w witrynie Azure portal.

1. W witrynie Azure Portal w okienku po lewej wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i "All users" Opcje](common/users.png)

1. W górnej części ekranu wybierz **+ nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W **użytkownika** okienko, wykonaj następujące czynności:

    ![W okienku użytkownika](common/user-properties.png)

    1. W **nazwa** wprowadź **BSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **BSimon\@\<domenatwojejfirmy >.\< Rozszerzenia >** . Na przykład **BSimon\@contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Simon B. korzystać z platformy Azure logowania jednokrotnego przez przyznanie im dostępu do Uberflip.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **Uberflip**.

    ![Okienko aplikacje przedsiębiorstwa](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **Uberflip**.

    ![Uberflip na liście aplikacji](common/all-applications.png)

1. W okienku po lewej stronie w obszarze **ZARZĄDZAJ**, wybierz opcję **użytkowników i grup**.

    ![Opcji "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **+ Dodaj użytkownika**, a następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okienka.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **B Simon** w **użytkowników** , a następnie wybierz **wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartość roli dla asercji SAML, następnie w **wybierz rolę** okienku, wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-an-uberflip-test-user"></a>Tworzenie użytkownika testowego Uberflip

Użytkownik o nazwie B. Simon został utworzony w Uberflip. Nie trzeba podejmować żadnych działań do utworzenia tego użytkownika. Uberflip obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. Jeśli jeszcze nie istnieje użytkownik o nazwie B. Simon w Uberflip, nowy katalog jest tworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po wybraniu **Uberflip** w portalu Moje aplikacje powinny być automatycznie zarejestrowaniu w usłudze Uberflip subskrypcji, dla którego możesz skonfigurować logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
