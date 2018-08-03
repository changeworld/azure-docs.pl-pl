---
title: 'Samouczek: Integracja usługi Azure Active Directory z polem | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i pola.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: f5aa724e9848c9794eef093aef15b0aaed9cae97
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435764"
---
# <a name="integrate-azure-active-directory-with-box"></a>Integrowanie usługi Azure Active Directory z polem

W tym samouczku dowiesz się, jak zintegrować usługi Azure Active Directory (Azure AD) z polem.

Dzięki integracji usługi Azure AD z usługą Box, możesz uzyskać następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do pola.
- Aby umożliwić użytkownikom uzyskiwanie zalogowany automatycznie do pola (logowanie jednokrotne, lub logowania jednokrotnego) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji, witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z polem, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Subskrypcja z obsługą logowania jednokrotnego pole

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, firma Microsoft zaleca wykonanie *nie* za pomocą środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie pola z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-box-from-the-gallery"></a>Dodaj pole z galerii
Aby skonfigurować integrację usługi Azure AD z polem, Dodaj pole z galerii z listą zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

1. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W oknie "Aplikacje dla przedsiębiorstw"][2]
    
1. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna.

    !["Nowa aplikacja" przycisk][3]

1. W polu wyszukiwania wpisz **pole**, wybierz opcję **pole** w na liście wyników, a następnie wybierz **Dodaj**.

    ![Pola na liście wyników](./media/box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z polem, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługa Azure AD wymaga do identyfikowania użytkownika pole i jego odpowiednika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownika usługi Azure AD i tego samego użytkownika w usłudze Box.

Aby ustanowić relację łącza, przypisz jako pole *Username* wartość *nazwy użytkownika* w usłudze Azure AD.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z polem, wykonaj bloki konstrukcyjne w pięć następnych sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji pole, wykonując następujące czynności:

1. W witrynie Azure portal w **pole** okna integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link "Logowanie jednokrotne"][4]

1. W **logowanie jednokrotne** okna w **tryb rejestracji jednokrotnej** wybierz opcję **opartej na SAML logowania jednokrotnego**.
 
    ![W oknie "Logowanie jednokrotne"](./media/box-tutorial/tutorial_box_samlbase.png)

1. W obszarze **pole domena i adresy URL**, wykonaj następujące czynności:

    !["W polu Domena i adresy URL" pojedynczy informacje logowania jednokrotnego](./media/box-tutorial/url3.png)

    a. W **adres URL logowania** wpisz adres URL w następującym formacie: *https://\<poddomena >. box.com*.

    b. W **identyfikator** polu tekstowym wpisz **box.net**.
     
    > [!NOTE] 
    > Powyższe wartości nie są prawdziwe. Zaktualizuj je za pomocą rzeczywisty adres URL logowania i identyfikator. Aby uzyskać wartości, skontaktuj się z pomocą [zespołem pomocy technicznej klienta pole](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

1. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **XML metadanych**, a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/box-tutorial/tutorial_box_certificate.png) 

1. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/box-tutorial/tutorial_general_400.png)
    
1. Aby skonfigurować logowanie Jednokrotne dla aplikacji, wykonaj procedurę opisaną w [skonfigurować logowanie Jednokrotne na własnych](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Jeśli ustawień logowania jednokrotnego nie można włączyć na koncie usługi Box, może być konieczne skontaktowanie się z [zespołem pomocy technicznej klienta pole](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) i podaj pobrany plik XML.

> [!TIP]
> Ponieważ konfigurujesz aplikacji może odczytywać zwięzłe wersję poprzednich instrukcji w [witryny Azure portal](https://portal.azure.com). Po dodaniu aplikacji w **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę, a następnie przejść osadzone w dokumentacji **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji na temat funkcji dokumentacja embedded zobacz [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego Britta Simon w witrynie Azure portal, wykonując następujące czynności:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Połącz usługi Azure Active Directory](./media/box-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę bieżących użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/box-tutorial/create_aaduser_02.png)

1. W górnej części **wszyscy użytkownicy** wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/box-tutorial/create_aaduser_03.png)

    **Użytkownika** zostanie otwarte okno.

1. W **użytkownika** okna, wykonaj następujące czynności:

    ![W oknie użytkownika](./media/box-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-box-test-user"></a>Tworzenie użytkownika testowego pole

W tej sekcji utworzysz użytkownika testowego Britta Simon w polu. Pole obsługę just-in-time, który jest domyślnie włączona. Jeśli użytkownik jeszcze nie istnieje, jest tworzony nowy, przy próbie uzyskania dostępu do pola. Jest wymagana żadna akcja ze strony użytkownika, aby utworzyć użytkownika.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Box. Aby to zrobić, wykonaj następujące czynności:

![Przypisanie roli użytkownika][200]

1. W witrynie Azure portal Otwórz **aplikacje** przejdź do widoku **katalogu** wyświetlić, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    !["Aplikacje dla przedsiębiorstw" i "Wszystkie aplikacje" linki][201] 

1. W **aplikacje** listy wybierz **pole**.

    ![Łącze pola](./media/box-tutorial/tutorial_box_app.png)  

1. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Wybierz **Dodaj** a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

1. W **użytkowników i grup** okna w **użytkowników** listy wybierz **Britta Simon**.

1. Wybierz **wybierz** przycisku.

1. W **Dodaj przydziału** wybierz **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu **pole** kafelka w panelu dostępu, po otwarciu strony logowania do logowania do aplikacji Box.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie aprowizowania użytkowników](box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/box-tutorial/tutorial_general_01.png
[2]: ./media/box-tutorial/tutorial_general_02.png
[3]: ./media/box-tutorial/tutorial_general_03.png
[4]: ./media/box-tutorial/tutorial_general_04.png

[100]: ./media/box-tutorial/tutorial_general_100.png

[200]: ./media/box-tutorial/tutorial_general_200.png
[201]: ./media/box-tutorial/tutorial_general_201.png
[202]: ./media/box-tutorial/tutorial_general_202.png
[203]: ./media/box-tutorial/tutorial_general_203.png

