---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą abstrakcyjny | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i abstrakcyjny.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8038784c9da30a42541688536169576cd9a92e9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235011"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Samouczek: Integrowanie abstrakcyjny za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować abstrakcyjny za pomocą usługi Azure Active Directory (Azure AD). W ramach abstrakcyjny integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do abstrakcyjny.
* Umożliwianie użytkownikom można automatycznie zalogowany abstrakcyjny za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Abstrakcyjna logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym.

* Abstrakcyjna obsługuje **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-abstract-from-the-gallery"></a>Dodawanie abstrakcyjny z galerii

Aby skonfigurować integrację abstrakcyjny w usłudze Azure AD, należy dodać abstrakcyjny z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **abstrakcyjne** w polu wyszukiwania.
1. Wybierz **abstrakcyjne** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą abstrakcyjny za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w abstrakcyjny.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą abstrakcyjny, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego abstrakcyjne](#configure-abstract-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego abstrakcyjne](#create-abstract-test-user)**  — aby odpowiednikiem Britta Simon w abstrakcyjny, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **abstrakcyjne** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** aplikacja jest wstępnie skonfigurowana w sekcji **tożsamości** zainicjowano tryb i niezbędne adresy URL są już wstępnie wypełnione przy użyciu platformy Azure. Użytkownik musi zapisać konfigurację, klikając **Zapisz** przycisku.

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://app.abstract.com/signin`

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurowanie logowania jednokrotnego abstrakcyjne

Upewnij się pobrać swojej `App Federation Metadata Url` i `Azure AD Identifier` w witrynie Azure portal, co Ty, należy te, aby skonfigurować logowanie Jednokrotne na ogólny.

Te informacje można znaleźć na **Ustaw się logowanie jednokrotne z SAML** strony:

* `App Federation Metadata Url` Znajduje się w **certyfikat podpisywania SAML** sekcji.
* `Azure AD Identifier` Znajduje się w **Konfigurowanie abstrakcyjny** sekcji.


Teraz można przystąpić do konfigurowania logowania jednokrotnego na ogólny:

>[!Note]
>Należy do uwierzytelniania za pomocą organizacji konta administratora do dostępu do ustawień logowania jednokrotnego na abstrakcyjny.

1. Otwórz [aplikacji sieci web abstrakcyjne](https://app.abstract.com/).
2. Przejdź do **uprawnienia** strony w lewym pasku bocznym.
3. W **Konfigurowanie logowania jednokrotnego** sekcji, wprowadź swoje **adres URL metadanych** i **identyfikator jednostki**.
4. Wprowadź ręcznie wyjątki mogą się. Wiadomości e-mail, wymienione w sekcji ręczne wyjątki będą logowania jednokrotnego obejścia i będzie można zalogować się przy użyciu adresu e-mail i hasła. 
5. Kliknij przycisk **Zapisz zmiany**.

>[!Note] 
>Należy używać adresów podstawowy adres e-mail, na liście wyjątków ręczne. Aktywacja rejestracji Jednokrotnej zakończy się niepowodzeniem, jeśli adres e-mail, na liście został pomocniczego adresu e-mail użytkownika. Jeśli tak się stanie, zobaczysz komunikat o błędzie przy użyciu podstawowego adresu e-mail konta się niepowodzeniem. Ten podstawowy adres e-mail należy dodać do listy wyjątków ręczne po upewnieniu się, że znasz użytkownika.

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

W tej sekcji można udostępnić B.Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do abstrakcyjny.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **abstrakcyjne**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-abstract-test-user"></a>Tworzenie użytkownika testowego abstrakcyjne

W celu przetestowania logowania jednokrotnego w abstrakcyjny:

1. Otwórz [aplikacji sieci web abstrakcyjne](https://app.abstract.com/).
2. Przejdź do **uprawnienia** strony w lewym pasku bocznym.
3. Kliknij przycisk **testu z moim kontem**. Jeśli test wypadnie niepomyślnie, [skontaktuj się z naszym zespołem pomocy technicznej](https://www.abstract.com/help/contact/).

>[!Note]
>Należy do uwierzytelniania za pomocą organizacji konta administratora do dostępu do ustawień logowania jednokrotnego na abstrakcyjny.
Ta organizacja konta administratora należy do przypisania do abstrakcyjnej w witrynie Azure portal.

### <a name="test-sso"></a>Test rejestracji Jednokrotnej 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka abstrakcyjne w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze abstrakcyjna, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

