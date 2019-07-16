---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją AirWatch | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227725"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Samouczek: Integracja AirWatch za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować AirWatch w usłudze Azure Active Directory (Azure AD). W ramach AirWatch integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do AirWatch.
* Umożliwianie użytkownikom można automatycznie zalogowany do AirWatch za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* AirWatch logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje AirWatch **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-airwatch-from-the-gallery"></a>Dodawanie aplikacji AirWatch z galerii

Aby skonfigurować integrację aplikacji AirWatch z usługą Azure AD, musisz dodać aplikację AirWatch z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **AirWatch** w polu wyszukiwania.
1. Wybierz **AirWatch** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą AirWatch, za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w AirWatch.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą AirWatch, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego AirWatch](#configure-airwatch-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Tworzenie użytkownika testowego aplikacji AirWatch](#create-airwatch-test-user)** — aby mieć w aplikacji AirWatch odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
5. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **AirWatch** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `AirWatch`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z [zespołem obsługi klienta firmy AirWatch](https://www.air-watch.com/company/contact-us/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja AirWatch oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:

    | Name (Nazwa) |  Atrybut źródłowy|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij polecenie **Zapisz**.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **XML metadanych Federacji** i wybierz **Pobierz** pobrać kod XML metadanych i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. Na **Konfigurowanie AirWatch** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Konfigurowanie logowania jednokrotnego AirWatch

1. W oknie przeglądarki internetowej innej Zaloguj się w AirWatch firmowa witryna jako administrator.

1. Na stronie Ustawienia. Wybierz **Ustawienia > integracja w przedsiębiorstwie > Usługa katalogowa**.

   ![Ustawienia](./media/airwatch-tutorial/ic791921.png "Ustawienia")

1. Kliknij kartę **User** (Użytkownik), w polu tekstowym **Base DN** (Bazowa nazwa domeny) wpisz nazwę domeny, a następnie kliknij pozycję **Save** (Zapisz).

   ![Użytkownik](./media/airwatch-tutorial/ic791922.png "Użytkownik")

1. Kliknij kartę **Server** (Serwer).

   ![Serwer](./media/airwatch-tutorial/ic791923.png "Serwer")

1. Wykonaj następujące czynności na **LDAP** sekcji:

    ![Przekaż](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. W polu **Directory Type** (Typ katalogu) wybierz pozycję **None** (Brak).

    b. Wybierz pozycję **Use SAML For Authentication** (Użyj protokołu SAML na potrzeby uwierzytelniania).

1. Na **SAML 2.0** sekcji, aby przekazać pobranego certyfikatu kliknij **przekazywanie**.

    ![Przekazywanie](./media/airwatch-tutorial/ic791932.png "Przekazywanie")

1. W sekcji **Request** (Żądanie) wykonaj następujące czynności:

    ![Żądanie](./media/airwatch-tutorial/ic791925.png "Żądanie")  

    a. W polu **Request Binding Type** (Typ powiązania żądania) wybierz opcję **POST**.

    b. W witrynie Azure portal na **skonfigurować logowanie jednokrotne w AirWatch** strony okna dialogowego, kopia **adres URL logowania** wartość, a następnie wklej go do **pojedynczy znak na adres URL dostawcy tożsamości** pole tekstowe.

    c. W polu **NameID Format** (Format identyfikatora nazwy) wybierz opcję **Email Address** (Adres e-mail).

    d. Jako **zabezpieczeń żądania uwierzytelniania**, wybierz opcję **Brak**.

    e. Kliknij polecenie **Zapisz**.

1. Ponownie kliknij kartę **User** (Użytkownik).

    ![Użytkownik](./media/airwatch-tutorial/ic791926.png "Użytkownik")

1. W sekcji **Attribute** (Atrybut) wykonaj następujące czynności:

    ![Atrybut](./media/airwatch-tutorial/ic791927.png "Atrybut")

    a. W polu tekstowym **Object Identifier** (Identyfikator obiektu) wpisz `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. W polu tekstowym **Username** (Nazwa użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. W polu tekstowym **Display Name** (Nazwa wyświetlana) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. W polu tekstowym **First Name** (Imię) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. W polu tekstowym **Last Name** (Nazwisko) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Kliknij polecenie **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do AirWatch.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **AirWatch**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-airwatch-test-user"></a>Tworzenie użytkownika testowego aplikacji AirWatch

Aby umożliwić użytkownikom usługi Azure AD zarejestrować się w AirWatch, ich musi być obsługiwana w celu AirWatch. W przypadku aplikacji AirWatch aprowizowanie jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **AirWatch** witryny firmy jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Accounts** (Konta), a następnie kliknij pozycję **Users** (Użytkownicy).
  
   ![Użytkownicy](./media/airwatch-tutorial/ic791929.png "Użytkownicy")

3. W **użytkowników** menu, kliknij przycisk **widok listy**, a następnie kliknij przycisk **Dodaj > Dodaj użytkownika**.
  
   ![Dodawanie użytkownika](./media/airwatch-tutorial/ic791930.png "Dodawanie użytkownika")

4. W oknie dialogowym **Add / Edit User** (Dodawanie/edytowanie użytkownika) wykonaj następujące czynności:

   ![Dodawanie użytkownika](./media/airwatch-tutorial/ic791931.png "Dodawanie użytkownika")

   a. W polach tekstowych **Username** (Nazwa użytkownika), **Password** (Hasło), **Confirm Password** (Potwierdź hasło), **First Name** (Imię), **Last Name** (Nazwisko), **Email Address** (Adres e-mail) wpisz wartości prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

   b. Kliknij polecenie **Zapisz**.

> [!NOTE]
> Aby aprowizować konta użytkowników usługi AAD, można użyć jakichkolwiek innych narzędzi tworzenia konta użytkownika lub interfejsów API dostarczonych przez firmę AirWatch.

### <a name="test-sso"></a>Test rejestracji Jednokrotnej

Po wybraniu kafelka AirWatch w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze AirWatch, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
