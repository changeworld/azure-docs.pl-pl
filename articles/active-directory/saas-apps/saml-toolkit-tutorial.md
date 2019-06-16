---
title: 'Samouczek: Integracja usługi Azure Active Directory z zestawem narzędzi usług Azure AD SAML | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i zestaw narzędzi Azure AD SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baeda4fca69969fae3f866e642ad3dc8ad46509
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091932"
---
# <a name="tutorial-integrate-azure-ad-saml-toolkit-with-azure-active-directory"></a>Samouczek: Integracja zestawu narzędzi platformy Azure SAML AD z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować zestaw narzędzi Azure AD SAML z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Azure AD SAML zestaw narzędzi z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do narzędzi usługi Azure AD SAML.
* Umożliwianie użytkownikom można automatycznie zalogowany do usługi Azure AD SAML Toolkit za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Usługa Azure AD SAML Toolkit logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Zestaw narzędzi platformy Azure SAML AD obsługuje **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Dodawanie zestawu narzędzi usługi Azure AD SAML z galerii

Aby skonfigurować integrację programu Azure AD SAML Toolkit w usłudze Azure AD, należy dodać zestaw narzędzi Azure AD SAML z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **zestaw narzędzi Azure AD SAML** w polu wyszukiwania.
1. Wybierz **zestaw narzędzi Azure AD SAML** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD przy użyciu narzędzi Azure AD SAML za pomocą użytkownika testu o nazwie **B.Simon**. Logowania jednokrotnego do pracy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w zestawie narzędzi usługi Azure AD SAML.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD z zestawem narzędzi usług Azure AD SAML, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie narzędzi usługi Azure AD SAML logowania jednokrotnego](#configure-azure-ad-saml-toolkit-sso)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego zestawu narzędzi usługi Azure AD SAML](#create-azure-ad-saml-toolkit-test-user)**  mieć odpowiednikiem B.Simon w zestawie narzędzi usługi Azure AD SAML, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **zestaw narzędzi Azure AD SAML** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://samltoolkit.azurewebsites.net/`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://samltoolkit.azurewebsites.net`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji**  z danymi opcjami zgodnie z wymaganiami, a następnie zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. Na **Konfigurowanie narzędzi usługi Azure AD SAML** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurowanie logowania jednokrotnego Toolkit SAML usługi Azure AD

1. Otwórz nowe okno przeglądarki sieci web, jeśli nie została zarejestrowana w witrynie usługi Azure AD SAML Toolkit najpierw zarejestrować, klikając **zarejestrować**. Jeśli zarejestrowano już, zaloguj się do witryny firmy zestaw narzędzi Azure AD SAML przy użyciu zarejestrowanego znaku w poświadczeniach.

    ![Zarejestruj zestaw narzędzi języka SAML usługi Azure AD](./media/saml-toolkit-tutorial/register.png)

1. Kliknij pozycję **plik konfiguracji SAML**.

    ![Plik konfiguracji SAML zestawu narzędzi platformy Azure AD SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Kliknij pozycję **Utwórz**.

    ![Zestaw narzędzi Azure AD SAML tworzenia rejestracji Jednokrotnej](./media/saml-toolkit-tutorial/createsso.png)

1. Na **konfiguracji logowania jednokrotnego SAML** strony, wykonaj następujące czynności:

    ![Zestaw narzędzi Azure AD SAML tworzenia rejestracji Jednokrotnej](./media/saml-toolkit-tutorial/fill-details.png)

    1. W **adres URL logowania** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    1. W **usługi Azure AD identyfikator** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    1. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    1. Kliknij przycisk **wybierz plik** i przekaż **kod XML metadanych Federacji** pliku, który został pobrany z witryny Azure portal.

    1. Kliknij pozycję **Utwórz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Azure AD SAML Toolkit.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **zestaw narzędzi Azure AD SAML**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Tworzenie użytkownika testowego zestawu narzędzi usługi Azure AD SAML

W tej sekcji użytkownika o nazwie B.Simon jest tworzony w zestawie narzędzi usługi Azure AD SAML. Zestaw narzędzi platformy Azure AD SAML obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w zestawie narzędzi usługi Azure AD SAML, nowy katalog jest tworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka zestaw narzędzi Azure AD SAML w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze zestaw narzędzi Azure AD SAML, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
