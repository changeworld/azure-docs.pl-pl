---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą programu Adobe Experience Manager | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 56b392e57809cea0ae93800df39bb9dacd164ce2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054186"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą programu Adobe Experience Manager

W tym samouczku dowiesz się, jak zintegrować Adobe Experience Manager z usługą Azure Active Directory (Azure AD).

Integrowanie Adobe Experience Manager z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Adobe Experience Manager.
- Aby umożliwić użytkownikom automatycznie pobrać zalogowany do Adobe Experience Manager za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą programu Adobe Experience Manager, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Adobe Experience Manager logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Nie zaleca się testowanie kroki opisane w tym samouczku przy użyciu środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli masz środowisko wersji próbnej usługi Azure AD [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Adobe Experience Manager za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Dodaj Adobe Experience Manager z galerii
Aby skonfigurować integrację programu Adobe Experience Manager w usłudze Azure AD, należy dodać Adobe Experience Manager za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Adobe Experience Manager z galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Adobe Experience Manager**. Wybierz **Adobe Experience Manager** z panel wyników, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Adobe Experience Manager na liście wyników](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą programu Adobe Experience Manager oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, kim jest odpowiednikiem użytkownik Adobe Experience Manager do użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w programie Adobe Experience Manager.

W programie Adobe Experience Manager podania wartości **Username** taką samą wartość **nazwa_użytkownika** w usłudze Azure AD. Łącze między dwóch użytkowników ma ustanowione. 

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą programu Adobe Experience Manager, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. [Tworzenie użytkownika testowego Adobe Experience Manager](#create-an-adobe-experience-manager-test-user) mieć odpowiednikiem Britta Simon w Adobe Experience Manager połączonej z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. [Testowanie logowania jednokrotnego](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Adobe Experience Manager.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą programu Adobe Experience Manager, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Adobe Experience Manager** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** dialogowym **tryb** menu rozwijanego wybierz opcję **opartej na SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. W **Adobe Experience Manager domena i adresy URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **tożsamości** trybu:

    ![Adobe Experience Manager domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. W **identyfikator** wpisz unikatową wartość, definiujące na danym serwerze AEM. 

    b. W **adres URL odpowiedzi** wpisz adres URL z następującym wzorcem: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości z rzeczywistego identyfikatora, a adres URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z pomocą [zespołem pomocy technicznej Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Następnie wykonaj następujące kroki, aby skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Adobe Experience Manager domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    W **na adres URL logowania** wpisz adres URL serwera Adobe Experience Manager. 

5. W **certyfikat podpisywania SAML** zaznacz **certyfikat (Base64)**. Następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Aby otworzyć okno konfiguracji logowania jednokrotnego w sekcji konfiguracji Adobe Experience Manager, wybierz **skonfigurować Adobe Experience Manager**. Kopiowania **URL SAML logowania jednokrotnego usługi**, **identyfikator jednostki SAML**, i **identyfikator wylogowania** z sekcji krótki.

    ![Link w sekcji konfiguracji](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. W innym oknie przeglądarki Otwórz **Adobe Experience Manager** portalu administracyjnym.

9. Wybierz **ustawienia** > **zabezpieczeń** > **użytkowników**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Wybierz **administratora** lub innych odpowiednich użytkowników.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Wybierz **ustawienia konta** > **Zarządzanie TrustStore**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. W obszarze **Dodaj certyfikat z pliku CER**, kliknij przycisk **wybierz plik certyfikatu**. Wyszukaj i wybierz plik certyfikatu, który został już pobrany z witryny Azure portal.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Certyfikat jest dodawany do TrustStore. Należy pamiętać, alias certyfikatu.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Na **użytkowników** wybierz opcję **usługi uwierzytelniania**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Wybierz **ustawienia konta** > **magazynu kluczy Utwórz/zarządzanie**. Tworzenie magazynu kluczy, podając hasło.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Wróć do ekranu administratora. Następnie wybierz pozycję **ustawienia** > **operacji** > **konsoli sieci Web**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Spowoduje to otwarcie strony konfiguracji.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Znajdź **Adobe Granite SAML 2.0 uwierzytelniania obsługi**. Następnie wybierz pozycję **Dodaj** ikony.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Wykonaj następujące czynności na tej stronie.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. W **ścieżki** wprowadź **/**.

    b. W **adresu URL protokołu IDP** wprowadź **URL SAML logowania jednokrotnego usługi** wartością skopiowaną z witryny Azure portal.

    c. W **Alias certyfikatu tożsamości** wprowadź **Alias certyfikatu** wartości, które zostały dodane w TrustStore.

    d. W **identyfikator jednostki podane zabezpieczeń** wprowadź unikatowy **identyfikator jednostki SAML** wartości, które zostały skonfigurowane w witrynie Azure portal.

    e. W **adres URL usługi konsumenta potwierdzenie** wprowadź **adres URL odpowiedzi** wartości, które zostały skonfigurowane w witrynie Azure portal.

    f. W **hasło klucza Store** wprowadź **hasło** ustawionym w magazynie kluczy.

    g. W **identyfikator atrybutu użytkownika** wprowadź **identyfikator nazwy** lub inny identyfikator użytkownika, która jest odpowiednia w Twoim przypadku.

    h. Wybierz **użytkowników automatycznie Utwórz CRX**.

    i. W **adres URL wylogowania** wprowadź unikatowy **adres URL wylogowania** wartość, która jest wyświetlana w witrynie Azure portal.

    j. Wybierz pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę. Następnie uzyskać dostęp do dokumentacji embedded za pomocą **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, w górnej części **wszyscy użytkownicy** okno dialogowe, wybierz opcję **Dodaj**.

    ![Przycisk Dodaj](./media/adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru. Zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Tworzenie użytkownika testowego Adobe Experience Manager

W tej sekcji utworzysz użytkownika o nazwie Britta Simon Adobe Experience Manager. W przypadku wybrania **użytkowników CRX automatycznie Utwórz** opcję, użytkownicy są tworzone automatycznie po pomyślnym uwierzytelnieniu. 

Jeśli chcesz ręcznie tworzyć użytkowników, pracować z [zespołem pomocy technicznej Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) Aby dodać użytkowników na platformie Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do nadawania im praw do Adobe Experience Manager za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do Adobe Experience Manager, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji. Następnie przejdź do widoku katalogu wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Adobe Experience Manager**.

    ![Łącze Adobe Experience Manager na liście aplikacji](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka Adobe Experience Manager w panelu dostępu, należy pobrać automatycznie zarejestrowaniu w usłudze aplikacji Adobe Experience Manager.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/adobeexperiencemanager-tutorial/tutorial_general_203.png

