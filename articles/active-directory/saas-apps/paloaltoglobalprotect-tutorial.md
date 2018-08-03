---
title: 'Samouczek: Integracja usługi Azure Active Directory z Palo Alto Networks - GlobalProtect | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: d3fdf52d07faa4242a0267ebc929946bbc95418a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427622"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Samouczek: Integracja usługi Azure Active Directory z Palo Alto Networks - GlobalProtect

W tym samouczku dowiesz się, jak zintegrować Palo Alto Networks - GlobalProtect w usłudze Azure Active Directory (Azure AD).

Integrowanie Palo Alto Networks - GlobalProtect z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Palo Alto Networks - GlobalProtect.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Palo Alto Networks - GlobalProtect (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Palo Alto Networks - GlobalProtect, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Palo Alto Networks — GlobalProtect logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Palo Alto Networks - GlobalProtect z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Dodawanie Palo Alto Networks - GlobalProtect z galerii
Aby skonfigurować integrację Palo Alto Networks - GlobalProtect w usłudze Azure AD, należy dodać Palo Alto Networks - GlobalProtect z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Palo Alto Networks - GlobalProtect z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Palo Alto Networks - GlobalProtect**, wybierz opcję **Palo Alto Networks - GlobalProtect** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację .

    ![Palo Alto Networks - GlobalProtect na liście wyników](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks — GlobalProtect w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Palo Alto Networks - GlobalProtect do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Palo Alto Networks — GlobalProtect musi zostać ustanowione.

W Palo Alto Networks - GlobalProtect, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks - GlobalProtect, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Utwórz Palo Alto Networks - użytkownika testowego GlobalProtect](#create-a-palo-alto-networks---globalprotect-test-user)**  — aby odpowiednikiem Britta Simon w Palo Alto Networks - GlobalProtect połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w sieci Palo Alto - GlobalProtect aplikacji.

**Aby skonfigurować usługę Azure AD w logowanie jednokrotne za pomocą Palo Alto Networks - GlobalProtect, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Palo Alto Networks - GlobalProtect** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

1. Na **Palo Alto Networks - GlobalProtect domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Sieci Palo Alto - GlobalProtect domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Customer Firewall URL>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [Palo Alto Networks - zespół obsługi klienta GlobalProtect](https://support.paloaltonetworks.com/support) do uzyskania tych wartości. 
 
1. Palo Alto Networks - GlobalProtect aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | nazwa użytkownika | user.userprincipalname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza. Zmapowane wartością user.userprincipalname jako przykładu, ale można mapować z odpowiednią wartość. 
    
    d. Kliknij przycisk **Ok**


1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/paloaltoglobalprotect-tutorial/tutorial_general_400.png)

1. Otwórz Palo Alto sieci zapory interfejsem użytkownika administratora z uprawnieniami administracyjnymi w innym oknie przeglądarki.

1. Kliknij pozycję **urządzenia**.

    ![Konfigurowanie Palo Alto logowania jednokrotnego](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

1. Wybierz **dostawcy tożsamości SAML** w lewym obszarze nawigacji paska, a następnie kliknij przycisk Importuj, aby zaimportować plik metadanych.

    ![Konfigurowanie Palo Alto logowania jednokrotnego](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Wykonaj następujące czynności na okno importu

    ![Konfigurowanie Palo Alto logowania jednokrotnego](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. W **nazwa profilu** pola tekstowego, podaj nazwę np. Azure AD GlobalProtect.
    
    b. W **metadanych dostawcy tożsamości**, kliknij przycisk **Przeglądaj** i wybierz plik metadata.xml, który został pobrany z witryny Azure portal
    
    c. Kliknij przycisk **OK**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/paloaltoglobalprotect-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/paloaltoglobalprotect-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/paloaltoglobalprotect-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>Utwórz Palo Alto Networks - GlobalProtect użytkownika testowego

Sieci Palo Alto - GlobalProtect obsługuje Just-in-time Inicjowanie obsługi użytkowników, dzięki czemu użytkownik zostanie automatycznie utworzony w systemie po pomyślnym uwierzytelnieniu Jeżeli tak nie jest już istnieje. Nie ma potrzeby wykonywania dowolnych akcji w tym miejscu. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Palo Alto Networks - GlobalProtect.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Palo Alto Networks - GlobalProtect, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Palo Alto Networks - GlobalProtect**.

    ![Sieci Palo Alto - GlobalProtect łącze na liście aplikacji](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu Palo Alto Networks — Kafelek GlobalProtect w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do Twojej Palo Alto Networks - GlobalProtect aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_203.png

