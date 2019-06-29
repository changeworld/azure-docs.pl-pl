---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją Cisco Webex | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc537a631cd083da0f902fb4fcd44d47756eeba
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471778"
---
# <a name="tutorial-integrate-cisco-webex-with-azure-active-directory"></a>Samouczek: Integrowanie Cisco Webex za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Cisco Webex w usłudze Azure Active Directory (Azure AD). W ramach Cisco Webex integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Webex firmy Cisco.
* Umożliwianie użytkownikom można automatycznie zalogowany Cisco Webex za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Cisco Webex logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Cisco Webex **dodatkiem SP oraz dostawców tożsamości** zainicjowane logowania jednokrotnego i obsługuje **automatyczne** Inicjowanie obsługi użytkowników.

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie aplikacji Cisco Webex z galerii

Aby skonfigurować integrację aplikacji Cisco Webex z usługą Azure AD, musisz dodać aplikację Cisco Webex z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Cisco Webex** w polu wyszukiwania.
1. Wybierz **Cisco Webex** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Webex Cisco za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Cisco Webex.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Cisco Webex, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Cisco Webex](#configure-cisco-webex)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Cisco Webex](#create-cisco-webex-test-user)**  mieć odpowiednikiem B.Simon Webex Cisco, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Cisco Webex** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż pobrany plik **metadanych dostawcy usług** i skonfiguruj aplikację, wykonując następujące kroki:

    >[!Note]
    >Zostanie wyświetlony plik metadanych dostawcy usług z **Cisco Webex** portalu. 

    a. Kliknij przycisk **przekazywania pliku metadanych**.

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    c. Po pomyślnym ukończeniu przekazywania pliku metadanych dostawcy usług wartości **Identyfikator** i **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** zostaną wypełnione automatycznie:

    W **adres URL logowania** pola tekstowego, Wklej wartość **adres URL odpowiedzi**, która pobiera autofilled SP przez przekazywanie pliku metadanych.

5. Aplikacja Cisco Webex oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby dodać atrybuty.

    ![image](common/edit-attribute.png)

6. Oprócz powyższych aplikacja Cisco Webex oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Name (Nazwa) |  Atrybut źródłowy|
    | ---------------|--------- |
    | Identyfikator UID | user.userprincipalname |

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

1. Na **Konfigurowanie Cisco Webex** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-cisco-webex"></a>Konfigurowanie Cisco Webex

1. Zaloguj się w portalu [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) przy użyciu pełnych poświadczeń administratora.

2. Wybierz pozycję **Settings** (Ustawienia) i w sekcji **Authentication** (Uwierzytelnianie) kliknij pozycję **Modify** (Modyfikuj).

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Wybierz pozycję **Integrate a 3rd-party identity provider. (Advanced)** (Integracja innego dostawcy tożsamości. (Zaawansowane)) i przejdź do następnego ekranu.

4. Na stronie **Import Idp Metadata** (Importowanie metadanych dostawcy tożsamości) przeciągnij i upuść plik metadanych usługi Azure AD lub użyj opcji przeglądarki plików, aby znaleźć i przekazać ten plik. Następnie zaznacz opcję **Require certificate signed by a certificate authority in Metadata (more secure)** (Wymagaj certyfikatu podpisanego przez urząd certyfikacji w metadanych (bezpieczniejsze)) i kliknij przycisk **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Wybierz pozycję **Test SSO Connection** (Testuj połączenie logowania jednokrotnego) i gdy zostanie otwarta nowa karta przeglądarki, uwierzytelnij się przy użyciu usługi Azure AD, logując się.

6. Wróć do karty przeglądarki **Cisco Cloud Collaboration Management**. Jeśli test zakończył się pomyślnie, wybierz pozycję **This test was successful. Enable Single Sign-On option** (Test zakończył się pomyślnie. Włącz opcję logowania jednokrotnego) i kliknij przycisk **Next** (Dalej).

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

W tej sekcji można udostępnić B.Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Webex firmy Cisco.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Cisco Webex**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-cisco-webex-test-user"></a>Tworzenie użytkownika testowego aplikacji Cisco Webex

W tej sekcji utworzysz użytkownika Britta Simon w aplikacji Cisco Webex. W tej sekcji utworzysz użytkownika Britta Simon w aplikacji Cisco Webex.

1. Przejdź do portalu [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) i zaloguj się przy użyciu pełnych poświadczeń administratora.

2. Kliknij pozycję **Users** (Użytkownicy), a następnie pozycję **Manage Users** (Zarządzaj użytkownikami).
   
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. W oknie **Manage User** (Zarządzanie użytkownikiem) wybierz pozycję **Manually add or modify users** (Ręcznie dodaj lub modyfikuj użytkowników) i kliknij przycisk **Next** (Dalej).

4. Wybierz pozycję **Names and Email address** (Nazwy i adres e-mail). Wypełnij pola tekstowe w następujący sposób:

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. W **imię** polu tekstowym wpisz imię użytkownika, takich jak **B**.

    b. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W **adres E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak b.simon@contoso.com.

5. Kliknij znak plus, aby dodać użytkownika Britta Simon. Następnie kliknij przycisk **Dalej**.

6. W oknie **Add Services for Users** (Dodawanie usług dla użytkowników) kliknij przycisk **Save** (Zapisz), a następnie przycisk **Finish** (Zakończ).

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Cisco Webex w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Webex Cisco, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)