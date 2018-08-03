---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługi Dropbox dla firm | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Dropbox dla firm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: d46f2aac5fb16b10f33cccabdcd76d60f0d6dfb9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438062"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Samouczek: Integracja usługi Azure Active Directory z usługi Dropbox dla firm

W tym samouczku dowiesz się, jak zintegrować usługa Dropbox dla firm z usługą Azure Active Directory (Azure AD).

Integrowanie usługi Dropbox dla firm z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Dropbox dla firm.
- Aby umożliwić użytkownikom automatycznie pobrać zalogowanych do usługi Dropbox dla firm (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi Dropbox dla firm, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dropbox dla firm logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi Dropbox dla firm za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dodawanie usługi Dropbox dla firm za pomocą galerii
Aby skonfigurować integrację z usługa Dropbox dla firm w usłudze Azure AD, należy dodać usługa Dropbox dla firm za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługa Dropbox dla firm z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **usługa Dropbox dla firm**, wybierz opcję **usługa Dropbox dla firm** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługa Dropbox dla firm z listy wyników](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu usługi Dropbox dla firm, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze Dropbox dla firm do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze Dropbox dla firm musi zostać ustanowione.

W usłudze Dropbox dla firm, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi Dropbox dla firm, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Utwórz Dropbox dla firm użytkownika testowego](#create-a-dropbox-for-business-test-user)**  — aby odpowiednikiem Britta Simon w usłudze Dropbox dla firm, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w usłudze Dropbox dla aplikacji biznesowych.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego z usługi Dropbox dla firm, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usługa Dropbox dla firm** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

1. Na **usługa Dropbox dla firm, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Usługa Dropbox dla firm domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.dropbox.com/sso/<id>`

    b. W **identyfikator** pole tekstowe, wpisz wartość: `Dropbox`

    > [!NOTE] 
    > Podaną wcześniej wartością adres URL logowania nie jest rzeczywistą wartość. Wartość zostanie zaktualizowana o rzeczywisty logowania jednokrotnego adresu URL, który zostało wyjaśnione w dalszej części tego samouczka. Skontaktuj się z pomocą [usługi Dropbox w celu zespół obsługi klienta firmy](https://www.dropbox.com/business/contact) można uzyskać wartość. 
 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

1. Na **Dropbox dla firm konfiguracji** , kliknij przycisk **skonfigurować Dropbox dla firm** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Usługa Dropbox dla firm konfiguracji](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **usługa Dropbox dla firm** stronie, przejdź w usłudze Dropbox dla firm dzierżawy.

    a. Zaloguj się do usługi Dropbox dla firm dzierżawy. 
   
    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769509.png "skonfigurować logowanie jednokrotne")
   
    b. W okienku nawigacji po lewej stronie kliknij pozycję **konsoli administracyjnej**. 
   
    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769510.png "skonfigurować logowanie jednokrotne")
   
    c. Na **konsoli administracyjnej**, kliknij przycisk **uwierzytelniania** w okienku nawigacji po lewej stronie. 
   
    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769511.png "skonfigurować logowanie jednokrotne")
   
    d. W **logowanie jednokrotne** zaznacz **włączyć rejestrację jednokrotną**, a następnie kliknij przycisk **więcej** do Rozwiń tę sekcję.  
   
    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769512.png "skonfigurować logowanie jednokrotne")
   
    e. Skopiuj adres URL w polu **użytkownicy mogą się logować, wprowadzając adres e-mail lub też przejść bezpośrednio do** i wklej go w **adres URL logowania** pole tekstowe z **usługa Dropbox dla firm, domena i adresy URL** sekcji w witrynie Azure portal. 
    
    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769513.png)
    
1. W **logowanie jednokrotne** części **uwierzytelniania** strony, wykonaj następujące czynności: 
   
    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/IC769516.png "skonfigurować logowanie jednokrotne")
   
    a. Kliknij przycisk **wymagane**.
   
    b. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. Kliknij przycisk **wybierz certyfikat**, a następnie przejdź do swojej **plik certyfikatu zakodowany Base64**.

    d. Kliknij przycisk **Zapisz zmiany** w celu ukończenia konfiguracji w usłudze DropBox dla firm dzierżawy.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Utwórz Dropbox dla firm użytkownika testowego

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w usłudze Dropbox dla firm. Usługa Dropbox dla firm obsługę just-in-time, który jest domyślnie włączona.

Brak elementu akcji dla Ciebie w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w usłudze Dropbox dla firm, jest tworzony nowy, przy próbie uzyskania dostępu do usługi Dropbox dla firm.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [usługa Dropbox dla zespołu pomocy technicznej firmy klienta](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Dropbox dla firm.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon usługa Dropbox dla firm, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **usługa Dropbox dla firm**.

    ![Usługa Dropbox dla firm łącze na liście aplikacji](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu Dropbox dla firm kafelka w panelu dostępu, należy uzyskać strony logowania usługi dropbox dla aplikacji biznesowych.
 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

