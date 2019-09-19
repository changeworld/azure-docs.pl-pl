---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z programem Cisco WebEx | Microsoft Docs'
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
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 503de723894388a198abbb687221cb1403a6fa84
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z programem Cisco WebEx

W tym samouczku dowiesz się, jak zintegrować Cisco WebEx z usługą Azure Active Directory (Azure AD). W przypadku integrowania programu Cisco WebEx z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do firmy Cisco WebEx.
* Zezwól użytkownikom na automatyczne logowanie do firmy Cisco WebEx przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Cisco WebEx.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Cisco WebEx obsługuje usługę **SP** zainicjowaną przez usługę SSO i obsługuje **Automatyczne** Inicjowanie obsługi użytkowników.

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie aplikacji Cisco Webex z galerii

Aby skonfigurować integrację aplikacji Cisco Webex z usługą Azure AD, musisz dodać aplikację Cisco Webex z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **Cisco WebEx** .
1. Wybierz pozycję **Cisco WebEx** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Cisco WebEx

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Cisco WebEx przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Cisco WebEx.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Cisco WebEx, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Cisco WebEx](#configure-cisco-webex)** , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Cisco WebEx](#create-cisco-webex-test-user)** , aby miał odpowiednik B. Simon w Cisco WebEx połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Cisco WebEx** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż pobrany plik **metadanych dostawcy usług** i skonfiguruj aplikację, wykonując następujące kroki:

    >[!Note]
    >Plik metadanych dostawcy usług zostanie pobrany z sekcji **Konfigurowanie Cisco WebEx** , która została omówiona w dalszej części tego samouczka. 

    a. Kliknij przycisk **przekazywania pliku metadanych**.

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    c. Po pomyślnym ukończeniu przekazywania pliku metadanych dostawcy usług wartości **Identyfikator** i **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** zostaną wypełnione automatycznie:

    W polu tekstowym **adres URL logowania** wklej wartość **adresu URL odpowiedzi**, który zostanie wypełniony autowypełniany przez przekazywanie plików metadanych Sp.

5. Aplikacja Cisco WebEx oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

6. Oprócz powyższych aplikacja Cisco Webex oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Name |  Atrybut źródłowy|
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

    g. Kliknij polecenie **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie programu Cisco WebEx** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Cisco WebEx.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Cisco Webex**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-cisco-webex"></a>Konfigurowanie rozwiązania Cisco WebEx

1. Zaloguj się w portalu [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) przy użyciu pełnych poświadczeń administratora.

2. Wybierz pozycję **Settings** (Ustawienia) i w sekcji **Authentication** (Uwierzytelnianie) kliknij pozycję **Modify** (Modyfikuj).

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
3. Wybierz pozycję **Integrate a 3rd-party identity provider. (Advanced)** (Integracja innego dostawcy tożsamości. (Zaawansowane)) i przejdź do następnego ekranu.

4. Na stronie **Import Idp Metadata** (Importowanie metadanych dostawcy tożsamości) przeciągnij i upuść plik metadanych usługi Azure AD lub użyj opcji przeglądarki plików, aby znaleźć i przekazać ten plik. Następnie zaznacz opcję **Require certificate signed by a certificate authority in Metadata (more secure)** (Wymagaj certyfikatu podpisanego przez urząd certyfikacji w metadanych (bezpieczniejsze)) i kliknij przycisk **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

5. Wybierz pozycję **Test SSO Connection** (Testuj połączenie logowania jednokrotnego) i gdy zostanie otwarta nowa karta przeglądarki, uwierzytelnij się przy użyciu usługi Azure AD, logując się.

6. Wróć do karty przeglądarki **Cisco Cloud Collaboration Management**. Jeśli test zakończył się pomyślnie, wybierz pozycję **This test was successful. Enable Single Sign-On option** (Test zakończył się pomyślnie. Włącz opcję logowania jednokrotnego) i kliknij przycisk **Next** (Dalej).

### <a name="create-cisco-webex-test-user"></a>Tworzenie użytkownika testowego aplikacji Cisco Webex

W tej sekcji utworzysz użytkownika o nazwie B. Simon w programie Cisco WebEx. W tej sekcji utworzysz użytkownika o nazwie B. Simon w programie Cisco WebEx.

1. Przejdź do portalu [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) i zaloguj się przy użyciu pełnych poświadczeń administratora.

2. Kliknij pozycję **Users** (Użytkownicy), a następnie pozycję **Manage Users** (Zarządzaj użytkownikami).
   
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. W oknie **Manage User** (Zarządzanie użytkownikiem) wybierz pozycję **Manually add or modify users** (Ręcznie dodaj lub modyfikuj użytkowników) i kliknij przycisk **Next** (Dalej).

4. Wybierz pozycję **Names and Email address** (Nazwy i adres e-mail). Wypełnij pola tekstowe w następujący sposób:

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, na przykład **B**.

    b. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika, np b.simon@contoso.com.

5. Kliknij znak plus, aby dodać B. Simon. Następnie kliknij przycisk **Dalej**.

6. W oknie **Add Services for Users** (Dodawanie usług dla użytkowników) kliknij przycisk **Save** (Zapisz), a następnie przycisk **Finish** (Zakończ).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Cisco WebEx w panelu dostępu należy automatycznie zalogować się do programu Cisco WebEx, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Cisco WebEx z usługą Azure AD](https://aad.portal.azure.com)