---
title: 'Samouczek: Azure Active Directory integrację z serwerem Tableau | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i serwera Tableau.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: a10e0bda9728aed9aae74a85d4bb4bdb4113292a
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35943466"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Samouczek: Azure Active Directory integrację z serwerem Tableau

Z tego samouczka dowiesz się integrowanie Tableau serwera z usługą Azure Active Directory (Azure AD).

Integrowanie Tableau serwera z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do serwera Tableau
- Umożliwia użytkownikom automatycznie pobrać zalogowane serwerowi Tableau (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z serwerem Tableau, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Serwer Tableau logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodanie serwera Tableau z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-tableau-server-from-the-gallery"></a>Dodanie serwera Tableau z galerii
Aby skonfigurować integrację serwera Tableau do usługi Azure AD, należy dodać serwer Tableau z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać serwer Tableau z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **serwera Tableau**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/tutorial_tableauserver_search.png)

5. W panelu wyników wybierz **serwera Tableau**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z serwerem Tableau oparte na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Tableau serwera jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi na serwerze Tableau musi określone.

Na serwerze Tableau, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z serwerem Tableau, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego serwera Tableau](#creating-a-tableau-server-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Tableau serwera, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji serwera Tableau.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z serwerem Tableau, wykonaj następujące czynności:**

1. W portalu Azure na **serwera Tableau** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Na **adresy URL i domeną serwera Tableau** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://azure.<domain name>.link`
    
    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://azure.<domain name>.link`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Poprzednie wartości nie są wartości rzeczywistych. Później należy zaktualizować wartości z rzeczywistego adresu URL i identyfikator ze strony konfiguracji serwera Tableau. 

4. Aplikacja serwera TABLEAU oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **"Atrybuty użytkownika"** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład dla tego samego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/3.png)
    
5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | nazwa użytkownika | *user.mailnickname* |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_officespace_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **Ok**


6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, musisz logowanie do serwera Tableau dzierżawy z uprawnieniami administratora.
   
   a. W konfiguracji serwera Tableau, kliknij przycisk **SAML** kartę.
  
    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Zaznacz pole wyboru z **SAML używana dla logowania jednokrotnego**.
   
   c. Serwer TABLEAU adres zwrotny URL — adres URL, który serwer Tableau użytkownicy będą uzyskiwać dostęp do, takich jak http://tableau_server. Przy użyciu http://localhost nie jest zalecane. Przy użyciu adresu URL znakiem ukośnika (na przykład http://tableau_server/) nie jest obsługiwane. Kopia **serwera Tableau zwrotnego adresu URL** i wklej go do usługi Azure AD **na adres URL logowania** textbox w **adresy URL i domeną serwera Tableau** sekcji.
   
   d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje instalacją serwera Tableau do dostawców tożsamości. Możesz wprowadzić adres URL serwera Tableau ponownie, jeśli chcesz, ale nie musi być adres URL serwera Tableau. Kopiuj **SAML identyfikator jednostki** i wklej go do usługi Azure AD **identyfikator** textbox w **adresy URL i domeną serwera Tableau** sekcji.
     
   e. Kliknij przycisk **Eksportuj plik metadanych** i otwórz go w aplikacji, Edytor tekstu. Znajdź adres URL usługi klienta potwierdzenia z Http Post i indeksu 0 i skopiuj adres URL. Teraz wklej go do usługi Azure AD **adres URL odpowiedzi** textbox w **adresy URL i domeną serwera Tableau** sekcji.
   
   f. Zlokalizuj plik metadanych Federacji pobrany z portalu Azure, a następnie przekaż go w **pliku metadanych SAML Idp**.
   
   g. Kliknij przycisk **OK** przycisku na stronie Konfiguracja serwera Tableau.
   
    >[!NOTE] 
    >Trzeba przekazać dowolny certyfikat w konfiguracji logowania jednokrotnego SAML serwera Tableau i zostaną uzyskać pominięte w procesie logowania jednokrotnego.
    >Jeśli konieczne pomoc w konfigurowaniu SAML na serwerze Tableau, można znaleźć w tym artykule [skonfigurować SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-tableau-server-test-user"></a>Tworzenie użytkownika testowego Tableau serwera

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Tableau serwera. Należy udostępnić wszystkich użytkowników na serwerze Tableau. 

Że nazwa użytkownika powinna być zgodna wartość, które zostały skonfigurowane w atrybucie niestandardowym usługi Azure AD z **username**. Z mapowaniem poprawną integrację powinny działać [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Należy ręcznie utworzyć użytkownika, należy skontaktować się z administratorem serwera Tableau w Twojej organizacji.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do serwera Tableau.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Tableau serwera, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **serwera Tableau**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Tableau serwera w panelu dostępu należy powinien pobrać automatycznie podpisany w przypadku aplikacji serwera Tableau.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/tableauserver-tutorial/tutorial_general_203.png

