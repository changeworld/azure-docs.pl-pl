---
title: 'Samouczek: Integracji Azure Active Directory z SAP Business obiektu chmury | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SAP Business obiektu chmury.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 7b05da97602ba923e5fc4a5b6a922fc2e8699994
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941079"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Samouczek: Integracji Azure Active Directory z SAP Business obiektu chmury

Z tego samouczka dowiesz się integrowanie SAP Business obiektu chmury w usłudze Azure Active Directory (Azure AD).

Po zintegrowaniu z usługą Azure AD SAP Business obiektu chmury można uzyskać następujące korzyści:

- W usłudze Azure AD można kontrolować, kto ma dostęp do programu SAP Business obiektu chmury.
- Użytkownikom programu SAP Business obiektu chmury mogą automatycznie zaloguj przy użyciu rejestracji jednokrotnej i konta usługi Azure AD.
- Możesz zarządzać kont w jednej, centralnej lokalizacji, portalu Azure.

Aby dowiedzieć się więcej na temat oprogramowania jako usługa (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SAP Business obiektu chmury, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Chmura obiektu SAP Business, z logowanie jednokrotne włączone

> [!NOTE]
> W przypadku testowania czynności w tym samouczku, zaleca się nie przetestować ich w środowisku produkcyjnym.

Zalecenia dotyczące testowania czynności w tym samouczku:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [Pobierz bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodaj SAP Business obiektu chmury z galerii.
2. Instalowanie i testowanie usługi Azure AD rejestracji jednokrotnej.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Dodaj SAP Business obiektu chmury z galerii
Aby skonfigurować integrację SAP Business obiektu chmury z usługą Azure AD w galerii, należy dodać SAP Business obiektu chmury do listy zarządzanych aplikacji SaaS.

Aby dodać SAP Business obiektu chmury z galerii:

1. W [portalu Azure](https://portal.azure.com), w menu po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Strona aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji**.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wprowadź **SAP Business obiektu chmury**.

    ![Pole wyszukiwania](./media/sapboc-tutorial/tutorial_sapboc_search.png)

5. W panelu wyników wybierz **SAP Business obiektu chmury**, a następnie wybierz **Dodaj**.

    ![SAP Business obiektu chmury na liście wyników](./media/sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Instalowanie i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz zdefiniować i test usługi Azure AD rejestracji jednokrotnej z SAP Business obiektu chmury w oparciu o nazwie użytkownika testowego *Simona Britta*.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi znać użytkownika odpowiednikiem usługi Azure AD w chmurze programu SAP Business obiektu. Należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w chmurze programu SAP Business obiektu.

Ustanowienie relacji link, w chmurze obiektu biznesowych SAP, dla **Username**, przypisz wartość **nazwy użytkownika** w usłudze Azure AD.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SAP Business obiektu chmury, należy wykonać następujące zadania:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#set-up-azure-ad-single-sign-on). Ustawia użytkownika, aby użyć tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user). Testy usługi Azure AD rejestracji jednokrotnej z użytkownikiem Simona Britta.
3. [Tworzenie użytkownika testowego SAP Business obiektu chmury](#create-an-sap-business-object-cloud-test-user). Tworzy odpowiednikiem Simona Britta SAP Business obiektu chmury połączonego z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user). Konfiguruje Simona Britta przy użyciu usługi Azure AD logowanie jednokrotne.
5. [Test rejestracji jednokrotnej](#test-single-sign-on). Sprawdza, czy konfiguracja działa.

### <a name="set-up-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz włączyć usługi Azure AD pojedynczego logowania w portalu Azure. Następnie skonfigurowaniu rejestracji jednokrotnej w aplikacji SAP Business obiektu chmury.

Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SAP Business obiektu chmury:

1. W portalu Azure na **SAP Business obiektu chmury** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Wybierz rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** strony, dla **tryb**, wybierz pozycję **na języku SAML logowania jednokrotnego**.
 
    ![Wybierz na języku SAML logowania jednokrotnego](./media/sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. W obszarze **SAP Business obiektu chmury domeny i adres URL**, wykonaj następujące czynności:

    1. W **adres URL logowania** wprowadź adres URL, który ma następujący wzór: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. W **identyfikator** wprowadź adres URL, który ma następujący wzór:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![Adresy URL i SAP Business obiektu chmury domeny adresów URL stron](./media/sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Wartości te adresy URL są do pokazania tylko. Zaktualizuj wartości z rzeczywisty adres URL logowania i adres URL identyfikatora. Aby uzyskać adres URL logowania, skontaktuj się z [zespołem pomocy technicznej SAP Business obiektu chmury klienta](https://www.sap.com/product/analytics/cloud-analytics.support.html). Adres URL identyfikatora można uzyskać, pobierając metadanych SAP Business obiektu chmury z poziomu konsoli administracyjnej. Wyjaśnienie jest zawarte w dalszej części tego samouczka. 

4. W obszarze **certyfikat podpisywania SAML**, wybierz pozycję **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Wybierz XML metadanych](./media/sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Wybierz opcję Zapisz](./media/sapboc-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy SAP Business obiektu chmury.

7. Wybierz **Menu** > **systemu** > **administracji**.
    
    ![Wybierz Menu, a następnie systemu, a następnie administracji](./media/sapboc-tutorial/config1.png)

8. Na **zabezpieczeń** wybierz opcję **Edytuj** ikona (pióra).
    
    ![Na karcie Zabezpieczenia wybierz ikonę edycji](./media/sapboc-tutorial/config2.png)  

9. Aby uzyskać **metodę uwierzytelniania**, wybierz pozycję **SAML pojedynczego logowania jednokrotnego (SSO)**.

    ![Wybierz SAML logowania jednokrotnego dla metody uwierzytelniania](./media/sapboc-tutorial/config3.png)  

10. Aby pobrać metadane dostawcy usługi (krok 1), wybierz **Pobierz**. W pliku metadanych, należy znaleźć i skopiować **entityID** wartości. W portalu Azure w obszarze **adresy URL i SAP Business obiektu chmury domeny**, Wklej wartość **identyfikator** pole.

    ![Skopiuj i Wklej wartość identyfikator jednostki](./media/sapboc-tutorial/config4.png)  

11. Aby przekazać metadane dostawcy usługi (krok 2) w pliku, który został pobrany z portalu Azure w obszarze **metadanych Przekaż dostawcy tożsamości**, wybierz pozycję **przekazać**.  

    ![W obszarze metadanych Przekaż dostawcy tożsamości wybierz przekazywania](./media/sapboc-tutorial/config5.png)

12. W **atrybut użytkownika** wybierz atrybut użytkownika (krok 3), który ma być używany dla implementacji. Ten atrybut użytkownika mapuje dostawcy tożsamości. Aby wprowadzić atrybutów niestandardowych na stronie użytkownika, należy użyć **niestandardowe mapowanie SAML** opcji. Alternatywnie można wybrać **poczty E-mail** lub **identyfikator użytkownika** jako atrybut użytkownika. W naszym przykładzie wybrano **E-mail** możemy mapowane oświadczenia identyfikatora użytkownika z **userprincipalname** atrybutu w **atrybuty użytkownika** części platformy Azure Portal. Zapewnia to adres e-mail użytkownika unikatowy, który jest wysyłany do aplikacji SAP Business obiektu chmury w każdym pomyślnym odpowiedzi SAML.

    ![Wybierz atrybut użytkownika](./media/sapboc-tutorial/config6.png)

13. Aby zweryfikować konto z dostawcy tożsamości (krok 4) w **poświadczeń logowania (Poczta E-mail)** wprowadź adres e-mail użytkownika. Następnie wybierz opcję **Sprawdź konto**. System dodaje poświadczenia logowania do konta użytkownika.

    ![Wprowadź adres e-mail, a następnie wybierz zweryfikować konta](./media/sapboc-tutorial/config7.png)

14. Wybierz **zapisać** ikony.

    ![Zapisz](./media/sapboc-tutorial/save.png)

> [!TIP]
> Możesz przeczytać zwięzły wersji tych instrukcji w [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji! Po dodaniu aplikacji przez wybranie **usługi Active Directory** > **aplikacje dla przedsiębiorstw**, wybierz pozycję **rejestracji jednokrotnej** kartę. Są dostępne w dokumentacji osadzony w **konfiguracji** sekcji w dolnej części strony. Aby uzyskać więcej informacji, zobacz [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji utworzysz użytkownika testu o nazwie Simona Britta w portalu Azure.

Aby utworzyć użytkownika testowego w usłudze Azure AD:

1. W portalu Azure, w menu po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sapboc-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, wybierz **użytkowników i grup**, a następnie wybierz **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sapboc-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj**.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sapboc-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:
 
    1. W **nazwa** wprowadź **BrittaSimon**.

    2. W **nazwy użytkownika** wprowadź adres e-mail użytkownika Simona Britta.

    3. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    4. Wybierz pozycję **Utwórz**.

        ![Okno dialogowe użytkownika](./media/sapboc-tutorial/create_aaduser_04.png) 

    ![Tworzenie użytkowników usługi Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Tworzenie użytkownika testowego SAP Business obiektu chmury

Użytkownicy usługi Azure AD należy udostępnić w chmurze programu SAP Business obiektu, aby móc zalogować się do programu SAP Business obiektu chmury. W chmurze obiektu biznesowych SAP Inicjowanie obsługi to zadanie ręczne.

Aby udostępnić konta użytkownika:

1. Zaloguj się do witryny firmy SAP Business obiektu chmury jako administrator.

2. Wybierz **Menu** > **zabezpieczeń** > **użytkowników**.

    ![Dodawanie pracownika](./media/sapboc-tutorial/user1.png)

3. Na **użytkowników** , aby dodać nowe szczegóły użytkownika, wybierz **+**. 

    ![Strona dodawania użytkowników](./media/sapboc-tutorial/user4.png)

    Następnie należy wykonać następujące czynności:

    1. W **identyfikator użytkownika** wprowadź identyfikator użytkownika, nazwa użytkownika, takie jak **Britta**.

    2. W **imię** Wprowadź imię użytkownika, takie jak **Britta**.

    3. W **nazwisko** wprowadź nazwisko użytkownika, takie jak **Simona**.

    4. W **nazwa WYŚWIETLANA** wpisz pełną nazwę użytkownika, takie jak **Simona Britta**.

    5. W **E-MAIL** wprowadź adres e-mail użytkownika, takie jak **brittasimon@contoso.com**.

    6. Na **Wybieranie ról** , wybierz odpowiednią rolę dla użytkownika, a następnie wybierz **OK**.

      ![Wybór roli](./media/sapboc-tutorial/user3.png)

    7. Wybierz **zapisać** ikony.    


### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz zezwolić na użytkownika Simona Britta na przyznanie dostępu kontu użytkownika SAP Business obiektu chmury za pomocą usługi Azure AD rejestracji jednokrotnej.

Aby przypisać Simona Britta SAP Business obiektu chmury:

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SAP Business obiektu chmury**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/sapboc-tutorial/tutorial_sapboc_app.png) 

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Wybierz użytkowników i grup][202] 

4. Wybierz pozycję **Dodaj**. Następnie na **Dodaj przydziału** wybierz pozycję **użytkowników i grup**.

    ![Strona Dodawanie przypisania][203]

5. Na **użytkowników i grup** strony, na liście użytkowników, wybierz opcję **Simona Britta**.

6. Na **użytkowników i grup** wybierz pozycję **wybierz**.

7. Na **Dodaj przydziału** wybierz pozycję **przypisać**.

![Przypisanie roli użytkownika][200] 
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka SAP Business obiektu chmury w panelu dostępu należy powinny być automatycznie zalogowany do aplikacji SAP Business obiektu chmury.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/sapboc-tutorial/tutorial_general_01.png
[2]: ./media/sapboc-tutorial/tutorial_general_02.png
[3]: ./media/sapboc-tutorial/tutorial_general_03.png
[4]: ./media/sapboc-tutorial/tutorial_general_04.png

[100]: ./media/sapboc-tutorial/tutorial_general_100.png

[200]: ./media/sapboc-tutorial/tutorial_general_200.png
[201]: ./media/sapboc-tutorial/tutorial_general_201.png
[202]: ./media/sapboc-tutorial/tutorial_general_202.png
[203]: ./media/sapboc-tutorial/tutorial_general_203.png

