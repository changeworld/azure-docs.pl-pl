---
title: 'Samouczek: Azure Active Directory integracji z menedżerem środowisko Adobe | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między Azure Active Directory i Adobe środowisko Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: d63ee16df9b49517c93cdc405cd2525f578d2a7f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347299"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Samouczek: Azure Active Directory integracji z menedżerem środowisko Adobe

Z tego samouczka dowiesz się integrowanie Adobe środowisko Manager z usługą Azure Active Directory (Azure AD).

Integrowanie programu Adobe środowisko Manager z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do programu Adobe środowisko Manager.
- Można umożliwić użytkownikom automatycznie pobrać zalogowany do Menedżera środowisko Adobe z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z menedżerem środowisko Adobe, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Menedżera środowisko Adobe logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Nie zaleca się używanie środowiska produkcyjnego do testowania czynności w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, [Pobierz bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie menedżera środowisko Adobe z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Dodaj Menedżera środowisko Adobe z galerii
Aby skonfigurować integrację programu Adobe środowisko Manager z usługą Azure AD, należy dodać Menedżera środowisko Adobe z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Menedżera środowisko Adobe z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Menedżera środowisko Adobe**. Wybierz **Menedżera środowisko Adobe** z panelu wyniki, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Menedżer obsługi Adobe na liście wyników](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Adobe środowisko Manager na podstawie użytkownika testowego o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, który użytkownik odpowiednika w Menedżerze środowisko Adobe jest dla użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i danemu użytkownikowi w Menedżerze środowisko Adobe.

W Menedżerze środowisko Adobe nadać wartość **Username** taką samą wartość **nazwy użytkownika** w usłudze Azure AD. Teraz ustanowieniu połączenia między dwóch użytkowników. 

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z menedżerem środowisko Adobe, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego Menedżera środowisko Adobe](#create-an-adobe-experience-manager-test-user) aby odpowiednikiem Simona Britta w Adobe środowisko Manager połączonej z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Adobe obsługi Menedżera.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z menedżerem środowisko Adobe, należy wykonać następujące czynności:**

1. W portalu Azure na **Menedżera środowisko Adobe** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** okna dialogowego, **tryb** menu rozwijanego wybierz **na języku SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. W **adresy URL i domeny Menedżera środowisko Adobe** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IdP** tryb:

    ![Adresy URL i domeny Menedżera środowisko Adobe pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. W **identyfikator** wpisz unikatową wartość, zdefiniowanego na tym serwerze AEM. 

    b. W **adres URL odpowiedzi** wpisz adres URL z następującego wzorca: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości z rzeczywistego identyfikatora i adres URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej Menedżera środowisko Adobe](https://helpx.adobe.com/support/experience-manager.html).
 
4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Następnie wykonaj następujące kroki, aby skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Menedżera środowisko Adobe pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    W **na adres URL logowania** wpisz adres URL serwera Adobe środowisko Manager. 

5. W **certyfikat podpisywania SAML** zaznacz **certyfikatu (Base64)**. Następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Aby otworzyć okno logowania jednokrotnego Konfiguracja w sekcji konfiguracji Menedżera środowisko Adobe, wybierz **Konfigurowanie Menedżera środowisko Adobe**. Kopiowania **SAML logowania jednokrotnego usługi adres URL**, **identyfikator jednostki SAML**, i **Sign-Out identyfikator** z sekcji krótkimi opisami.

    ![Link w sekcji konfiguracji](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. W innym oknie przeglądarki Otwórz **Menedżera środowisko Adobe** portalu administratora.

9. Wybierz **ustawienia** > **zabezpieczeń** > **użytkowników**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Wybierz **administratora** lub innych odpowiednich użytkowników.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Wybierz **ustawienia konta** > **Zarządzanie TrustStore**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. W obszarze **dodać certyfikat z pliku CER**, kliknij przycisk **wybierz plik certyfikatu**. Wyszukaj i wybierz plik certyfikatu, który został już pobrany z portalu Azure.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Certyfikat jest dodawany do TrustStore. Należy pamiętać, alias certyfikatu.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Na **użytkowników** wybierz pozycję **usługi uwierzytelniania**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Wybierz **ustawienia konta** > **KeyStore Utwórz/Zarządzaj**. Tworzenie magazynu kluczy, podając hasło.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Przejść do ekranu administratora. Następnie wybierz **ustawienia** > **operacji** > **konsoli sieci Web**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Spowoduje to otwarcie strony konfiguracji.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Znajdź **Adobe granitu SAML 2.0 uwierzytelniania obsługi**. Następnie wybierz **Dodaj** ikony.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Na tej stronie, należy wykonać następujące czynności.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. W **ścieżki** wprowadź **/**.

    b. W **IDP URL** wprowadź **SAML logowania jednokrotnego usługi adres URL** wartość skopiowany z portalu Azure.

    c. W **Alias certyfikatu IDP** wprowadź **Alias certyfikatu** wartości, który został dodany w TrustStore.

    d. W **zabezpieczeń podany identyfikator jednostki** wprowadź unikatowy **identyfikator jednostki SAML** wartość skonfigurowaną w portalu Azure.

    e. W **adres URL usługi klienta potwierdzenia** wprowadź **adres URL odpowiedzi** wartość skonfigurowaną w portalu Azure.

    f. W **magazynu haseł klucza** wprowadź **hasło** ustawioną w magazynie kluczy.

    g. W **identyfikator atrybutu użytkownika** wprowadź **Identyfikatora nazwy** lub inną nazwę użytkownika, która jest odpowiednia w Twoim przypadku.

    h. Wybierz **użytkowników automatycznie Utwórz CRX**.

    i. W **adresu URL wylogowania** wprowadź unikatowy **Sign-Out URL** wartość pochodzący z portalu Azure.

    j. Wybierz pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** kartę. Dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Możesz przeczytać dodatkowe informacje o funkcji dokumentacji osadzony w [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, należy wykonać następujące czynności:**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, w górnej części **wszyscy użytkownicy** okno dialogowe, wybierz opcję **Dodaj**.

    ![Przycisk Dodaj](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru. Zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Tworzenie użytkownika testowego Adobe środowisko Manager

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Menedżerze środowisko Adobe. W przypadku wybrania **użytkowników CRX automatycznie Utwórz** opcję, użytkownicy są tworzone automatycznie po pomyślnym uwierzytelnieniu. 

Jeśli chcesz ręcznie utworzyć użytkowników, pracy z [zespołem pomocy technicznej Menedżera środowisko Adobe](https://helpx.adobe.com/support/experience-manager.html) Aby dodać użytkowników na platformie Adobe obsługi Menedżera. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do przyznania im dostęp do programu Adobe środowisko Manager za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisanie roli użytkownika][200] 

**Aby przypisać do Menedżera środowisko Adobe Simona Britta, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji. Następnie przejdź do widoku katalogu, wybierz opcję **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Menedżera środowisko Adobe**.

    ![Łączy Menedżera środowisko Adobe na liście aplikacji](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka Adobe środowisko Manager w panelu dostępu należy należy pobrać automatycznie zalogowany do aplikacji Adobe obsługi Menedżera.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

