---
title: 'Samouczek: Azure Active Directory integracji z oprogramowaniem OfficeSpace | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między oprogramowaniem OfficeSpace i Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 0d213c251a463406d50d442b7c78b5f819b8534e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938901"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Samouczek: Azure Active Directory integracji z oprogramowaniem OfficeSpace

Z tego samouczka dowiesz się integrowanie OfficeSpace oprogramowania z usługi Azure Active Directory (Azure AD).

Integracja oprogramowania OfficeSpace z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do oprogramowania OfficeSpace.
- Umożliwia użytkownikom automatycznie pobrać zalogowane oprogramowania OfficeSpace (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem OfficeSpace, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie OfficeSpace logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie oprogramowania OfficeSpace z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-officespace-software-from-the-gallery"></a>Dodawanie oprogramowania OfficeSpace z galerii
Aby skonfigurować integrację usługi Azure AD OfficeSpace oprogramowania, należy dodać OfficeSpace oprogramowania z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać OfficeSpace oprogramowanie z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **oprogramowania OfficeSpace**, wybierz pozycję **oprogramowania OfficeSpace** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![OfficeSpace oprogramowania z listy wyników](./media/officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z oprogramowaniem OfficeSpace w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w oprogramowaniu OfficeSpace jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w oprogramowaniu OfficeSpace musi określone.

W oprogramowaniu OfficeSpace, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem OfficeSpace, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego oprogramowania OfficeSpace](#create-a-officespace-software-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta OfficeSpace oprogramowania, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w używanej aplikacji OfficeSpace.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem OfficeSpace, wykonaj następujące czynności:**

1. W portalu Azure na **oprogramowania OfficeSpace** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/officespace-tutorial/tutorial_officespace_samlbase.png)

3. Na **OfficeSpace oprogramowania domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny oprogramowania OfficeSpace pojedynczy informacje logowania jednokrotnego](./media/officespace-tutorial/tutorial_officespace_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania OfficeSpace](mailto:support@officespacesoftware.com) uzyskać te wartości. 

4. Aplikacja OfficeSpace oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie atrybutów](./media/officespace-tutorial/tutorial_officespace_attribute.png)

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, wybierz opcję **user.mail** jako **identyfikator użytkownika** i dla każdego wiersza w tabeli poniżej, wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | wyślij wiadomość e-mail | User.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Skonfiguruj Dodaj ](./media/officespace-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie atrybutów](./media/officespace-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **Ok**
 
6. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartości certyfikatu.

    ![Łącze pobierania certyfikatu](./media/officespace-tutorial/tutorial_officespace_certificate.png) 

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/officespace-tutorial/tutorial_general_400.png)

8. Na **konfiguracji oprogramowania OfficeSpace** , kliknij przycisk **Konfigurowanie oprogramowania OfficeSpace** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja oprogramowania OfficeSpace](./media/officespace-tutorial/tutorial_officespace_configure.png) 

9. W oknie przeglądarki innej witryny sieci web Zaloguj się do dzierżawy OfficeSpace oprogramowania jako administrator.

10. Przejdź do **ustawienia** i kliknij przycisk **łączniki**.

    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_002.png)

11. Kliknij przycisk **uwierzytelnianie SAML**.

    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_003.png)

12. W **uwierzytelnianie SAML** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. W **adres url wylogowania dostawcy** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.

    b. W **klienta idp docelowy adres url** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    c. Wklej **odcisk palca** wartość, która została skopiowana z portalu Azure do **odcisk palca certyfikatu klienta IDP** pola tekstowego. 

    d. Kliknij przycisk **Zapisz ustawienia**.


> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/officespace-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/officespace-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/officespace-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/officespace-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-officespace-software-test-user"></a>Tworzenie użytkownika testowego OfficeSpace oprogramowania

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta OfficeSpace oprogramowania. Oprogramowanie OfficeSpace obsługę w czasie, który jest domyślnie włączone.

Nie ma elementu akcji można w tej sekcji. Nowy użytkownik zostanie utworzony podczas próby dostępu do oprogramowania OfficeSpace, jeśli go jeszcze nie istnieje.

> [!NOTE]
> Jeśli trzeba ręcznie utworzyć użytkownika, musisz skontaktuj się z [zespołem pomocy technicznej oprogramowania OfficeSpace](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do oprogramowania OfficeSpace.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta OfficeSpace oprogramowania, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **oprogramowania OfficeSpace**.

    ![Łącze OfficeSpace oprogramowania na liście aplikacji](./media/officespace-tutorial/tutorial_officespace_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka oprogramowania OfficeSpace w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji oprogramowania OfficeSpace.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/officespace-tutorial/tutorial_general_01.png
[2]: ./media/officespace-tutorial/tutorial_general_02.png
[3]: ./media/officespace-tutorial/tutorial_general_03.png
[4]: ./media/officespace-tutorial/tutorial_general_04.png

[100]: ./media/officespace-tutorial/tutorial_general_100.png

[200]: ./media/officespace-tutorial/tutorial_general_200.png
[201]: ./media/officespace-tutorial/tutorial_general_201.png
[202]: ./media/officespace-tutorial/tutorial_general_202.png
[203]: ./media/officespace-tutorial/tutorial_general_203.png

