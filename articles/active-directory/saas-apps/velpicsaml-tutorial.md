---
title: 'Samouczek: Integracja usługi Azure Active Directory z Velpic SAML | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: eb59c61f89b3b23ce8b0a393ee1e0e842826e90b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811795"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Samouczek: Integracja usługi Azure Active Directory z Velpic SAML

W tym samouczku dowiesz się, jak zintegrować Velpic SAML w usłudze Azure Active Directory (Azure AD).

Integrowanie Velpic SAML z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Velpic SAML
- Użytkowników, aby automatycznie uzyskać zalogowanych do Velpic SAML (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Velpic SAML, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Velpic SAML logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Velpic SAML z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-velpic-saml-from-the-gallery"></a>Dodawanie Velpic SAML z galerii
Aby skonfigurować integrację Velpic protokołu SAML w usłudze Azure AD, należy dodać Velpic SAML z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Velpic SAML z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Velpic SAML**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_search.png)

1. W panelu wyników wybierz **Velpic SAML**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą protokołu SAML Velpic oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Velpic SAML do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Velpic SAML musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Velpic SAML.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Velpic SAML, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Velpic SAML](#creating-a-velpic-saml-test-user)**  — aby odpowiednikiem Britta Simon w Velpic SAML, połączonego z usługi Azure AD reprezentacja jej.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji Velpic SAML.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Velpic SAML, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **Velpic SAML** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

1. Wprowadź szczegółowe informacje w **Velpic SAML domena i adresy URL** sekcji -

    ![Konfigurowanie logowania jednokrotnego](./media/velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. W **adres URL logowania** polu tekstowym wpisz wartość jako: `https://<sub-domain>.velpicsaml.net`

    b. W **identyfikator** pola tekstowego, Wklej **"Pojedynczy znak w adresie URL"** wartość `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Należy pamiętać, że adres URL logowania będzie świadczona przez zespół Velpic SAML i wartość identyfikatora będzie dostępny podczas konfigurowania wtyczkę logowania jednokrotnego Velpic SAML stronie. Musisz skopiować tę wartość ze strony aplikacji Velpic SAML i wklej go tutaj.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/velpicsaml-tutorial/tutorial_general_400.png)

1. W sekcji Konfiguracja SAML Velpic kliknij skonfigurować SAML Velpic, aby otworzyć Konfigurowanie logowania jednokrotnego okno. Skopiuj identyfikator jednostki SAML z sekcji krótki.

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Velpic SAML, jako administrator.

1. Kliknij pozycję **Zarządzaj** kartę, a następnie przejdź do **integracji** sekcji, w których trzeba kliknąć **wtyczek** przycisk, aby utworzyć nowy dodatek do logowania.

    ![Dodatek typu plug-in](./media/velpicsaml-tutorial/velpic_1.png)

1. Kliknij pozycję **"Dodawanie wtyczki"** przycisku.
    
    ![Dodatek typu plug-in](./media/velpicsaml-tutorial/velpic_2.png)

1. Kliknij pozycję **SAML** kafelka na stronie Dodaj wtyczkę.
    
    ![Dodatek typu plug-in](./media/velpicsaml-tutorial/velpic_3.png)

1. Wprowadź nazwę nowej wtyczki SAML, a następnie kliknij przycisk **"Dodaj"** przycisku.

    ![Dodatek typu plug-in](./media/velpicsaml-tutorial/velpic_4.png)

1. Wprowadź szczegółowe informacje w następujący sposób:

    ![Dodatek typu plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. W **nazwa** polu tekstowym wpisz nazwę wtyczki SAML.

    b. W **adres URL wystawcy** pola tekstowego, Wklej **identyfikator jednostki SAML** skopiowane z **Konfigurowanie logowania jednokrotnego** okna witryny Azure portal.

    c. W **dostawcy metadanych konfiguracji** przekazywanie pliku XML metadanych, który został pobrany z witryny Azure portal.

    d. Możesz również włączyć SAML dokładnie na czas inicjowania obsługi administracyjnej, należy włączyć **"Automatyczne tworzenie nowych użytkowników"** pola wyboru. Jeśli użytkownik nie istnieje w Velpic ta flaga nie jest włączona, logowanie z platformy Azure nie powiedzie się. Po włączeniu flagi użytkownika będą automatycznie udostępniane w Velpic podczas logowania. 

    e. Kopiuj **pojedynczy adres URL logowania** z tekstu i wklej go w witrynie Azure portal.
    
    f. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/velpicsaml-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/velpicsaml-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/velpicsaml-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/velpicsaml-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Tworzenie użytkownika testowego Velpic SAML

Ten krok zazwyczaj nie jest wymagane, ponieważ aplikacja obsługuje dokładnie na czas Inicjowanie obsługi użytkowników. Jeśli nie włączono automatycznej aprowizacji użytkowników Tworzenie ręcznego użytkownika można zrobić zgodnie z poniższym opisem.

Zaloguj się do witryny firmy Velpic SAML jako administrator i wykonać następujące kroki:
    
1. Kliknij kartę Zarządzaj i przejdź do sekcji użytkowników, a następnie kliknij przycisk Nowa, aby dodać użytkowników.

    ![Dodaj użytkownika](./media/velpicsaml-tutorial/velpic_7.png)

1. Na **"Tworzenie nowego użytkownika"** okna dialogowego strony, wykonaj następujące kroki.

    ![Użytkownik](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. W **imię** polu tekstowym Nazwa typu pierwszego obiektu Britta Simon.

    b. W **nazwisko** polu tekstowym wpisz nazwisko Britta Simon.

    c. W **nazwa_użytkownika** polu tekstowym wpisz nazwę żądanego użytkownika w pozycji Britta simon.

    d. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika Britta Simon.

    e. Pozostała część informacji jest opcjonalne, można go wypełnić, jeśli to konieczne.
    
    f. Kliknij przycisk **SAVE** (Zapisz).  

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej Velpic SAML.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Velpic SAML, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Velpic SAML**.

    ![Konfigurowanie logowania jednokrotnego](./media/velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

1. Po kliknięciu kafelka Velpic SAML, w panelu dostępu, należy uzyskać strony logowania Velpic SAML aplikacji. Powinien zostać wyświetlony **"Zaloguj się za pomocą usługi Azure AD"** przycisk na stronie logowania.

    ![Dodatek typu plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Kliknij pozycję **"Zaloguj się za pomocą usługi Azure AD"** przycisk, aby zalogować się do Velpic przy użyciu swojego konta usługi Azure AD.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/velpicsaml-tutorial/tutorial_general_203.png

