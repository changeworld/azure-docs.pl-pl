---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą RingCentral | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092870"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Samouczek: Integrowanie RingCentral za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować RingCentral w usłudze Azure Active Directory (Azure AD). W ramach RingCentral integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do RingCentral.
* Umożliwianie użytkownikom można automatycznie zalogowany do RingCentral za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* RingCentral logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje RingCentral **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-ringcentral-from-the-gallery"></a>Dodawanie RingCentral z galerii

Aby skonfigurować integrację RingCentral w usłudze Azure AD, należy dodać RingCentral z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **RingCentral** w polu wyszukiwania.
1. Wybierz **RingCentral** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą RingCentral za pomocą użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w RingCentral.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą RingCentral, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego RingCentral](#configure-ringcentral-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego RingCentral](#create-ringcentral-test-user)**  — aby odpowiednikiem Britta Simon w RingCentral połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **RingCentral** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    1. Kliknij przycisk **przekazywania pliku metadanych**.
    1. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.
    1. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji.

    > [!Note]
    > Możesz uzyskać **plik metadanych usługodawcy** na stronie konfiguracji logowania jednokrotnego RingCentral, które zostało wyjaśnione w dalszej części tego samouczka.

1. Jeśli nie masz **plik metadanych usługodawcy**, wprowadź wartości dla następujących pól:

    a. W **identyfikator** pole tekstowe, wpisz adres URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Konfigurowanie logowania jednokrotnego RingCentral

1. W oknie przeglądarki internetowej innej Zaloguj się do RingCentral jako Administrator zabezpieczeń.

1. Na górze, wybierz polecenie **narzędzia**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Przejdź do **logowanie jednokrotne**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na **logowania jednokrotnego** w obszarze **konfiguracji logowania jednokrotnego** sekcji z **kroku 1** kliknij **Edytuj** i wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Na **Konfigurowanie logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Kliknij przycisk **Przeglądaj** można przekazać pliku metadanych, który został pobrany z witryny Azure portal.

    b. Po przekazaniu metadanych wartości pobrać, automatycznie wypełnione w **logowania jednokrotnego ogólne informacje o** sekcji.

    c. W obszarze **mapowanie atrybutu** zaznacz **mapy atrybut poczty E-mail, aby** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kliknij pozycję **Zapisz**.

    e. Z **krok 2** kliknij **Pobierz** można pobrać **plik metadanych usługodawcy** i przekaż go w **podstawową konfigurację protokołu SAML** sekcji Aby automatycznie wypełniać **identyfikator** i **adres URL odpowiedzi** wartości w witrynie Azure portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na tej samej stronie, przejdź do **włączyć logowanie Jednokrotne** sekcji, a następnie wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Wybierz **Włącz usługę logowania jednokrotnego**.

    * Wybierz **umożliwiają użytkownikom logowanie się przy użyciu poświadczeń logowania jednokrotnego lub RingCentral**.

    * Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do RingCentral.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **RingCentral**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ringcentral-test-user"></a>Tworzenie użytkownika testowego RingCentral

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w RingCentral. Praca z [zespołem pomocy technicznej klienta RingCentral](https://success.ringcentral.com/RCContactSupp) Aby dodać użytkowników na platformie RingCentral. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka RingCentral w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze RingCentral, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
