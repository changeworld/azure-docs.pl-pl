---
title: 'Samouczek: Integracja usługi Azure Active Directory z Gigya | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Gigya.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 3759cfdb30620622912c5866e43f81699ac37eb7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424684"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Samouczek: Integracja usługi Azure Active Directory z Gigya

W tym samouczku dowiesz się, jak zintegrować Gigya w usłudze Azure Active Directory (Azure AD).

Integrowanie Gigya z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Gigya
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Gigya (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Gigya, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Gigya logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Gigya z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-gigya-from-the-gallery"></a>Dodawanie Gigya z galerii
Aby skonfigurować integrację Gigya w usłudze Azure AD, należy dodać Gigya z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Gigya z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Gigya**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gigya-tutorial/tutorial_gigya_search.png)

1. W panelu wyników wybierz **Gigya**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gigya-tutorial/tutorial_gigya_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Gigya w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Gigya do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Gigya musi można ustanowić.

W Gigya, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Gigya, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Gigya](#creating-a-gigya-test-user)**  — aby odpowiednikiem Britta Simon w Gigya połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Gigya.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Gigya, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Gigya** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/gigya-tutorial/tutorial_gigya_samlbase.png)

1. Na **Gigya domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/gigya-tutorial/tutorial_gigya_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `http://<companyname>.gigya.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Gigya](https://www.gigya.com/support-policy/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/gigya-tutorial/tutorial_gigya_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/gigya-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Gigya** , kliknij przycisk **skonfigurować Gigya** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/gigya-tutorial/tutorial_gigya_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Gigya, jako administrator.

1. Przejdź do **ustawienia \> logowania języka SAML**, a następnie kliknij przycisk **Dodaj** przycisku.
   
    ![Logowania języka SAML](./media/gigya-tutorial/ic789532.png "logowania języka SAML")

1. W **logowania języka SAML** sekcji, wykonaj następujące czynności:
   
    ![Plik konfiguracji SAML](./media/gigya-tutorial/ic789533.png "plik konfiguracji SAML")
   
    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji.
   
    b. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej z witryny Azure Portal. 
   
    c. W **pojedynczy znak na adres URL usługi** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi** skopiowanej z witryny Azure Portal.
   
    d. W **Format Identyfikatora nazwy** pola tekstowego, Wklej wartość **Format identyfikatora nazwy** skopiowanej z witryny Azure Portal.
   
    e. Otwórz swój certyfikat zakodowany base-64 w Notatniku pobranego z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509** pola tekstowego.
   
    f. Kliknij przycisk **Zapisz ustawienia**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gigya-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gigya-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gigya-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/gigya-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-gigya-test-user"></a>Tworzenie użytkownika testowego Gigya

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Gigya, musi być obsługiwana w Gigya.  
W przypadku Gigya Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby udostępnić konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się do Twojej **Gigya** witryny firmy jako administrator.

1. Przejdź do **administratora \> Zarządzanie użytkownikami**, a następnie kliknij przycisk **zaprosić użytkowników**.
   
    ![Zarządzanie użytkownikami](./media/gigya-tutorial/ic789535.png "Zarządzanie użytkownikami")

1. W oknie dialogowym zaprosić użytkowników wykonaj następujące czynności:
   
    ![Zaproś użytkowników](./media/gigya-tutorial/ic789536.png "zaprosić użytkowników")
   
    a. W **E-mail** polu tekstowym wpisz alias e-mail prawidłowego konta usługi Azure Active Directory do aprowizowania.
    
    b. Kliknij przycisk **zaprosić użytkownika**.
      
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail zawierającą link do potwierdzenia konta, zanim stanie się aktywny.
    > 
    

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Gigya.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Gigya, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Gigya**.

    ![Konfigurowanie logowania jednokrotnego](./media/gigya-tutorial/tutorial_gigya_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka Gigya w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Gigya.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/gigya-tutorial/tutorial_general_01.png
[2]: ./media/gigya-tutorial/tutorial_general_02.png
[3]: ./media/gigya-tutorial/tutorial_general_03.png
[4]: ./media/gigya-tutorial/tutorial_general_04.png

[100]: ./media/gigya-tutorial/tutorial_general_100.png

[200]: ./media/gigya-tutorial/tutorial_general_200.png
[201]: ./media/gigya-tutorial/tutorial_general_201.png
[202]: ./media/gigya-tutorial/tutorial_general_202.png
[203]: ./media/gigya-tutorial/tutorial_general_203.png

