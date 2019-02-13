---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą O.C. Napisu Czarnecka - AppreciateHub | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i O.C. Napisu Czarnecka - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57838d5f5a49045138ce9adbdcf7855aeab783e8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172119"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą O.C. Napisu Czarnecka - AppreciateHub

W tym samouczku dowiesz się, jak zintegrować O.C. Napisu Czarnecka - AppreciateHub za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie O.C. Napisu Czarnecka - AppreciateHub z usługą Azure AD oferuje następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do O.C. Napisu Czarnecka - AppreciateHub
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do O.C. Napisu Czarnecka - AppreciateHub (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą O.C. Napisu Czarnecka - AppreciateHub, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- O.C. Napisu Czarnecka - AppreciateHub logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie O.C. Napisu Czarnecka - AppreciateHub z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Dodawanie O.C. Napisu Czarnecka - AppreciateHub z galerii
Aby skonfigurować integrację O.C. Napisu Czarnecka - AppreciateHub w usłudze Azure AD, musisz dodać O.C. Napisu Czarnecka - AppreciateHub z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać O.C. Napisu Czarnecka - AppreciateHub z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **O.C. Napisu Czarnecka - AppreciateHub**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

1. W panelu wyników wybierz **O.C. Napisu Czarnecka - AppreciateHub**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą O.C. Napisu Czarnecka - AppreciateHub w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w O.C. Napisu Czarnecka - AppreciateHub jest użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w O.C. Napisu Czarnecka - AppreciateHub musi zostać nawiązane.

W O.C. Napisu Czarnecka - AppreciateHub, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą O.C. Napisu Czarnecka - AppreciateHub, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie O.C. Napisu Czarnecka - użytkownika testowego AppreciateHub](#creating-a-oc-tanner---appreciatehub-test-user)**  — aby odpowiednikiem Britta Simon w O.C. Napisu Czarnecka - AppreciateHub połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w sieci O.C. Napisu Czarnecka - AppreciateHub aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z O.C. Napisu Czarnecka - AppreciateHub, wykonaj następujące czynności:**

1. W witrynie Azure portal na **O.C. Napisu Czarnecka - AppreciateHub** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

1. Na **O.C. Napisu Czarnecka - AppreciateHub domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zaktualizuj tę wartość przy użyciu rzeczywistego adresu URL odpowiedzi. Skontaktuj się z [O.C. Napisu Czarnecka - zespołem pomocy technicznej AppreciateHub](mailto:sso@octanner.com) aby zyskać tę wartość.

    b. Otwórz plik metadanych, korzystając z następującego linku: [ https://fed.appreciatehub.com/fed/sp/metadata ](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Znajdź **md:AssertionConsumerService** węzła. 
   
    d. Skopiuj wartość **lokalizacji** atrybutu. 
   
    ![Konfigurowanie ustawień aplikacji][12]
   
    e. W **na adres URL logowania** textbox ostatnie wartości zostały uzyskane w poprzednim kroku.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/oc-tanner-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **O.C. Napisu Czarnecka - AppreciateHub** stronie, musisz wysłać pobrany **XML metadanych** do [O.C. Napisu Czarnecka - zespołem pomocy technicznej AppreciateHub](mailto:sso@octanner.com).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/oc-tanner-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/oc-tanner-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/oc-tanner-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Tworzenie O.C. Napisu Czarnecka - AppreciateHub użytkownika testowego

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w O.C. Napisu Czarnecka - AppreciateHub.

**Aby utworzyć użytkownika o nazwie Britta Simon w O.C. Napisu Czarnecka - AppreciateHub, wykonaj następujące czynności:**

Poproś użytkownika [O.C. Napisu Czarnecka - zespołem pomocy technicznej AppreciateHub](mailto:sso@octanner.com) Aby utworzyć użytkownika mającego jako atrybut nameID taką samą wartość jak nazwa użytkownika w pozycji Britta simon w usłudze Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji należy włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do O.C. Napisu Czarnecka - AppreciateHub.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon O.C. Napisu Czarnecka - AppreciateHub, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **O.C. Napisu Czarnecka - AppreciateHub**.

    ![Konfigurowanie logowania jednokrotnego](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  
Po kliknięciu O.C. Napisu Czarnecka — Kafelek AppreciateHub w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do Twojej O.C. Napisu Czarnecka - AppreciateHub aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png

