---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą FileCloud | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i FileCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2263e583-3eb2-4a06-982d-33f5f54858f4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: jeedes
ms.openlocfilehash: 74fa3065e99b1844bb315d1dcfa3f0e7ad1f8fee
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814617"
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą FileCloud

W tym samouczku dowiesz się, jak zintegrować FileCloud w usłudze Azure Active Directory (Azure AD).

Integrowanie FileCloud z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do FileCloud.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do FileCloud (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą FileCloud, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- FileCloud logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie FileCloud z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-filecloud-from-the-gallery"></a>Dodawanie FileCloud z galerii
Aby skonfigurować integrację FileCloud w usłudze Azure AD, należy dodać FileCloud z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać FileCloud z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **FileCloud**, wybierz opcję **FileCloud** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![FileCloud na liście wyników](./media/filecloud-tutorial/tutorial_filecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą FileCloud w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w FileCloud do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w FileCloud musi można ustanowić.

W FileCloud, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą FileCloud, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego FileCloud](#create-a-filecloud-test-user)**  — aby odpowiednikiem Britta Simon w FileCloud połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji FileCloud.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z FileCloud, wykonaj następujące czynności:**

1. W witrynie Azure portal na **FileCloud** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/filecloud-tutorial/tutorial_filecloud_samlbase.png)

1. Na **FileCloud domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![FileCloud domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/filecloud-tutorial/tutorial_filecloud_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.filecloudonline.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.filecloudonline.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta FileCloud](mailto:support@codelathe.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/filecloud-tutorial/tutorial_filecloud_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/filecloud-tutorial/tutorial_general_400.png)

1. Na **konfiguracji FileCloud** , kliknij przycisk **skonfigurować FileCloud** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![FileCloud Configuration](./media/filecloud-tutorial/tutorial_filecloud_configure.png) 

1. W oknie przeglądarki internetowej innej logowanie jednokrotne do swojej dzierżawy FileCloud jako administrator.

1. W okienku nawigacji po lewej stronie kliknij **ustawienia**. 
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/filecloud-tutorial/tutorial_filecloud_000.png)

1. Kliknij przycisk **logowania jednokrotnego** karty w sekcji Ustawienia. 
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/filecloud-tutorial/tutorial_filecloud_001.png)

1. Wybierz **SAML** jako **domyślny typ logowania jednokrotnego** na **ustawienia Logowanie jednokrotne (SSO)** panelu.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/filecloud-tutorial/tutorial_filecloud_002.png)

1. W **adres URL punktu końcowego protokołu IdP** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/filecloud-tutorial/tutorial_filecloud_003.png)

1. Otwórz plik metadanych pobranego w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **metadane dostawcy tożsamości** tekstowe na **ustawienia języka SAML** panelu.

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/filecloud-tutorial/tutorial_filecloud_004.png)

1. Kliknij przycisk **Save** (Zapisz).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/filecloud-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/filecloud-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/filecloud-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/filecloud-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-filecloud-test-user"></a>Tworzenie użytkownika testowego FileCloud

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w FileCloud. FileCloud obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu FileCloud, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta FileCloud](mailto:support@codelathe.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do FileCloud.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon FileCloud, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **FileCloud**.

    ![Link FileCloud na liście aplikacji](./media/filecloud-tutorial/tutorial_filecloud_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka FileCloud w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji FileCloud.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/filecloud-tutorial/tutorial_general_01.png
[2]: ./media/filecloud-tutorial/tutorial_general_02.png
[3]: ./media/filecloud-tutorial/tutorial_general_03.png
[4]: ./media/filecloud-tutorial/tutorial_general_04.png

[100]: ./media/filecloud-tutorial/tutorial_general_100.png

[200]: ./media/filecloud-tutorial/tutorial_general_200.png
[201]: ./media/filecloud-tutorial/tutorial_general_201.png
[202]: ./media/filecloud-tutorial/tutorial_general_202.png
[203]: ./media/filecloud-tutorial/tutorial_general_203.png

