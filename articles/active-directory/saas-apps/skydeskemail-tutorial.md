---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą adresu E-mail SkyDesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SkyDesk wiadomości E-mail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 29e1fd9999c70ed4a599cbc65d43402088d876e2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818884"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą adresu E-mail SkyDesk

W tym samouczku dowiesz się, jak zintegrować SkyDesk wiadomości E-mail z usługi Azure Active Directory (Azure AD).

Integrowanie SkyDesk wiadomości E-mail z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do poczty E-mail SkyDesk
- Użytkowników, aby automatycznie uzyskać zalogowanych do E-mail SkyDesk (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu adresu E-mail SkyDesk, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Adres E-mail SkyDesk logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie funkcji E-mail SkyDesk z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-skydesk-email-from-the-gallery"></a>Dodawanie funkcji E-mail SkyDesk z galerii
Aby skonfigurować integrację SkyDesk wiadomości e-mail w usłudze Azure AD, należy dodać SkyDesk wiadomości E-mail z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SkyDesk wiadomości E-mail z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **E-mail SkyDesk**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

1. W panelu wyników wybierz **E-mail SkyDesk**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować, i test usługi Azure AD logowania jednokrotnego przy użyciu adresu E-mail SkyDesk oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w SkyDesk wiadomości E-mail do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w wiadomości E-mail SkyDesk musi nawiązać.

W wiadomości E-mail SkyDesk, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu adresu E-mail SkyDesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego E-mail SkyDesk](#creating-a-skydesk-email-test-user)**  — aby odpowiednikiem Britta Simon w wiadomości E-mail SkyDesk, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji poczty E-mail SkyDesk.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu adresu E-mail SkyDesk, wykonaj następujące czynności:**

1. W witrynie Azure portal na **E-mail SkyDesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

1. Na **SkyDesk domeny poczty E-mail i adresów URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta poczty E-mail SkyDesk](https://www.skydesk.jp/apps/support/) można uzyskać wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_general_400.png)

1. Na **konfiguracji poczty E-mail SkyDesk** , kliknij przycisk **konfigurowanie poczty E-mail SkyDesk** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

1. Aby włączyć logowanie Jednokrotne w **E-mail SkyDesk**, wykonaj następujące czynności:

    a. Zaloguj się do swojego konta E-mail SkyDesk jako administrator.

    b. W menu u góry kliknij **instalacji**i wybierz **organizacji**. 
    
      ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Kliknij pozycję **domen** z lewego panelu.
    
      ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Kliknij pozycję **Dodawanie domeny**.
    
      ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Wprowadź nazwę domeny, a następnie zweryfikować domenę.
    
      ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Kliknij pozycję **uwierzytelnianie SAML** z lewego panelu.
    
      ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na **uwierzytelnianie SAML** okna dialogowego strony, wykonaj następujące czynności:
   
      ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Aby używać uwierzytelniania SAML na podstawie, użytkownik powinien mieć **zweryfikowaną domenę** lub **portal adresu URL** Instalatora. Możesz ustawić portalu adres URL o unikatowej nazwie.
    > 
    > 
   
    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.
   
    b. W **wylogowania** polu tekstowym adresu URL, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    c. **Zmień adres URL hasła** jest opcjonalny więc pozostaw to pole puste.

    d. Kliknij pozycję **uzyskać klucz z pliku** wybierz certyfikat pobrany z witryny Azure portal, a następnie kliknij przycisk **Otwórz** można przekazać certyfikatu.

    e. Dla ustawienia **Algorithm** (Algorytm) wybierz pozycję **RSA**.

    f. Kliknij przycisk **Ok** Aby zapisać zmiany.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Tworzenie użytkownika testowego SkyDesk wiadomości E-mail

W tej sekcji utworzysz użytkownika o nazwie Britta Simon SkyDesk wiadomości e-mail.

1. Kliknij pozycję **dostęp użytkownika** z lewej strony panelu SkyDesk wiadomości e-mail, a następnie wprowadź swoją nazwę użytkownika. 

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Jeśli potrzebujesz do tworzenia użytkowników zbiorczo, musisz skontaktować się z [zespołem pomocy technicznej klienta poczty E-mail SkyDesk](https://www.skydesk.jp/apps/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do poczty E-mail SkyDesk za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon SkyDesk poczty E-mail, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **E-mail SkyDesk**.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka SkyDesk poczty E-mail w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację SkyDesk poczty E-mail.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/skydeskemail-tutorial/tutorial_general_203.png

