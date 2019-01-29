---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi TINFOIL SECURITY | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: ae136c9aa0e21b2a5dabf06b050727871bddf03b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191979"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi TINFOIL SECURITY

W tym samouczku dowiesz się, jak zintegrować usługi TINFOIL SECURITY z usługą Azure Active Directory (Azure AD).

Integrowanie usługi TINFOIL SECURITY z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi TINFOIL SECURITY
- Użytkowników, aby automatycznie uzyskać zalogowanych do usługi TINFOIL SECURITY (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu usługi TINFOIL SECURITY, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługi TINFOIL SECURITY logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj usługi TINFOIL SECURITY z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="add-tinfoil-security-from-the-gallery"></a>Dodaj usługi TINFOIL SECURITY z galerii
Aby skonfigurować integrację usługi TINFOIL SECURITY w usłudze Azure AD, należy dodać usługi TINFOIL SECURITY z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi TINFOIL SECURITY z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **usługi TINFOIL SECURITY**, wybierz opcję **usługi TINFOIL SECURITY** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługi TINFOIL SECURITY z galerii](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi TINFOIL SECURITY, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usługi TINFOIL SECURITY dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usługi TINFOIL SECURITY musi zostać ustanowione.

Usługi TINFOIL SECURITY przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi TINFOIL SECURITY, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego usługi TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  — aby odpowiednikiem Britta Simon w usługi TINFOIL SECURITY, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi TINFOIL SECURITY.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu usługi TINFOIL SECURITY, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usługi TINFOIL SECURITY** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![SAML logowania opartego na](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

1. Na **TINFOIL SECURITY domena i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![Konfigurowanie logowania jednokrotnego](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość.

    ![Sekcji certyfikat podpisywania SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

1. Aby dodać mapowania wymaganego atrybutu, wykonaj następujące czynności:
    
    ![Karta Atrybuty](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "Karta Atrybuty")
    
    | Nazwa atrybutu    |   Wartość atrybutu |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Kliknij przycisk **Dodaj atrybut użytkownika**.
    
    ![Dodaj atrybut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "atrybutów")
    
    ![Dodaj atrybut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "atrybutów")
    
    b. W **nazwa atrybutu** polu tekstowym wpisz **accountid**.
    
    c. W **wartość atrybutu** polu tekstowym wartość Wklej identyfikator konta, która zostanie wyświetlony później w samouczku.
    
    d. Kliknij przycisk **OK**.    

1. Kliknij przycisk **Save** (Zapisz).

    ![Przycisk Save (Zapisz)](./media/tinfoil-security-tutorial/tutorial_general_400.png)

1. Na **TINFOIL SECURITY Configuration** , kliknij przycisk **skonfigurować usługi TINFOIL SECURITY** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja zabezpieczeń usługi TINFOIL](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny usługi TINFOIL SECURITY firmy, jako administrator.

1. Na pasku narzędzi u góry kliknij **Moje konto**.
   
    ![Pulpit nawigacyjny](./media/tinfoil-security-tutorial/ic798971.png "Pulpit nawigacyjny")

1. Kliknij pozycję **Zabezpieczenia**.
   
    ![Zabezpieczenia](./media/tinfoil-security-tutorial/ic798972.png "Zabezpieczenia")

1. Na **logowania jednokrotnego** konfiguracji strony, wykonaj następujące czynności:
   
    ![Logowanie jednokrotne](./media/tinfoil-security-tutorial/ic798973.png "Logowanie jednokrotne")
   
    a. Wybierz **Włącz SAML**.
   
    b. Kliknij przycisk **ręcznej konfiguracji**.
   
    c. W **adresu URL przesyłania SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal
   
    d. W **odcisk palca certyfikatu SAML** pola tekstowego, Wklej wartość **odcisk palca** skopiowanej z **certyfikat podpisywania SAML** sekcji.
  
    e. Kopiuj **Twój identyfikator konta** wartość i Wklej wartość w **wartość atrybutu** polu tekstowym w obszarze **Dodawanie atrybutu** sekcji w witrynie Azure portal.
   
    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Użytkownicy i grupy -> Wszyscy użytkownicy ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Użytkownik](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Tworzenie użytkownika testowego usługi TINFOIL SECURITY

Aby umożliwić użytkownikom usługi Azure AD zalogować się do usługi TINFOIL SECURITY, musi być obsługiwana w usługi TINFOIL SECURITY. W przypadku usługi TINFOIL SECURITY aprowizacji to zadanie ręczne.

**Aby uzyskać użytkownika zainicjowano obsługę administracyjną, wykonaj następujące czynności:**

1. Jeśli użytkownik jest część konta przedsiębiorstwa, musisz [skontaktuj się z zespołem pomocy technicznej usługi TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) można pobrać z utworzonym kontem użytkownika.

1. Jeśli użytkownik jest zwykły użytkownik usługi TINFOIL SECURITY SaaS, użytkownik może dodawać współpracownika do żadnej lokacji przez użytkownika. Spowoduje to wyzwolenie procesu o wysłanie zaproszenia na określony adres e-mail, aby utworzyć nowe konto użytkownika usługi TINFOIL SECURITY.

> [!NOTE]
> Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta usługi TINFOIL SECURITY użytkownika lub interfejsów API dostarczonych przez usługi TINFOIL SECURITY.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi TINFOIL SECURITY.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon usługi TINFOIL SECURITY, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **usługi TINFOIL SECURITY**.

    ![Wybierz usługi TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi TINFOIL SECURITY w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji usługi TINFOIL SECURITY. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

