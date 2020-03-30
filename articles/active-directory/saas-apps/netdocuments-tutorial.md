---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą NetDocuments | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a dokumentami NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71e5e13485c4a10664d98363e8e99bfd3b4f4bcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72035701"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Samouczek: Integracja logowania jednokrotnego usługi Azure Active Directory z usługą NetDocuments

W tym samouczku dowiesz się, jak zintegrować NetDocuments z usługą Azure Active Directory (Azure AD). Podczas integrowania NetDocuments z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do NetDocuments.
* Włącz użytkownikom automatyczne logowanie do netdocuments za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego NetDocuments.NetDocuments single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* NetDocuments obsługuje sso inicjowane przez **usługę SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Dodawanie dokumentów NetDocuments z galerii

Aby skonfigurować integrację netdocuments do usługi Azure AD, należy dodać NetDocuments z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **polecenie NetDocuments** w polu wyszukiwania.
1. Wybierz **polecenie NetDocuments** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla dokumentów NetDocuments

Konfigurowanie i testowanie sytuowania usługi Azure AD za pomocą netdocuments przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programach NetDocuments.

Aby skonfigurować i przetestować sytua usługi Azure AD z pomocą netdocuments, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne netdocuments](#configure-netdocuments-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego NetDocuments](#create-netdocuments-test-user)** — aby mieć odpowiednik B.Simon w NetDocuments, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **NetDocuments** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    d. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego adresu URL logowania i adresu URL odpowiedzi. Identyfikator repozytorium jest wartością zaczynaną od **urzędu certyfikacji,** po której następuje 8-znakowy kod skojarzony z repozytorium dokumentów NetDocuments. Aby uzyskać więcej informacji, można sprawdzić [dokument pomocy technicznej netdocuments federacyjnej tożsamości.](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) Alternatywnie można skontaktować się z [zespołem pomocy technicznej klienta NetDocuments,](https://support.netdocuments.com/hc/) aby uzyskać te wartości, jeśli masz trudności z konfiguracją przy użyciu powyższych informacji . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja NetDocuments oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja NetDocuments **oczekuje,** że identyfikator nazw zostanie zamapowany z **employeeid** lub innym roszczeniem, które ma zastosowanie do organizacji jako **identyfikatora nazwy,** więc musisz edytować mapowanie atrybutów, klikając ikonę **Edytuj** i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie dokumentów NetDocuments** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do NetDocuments.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **NetDocuments**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-netdocuments-sso"></a>Konfigurowanie dokumentu sytego netdocuments

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy NetDocuments jako administrator.

2. Przejdź do **admina**.

3. Kliknij **pozycję Dodaj i usuń użytkowników i grupy**.
   
    ![Repozytorium](./media/netdocuments-tutorial/ic795047.png "Repozytorium")

4. Kliknij **pozycję Konfiguruj zaawansowane opcje uwierzytelniania**.
    
    ![Konfigurowanie zaawansowanych opcji uwierzytelniania](./media/netdocuments-tutorial/ic795048.png "Konfigurowanie zaawansowanych opcji uwierzytelniania")

5. W oknie dialogowym **Tożsamość federacyjnej** wykonaj następujące czynności:
   
    ![Tożsamość federacyjna](./media/netdocuments-tutorial/ic795049.png "Tożsamość federacyjna")
   
    a. Jako **typ serwera tożsamości federacyjnej**wybierz pozycję Usługi **federacyjne Active Directory**.
   
    b. Kliknij **przycisk Wybierz plik**, aby przekazać pobrany plik metadanych pobrany z witryny Azure portal.
   
    d. Kliknij przycisk **OK**.

### <a name="create-netdocuments-test-user"></a>Utwórz użytkownika testowego NetDocuments

Aby umożliwić użytkownikom usługi Azure AD logowanie się do netdocuments, muszą one być aprowizować w NetDocuments.  
W przypadku NetDocuments inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy **NetDocuments** jako administrator.

2. W menu u góry kliknij pozycję **Admin** (Administrator).
   
    ![Administracja](./media/netdocuments-tutorial/ic795051.png "Administrator")

3. Kliknij **pozycję Dodaj i usuń użytkowników i grupy**.
   
    ![Repozytorium](./media/netdocuments-tutorial/ic795047.png "Repozytorium")

4. W polach **tekstowych Adres e-mail** wpisz adres e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować, a następnie kliknij pozycję **Dodaj użytkownika**.
   
    ![Adres e-mail](./media/netdocuments-tutorial/ic795053.png "Adres e-mail")
   
    >[!NOTE]
    >Posiadacz konta usługi Azure Active Directory otrzyma wiadomość e-mail zawierającą łącze potwierdzające konto, zanim stanie się aktywne. Do aprowizowania kont użytkowników usługi Azure Active Directory można użyć innych narzędzi do tworzenia kont użytkowników netdocuments lub interfejsów API udostępnianych przez program NetDocuments.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka NetDocuments w Panelu dostępu należy automatycznie zalogować się do dokumentów NetDocuments, dla których skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj netdocuments z usługą Azure AD](https://aad.portal.azure.com/)

