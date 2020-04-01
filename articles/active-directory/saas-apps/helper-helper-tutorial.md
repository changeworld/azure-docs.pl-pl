---
title: 'Samouczek: Integracja usługi Azure Active Directory z pomocnikiem pomocy | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pomocnikiem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5f42e4d7-4d92-4096-a0d5-02fa438a5dfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d70cdade93b028c90c9f62374cd6b997556dd52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67101282"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>Samouczek: Integracja pomocnika pomocnika z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Pomocnika pomocnika z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Pomocnika pomocnika z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Pomocnika Pomocnika.
* Włącz użytkownikom automatyczne logowanie do Pomocnika pomocnika za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego pomocnika Pomocnika .Helper Single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Pomocnik pomocniczy obsługuje **sp i idp** zainicjowane SSO i obsługuje **just in time** użytkownika inicjowania obsługi administracyjnej.

## <a name="adding-helper-helper-from-the-gallery"></a>Dodawanie pomocnika z galerii

Aby skonfigurować integrację Pomocnika pomocnika z usługą Azure AD, należy dodać Pomocnika pomocnika z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **pomocnik pomocnika** w polu wyszukiwania.
1. Wybierz **pozycję Pomocnik pomocniczy** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą pomocnika pomocnika przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Pomocniku pomocnika.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą pomocnika pomocnika, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Pomocnika pomocnika,](#configure-helper-helper)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz pomocnika pomocnika użytkownika testowego,](#create-helper-helper-test-user)** aby mieć odpowiednik B. Simon w Pomocnik pomocnika, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Pomocnika** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli masz **plik metadanych dostawcy usług** i chcesz skonfigurować w trybie inicjowanym przez **IDP,** w sekcji **Podstawowa konfiguracja SAML,** wykonaj następujące czynności:

    >[!NOTE]
    >Przejdź do `https://sso.helperhelper.com/saml/<customer_id>` adresu URL, aby uzyskać plik metadanych dostawcy usług. Skontaktuj się z [zespołem pomocy technicznej pomocnika pomocnika](mailto:info@helperhelper.com) klienta dla `<customer_id>`.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    d. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    > [!Note]
    > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://sso.helperhelper.com/saml/<customer_id>/login`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej pomocnika pomocnika klienta,](mailto:info@helperhelper.com) aby uzyskać tę wartość. Można również odwołać się do wzorców pokazanych w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.l.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na Notatniku.

   ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. W sekcji **Konfigurowanie pomocnika** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Konfigurowanie pomocnika

Aby skonfigurować logowanie jednokrotne po stronie **Pomocnika** pomocnika, należy wysłać **adres URL metadanych federacji aplikacji** do zespołu pomocy pomocnika pomocnika [.](mailto:info@helperhelper.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B. Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Pomocnika Pomocnika.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Pomocnik**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-helper-helper-test-user"></a>Utwórz użytkownika testowego pomocnika

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Pomocnik pomocnika. Pomocnik pomocnika obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Pomocniku pomocnika, nowy jest tworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Pomocnika pomocnika w Panelu dostępu należy automatycznie zalogować się do pomocnika pomocnika, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)