---
title: 'Samouczek: Integracji Azure Active Directory z Dropbox dla firm | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i skrzynki dla firm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: aa00a88f8325345b1b45d7d0971a03590bce1029
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342634"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Samouczek: Integracji Azure Active Directory z Dropbox dla firm

Z tego samouczka dowiesz się integrowanie skrzynki dla firm z usługą Azure Active Directory (Azure AD).

Integrowanie usługi Dropbox dla firm z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do skrzynki dla firm.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do skrzynki dla firm (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Dropbox dla firm, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dropbox dla firm logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie skrzynki dla firm z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dodawanie skrzynki dla firm z galerii
Aby skonfigurować integrację usługi Dropbox dla firm z usługą Azure AD, należy dodać skrzynki dla firm z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać skrzynki dla firm z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Dropbox dla firm**, wybierz pozycję **Dropbox dla firm** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Dropbox dla firm na liście wyników](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Dropbox dla firm w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Dropbox dla firm jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Dropbox dla firm.

W Dropbox dla firm, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Dropbox dla firm, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Utwórz Dropbox dla użytkownika testowego firm](#create-a-dropbox-for-business-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Dropbox dla firm, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w usłudze Dropbox dla aplikacji biznesowych.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Dropbox dla firm, wykonaj następujące czynności:**

1. W portalu Azure na **Dropbox dla firm** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. Na **Dropbox domeny biznesowych i adresów URL** sekcji, wykonaj następujące czynności:

    ![Dropbox domeny biznesowych i adresów URL jednym logowania jednokrotnego informacji](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.dropbox.com/sso/<id>`

    b. W **identyfikator** tekstowym, wpisz wartość: `Dropbox`

    > [!NOTE] 
    > Poprzedni wartość adres URL logowania nie jest rzeczywistą wartość. Wartość zaktualizuje rzeczywiste logowania jednokrotnego adres URL, który znajduje się w dalszej części tego samouczka. Skontaktuj się z [Dropbox dla zespołu pomocy technicznej klienta Business](https://www.dropbox.com/business/contact) można uzyskać wartość. 
 

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. Na **Dropbox konfiguracji Business** , kliknij przycisk **Konfigurowanie skrzynki dla firm** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Dropbox konfiguracji biznesowa](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Aby skonfigurować logowanie jednokrotne w **skrzynki dla firm** po stronie znajduje się w usłudze Dropbox dla dzierżawy biznesowych.

    a. Zaloguj się do Twojej skrzynki dla firm dzierżawcy. 
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "skonfigurować logowanie jednokrotne")
   
    b. W okienku nawigacji po lewej stronie kliknij **konsoli administracyjnej**. 
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "skonfigurować logowanie jednokrotne")
   
    c. Na **konsoli administracyjnej**, kliknij przycisk **uwierzytelniania** w lewym okienku nawigacji. 
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "skonfigurować logowanie jednokrotne")
   
    d. W **logowanie jednokrotne** zaznacz **Włącz rejestrację jednokrotną**, a następnie kliknij przycisk **więcej** do Rozwiń tę sekcję.  
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "skonfigurować logowanie jednokrotne")
   
    e. Skopiuj adres URL w polu **użytkownicy mogą rejestrować wprowadź swój adres e-mail lub można przejść bezpośrednio do** i wklej ją do **adres URL logowania** pole tekstowe z **Dropbox domeny biznesowych i adresów URL** sekcji z portalu Azure. 
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
8. W **logowanie jednokrotne** sekcji **uwierzytelniania** wykonaj następujące czynności: 
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "skonfigurować logowanie jednokrotne")
   
    a. Kliknij przycisk **wymagane**.
   
    b. W **adres URL logowania** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    c. Kliknij przycisk **wybierz certyfikat**, a następnie przejdź do Twojej **pliku zakodowanego certyfikatu Base64**.

    d. Kliknij przycisk **zapisać zmiany** w celu ukończenia konfiguracji w usłudze DropBox dla dzierżawy biznesowych.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Utwórz Dropbox dla firm użytkownika testowego

W tej sekcji użytkownika o nazwie Simona Britta jest tworzony w Dropbox dla firm. Dropbox dla firm obsługę w czasie, który jest domyślnie włączona.

Nie ma elementu akcji można w tej sekcji. Jeśli użytkownik nie istnieje w Dropbox dla firm, nowy jest tworzony podczas próby dostępu Dropbox dla firm.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [Dropbox dla zespołu pomocy technicznej klienta biznesowa](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do usługi Dropbox dla firm.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Dropbox dla firm, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Dropbox dla firm**.

    ![Dropbox łącza biznesowe na liście aplikacji](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu skrzynki dla firm kafelka w panelu dostępu, należy pobrać strony logowania o usłudze Dropbox dla aplikacji biznesowych.
 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png

