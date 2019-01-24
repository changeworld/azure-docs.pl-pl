---
title: 'Samouczek: Integracja usługi Azure Active Directory IBM Kenexa ankiety Enterprise | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i IBM Kenexa ankiety Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: d8027aca628185b1e3d2e80323c9f83e6832a913
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823933"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory IBM Kenexa ankiety Enterprise

W tym samouczku dowiesz się, jak zintegrować IBM Kenexa ankiety Enterprise z usługą Azure Active Directory (Azure AD).

Integrowanie IBM Kenexa ankiety Enterprise z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do programu IBM Kenexa ankiety Enterprise.
- Aby umożliwić użytkownikom automatyczne logowanie do IBM Kenexa ankiety przedsiębiorstwa przy użyciu logowania jednokrotnego (SSO) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD z usługą Enterprise ankiety Kenexa IBM, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcję z obsługą IBM Kenexa ankiety przedsiębiorstwa z logowania jednokrotnego

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, zaleca się, że należy używać w środowisku produkcyjnym.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować logowania jednokrotnego usługi Azure AD w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie IBM Kenexa ankiety przedsiębiorstwa z galerii
* Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Dodaj IBM Kenexa ankiety przedsiębiorstwa z galerii
Aby skonfigurować integrację programu IBM Kenexa ankiety Enterprise w usłudze Azure AD, należy dodać IBM Kenexa ankiety przedsiębiorstwa z galerii z listą zarządzanych aplikacji SaaS.

Aby dodać IBM Kenexa ankiety przedsiębiorstwa z galerii, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku. 

    ![Przycisk Azure Active Directory][1]

1. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw][2]
    
1. Aby dodać aplikację, kliknij przycisk **nową aplikację** przycisku.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **IBM Kenexa ankiety Enterprise**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

1. Na liście wyników wybierz **IBM Kenexa ankiety Enterprise**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![IBM Kenexa ankiety Enterprise na liście wyników](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji możesz skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą ankiety Enterprise Kenexa IBM, oparte na użytkownika testu o nazwie "Britta Simon."

Do logowania jednokrotnego do pracy usługi Azure AD musi zidentyfikować odpowiednikiem IBM Kenexa ankiety przedsiębiorstwa użytkownika w usłudze Azure AD. Oznacza to usługi Azure AD należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w przedsiębiorstwie ankiety Kenexa IBM.

Do ustanawiania relacji łączy, przypisz wartość **nazwa_użytkownika** w przedsiębiorstwie ankiety Kenexa IBM, jako wartość **Username** w usłudze Azure AD.

Aby konfiguracja i testowanie logowania jednokrotnego usługi Azure AD z usługą Enterprise ankiety Kenexa IBM, należy wykonać bloki konstrukcyjne w dwóch następnych sekcjach.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do włączenia funkcji logowania jednokrotnego usługi Azure AD w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji przedsiębiorstwa ankiety Kenexa IBM, wykonując następujące czynności:

1. W witrynie Azure portal na **IBM Kenexa ankiety Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![IBM Kenexa ankiety przedsiębiorstwa konfigurowania pojedynczego linku logowania jednokrotnego][4]

1. W **logowanie jednokrotne** dialogowym **tryb** wybierz opcję **opartej na SAML logowania jednokrotnego** do włączenia funkcji logowania jednokrotnego.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

1. W **IBM Kenexa ankiety Enterprise domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![IBM Kenexa ankiety Enterprise domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL z następującym wzorcem: `https://surveys.kenexa.com/<companycode>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL z następującym wzorcem: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Podane wyżej wartości nie są rzeczywiste. Można aktualizować rzeczywisty identyfikator i adres URL odpowiedzi. Aby uzyskać rzeczywiste wartości, skontaktuj się z pomocą [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw).

1. W obszarze **certyfikat podpisywania SAML**, kliknij przycisk **certyfikat (Base64)**, a następnie zapisz plik certyfikatu do komputera.

    ![Link pobierania certyfikat (Base64)](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    Aplikacja przedsiębiorstwa ankiety Kenexa IBM oczekuje otrzymywać potwierdzeń zabezpieczeń potwierdzenia Markup Language (SAML) w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych do konfiguracji usługi atrybuty tokenu języka SAML. Wartość oświadczenia identyfikatora użytkownika w odpowiedzi musi być zgodna z Identyfikatorem logowania jednokrotnego, który jest skonfigurowany w systemie Kenexa. Aby zamapować identyfikator odpowiedniego użytkownika w Twojej organizacji jako usługa rejestracji Jednokrotnej Internet Datagram Protocol (IDP), współpracować z [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw). 

    Domyślnie usługa Azure AD Ustawia identyfikator użytkownika jako wartość głównej nazwy (UPN) użytkownika. Tę wartość można zmienić na **atrybut** karty, jak pokazano na poniższym zrzucie ekranu. Integracja działa tylko wtedy, gdy został ukończony mapowanie poprawnie.
    
    ![Okno dialogowe atrybuty użytkownika](./media/kenexasurvey-tutorial/tutorial_attribute.png) 

1. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/kenexasurvey-tutorial/tutorial_general_400.png)

1. Aby otworzyć **Konfigurowanie logowania jednokrotnego** okna, w obszarze **konfiguracja dla przedsiębiorstw ankiety Kenexa IBM**, kliknij przycisk **skonfigurować IBM Kenexa ankiety Enterprise**. 
 
    ![Łącze Konfiguruj IBM Kenexa ankiety Enterprise](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

1. Kopiuj **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczego logowania jednokrotnego adres URL usługi** wartości z kolekcji **krótki przewodnik po** sekcji.

1. W **Konfigurowanie logowania jednokrotnego** okna, w obszarze **krótki**, kopiowania **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczy adres URL logowania jednokrotnego usługi** wartości.

1. Aby skonfigurować logowanie Jednokrotne na **IBM Kenexa ankiety Enterprise** po stronie, Wyślij pobrany **certyfikat (Base64)**, **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Można się odwoływać do zwięzłe wersji tych instrukcji w [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowanie jednokrotne** kartę, a następnie przejść Dokumentacja za pośrednictwem Embedded **konfiguracji** sekcji na końcu. Aby dowiedzieć się więcej na temat funkcji dokumentacja embedded, zobacz [dokumentacja embedded usługi Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji utworzysz użytkownika testowego Britta Simon w witrynie Azure portal, wykonując następujące czynności:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/kenexasurvey-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/kenexasurvey-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.
 
    ![Przycisk Dodaj](./media/kenexasurvey-tutorial/create_aaduser_03.png) 

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/kenexasurvey-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Tworzenie użytkownika testowego IBM Kenexa ankiety Enterprise

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w przedsiębiorstwie ankiety Kenexa firmy IBM. 

Aby utworzyć użytkowników w systemie IBM Kenexa ankiety przedsiębiorstwa i mapowanie Identyfikatora logowania jednokrotnego dla nich, można pracować z [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw). Wartość tego Identyfikatora logowania jednokrotnego powinny być mapowane na wartość identyfikatora użytkownika z usługi Azure AD. To ustawienie domyślne można zmienić na **atrybut** kartę.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do udzielania dostępu do programu IBM Kenexa ankiety Enterprise za pomocą logowania jednokrotnego usługi Azure.

![Przypisanie roli użytkownika][200] 

Aby przypisać użytkownika Britta Simon IBM Kenexa ankiety Enterprise, wykonaj następujące czynności:

1. W witrynie Azure portal Otwórz **aplikacje** przejdź do widoku **katalogu** widoku, wybierz opcję **aplikacje dla przedsiębiorstw**, a następnie kliknij przycisk **wszystkie aplikacje** .

    !["Aplikacje dla przedsiębiorstw" i "Wszystkie aplikacje" linki][201] 

1. W **aplikacje** listy wybierz **IBM Kenexa ankiety Enterprise**.

    ![Link IBM Kenexa ankiety Enterprise na liście aplikacji](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

1. W okienku po lewej stronie kliknij **użytkowników i grup**.

    ![Link „Użytkownicy i grupy”][202] 

1. Kliknij przycisk **Dodaj** przycisk a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

1. W **użytkowników i grup** dialogowym **użytkowników** listy wybierz **Britta Simon**.

1. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

1. W **Dodaj przydziału** okno dialogowe, kliknij przycisk **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu **IBM Kenexa ankiety Enterprise** kafelka w panelu dostępu, powinny być automatycznie zalogowano się do aplikacji przedsiębiorstwa ankiety Kenexa IBM.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/kenexasurvey-tutorial/tutorial_general_203.png

 
