---
title: 'Samouczek: Integracja usługi Azure Active Directory z Thoughtworks Mingle | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a685b5702aa9f74f3e0abf2a06774a30ac0d996f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436997"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Samouczek: Integracja usługi Azure Active Directory z Thoughtworks Mingle

W tym samouczku dowiesz się, jak zintegrować Thoughtworks Mingle w usłudze Azure Active Directory (Azure AD).

Integrowanie Thoughtworks Mingle z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Thoughtworks Mingle
- Użytkowników, aby automatycznie uzyskać zalogowanych do Thoughtworks Mingle (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Thoughtworks Mingle, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Thoughtworks Mingle logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Thoughtworks Mingle z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Dodawanie Thoughtworks Mingle z galerii
Aby skonfigurować integrację z Thoughtworks Mingle w usłudze Azure AD, należy dodać Thoughtworks Mingle z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Thoughtworks Mingle z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Thoughtworks Mingle**, wybierz opcję **Thoughtworks Mingle** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Thoughtworks Mingle na liście wyników](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego
W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Thoughtworks Mingle w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Thoughtworks Mingle do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Thoughtworks Mingle musi zostać nawiązane.

W Thoughtworks Mingle, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Thoughtworks Mingle, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)**  — aby odpowiednikiem Britta Simon w Thoughtworks Mingle połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Thoughtworks Mingle.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą Thoughtworks Mingle, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Thoughtworks Mingle** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

1. Na **Thoughtworks Mingle domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Thoughtworks Mingle domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Mingle Thoughtworks](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) można uzyskać wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

1. Zaloguj się do Twojej **Thoughtworks Mingle** witryny firmy jako administrator.

1. Kliknij przycisk **administratora** kartę, a następnie kliknij przycisk **konfiguracji logowania jednokrotnego**.
   
    ![Karta Admin](./media/thoughtworks-mingle-tutorial/ic785157.png "konfiguracji logowania jednokrotnego")

1. W **konfiguracji logowania jednokrotnego** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja logowania jednokrotnego](./media/thoughtworks-mingle-tutorial/ic785158.png "konfiguracji logowania jednokrotnego")
    
    a. Aby przekazać plik metadanych, kliknij przycisk **Choose file**. 

    b. Kliknij przycisk **Zapisz zmiany**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk Dodaj](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Tworzenie użytkownika testowego Thoughtworks Mingle

Dla użytkowników usługi Azure AD można było się zalogować musi być obsługiwana Thoughtworks Mingle aplikacji przy użyciu nazwy użytkowników usługi Azure Active Directory. W przypadku Thoughtworks Mingle Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Thoughtworks Mingle jako administrator.

1. Kliknij przycisk **profilu**.
   
    ![Swój pierwszy projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "pierwszego projektu")

1. Kliknij przycisk **administratora** kartę, a następnie kliknij przycisk **użytkowników**.
   
    ![Użytkownicy](./media/thoughtworks-mingle-tutorial/ic785161.png "użytkowników")

1. Kliknij przycisk **nowego użytkownika**.
   
    ![Nowy użytkownik](./media/thoughtworks-mingle-tutorial/ic785162.png "nowego użytkownika")

1. Na **nowego użytkownika** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Okno dialogowe Nowy użytkownik](./media/thoughtworks-mingle-tutorial/ic785163.png "nowego użytkownika")  
 
    a. Typ **nazwy logowania**, **nazwę wyświetlaną**, **hasła wybierz**, **Potwierdź hasło** prawidłowe platformy Azure konto usługi AD do aprowizowania do powiązanych pól tekstowych. 

    b. Jako **typ użytkownika**, wybierz opcję **pełnej**.

    c. Kliknij przycisk **utworzony ten profil**.

>[!NOTE]
>Można użyć jakichkolwiek innych Thoughtworks Mingle użytkownika konta tworzenie narzędzi lub interfejsów API udostępniane przez Thoughtworks Mingle aprowizacji kont użytkowników usługi AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Thoughtworks Mingle.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Thoughtworks Mingle, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Thoughtworks Mingle**.

    ![Łącze Thoughtworks Mingle na liście aplikacji](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Thoughtworks Mingle w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Thoughtworks Mingle aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

