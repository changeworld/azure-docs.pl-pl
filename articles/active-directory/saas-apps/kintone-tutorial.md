---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą platformy Kintone | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i platformy Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 600165c06fcc814fbb8df180d955235104153abd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157520"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą platformy Kintone

W tym samouczku dowiesz się, jak zintegrować platformy Kintone w usłudze Azure Active Directory (Azure AD).

Integrowanie platformy Kintone z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do platformy Kintone
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do platformy Kintone (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu platformy Kintone, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Platformy Kintone logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie platformy Kintone z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-kintone-from-the-gallery"></a>Dodawanie platformy Kintone z galerii
Aby skonfigurować integrację platformy Kintone w usłudze Azure AD, należy dodać platformy Kintone z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać platformy Kintone z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **platformy Kintone**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/tutorial_kintone_search.png)

1. W panelu wyników wybierz **platformy Kintone**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą platformy Kintone w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w platformy Kintone do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w platformy Kintone musi zostać ustanowione.

W platformy Kintone, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu platformy Kintone, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego platformy Kintone](#creating-a-kintone-test-user)**  — aby odpowiednikiem Britta Simon w platformy Kintone połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji platformy Kintone.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu platformy Kintone, wykonaj następujące czynności:**

1. W witrynie Azure portal na **platformy Kintone** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/kintone-tutorial/tutorial_kintone_samlbase.png)

1. Na **platformy Kintone domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kintone-tutorial/tutorial_kintone_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.kintone.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespół obsługi klienta platformy Kintone](https://www.kintone.com/contact/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/kintone-tutorial/tutorial_kintone_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/kintone-tutorial/tutorial_general_400.png)

1. Na **konfiguracji platformy Kintone** kliknij **Konfigurowanie platformy Kintone** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/kintone-tutorial/tutorial_kintone_configure.png) 

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **platformy Kintone** witryny firmy jako administrator.

1. Kliknij pozycję **Ustawienia**.
   
    ![Ustawienia](./media/kintone-tutorial/ic785879.png "Ustawienia")

1. Kliknij przycisk **użytkowników i administrowanie systemem**.
   
    ![Użytkownicy i administrowanie systemem](./media/kintone-tutorial/ic785880.png "użytkowników i Administracja")

1. W obszarze **administrowania systemem \> zabezpieczeń** kliknij **logowania**.
   
    ![Zaloguj się](./media/kintone-tutorial/ic785881.png "logowania")

1. Kliknij przycisk **uwierzytelnianie SAML Włącz**.
   
    ![Uwierzytelnianie SAML](./media/kintone-tutorial/ic785882.png "Uwierzytelnianie SAML")

1. W sekcji uwierzytelnianie SAML wykonaj następujące czynności:
    
    ![Uwierzytelnianie SAML](./media/kintone-tutorial/ic785883.png "Uwierzytelnianie SAML")
    
    a. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    b. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
    
    c. Kliknij przycisk **Przeglądaj** Aby przekazać certyfikat pobrany.
    
    d. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-kintone-test-user"></a>Tworzenie użytkownika testowego platformy Kintone

Aby umożliwić użytkownikom usługi Azure AD zalogować się do platformy Kintone, musi być przygotowana do platformy Kintone.  
W przypadku platformy Kintone Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojej **platformy Kintone** witryny firmy jako administrator.

1. Kliknij przycisk **ustawienie**.
   
    ![Ustawienia](./media/kintone-tutorial/ic785879.png "Ustawienia")

1. Kliknij przycisk **użytkowników i administrowanie systemem**.
   
    ![Administracja systemu & użytkownika](./media/kintone-tutorial/ic785880.png "Administracja systemu & użytkownika")

1. W obszarze **Administracja użytkownikami**, kliknij przycisk **działów i użytkownicy**.
   
    ![Dział & użytkowników](./media/kintone-tutorial/ic785888.png "działu & użytkowników")

1. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Nowi użytkownicy](./media/kintone-tutorial/ic785889.png "nowych użytkowników")

1. W **nowego użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Nowi użytkownicy](./media/kintone-tutorial/ic785890.png "nowych użytkowników")
   
    a. Wpisz **nazwy wyświetlanej**, **nazwa logowania**, **nowe hasło**, **Potwierdź hasło**, **adres E-mail**, i inne informacje szczegółowe dotyczące prawidłowego konta usługi AAD chcesz aprowizować do powiązanych pól tekstowych.
 
    b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Można użyć jakichkolwiek innych platformy Kintone użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez platformy Kintone do aprowizacji kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu do platformy Kintone.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do platformy Kintone, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **platformy Kintone**.

    ![Konfigurowanie logowania jednokrotnego](./media/kintone-tutorial/tutorial_kintone_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka platformy Kintone w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji platformy Kintone.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kintone-tutorial/tutorial_general_01.png
[2]: ./media/kintone-tutorial/tutorial_general_02.png
[3]: ./media/kintone-tutorial/tutorial_general_03.png
[4]: ./media/kintone-tutorial/tutorial_general_04.png

[100]: ./media/kintone-tutorial/tutorial_general_100.png

[200]: ./media/kintone-tutorial/tutorial_general_200.png
[201]: ./media/kintone-tutorial/tutorial_general_201.png
[202]: ./media/kintone-tutorial/tutorial_general_202.png
[203]: ./media/kintone-tutorial/tutorial_general_203.png

