---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Datahug | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Datahug.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: a7a7a9cffa2dab3cc50a393f15d7419728502f8e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814227"
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Datahug

W tym samouczku dowiesz się, jak zintegrować Datahug w usłudze Azure Active Directory (Azure AD).

Integrowanie Datahug z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Datahug
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Datahug (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz. [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Datahug, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Datahug logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Datahug z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-datahug-from-the-gallery"></a>Dodawanie Datahug z galerii
Aby skonfigurować integrację Datahug w usłudze Azure AD, należy dodać Datahug z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Datahug z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Datahug**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/tutorial_datahug_search.png)

1. W panelu wyników wybierz **Datahug**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Datahug w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Datahug do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Datahug musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Datahug.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Datahug, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Datahug](#creating-a-datahug-test-user)**  — aby odpowiednikiem Britta Simon w Datahug połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Datahug.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Datahug, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Datahug** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_datahug_samlbase.png)

1. Na **Datahug domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_datahug_ur1.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://apps.datahug.com/identity/<uniqueID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://apps.datahug.com/identity/<uniqueID>/acs`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_datahug_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://apps.datahug.com/`
     
    > [!NOTE] 
    > Te wartości nie są rzeczywiste. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. W tym miejscu zalecamy przy użyciu unikatowej wartości ciągu w identyfikatorze i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Datahug](http://datahug.com/about/contact-us/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_datahug_certificate.png) 

1.  Sprawdź **"Pokaż zaawansowane ustawienia podpisywania certyfikatów"** i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_datahug_cert.png)

    a. W **opcja podpisywania**, wybierz opcję **potwierdzenie SAML logowania**.
    
    b. W **algorytmu podpisywania**, wybierz opcję **SHA1**.
 
1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji Datahug** , kliknij przycisk **skonfigurować Datahug** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki** i **SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_datahug_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Datahug** stronie, musisz wysłać pobrany **XML metadanych**, **identyfikator jednostki SAML** i **SAML pojedynczego logowania jednokrotnego adres URL usługi**  do [obsługi Datahug](http://datahug.com/about/contact-us/). Mogą skonfigurować tę aplikację do zostały ustawione prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-datahug-test-user"></a>Tworzenie użytkownika testowego Datahug

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Datahug, musi być obsługiwana w Datahug.  
Gdy Datahug, inicjowanie obsługi to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Datahug jako administrator.

1. Umieść kursor nad **koło zębate** w prawym górnym rogu i kliknij **ustawienia**
   
   ![Dodawanie pracownika](./media/datahug-tutorial/1.png)

1. Wybierz **osób** i kliknij przycisk **Add Users** kartę

    ![Dodawanie pracownika](./media/datahug-tutorial/2.png)

1. Wpisz adres e-mail osoby, czy chcesz utworzyć konto, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie pracownika](./media/datahug-tutorial/3.png)

    > [!NOTE] 
    > Możesz wysłać wiadomość e-mail dotycząca rejestracji użytkownika, wybierając **wysyłania powitalnej wiadomości e-mail** pola wyboru.  
    > W przypadku tworzenia konta usługi Salesforce nie będą wysyłane powitalnej wiadomości e-mail.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Datahug.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Datahug, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Datahug**.

    ![Konfigurowanie logowania jednokrotnego](./media/datahug-tutorial/tutorial_datahug_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.
Po kliknięciu kafelka Datahug w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Datahug. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/datahug-tutorial/tutorial_general_01.png
[2]: ./media/datahug-tutorial/tutorial_general_02.png
[3]: ./media/datahug-tutorial/tutorial_general_03.png
[4]: ./media/datahug-tutorial/tutorial_general_04.png

[100]: ./media/datahug-tutorial/tutorial_general_100.png

[200]: ./media/datahug-tutorial/tutorial_general_200.png
[201]: ./media/datahug-tutorial/tutorial_general_201.png
[202]: ./media/datahug-tutorial/tutorial_general_202.png
[203]: ./media/datahug-tutorial/tutorial_general_203.png

