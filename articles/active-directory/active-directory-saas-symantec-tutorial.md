---
title: 'Samouczek: Integracji Azure Active Directory z usługi zabezpieczeń firmy Symantec w sieci Web (WSS) | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usług zabezpieczeń firmy Symantec w sieci Web (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 0dc75a1091c56ddbbffcd54047b2465ece231254
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Samouczek: Integracji Azure Active Directory z usługi zabezpieczeń firmy Symantec w sieci Web (WSS)

Z tego samouczka dowiesz się, jak integracji Twoje konto usługi zabezpieczeń firmy Symantec w sieci Web (WSS) przy użyciu konta usługi Azure Active Directory (Azure AD), dzięki czemu WSS można uwierzytelnić użytkownika końcowego udostępniane w usłudze Azure AD przy użyciu uwierzytelniania SAML i wymuszać reguły zasady na poziomie użytkownika lub grupy.

Integrowanie usługi zabezpieczeń firmy Symantec w sieci Web (WSS) z usługą Azure AD zapewnia następujące korzyści:

- Zarządzaj wszystkimi użytkowników i grupy używane przez Twoje konto programu WSS w portalu usługi Azure AD. 

- Zezwalaj użytkownikom końcowym uwierzytelnić się programu WSS przy użyciu swoich poświadczeń usługi Azure AD.

- Włącz wymuszania użytkowników i grupy poziomu reguł zdefiniowanych w ramach konta programu WSS.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sieci Web usług zabezpieczeń (WSS) firmy Symantec, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Konto usługi zabezpieczeń firmy Symantec w sieci Web (WSS)

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się przy użyciu konta programu WSS, który jest obecnie używana w celu produkcji.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać konta programu WSS, który jest obecnie używana w celu produkcji dla tego testu, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku skonfigurujesz usługi Azure AD, aby włączyć logowanie jednokrotne do programu WSS przy użyciu poświadczeń użytkownika zdefiniowane w ramach konta usługi Azure AD.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie aplikacji usługi zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Dodawanie usługi zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii
Aby skonfigurować integrację usługi zabezpieczeń firmy Symantec w sieci Web (WSS) do usługi Azure AD, należy dodać usługi zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **usługi zabezpieczeń firmy Symantec w sieci Web (WSS)**, wybierz pozycję **usługi zabezpieczeń firmy Symantec w sieci Web (WSS)** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Symantec Web zabezpieczeń usługi (WSS) na liście wyników](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z firmy Symantec w sieci Web zabezpieczeń usługi (WSS) w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem w sieci Web usług zabezpieczeń (WSS) firmy Symantec jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i powiązanych użytkowników w sieci Web usług zabezpieczeń (WSS) firmy Symantec.

W firmy Symantec w sieci Web zabezpieczeń usługi (WSS), przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi zabezpieczeń firmy Symantec w sieci Web (WSS), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego usługi zabezpieczeń firmy Symantec w sieci Web (WSS)](#create-a-symantec-web-security-service-wss-test-user)**  — aby odpowiednikiem Simona Britta w firmy Symantec w sieci Web usługi zabezpieczeń (WSS) połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji usługi zabezpieczeń firmy Symantec w sieci Web (WSS).

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z usługi zabezpieczeń firmy Symantec w sieci Web (WSS), wykonaj następujące czynności:**

1. W portalu Azure na **usługi zabezpieczeń firmy Symantec w sieci Web (WSS)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. Na **domeny usługi (WSS) zabezpieczeń firmy Symantec w sieci Web i adres URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi zabezpieczeń firmy Symantec w sieci Web (WSS) i adresy URL pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Skontaktuj się z [zespołem pomocy technicznej klienta usługi zabezpieczeń firmy Symantec w sieci Web (WSS)](https://www.symantec.com/contact-us) Jeśli wartości **identyfikator** i **adres URL odpowiedzi** jakiegoś powodu nie działają.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Aby skonfigurować rejestrację jednokrotną po stronie usługi zabezpieczeń firmy Symantec w sieci Web (WSS), zapoznaj się z dokumentacją online programu WSS. Pobrany **XML metadanych** pliku musi być importowane do portalu programu WSS. Skontaktuj się z [usługi zabezpieczeń firmy Symantec w sieci Web (WSS) obsługuje zespołu](https://www.symantec.com/contact-us) Jeśli potrzebujesz pomocy z konfiguracją w portalu programu WSS.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Tworzenie użytkownika testowego usługi zabezpieczeń firmy Symantec w sieci Web (WSS)

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w sieci Web usług zabezpieczeń (WSS) firmy Symantec. Odpowiednie nazwy użytkownika końcowego mogą być utworzone ręcznie w portalu programu WSS lub poczekać dla użytkowników/grupy udostępniane w usłudze Azure AD do synchronizacji programu WSS portalu po kilku minutach (~ 15 minut). Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. Publiczny adres IP komputera użytkownika końcowego, która będzie używana do przeglądania witryny sieci Web muszą być udostępniane w portalu usługi zabezpieczeń firmy Symantec w sieci Web (WSS).

> [!NOTE]
> Sprawdź [kliknij tutaj](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) uzyskać komputera na publiczny adres IP.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do sieci Web usług zabezpieczeń (WSS) firmy Symantec.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do usługi zabezpieczeń firmy Symantec w sieci Web (WSS), wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **usługi zabezpieczeń firmy Symantec w sieci Web (WSS)**.

    ![Łącze usługi zabezpieczeń firmy Symantec w sieci Web (WSS) na liście aplikacji](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz funkcji pojedynczego logowania jednokrotnego teraz, gdy skonfigurowano konto programu WSS do użycia usługi Azure AD na potrzeby uwierzytelniania SAML.

Po skonfigurowaniu serwera proxy ruchu programu WSS, przeglądarki sieci web po otwarciu przeglądarki sieci web i spróbuj przejść do lokacji, a następnie użytkownik zostanie przekierowany do strony logowania systemu Azure. Wprowadź poświadczenia użytkownika testowego, że zainicjowano w usłudze Azure AD (czyli BrittaSimon) oraz skojarzone hasło. Po uwierzytelnieniu będzie mógł przejść do witryny sieci Web, który został wybrany. Należy utworzyć reguły na stronie programu WSS Aby zablokować BrittaSimon przeglądanie do określonej lokacji, a następnie powinna zostać wyświetlona strona bloku WSS przy próbie przejdź do tej lokacji jako użytkownik BrittaSimon.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

