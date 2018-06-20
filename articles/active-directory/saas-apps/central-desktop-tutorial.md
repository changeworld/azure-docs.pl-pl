---
title: 'Samouczek: Integracji Azure Active Directory przy użyciu centralnej pulpitu | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i pulpitu centralnej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 4f49d841cc9d6d799b1cd047454acaa2c618af73
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211319"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Samouczek: Integracji Azure Active Directory przy użyciu centralnej pulpitu

Z tego samouczka dowiesz integrowanie centralnej pulpitu w usłudze Azure Active Directory (Azure AD).

Integrowanie centralnej pulpitu z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do pulpitu centralnej.
- Można umożliwić użytkownikom automatycznie pobrać zalogowany do centralnej pulpitu przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu centralnej pulpitu, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Centralnej pulpitu pojedynczy znak z włączoną subskrypcji

> [!NOTE]
> Nie zaleca się używanie środowiska produkcyjnego do testowania czynności w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz jeszcze środowisko wersji próbnej usługi Azure AD, [Pobierz bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie centralnej pulpitu z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-central-desktop-from-the-gallery"></a>Dodawanie centralnej pulpitu z galerii
Aby skonfigurować integrację usługi Azure AD centralnej pulpitu, należy dodać centralną pulpitu z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać centralną pulpitu z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nowe aplikacje, wybierz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **centralnej pulpitu**. Wybierz **centralnej pulpitu** z panelu wyniki, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Centralna pulpitu na liście wyników](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu centralnej pulpitu oparte na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, który użytkownika odpowiednika w centralnej pulpitu użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i powiązanych użytkowników na pulpicie centralnej.

W centralnej pulpitu **Username** tę samą wartość jak **nazwy użytkownika** w usłudze Azure AD. Teraz ustanowieniu połączenia między dwóch użytkowników.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu centralnej pulpitu, należy wykonać poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego centralnej pulpitu](#create-a-central-desktop-test-user) w celu zapewnienia odpowiednikiem Simona Britta centralnej pulpitu, połączonej z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji centralnej pulpitu.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pulpitem centralną, należy wykonać następujące czynności:**

1. W portalu Azure na **centralnej pulpitu** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** okna dialogowego, **tryb** listy rozwijanej wybierz **na języku SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. W **centralnej domeny pulpitu i adres URL** sekcji, wykonaj następujące czynności:

    ![Centralnej domeny pulpitu i adresów URL jednym informacje logowania jednokrotnego](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. W **adres URL logowania** wpisz adres URL z następującego wzorca: `https://<companyname>.centraldesktop.com`

    b. W **identyfikator** wpisz adres URL z następującego wzorca:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. W **adres URL odpowiedzi** wpisz adres URL z następującego wzorca: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości przy użyciu rzeczywistego identyfikatora odpowiedzi na adres URL i adresu URL logowania jednokrotnego. Skontaktuj się z [zespołem pomocy technicznej klienta pulpitu centralnej](https://imeetcentral.com/contact-us) uzyskać te wartości. 

4. W **certyfikat podpisywania SAML** zaznacz **certyfikatu**. Następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/central-desktop-tutorial/tutorial_general_400.png)
    
6. W **centralnej konfiguracji pulpitu** zaznacz **Konfigurowanie pulpitu centralnej** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **krótkimi opisami** sekcji.

    ![Konfiguracja centralnych pulpitu](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Zaloguj się do Twojego **centralnej pulpitu** dzierżawy.

8. Przejdź do **ustawienia**. Wybierz **zaawansowane**, a następnie wybierz **rejestracji jednokrotnej**.

    ![Instalator — zaawansowane](./media/central-desktop-tutorial/ic769563.png "Instalator — zaawansowane")

9. Na **pojedynczy znak na ustawienia** wykonaj następujące czynności:

    ![Pojedyncze ustawienia logowania jednokrotnego](./media/central-desktop-tutorial/ic769564.png "pojedynczy znak na ustawienia")
    
    a. Wybierz **Włącz SAML v2 logowania jednokrotnego**.
    
    b. W **adres URL logowania jednokrotnego** Wklej **identyfikator jednostki SAML** wartość skopiowany z portalu Azure.
    
    c. W **adres URL logowania jednokrotnego logowania** Wklej **SAML pojedynczy znak na adres URL usługi** wartość skopiowany z portalu Azure.
    
    d. W **adresu URL wylogowania logowania jednokrotnego** Wklej **Sign-Out URL** wartość skopiowany z portalu Azure.

10. W **metody weryfikacji podpisu wiadomości** sekcji, wykonaj następujące czynności:

    ![Metoda weryfikacji podpisu wiadomości](./media/central-desktop-tutorial/ic769565.png "metody weryfikacji podpisu wiadomości") . Wybierz pozycję **Certyfikat**.
    
    b. W **certyfikatu logowania jednokrotnego** listy, wybierz **RSH SHA256**.
    
    c. Otwórz pobrany certyfikat w Notatniku. Następnie skopiuj zawartość certyfikatu i wklej ją do **certyfikatu logowania jednokrotnego** pola.
        
    d. Wybierz **wyświetlone łącze do strony logowania SAMLv2**.
    
    e. Wybierz **aktualizacji**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i uzyskuje dostęp do osadzonego Dokumentacja za pośrednictwem **konfiguracji** sekcji u dołu. Możesz przeczytać dodatkowe informacje o funkcji dokumentacji osadzony w [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, należy wykonać następujące czynności:**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/central-desktop-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/central-desktop-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/central-desktop-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:

    ![Okno dialogowe użytkownika](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-central-desktop-test-user"></a>Tworzenie użytkownika testowego centralnej pulpitu

Dla użytkowników usługi Azure AD można było się zalogować muszą mieć przydzielone w aplikacji centralnej pulpitu. W tej sekcji opisano sposób tworzenia kont użytkowników usługi Azure AD w centralnej pulpitu.

> [!NOTE]
> Aby udostępnić konta użytkownika usługi Azure AD, można użyć inne narzędzia do tworzenia konta użytkownika pulpitu centralnej lub interfejsów API, które są udostępniane przez centralną pulpitu.

**Do obsługi administracyjnej kont użytkowników do centralnej pulpitu:**

1. Zaloguj się do dzierżawy centralnej pulpitu.

2. Przejdź do **osób** > **wewnętrzne elementy członkowskie**.

3. Wybierz **Dodaj wewnętrzne elementy członkowskie**.

    ![Osoby](./media/central-desktop-tutorial/ic781051.png "osób")
    
4. W **wiadomości E-mail adres z nowym elementom członkowskim** wpisz konto usługi Azure AD, które chcesz udostępnić, a następnie wybierz **dalej**.

    ![Adresy nowe elementy członkowskie e-mail](./media/central-desktop-tutorial/ic781052.png "E-mail adresów nowych elementów członkowskich")

5. Wybierz **Dodaj wewnętrzne elementy członkowskie**.

    ![Dodaj wewnętrzne elementu członkowskiego](./media/central-desktop-tutorial/ic781053.png "Dodaj wewnętrznego elementu członkowskiego")
   
   >[!NOTE]
   >Użytkownicy, które można dodać otrzymywać wiadomość e-mail zawierającą link potwierdzenia do aktywowania kont.
   
### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji możesz umożliwić użytkownikowi Simona Britta do przyznania im dostępu do centralnego pulpitu za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta centralnej pulpitu, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji. Przejdź do widoku katalogu, a następnie przejdź do **aplikacje dla przedsiębiorstw**.

2. Wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **centralnej pulpitu**.

    ![Łącze centralnej pulpitu na liście aplikacji](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** w **użytkowników** listy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji należy przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka pulpitu centralnej w panelu dostępu, można automatycznie pobrać zalogowany do aplikacji centralnej pulpitu.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

