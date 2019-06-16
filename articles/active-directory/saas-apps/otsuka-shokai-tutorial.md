---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Otsuka Shokai | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Otsuka Shokai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c04bb636-a3c7-4940-9b36-810425b855d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb5e667151f0148468e9eda8189c0f249b402b56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095288"
---
# <a name="tutorial-integrate-otsuka-shokai-with-azure-active-directory"></a>Samouczek: Integracja Otsuka Shokai za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Otsuka Shokai w usłudze Azure Active Directory (Azure AD). W ramach Otsuka Shokai integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Otsuka Shokai.
* Umożliwianie użytkownikom można automatycznie zalogowany Otsuka Shokai za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Otsuka Shokai logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Otsuka Shokai **tożsamości** jednokrotne logowanie inicjowane przez.

## <a name="adding-otsuka-shokai-from-the-gallery"></a>Dodawanie Otsuka Shokai z galerii

Aby skonfigurować integrację Otsuka Shokai w usłudze Azure AD, należy dodać Otsuka Shokai z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Otsuka Shokai** w polu wyszukiwania.
1. Wybierz **Otsuka Shokai** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.
1. Kliknij pozycję **właściwości** kartę, skopiuj **identyfikator aplikacji** i zapisz go na komputerze do późniejszego użytku.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Otsuka Shokai za pomocą użytkownika testu o nazwie **B. Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Otsuka Shokai.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Otsuka Shokai, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Otsuka Shokai](#configure-otsuka-shokai)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Otsuka Shokai](#create-otsuka-shokai-test-user)**  mieć odpowiednikiem B. Simon Otsuka Shokai, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Otsuka Shokai** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **Ustaw się logowanie jednokrotne z SAML** stronie aplikacja jest wstępnie skonfigurowana i wymaganych adresów URL już są wstępnie wypełniane przy użyciu platformy Azure. Użytkownik musi zapisać konfigurację, klikając **Zapisz** przycisku.

1. Aplikacja Otsuka Shokai oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych w przypadku gdy **nameidentifier** jest zamapowana **user.userprincipalname**. Aplikacja Otsuka Shokai oczekuje **nameidentifier** mają być mapowane z **user.objectid**, więc należy edytować mapowanie atrybutów, klikając **Edytuj**  ikonę i zmień mapowanie atrybutu.

    ![image](common/edit-attribute.png)

1. Ponadto powyżej Otsuka Shokai aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy|
    | ---------------| --------------- |
    | Identyfikator aplikacji | `<Application ID>` |

    >[!NOTE]
    >`<Application ID>` jest to wartość, które zostały skopiowane z **właściwości** kartę w witrynie Azure Portal.

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i zapisz go na swoje Notatnik .

   ![Link pobierania certyfikatu](common/copy-metadataurl.png)

1. Na **Konfigurowanie Otsuka Shokai** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-otsuka-shokai"></a>Konfigurowanie Otsuka Shokai

Aby skonfigurować logowanie jednokrotne na **Otsuka Shokai** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji** do [zespołem pomocy technicznej Otsuka Shokai](mailto:Tatsuya.Satoh@otsuka-shokai.co.jp). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B. Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Simon B. do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Otsuka Shokai.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Otsuka Shokai**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-otsuka-shokai-test-user"></a>Tworzenie użytkownika testowego Otsuka Shokai

W tej sekcji utworzysz użytkownika o nazwie B.Simon w Otsuka Shokai. Praca z [zespołem pomocy technicznej Otsuka Shokai](mailto:Tatsuya.Satoh@otsuka-shokai.co.jp) Aby dodać użytkowników na platformie Otsuka Shokai. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Otsuka Shokai w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Otsuka Shokai, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
