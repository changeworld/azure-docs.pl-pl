---
title: 'Samouczek: Integracji Azure Active Directory z Menedżera haseł Opiekun & cyfrowe magazynu | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między & cyfrowe magazynu usługi Azure Active Directory i Opiekun menedżera haseł.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: f314705bc74b70e6184720a18c44761131a5a07e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35940387"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Samouczek: Integracji Azure Active Directory z Menedżera haseł Opiekun & cyfrowe magazynu

W tym samouczku Dowiedz się jak zintegrować Menedżera haseł Opiekun & cyfrowe magazynu z usługą Azure Active Directory (Azure AD).

Integrowanie Menedżera haseł Opiekun & cyfrowe magazynu z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Menedżera haseł Opiekun & cyfrowe magazynu
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Menedżera haseł Opiekun & cyfrowe magazynu (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Menedżera haseł Opiekun & cyfrowe magazynu, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Menedżer haseł Opiekun & cyfrowe magazynu jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie menedżera haseł Opiekun & cyfrowe magazynu z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Dodawanie menedżera haseł Opiekun & cyfrowe magazynu z galerii
Aby skonfigurować integrację Menedżera haseł Opiekun & cyfrowe magazynu w usłudze Azure Active Directory, należy dodać Menedżera haseł Opiekun & cyfrowe magazynu z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Menedżera haseł Opiekun & cyfrowe magazynu z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Menedżera haseł Opiekun & cyfrowe magazynu**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/tutorial_keeper_search.png)

5. W panelu wyników wybierz **Menedżera haseł Opiekun & cyfrowe magazynu**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/tutorial_keeper_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Menedżera haseł Opiekun & cyfrowe magazyn oparty na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem w Menedżera haseł Opiekun & cyfrowe magazynu jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi Menedżera haseł Opiekun & cyfrowe magazynu w musi się.

W Menedżera haseł Opiekun & cyfrowe magazynu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Menedżera haseł Opiekun & cyfrowe magazynu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Menedżera haseł Opiekun & cyfrowe magazynu](#creating-a-keeperpasswordmanager-test-user)**  — aby mieć odpowiednikiem Simona Britta w Menedżera haseł Opiekun & cyfrowe magazynu, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Menedżera haseł Opiekun & cyfrowe magazynu.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Menedżera haseł Opiekun & cyfrowe magazynu, wykonaj następujące czynności:**

1. W portalu Azure na **Menedżera haseł Opiekun & cyfrowe magazynu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/keeperpasswordmanager-tutorial/tutorial_keeper_samlbase.png)

3. Na **Menedżera haseł Opiekun & cyfrowe domeny magazynu i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/keeperpasswordmanager-tutorial/tutorial_keeper_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    c. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://{SSO CONNECT SERVER}/sso-connect`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [Menedżera haseł Opiekun & cyfrowe magazynu obsługę klientów zespołu](https://keepersecurity.com/contact.html) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/keeperpasswordmanager-tutorial/tutorial_keeper_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/keeperpasswordmanager-tutorial/tutorial_general_400.png)
    
6. Na **Menedżera haseł Opiekun & cyfrowe konfiguracji magazynu** kliknij **Konfigurowanie Menedżera haseł Opiekun & cyfrowe magazynu** otworzyć **Konfigurowanie logowania jednokrotnego** okno. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/keeperpasswordmanager-tutorial/tutorial_keeper_configure.png) 

7. Skonfigurować logowanie jednokrotne w **Menedżera haseł Opiekun & cyfrowe konfiguracji magazynu** po stronie, postępuj zgodnie z wytycznymi na [Opiekun Przewodnik obsługi](https://keepersecurity.com/assets/pdf/SettingupAzurewithKeeperSSOConnect.pdf)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-keeper-password-manager--digital-vault-test-user"></a>Tworzenie użytkownika testowego Menedżera haseł Opiekun & cyfrowe magazynu

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Menedżera haseł Opiekun & cyfrowe magazynu, muszą mieć przydzielone do Menedżera haseł Opiekun & cyfrowe magazynu. Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników zostaną utworzone w aplikacji automatycznie. Możesz skontaktować się [Obsługa Opiekun](https://keepersecurity.com/contact.html), jeśli chcesz ręcznie skonfigurować użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Menedżera haseł Opiekun & cyfrowe magazynu.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Menedżera haseł Opiekun & cyfrowe magazynu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Menedżera haseł Opiekun & cyfrowe magazynu**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/keeperpasswordmanager-tutorial/tutorial_keeper_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Menedżera haseł Opiekun & cyfrowe magazynu w panelu dostępu, należy pobrać strony logowania Menedżera haseł Opiekun & cyfrowe magazynu aplikacji. Po pomyślnym uwierzytelnieniu należy pobrać aplikację. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keeperpasswordmanager-tutorial/tutorial_general_01.png
[2]: ./media/keeperpasswordmanager-tutorial/tutorial_general_02.png
[3]: ./media/keeperpasswordmanager-tutorial/tutorial_general_03.png
[4]: ./media/keeperpasswordmanager-tutorial/tutorial_general_04.png

[100]: ./media/keeperpasswordmanager-tutorial/tutorial_general_100.png

[200]: ./media/keeperpasswordmanager-tutorial/tutorial_general_200.png
[201]: ./media/keeperpasswordmanager-tutorial/tutorial_general_201.png
[202]: ./media/keeperpasswordmanager-tutorial/tutorial_general_202.png
[203]: ./media/keeperpasswordmanager-tutorial/tutorial_general_203.png

