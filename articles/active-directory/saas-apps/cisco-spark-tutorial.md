---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą Cisco Webex | Dokumenty firmy Microsoft'
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
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01fe4d06d5f73eacee1d1cdaf1963232b84daf05
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046785"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą Cisco Webex

W tym samouczku dowiesz się, jak zintegrować program Cisco Webex z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Cisco Webex z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi Cisco Webex.
* Włącz automatyczne logowanie użytkowników do usługi Cisco Webex za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) firmy Cisco Webex.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Cisco Webex obsługuje sso inicjowane przez **usługę SP.**
* Cisco Webex obsługuje **automatyczne** inicjowanie obsługi administracyjnej użytkowników.
* Po skonfigurowaniu usługi Cisco Webex można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie aplikacji Cisco Webex z galerii

Aby skonfigurować integrację aplikacji Cisco Webex z usługą Azure AD, musisz dodać aplikację Cisco Webex z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **cisco Webex** w polu wyszukiwania.
1. Wybierz **pozycję Cisco Webex** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi Cisco Webex

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą aplikacji Cisco Webex przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w witrynie Cisco Webex.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD sytuować za pomocą aplikacji Cisco Webex, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
2. **[Skonfiguruj aplikację Cisco Webex,](#configure-cisco-webex)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
    1. **[Utwórz cisco Webex użytkownika testowego,](#create-cisco-webex-test-user)** aby mieć odpowiednik B.Simon w Cisco Webex, który jest połączony z reprezentacji usługi Azure AD użytkownika.
3. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Cisco Webex** znajdź sekcję **Zarządzaj** i wybierz **pozycję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż pobrany plik **metadanych dostawcy usług** i skonfiguruj aplikację, wykonując następujące kroki:

    >[!Note]
    >Plik metadanych dostawcy usług otrzymasz z sekcji **Konfiguruj cisco Webex,** co wyjaśniono w dalszej części samouczka. 

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    d. Po pomyślnym ukończeniu przekazywania pliku metadanych dostawcy usług wartości **Identyfikator** i **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** zostaną wypełnione automatycznie:

    W polu tekstowym **Podpisz na adresie URL** wklej wartość **adresu URL odpowiedzi,** która zostanie automatycznie wypełniona przez przekazywanie pliku metadanych SP.

1. Aplikacja Cisco Webex oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji Cisco Webex oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
  
    | Nazwa |  Atrybut źródłowy|
    | ---------------|--------- |
    | Identyfikator UID | user.userprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie cisco Webex** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do cisco Webex.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Cisco Webex**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-cisco-webex"></a>Konfigurowanie programu Cisco Webex

1. Aby zautomatyzować konfigurację w programie Cisco Webex, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Przycisk Skonfiguruj Cisco Webex** przekieruje Cię do aplikacji Cisco Webex. W tym miejscu podaj poświadczenia administratora, aby zalogować się do usługi Cisco Webex. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-8.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować usługę Cisco Webex, zaloguj się do [usługi Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) przy użyciu pełnych poświadczeń administratora.

4. Wybierz pozycję **Settings** (Ustawienia) i w sekcji **Authentication** (Uwierzytelnianie) kliknij pozycję **Modify** (Modyfikuj).

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Wybierz **pozycję Zintegruj dostawcę tożsamości innej firmy. (Zaawansowane)** i przejdź do następnego ekranu.

6. Na stronie **Import Idp Metadata** (Importowanie metadanych dostawcy tożsamości) przeciągnij i upuść plik metadanych usługi Azure AD lub użyj opcji przeglądarki plików, aby znaleźć i przekazać ten plik. Następnie zaznacz opcję **Require certificate signed by a certificate authority in Metadata (more secure)** (Wymagaj certyfikatu podpisanego przez urząd certyfikacji w metadanych (bezpieczniejsze)) i kliknij przycisk **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Wybierz pozycję **Test SSO Connection** (Testuj połączenie logowania jednokrotnego) i gdy zostanie otwarta nowa karta przeglądarki, uwierzytelnij się przy użyciu usługi Azure AD, logując się.

8. Wróć do karty przeglądarki **Cisco Cloud Collaboration Management.** Jeśli test zakończył się pomyślnie, wybierz **ten test zakończył się pomyślnie. Włącz opcję logowanie jednokrotne** i kliknij przycisk **Dalej**.

### <a name="create-cisco-webex-test-user"></a>Tworzenie użytkownika testowego aplikacji Cisco Webex

W tej sekcji utworzysz użytkownika o nazwie B.Simon w cisco Webex. W tej sekcji utworzysz użytkownika o nazwie B.Simon w cisco Webex.

1. Przejdź do portalu [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) i zaloguj się przy użyciu pełnych poświadczeń administratora.

2. Kliknij pozycję **Users** (Użytkownicy), a następnie pozycję **Manage Users** (Zarządzaj użytkownikami).
   
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. W oknie **Manage User** (Zarządzanie użytkownikiem) wybierz pozycję **Manually add or modify users** (Ręcznie dodaj lub modyfikuj użytkowników) i kliknij przycisk **Next** (Dalej).

4. Wybierz pozycję **Names and Email address** (Nazwy i adres e-mail). Wypełnij pola tekstowe w następujący sposób:

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. W polu tekstowym **Imię** wpisz imię użytkownika, takiego jak **B**.

    b. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    d. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład b.simon@contoso.com.

5. Kliknij znak plus, aby dodać B.Simon. Następnie kliknij przycisk **Dalej**.

6. W oknie **Add Services for Users** (Dodawanie usług dla użytkowników) kliknij przycisk **Save** (Zapisz), a następnie przycisk **Finish** (Zakończ).

## <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Cisco Webex w Panelu dostępu należy automatycznie zalogować się do programu Cisco Webex, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Cisco Webex z usługą Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić cisco webex dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/protect-webex)