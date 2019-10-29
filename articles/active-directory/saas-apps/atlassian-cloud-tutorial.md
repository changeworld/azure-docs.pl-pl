---
title: 'Samouczek: integracja Azure Active Directory z chmurą Atlassian | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f901bd37a54d12dc2afe805f71c7b018a277e34
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969626"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Samouczek: integracja chmury Atlassian z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować chmurę Atlassian z usługą Azure Active Directory (Azure AD). W przypadku integracji chmury Atlassian z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury Atlassian.
* Zezwól użytkownikom na automatyczne logowanie do usługi Atlassian Cloud przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym w chmurze Atlassian.
* Aby włączyć logowanie jednokrotne SAML (Security Assertion Markup Language) dla produktów Atlassian Cloud, należy skonfigurować usługę Atlassian Access. Dowiedz się więcej o usłudze [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

* Usługa Atlassian Cloud obsługuje logowanie jednokrotne inicjowane za pomocą **SP oraz IDP**

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Dodawanie usługi Atlassian Cloud z galerii

Aby skonfigurować integrację usługi Atlassian Cloud z usługą Azure AD, należy dodać usługę Atlassian Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Atlassian Cloud** w polu wyszukiwania.
1. Wybierz pozycję **Atlassian Cloud** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą chmury Atlassian przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze Atlassian.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury Atlassian, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w chmurze Atlassian](#configure-atlassian-cloud-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego w chmurze Atlassian](#create-atlassian-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w chmurze Atlassian, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji w **chmurze Atlassian** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://auth.atlassian.com/saml/<unique ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    d. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Te prawdziwe wartości są uzyskiwane z poziomu ekranu **Konfiguracja SAML chmury Atlassian** , który został wyjaśniony w dalszej części tego samouczka **Konfigurowanie usługi Atlassian w chmurze logowanie** jednokrotne.

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Wklej wartość z wystąpienia, którego używasz do logowania się do portalu administracyjnego w chmurze Atlassian.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Aplikacja Atlassian Cloud oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Atlassian Cloud oczekuje, że atrybut **nameidentifier** będzie zamapowany na atrybut **user.mail**, dlatego należy zmodyfikować mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj usługę Atlassian Cloud** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury Atlassian.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Atlassian Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-atlassian-cloud-sso"></a>Konfigurowanie logowania jednokrotnego w chmurze Atlassian

1. Aby zautomatyzować konfigurację w chmurze Atlassian, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup Atlassian Cloud** , aby kierować do aplikacji w chmurze Atlassian. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do chmury Atlassian. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować chmurę Atlassian, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny usługi Atlassian w chmurze jako administrator i wykonaj następujące czynności:

1. Musisz zweryfikować swoją domenę przed przejściem do konfigurowania logowania jednokrotnego. Aby uzyskać więcej informacji, zobacz dokument [Weryfikacja domeny Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

1. W lewym okienku wybierz pozycję **zabezpieczenia** > **Logowanie jednokrotne w protokole SAML**. Jeśli nie zostało to jeszcze zrobione, zasubskrybuj usługę Atlassian Identity Manager.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. W oknie **Add SAML configuration** (Dodawanie konfiguracji SAML) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. W polu **Identyfikator jednostki dostawcy tożsamości** wklej **Identyfikator usługi Azure AD** , który został skopiowany z Azure Portal.

    b. W polu **adres URL logowania JEDNOkrotnego dostawcy tożsamości** wklej **adres URL logowania** skopiowany z Azure Portal.

    d. Otwórz pobrany certyfikat w witrynie Azure Portal w pliku TXT, skopiuj wartość (bez wierszy *Begin Certificate* i *End Certificate*), a następnie wklej ją w polu **Public X509 certificate** (Publiczny certyfikat X509).

    d. Kliknij pozycję **Save Configuration** (Zapisz konfigurację).

1. Aby upewnić się, że skonfigurowano poprawne adresy URL, zaktualizuj ustawienia usługi Azure AD, wykonując następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. W oknie SAML Skopiuj **Identyfikator tożsamości Sp** , a następnie w Azure Portal w obszarze Atlassian Cloud **Basic SAML Configuration**, wklej go w polu **identyfikatora** .

    b. W oknie SAML Skopiuj **adres URL usługi odbiorcy potwierdzenia Sp** , a następnie w Azure Portal, w obszarze Atlassian Cloud **Basic SAML Configuration**, wklej go w polu **adres URL odpowiedzi** . Adres URL logowania jest adresem URL dzierżawy Twojej usługi Atlassian Cloud.

    > [!NOTE]
    > Jeśli jesteś istniejącym klientem, po zaktualizowaniu wartości **Identyfikator tożsamości SP** i **Adres URL usługi Assertion Consumer Service SP** w witrynie Azure Portal wybierz opcję **Tak, zaktualizuj konfigurację**. Jeśli jesteś nowym klientem, możesz pominąć ten krok.

### <a name="create-atlassian-cloud-test-user"></a>Tworzenie użytkownika testowego usługi Atlassian Cloud

Aby umożliwić użytkownikom usługi Azure AD logowanie do usługi Atlassian Cloud, aprowizuj ręcznie konta użytkowników w usłudze Atlassian Cloud, wykonując następujące czynności:

1. W okienku **Administracja** okienku wybierz pozycję **Użytkownicy**.

    ![Link Użytkownicy usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Aby utworzyć użytkownika w usłudze Atlassian Cloud, wybierz pozycję **Zaproś użytkownika**.

    ![Tworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. W polu **Adres e-mail** wprowadź adres e-mail użytkownika, a następnie przypisz dostęp do aplikacji.

    ![Tworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Aby wysłać wiadomość e-mail z zaproszeniem do użytkownika, wybierz pozycję **Zaproś użytkowników**. Wiadomość e-mail z zaproszeniem zostanie wysłana do użytkownika, a po zaakceptowaniu zaproszenia użytkownik będzie aktywny w systemie.

> [!NOTE]
> Możesz również zbiorczo utworzyć użytkowników, wybierając przycisk **Tworzenie zbiorcze** w sekcji **Użytkownicy**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Atlassian Cloud w panelu dostępu należy automatycznie zalogować się do chmury Atlassian, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj chmurę Atlassian z usługą Azure AD](https://aad.portal.azure.com/)