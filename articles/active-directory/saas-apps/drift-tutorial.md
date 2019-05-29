---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Drift | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Drift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e645dd40071416a28ced475e02c47688a5759eb4
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304065"
---
# <a name="tutorial-integrate-drift-with-azure-active-directory"></a>Samouczek: Integrowanie odejściem od tego stanu za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować odejściem od tego stanu za pomocą usługi Azure Active Directory (Azure AD). Po odejściem od tego stanu jest integracja z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do odejściem od tego stanu.
* Umożliwianie użytkownikom można automatycznie zalogowany odejściem od tego stanu za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Odejściem od tego stanu logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Odstępstw obsługuje **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez i **Just In Time** Inicjowanie obsługi użytkowników.

## <a name="adding-drift-from-the-gallery"></a>Dodawanie aplikacji Drift z galerii

Aby skonfigurować integrację aplikacji Drift z usługą Azure AD, musisz dodać aplikację Drift z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **dryfu** w polu wyszukiwania.
1. Wybierz **dryfu** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą odejściem od tego stanu za pomocą użytkownika testu o nazwie **B. Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w kilka.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą odejściem od tego stanu, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Skonfiguruj kilka](#configure-drift)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego dryfu](#create-drift-test-user)**  mieć odpowiednikiem B. Simon dryfu połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **dryfu** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** sekcji, aplikacja jest wstępnie skonfigurowana i wymaganych adresów URL już są wstępnie wypełniane przy użyciu platformy Azure. Użytkownik musi zapisać konfigurację, klikając **Zapisz** znajdujący się i wykonać następujące czynności:

    a. Kliknij pozycję **Ustaw dodatkowe adresy URL**.
 
    b. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `https://app.drift.com` 

    c. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę usług**, wykonaj następujący krok:

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://start.drift.com`

6. Aplikacja dryfu oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

7. Ponadto do powyżej, aplikacja oczekuje, że kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji oświadczenia użytkownika w oknie dialogowym atrybutów użytkownika wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli: 

    | Name (Nazwa) | Atrybut źródłowy|
    | ---------------| --------------- |    
    | Name (Nazwa) | user.displayname |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **XML metadanych Federacji** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. Na **Konfigurowanie dryfu** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-drift"></a>Konfigurowanie odejściem od tego stanu

1. Aby zautomatyzować konfigurację w ramach odejściem od tego stanu, należy zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Dryft konfiguracji** nastąpi bezpośrednie przekierowanie do aplikacji odejściem od tego stanu. W tym miejscu podaj poświadczenia administratora do logowania się do odejściem od tego stanu. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroki 3 i 4.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować dryfu, Otwórz nowe okno przeglądarki sieci web i logowania do witryny firmy dryfu jako administrator i wykonaj następujące czynności:

4. Z lewej strony paska menu kliknij kolejno pozycje **ikonę ustawień** > **App Settings (Ustawienia aplikacji)**  > **Authentication (Uwierzytelnianie)** i wykonaj następujące kroki:

    ![Link administratora](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Przekaż **plik XML metadanych federacji** pobrany z witryny Azure Portal do pola tekstowego **Upload Identity Provider metadata file (Przekaż plik metadanych dostawcy tożsamości)** .

    b. Po przekazaniu pliku metadanych pozostałe wartości na stronie zostaną automatycznie wypełnione.

    c. Kliknij pozycję **Enable SAML** (Włącz protokół SAML).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B. Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Simon B. korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu po odejściem od tego stanu.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Drift**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-drift-test-user"></a>Tworzenie użytkownika testowego aplikacji Drift

W tej sekcji w aplikacji Drift jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Drift obsługuje aprowizację użytkowników typu just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Drift, zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej aplikacji Drift](mailto:integrations@drift.com).

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka odejściem od tego stanu w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze odejściem od tego stanu, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)