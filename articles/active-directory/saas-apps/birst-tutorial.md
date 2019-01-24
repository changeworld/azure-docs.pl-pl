---
title: 'Samouczek: Integracja usługi Azure Active Directory z analizą biznesową z Agile Birst | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Birst Agile analiz biznesowych.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 761db64856f0ea6068512e4ba6f9ddcff403244c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821094"
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Samouczek: Integracja usługi Azure Active Directory z Birst Agile analizą biznesową

W tym samouczku dowiesz się, jak zintegrować Birst Agile analiz biznesowych za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie Birst Agile analiz biznesowych za pomocą usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Birst Agile analiza biznesowa
- Użytkowników, aby automatycznie uzyskać zalogowanych do Birst Agile analizy biznesowej (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Birst Agile analizą biznesową, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Agile analiz biznesowych Birst logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Birst Agile analiz biznesowych za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>Dodawanie Birst Agile analiz biznesowych za pomocą galerii
Aby skonfigurować integrację Birst Agile analizy biznesowe w usłudze Azure AD, należy dodać Birst Agile analiz biznesowych za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Birst Agile analizy biznesowej z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Birst Agile analiz biznesowych**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/birst-tutorial/tutorial_birst_search.png)

1. W panelu wyników wybierz **Birst Agile analiz biznesowych**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/birst-tutorial/tutorial_birst_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Birst Agile analiz biznesowych na podstawie użytkownika testowego, o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Birst Agile analizy biznesowej do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Birst Agile analiz biznesowych musi można ustanowić.

W Birst Agile analizy biznesowej, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Birst Agile analizy biznesowej, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Birst Agile analiz biznesowych](#creating-a-birst-agile-business-analytics-test-user)**  — aby odpowiednikiem Britta Simon w Birst Agile analiz biznesowych połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Birst Agile analizy biznesowej.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Birst Agile analiza biznesowa, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Birst Agile analiz biznesowych** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/birst-tutorial/tutorial_birst_samlbase.png)

1. Na **Birst Agile domeny analizy biznesowej i adresów URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/birst-tutorial/tutorial_birst_url.png)

     W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

     Adres URL jest zależna od centrum danych, czy znajduje się konto Birst: 

     * Aby uzyskać Użyj Centrum danych USA następującego wzorca: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` 

     * Dla Europy centrum danych, użyj następującego wzorca: `https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta analizy biznesowej Agile Birst](mailto:info@birst.com) można uzyskać wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/birst-tutorial/tutorial_birst_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/birst-tutorial/tutorial_general_400.png)

1. Na **Birst Agile konfiguracji analizy biznesowej** , kliknij przycisk **skonfigurować Birst Agile analiz biznesowych** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/birst-tutorial/tutorial_birst_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Birst Agile analiz biznesowych** stronie, musisz wysłać pobrany **certyfikat (Base64)**, **adres URL wylogowania, identyfikator jednostki języka SAML i SAML logowania jednokrotnego Adres URL usługi** do [zespołu pomocy technicznej Birst Agile analiz biznesowych](mailto:info@birst.com). 

    > [!NOTE]
    > Wspominanie Birst zespołowi, że ta integracja wymaga algorytm SHA256 (SHA1 nie będą obsługiwane) tak, aby ustawiają logowania jednokrotnego na odpowiednim serwerze, takich jak **app2101** itp.
  

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/birst-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/birst-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/birst-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/birst-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Tworzenie użytkownika testowego Birst Agile analiza biznesowa

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w Birst Agile analiz biznesowych. Praca z [zespołu pomocy technicznej Birst Agile analiz biznesowych](mailto:info@birst.com) Aby dodać użytkowników w ramach konta Birst. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Birst Agile analiz biznesowych.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Birst Agile analiza biznesowa, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Birst Agile analiz biznesowych**.

    ![Konfigurowanie logowania jednokrotnego](./media/birst-tutorial/tutorial_birst_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka Birst Agile analiza biznesowa w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Birst Agile analiz biznesowych. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/birst-tutorial/tutorial_general_01.png
[2]: ./media/birst-tutorial/tutorial_general_02.png
[3]: ./media/birst-tutorial/tutorial_general_03.png
[4]: ./media/birst-tutorial/tutorial_general_04.png

[100]: ./media/birst-tutorial/tutorial_general_100.png

[200]: ./media/birst-tutorial/tutorial_general_200.png
[201]: ./media/birst-tutorial/tutorial_general_201.png
[202]: ./media/birst-tutorial/tutorial_general_202.png
[203]: ./media/birst-tutorial/tutorial_general_203.png

