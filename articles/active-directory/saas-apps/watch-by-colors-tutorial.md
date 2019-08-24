---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z funkcją oglądania według kolorów | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i oglądać kolory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0882be0419cbbbc7d94cb8d517e27bdb06a780
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014139"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z oglądaniem przez kolory

W tym samouczku dowiesz się, jak zintegrować usługę Watch przez kolory z Azure Active Directory (Azure AD). Po zintegrowaniu oglądania przez kolory z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do oglądania przez kolory.
* Zezwól użytkownikom na automatyczne logowanie się, aby oglądać kolory na kontach usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Obejrzyj w ramach subskrypcji z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Kontrolki według kolorów obsługują usługę **SP i dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-watch-by-colors-from-the-gallery"></a>Dodawanie czujki przez kolory z galerii

Aby skonfigurować integrację oglądania przez kolory z usługą Azure AD, musisz dodać kontrolkę Obejrzyj według kolorów z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Watch według kolorów** w polu wyszukiwania.
1. Wybierz pozycję **Obejrzyj według kolorów** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby oglądania przez kolory

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu funkcji oglądania przez kolory za pomocą użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w obszarze Obejrzyj według kolorów.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą kolorów Watch, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj kontrolowanie według kolorów logowanie](#configure-watch-by-colors-sso)** jednokrotne — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego czujki według kolorów](#create-watch-by-colors-test-user)** , aby uzyskać odpowiednika B. Simon w Obejrzyj według kolorów, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na [Azure Portal](https://portal.azure.com/)na stronie " **Obejrzyj według kolorów** " integracji aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja** SAML aplikacja została wstępnie skonfigurowana w trybie inicjalizacji **dostawcy tożsamości** , a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://app.colorscorporation.com/login`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do oglądania przez kolory.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Obejrzyj według kolorów**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-watch-by-colors-sso"></a>Konfigurowanie czujki według kolorów Logowanie jednokrotne

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Watch przez kolory jako administrator i wykonaj następujące czynności:

1. W prawym górnym rogu strony kliknij pozycję **profil** > **Ustawienia** > konta**Logowanie jednokrotne (logowanie**jednokrotne).

    ![Konfiguracja czujki według kolorów](./media/watch-by-colors-tutorial/config01.png)

1. Na stronie Logowanie jednokrotne **(logowanie** jednokrotne) wykonaj następujące czynności:

    ![Konfiguracja czujki według kolorów](./media/watch-by-colors-tutorial/config02.png)

    a. Przełącz **opcję Włącz protokół SAML** na **włączony**.

    b. W polu tekstowym **adres URL** wklej **adres URL metadanych Federacji**, który został skopiowany z Azure Portal.

    c. Kliknij przycisk **Importuj**, a następnie następujące pola automatycznie wypełnią na stronie.

    d. Kliknij polecenie **Zapisz**.

### <a name="create-watch-by-colors-test-user"></a>Tworzenie użytkownika testowego czujki przez kolory

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu oglądania według kolorów, muszą one być obsługiwane do oglądania przez kolory. W obszarze Obejrzyj według kolorów Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się, aby obejrzeć kolory jako administrator zabezpieczeń.

1. W prawym górnym rogu strony kliknij pozycję **Profile** > **Użytkownicy** > **Dodaj użytkownika**.

    ![Konfiguracja czujki według kolorów](./media/watch-by-colors-tutorial/config03.png)

1. Na stronie **szczegóły użytkownika** wykonaj następujące czynności:

    ![Konfiguracja czujki według kolorów](./media/watch-by-colors-tutorial/config04.png)

    a. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B**.

    b. W polu tekstowym **Last name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    c. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np `B.Simon@contoso.com`.

    d. W polu tekstowym **hasło** wprowadź hasło.

    e. Wybierz **uprawnienia konta** zgodnie z Twoją organizacją.

    f. Kliknij polecenie **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Obejrzyj według kolorów w panelu dostępu należy automatycznie zalogować się do opcji Obejrzyj według kolorów, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)

