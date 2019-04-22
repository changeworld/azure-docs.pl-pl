---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą O.C. Napisu Czarnecka - AppreciateHub | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i O.C. Napisu Czarnecka - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 21cbef1532928d51ba0c9f11e80304933df505b1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261755"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą O.C. Napisu Czarnecka - AppreciateHub

W tym samouczku dowiesz się, jak zintegrować O.C. Napisu Czarnecka - AppreciateHub za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie O.C. Napisu Czarnecka - AppreciateHub z usługą Azure AD oferuje następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do O.C. Napisu Czarnecka - AppreciateHub.
* Umożliwia użytkownikom można automatycznie zalogowany do O.C. Napisu Czarnecka - AppreciateHub (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą O.C. Napisu Czarnecka - AppreciateHub, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* O.C. Napisu Czarnecka - AppreciateHub logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* O.C. Obsługuje AppreciateHub napisu Czarnecka - **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Dodawanie O.C. Napisu Czarnecka - AppreciateHub z galerii

Aby skonfigurować integrację O.C. Napisu Czarnecka - AppreciateHub w usłudze Azure AD, musisz dodać O.C. Napisu Czarnecka - AppreciateHub z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać O.C. Napisu Czarnecka - AppreciateHub z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **O.C. Napisu Czarnecka - AppreciateHub**, wybierz opcję **O.C. Napisu Czarnecka - AppreciateHub** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![O.C. Napisu Czarnecka - AppreciateHub na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą O.C. Napisu Czarnecka - AppreciateHub w oparciu o nazwie użytkownika testowego **Britta Simon**.
Logowanie jednokrotne do pracy, relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w O.C. Napisu Czarnecka - AppreciateHub musi zostać nawiązane.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą O.C. Napisu Czarnecka - AppreciateHub, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie O.C. Napisu Czarnecka - AppreciateHub logowania jednokrotnego](#configure-oc-tanner---appreciatehub-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz O.C. Napisu Czarnecka - użytkownika testowego AppreciateHub](#create-oc-tanner---appreciatehub-test-user)**  — aby odpowiednikiem Britta Simon w O.C. Napisu Czarnecka - AppreciateHub połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z O.C. Napisu Czarnecka - AppreciateHub, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **O.C. Napisu Czarnecka - AppreciateHub** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >Możesz pobrać **plik metadanych usługodawcy** z [tutaj](https://fed.appreciatehub.com/fed/sp/metadata)

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w sekcji podstawową konfigurację protokołu SAML.

     ![O.C. Napisu Czarnecka - AppreciateHub domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    > [!Note]
    > Jeśli **identyfikator** i **adres URL odpowiedzi** wartości nie uzyskać polulated automatycznie, a następnie wprowadź wartości ręcznie zgodnie z wymaganiami. Skontaktuj się z [O.C. Napisu Czarnecka - zespół obsługi klienta AppreciateHub](mailto:sso@octanner.com) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Na **Konfigurowanie O.C. Napisu Czarnecka - AppreciateHub** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-oc-tanner---appreciatehub-single-sign-on"></a>Konfigurowanie O.C. Napisu Czarnecka - AppreciateHub logowanie jednokrotne

Aby skonfigurować logowanie jednokrotne na **O.C. Napisu Czarnecka - AppreciateHub** stronie, musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowany adresy URL z portalu Azure w celu [O.C. Napisu Czarnecka - zespołem pomocy technicznej AppreciateHub](mailto:sso@octanner.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** użytkownika typu pola, takie jak BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji należy włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do O.C. Napisu Czarnecka - AppreciateHub.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **O.C. Napisu Czarnecka - AppreciateHub**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **O.C. Napisu Czarnecka - AppreciateHub**.

    ![O.C. Napisu Czarnecka - AppreciateHub łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Utwórz O.C. Napisu Czarnecka - AppreciateHub użytkownika testowego

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w O.C. Napisu Czarnecka - AppreciateHub.

**Aby utworzyć użytkownika o nazwie Britta Simon w O.C. Napisu Czarnecka - AppreciateHub, wykonaj następujące czynności:**

Poproś użytkownika [O.C. Napisu Czarnecka - zespołem pomocy technicznej AppreciateHub](mailto:sso@octanner.com) Aby utworzyć użytkownika mającego jako atrybut nameID taką samą wartość jak nazwa użytkownika w pozycji Britta simon w usłudze Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu O.C. Napisu Czarnecka - AppreciateHub kafelka w panelu dostępu użytkownik powinien być automatycznie zalogowany do O.C. Napisu Czarnecka - AppreciateHub, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
