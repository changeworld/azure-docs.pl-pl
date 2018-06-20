---
title: 'Samouczek: Integracji Azure Active Directory z SignalFx | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 69a9aa2ab6c1fd379d7f3421fc70cc8cffd1cb3a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214274"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Samouczek: Integracji Azure Active Directory z SignalFx

Z tego samouczka dowiesz się integrowanie SignalFx z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD SignalFx zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do SignalFx.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do SignalFx (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SignalFx, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SignalFx logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SignalFx z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-signalfx-from-the-gallery"></a>Dodawanie SignalFx z galerii
Aby skonfigurować integrację usługi Azure AD SignalFx, należy dodać SignalFx z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SignalFx z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SignalFx**, wybierz pozycję **SignalFx** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SignalFx na liście wyników](./media/signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SignalFx w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SignalFx jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w SignalFx musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SignalFx, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego SignalFx](#create-a-signalfx-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SignalFx połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji SignalFx.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SignalFx, wykonaj następujące czynności:**

1. W portalu Azure na **SignalFx** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. Na **SignalFx domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny SignalFx pojedynczy informacje logowania jednokrotnego](./media/signalfx-tutorial/tutorial_signalfx_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://api.signalfx.com/v1/saml/metadata`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Powyższe nie jest rzeczywistą wartość. Rzeczywiste odpowiedzi adres URL, który znajduje się w dalszej części tego samouczka można zaktualizować wartości.

4. Aplikacja SignalFx oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.   

    ![Konfigurowanie rejestracji jednokrotnej](./media/signalfx-tutorial/tutorial_signalfx_attribute.png)

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | User.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/signalfx-tutorial/tutorial_attribute_04.png)

    ![Skonfiguruj Addattb rejestracji jednokrotnej](./media/signalfx-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.
 
6. Na **certyfikat podpisywania SAML** sekcji, wykonaj następujące czynności: 

    ![Łącze pobierania certyfikatu](./media/signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

    b. Kliknij przycisk **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/signalfx-tutorial/tutorial_general_400.png)

8. Na **konfiguracji SignalFx** , kliknij przycisk **skonfigurować SignalFx** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    ![Konfiguracja SignalFx](./media/signalfx-tutorial/tutorial_signalfx_configure.png) 

9. Logowanie do witryny firmy SignalFx jako administrator.

10. W SignalFx kliknij górny **integracji** aby otworzyć stronę integracji.

    ![SignalFx Integration](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

11. Polecenie **usługi Azure Active Directory** kafelka w obszarze **logowania usługi** sekcji.
 
    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

12. Polecenie **nowa INTEGRACJA** i w obszarze **zainstalować** kartę należy wykonać następujące czynności:
 
    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. W **nazwa** typu pole tekstowe, Nowa nazwa integracji, takich jak **logowania jednokrotnego SAML OurOrgName**.

    b. Kopia **identyfikator integracji** wartości i dołączenia **adres URL odpowiedzi służący** jak `https://api.signalfx.com/v1/saml/acs/<integration ID>` w **adres URL odpowiedzi służący** pole tekstowe z **SignalFx domeny i adres URL** sekcji w portalu Azure.

    c. Polecenie **Przekaż plik** przekazać **certyfikatu szyfrowania Base64** pobrany z portalu Azure w **certyfikatu** pola tekstowego.

    d. W **adres URL wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    e. W **adres URL metadanych** pole tekstowe, Wklej **adres Url metadanych Federacji aplikacji** którego została skopiowana z portalu Azure.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/signalfx-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/signalfx-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/signalfx-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/signalfx-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-signalfx-test-user"></a>Tworzenie użytkownika testowego SignalFx

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w SignalFx. SignalFx obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu SignalFx, jeśli go jeszcze nie istnieje.

Kiedy użytkownik zaloguje się SignalFx z logowania jednokrotnego SAML po raz pierwszy, [zespołem pomocy technicznej SignalFx](mailto:kmazzola@signalfx.com) wysyła je wiadomość e-mail zawierającą łącze, które użytkownik musi kliknąć przycisk za pośrednictwem do uwierzytelniania. Dzieje się tak tylko po raz pierwszy zaloguje się użytkownik; kolejnych logowań nie wymaga weryfikacji wiadomości e-mail.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [SignalFx zespołem pomocy technicznej](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu SignalFx.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SignalFx, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SignalFx**.

    ![Łącze SignalFx na liście aplikacji](./media/signalfx-tutorial/tutorial_signalfx_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka SignalFx w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji SignalFx.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/signalfx-tutorial/tutorial_general_01.png
[2]: ./media/signalfx-tutorial/tutorial_general_02.png
[3]: ./media/signalfx-tutorial/tutorial_general_03.png
[4]: ./media/signalfx-tutorial/tutorial_general_04.png

[100]: ./media/signalfx-tutorial/tutorial_general_100.png

[200]: ./media/signalfx-tutorial/tutorial_general_200.png
[201]: ./media/signalfx-tutorial/tutorial_general_201.png
[202]: ./media/signalfx-tutorial/tutorial_general_202.png
[203]: ./media/signalfx-tutorial/tutorial_general_203.png

