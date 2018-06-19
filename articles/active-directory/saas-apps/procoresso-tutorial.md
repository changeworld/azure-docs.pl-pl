---
title: 'Samouczek: Integracji usługi Azure Active Directory z logowania jednokrotnego Procore | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Procore logowania jednokrotnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 8e6ea4103ce8c36a82f86c5b66eb8e519818023b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942575"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Samouczek: Integracji Azure Active Directory z Procore logowania jednokrotnego

Z tego samouczka dowiesz się integrowanie Procore logowania jednokrotnego w usłudze Azure Active Directory (Azure AD).

Integrowanie Procore rejestracji Jednokrotnej z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Procore logowania jednokrotnego
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Procore SSO (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Procore logowania jednokrotnego, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Procore logowania jednokrotnego jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Procore rejestracji Jednokrotnej z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-procore-sso-from-the-gallery"></a>Dodawanie Procore rejestracji Jednokrotnej z galerii
Aby skonfigurować integrację Procore logowania jednokrotnego do usługi Azure AD, należy dodać Procore rejestracji Jednokrotnej z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Procore rejestracji Jednokrotnej z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Procore logowania jednokrotnego**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/tutorial_procoresso_search.png)

5. W panelu wyników wybierz **Procore logowania jednokrotnego**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej Procore logowaniem jednokrotnym w oparciu o użytkownika testowego o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w Procore rejestracji Jednokrotnej dla użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Procore logowania jednokrotnego.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Procore logowania jednokrotnego.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Procore logowania jednokrotnego, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Procore logowania jednokrotnego](#creating-a-procore-sso-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Procore Usługa rejestracji Jednokrotnej jest połączona z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w aplikacji Procore logowania jednokrotnego.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Procore logowania jednokrotnego, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **Procore logowania jednokrotnego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. Na **Procore domena logowania jednokrotnego i adresy URL** sekcji, użytkownik nie trzeba wykonywać żadnych czynności, jak aplikacja już jest wstępna Integracja z usługą Azure.

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/tutorial_procoresso_url.png)

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/tutorial_general_400.png)

6. Na **Procore konfiguracji logowania jednokrotnego** kliknij **Konfigurowanie logowania jednokrotnego Procore** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Aby skonfigurować logowanie jednokrotne w **Procore logowania jednokrotnego** strona, zaloguj się do witryny procore firmy jako administrator.

8. Z listy przybornika w dół, kliknij pozycję **Admin** aby otworzyć stronę ustawienia logowania jednokrotnego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/procore_tool_admin.png)

9. Wklej wartości w polach, zgodnie z opisem poniżej-

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. W **pojedynczy znak na adres URL wystawcy** okno, wklej identyfikator jednostki SAML skopiowany z portalu Azure.

    b. W **SAML logowania na docelowy adres URL** okno, Wklej SAML pojedynczy znak na adres URL usługi skopiowany z portalu Azure.

    c. Teraz otworzyć **XML metadanych** pobrane wcześniej z portalu Azure i skopiuj certyfikat w tagu o nazwie **X509Certificate**. Wklej skopiowane wartości do **certyfikatu rejestracji jednokrotnej x509** pole.

10. Polecenie **zapisać zmiany**.

11. Po tych ustawień, użytkownik musi wysłać **nazwy domeny** (np. **contoso.com**) za pomocą którego logujesz się do Procore do [zespołem pomocy technicznej Procore](https://support.procore.com/) i zostaną one Aktywuj federacyjną rejestracją Jednokrotną dla tej domeny.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-procore-sso-test-user"></a>Tworzenie użytkownika testowego Procore logowania jednokrotnego

Wykonaj następujące czynności, aby utworzyć użytkownika testowego Procore w bok.

1. Zaloguj się do witryny procore firmy jako administrator.  

2. Z listy przybornika w dół, kliknij pozycję **katalogu** aby otworzyć stronę katalogu firmy.

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Polecenie **dodać osobę** opcję, aby otworzyć formularz i wprowadzić wykonania następujących opcji -

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/Procore_user_add.png)

    a. W **imię** pole tekstowe, imię użytkownika typu, takich jak **Britta**.

    b. W **nazwisko** pole tekstowe, nazwisko użytkownika typu, takich jak **Simona**.

    c. W **adres E-mail** , adres e-mail użytkownika typu pole tekstowe, takich jak **BrittaSimon@contoso.com**.

    d. Wybierz **szablon uprawnień** jako **później Zastosuj szablon uprawnień**.

    e. Kliknij przycisk **Utwórz**.

4. Sprawdź i zaktualizuj szczegóły dla nowo dodanego kontaktu.

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/Procore_user_check.png)

5. Polecenie **zapisywanie i wysyłanie Invitiation** (Jeśli wymagane jest zaproszenia pocztą) lub **zapisać** (Zapisz bezpośrednio) do ukończenia rejestracji użytkownika.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/Procore_user_save.png)  

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej Procore logowania jednokrotnego za pomocą platformy Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Procore logowania jednokrotnego, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Procore logowania jednokrotnego**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/procoresso-tutorial/tutorial_procoresso_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Jeśli chcesz przetestować jednego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://msdn.microsoft.com/library/dn308586). Po kliknięciu kafelka Procore logowania jednokrotnego w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Procore logowania jednokrotnego.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/procoresso-tutorial/tutorial_general_01.png
[2]: ./media/procoresso-tutorial/tutorial_general_02.png
[3]: ./media/procoresso-tutorial/tutorial_general_03.png
[4]: ./media/procoresso-tutorial/tutorial_general_04.png

[100]: ./media/procoresso-tutorial/tutorial_general_100.png

[200]: ./media/procoresso-tutorial/tutorial_general_200.png
[201]: ./media/procoresso-tutorial/tutorial_general_201.png
[202]: ./media/procoresso-tutorial/tutorial_general_202.png
[203]: ./media/procoresso-tutorial/tutorial_general_203.png

