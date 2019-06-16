---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Costpoint | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ebc5d1d7cac78066fa4e6028b1921211cb05bf5
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144641"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Samouczek: Integracja Costpoint za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Costpoint w usłudze Azure Active Directory (Azure AD). W ramach Costpoint integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Costpoint.
* Umożliwianie użytkownikom można automatycznie zalogowany do Costpoint za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Costpoint logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Costpoint **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez.

## <a name="adding-costpoint-from-the-gallery"></a>Dodawanie Costpoint z galerii

Aby skonfigurować integrację Costpoint w usłudze Azure AD, należy dodać Costpoint z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Costpoint** w polu wyszukiwania.
1. Wybierz **Costpoint** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Costpoint za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Costpoint.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Costpoint, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Costpoint](#configure-costpoint)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Costpoint](#create-costpoint-test-user)**  mieć odpowiednikiem B.Simon Costpoint połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Costpoint** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, aplikacja jest wstępnie skonfigurowana i wymaganych adresów URL już są wstępnie wypełniane przy użyciu platformy Azure. Użytkownik musi zapisać konfigurację, klikając **Zapisz** znajdujący się i wykonać następujące czynności:

    a. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    b. W **tan przekaźnika** pole tekstowe, wpisz wartość przy użyciu następującego wzorca:`<SYSTEM VALUE>`

    > [!NOTE]
    > Wartość stanu przekazywania nie jest prawdziwe. Zaktualizuj tę wartość rzeczywistą wartością stanu przekaźnika. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Costpoint](https://www.deltek.com/about/contact-us) aby zyskać tę wartość.

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę usług**, wykonaj następujący krok:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://costpointteea.deltek.com/cpweb/cploginform.htm`

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i zapisz go na swoje Notatnik .

   ![Link pobierania certyfikatu](common/copy-metadataurl.png)

1. Na **Konfigurowanie Costpoint** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-costpoint"></a>Konfigurowanie Costpoint

Aby skonfigurować logowanie jednokrotne na **Costpoint** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji** do [zespołem pomocy technicznej klienta Costpoint](https://www.deltek.com/about/contact-us). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

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

W tej sekcji można udostępnić B.Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Costpoint.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Costpoint**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-costpoint-test-user"></a>Tworzenie użytkownika testowego Costpoint

W tej sekcji utworzysz użytkownika o nazwie B.Simon w Costpoint. Praca z [zespołem pomocy technicznej klienta Costpoint](https://www.deltek.com/about/contact-us) Aby dodać użytkowników na platformie Costpoint. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Costpoint w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Costpoint, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)