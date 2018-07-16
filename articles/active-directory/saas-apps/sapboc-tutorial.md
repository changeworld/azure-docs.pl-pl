---
title: 'Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem SAP Business obiektu Cloud | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i obiektu chmury dla oprogramowania SAP Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: ffd4480a13549caba17becff27a43f51fcaa1988
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041742"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem SAP Cloud obiektu biznesowych

W tym samouczku dowiesz się, jak zintegrować SAP Business obiektu chmury za pomocą usługi Azure Active Directory (Azure AD).

Uzyskasz następujące korzyści, w ramach obiektu chmury dla oprogramowania SAP Business integracji z usługą Azure AD:

- W usłudze Azure AD można kontrolować, kto ma dostęp do obiektu chmury dla oprogramowania SAP Business.
- Użytkownicy do obiektu chmury dla oprogramowania SAP Business może automatycznie podpisywać za pomocą logowania jednokrotnego i konta usługi Azure AD.
- Możesz zarządzać konta w jednej, centralnej lokalizacji, witryny Azure portal.

Aby dowiedzieć się więcej na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD za pomocą obiektu chmury dla oprogramowania SAP Business, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SAP Business obiektu chmury, za pomocą logowania jednokrotnego włączona

> [!NOTE]
> Jeśli testujesz kroki opisane w tym samouczku firma Microsoft zaleca, że nie Testuj je w środowisku produkcyjnym.

Zalecenia dotyczące testowania kroki opisane w tym samouczku:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj obiekt chmury dla oprogramowania SAP Business z galerii.
2. Zainstaluj i przetestuj usługę Azure AD logowania jednokrotnego.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Dodaj obiekt chmury dla oprogramowania SAP Business z galerii
Aby skonfigurować integrację programu SAP Business obiektów chmury z usługą Azure AD w galerii, Dodaj obiekt chmury dla oprogramowania SAP Business z listą zarządzanych aplikacji SaaS.

Aby dodać obiekt chmury dla oprogramowania SAP Business z galerii:

1. W [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Strony aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację**.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wprowadź **obiektu chmury dla oprogramowania SAP Business**.

    ![Pole wyszukiwania](./media/sapboc-tutorial/tutorial_sapboc_search.png)

5. W panelu wyników wybierz **obiektu chmury dla oprogramowania SAP Business**, a następnie wybierz pozycję **Dodaj**.

    ![Chmura SAP Business obiektu, na liście wyników](./media/sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Instalowanie i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować i test usługi Azure AD logowanie jednokrotne za pomocą obiektu chmury dla oprogramowania SAP Business w oparciu o nazwie użytkownika testowego *Britta Simon*.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika usługi Azure AD w chmurze obiektu firmy SAP. Należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w chmurze obiektu firmy SAP.

Ustanowienie relacji łączy, w chmurze obiektu firmy SAP, dla **Username**, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z rozwiązaniem SAP Cloud obiektu biznesowych, należy wykonać następujące zadania:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#set-up-azure-ad-single-sign-on). Konfiguruje użytkownika, aby użyć tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user). Testy usługi Azure AD logowanie jednokrotne z użytkownikiem Britta Simon.
3. [Tworzenie użytkownika testowego obiektu chmury dla oprogramowania SAP Business](#create-an-sap-business-object-cloud-test-user). Tworzy odpowiednikiem Britta Simon w chmurze obiektu firmy SAP, połączonego z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user). Skonfigurowanie Britta Simon używać usługi Azure AD logowanie jednokrotne.
5. [Testowanie logowania jednokrotnego](#test-single-sign-on). Sprawdza, czy konfiguracja działa.

### <a name="set-up-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć pojedynczej usługi Azure AD, logowania jednokrotnego w witrynie Azure portal. Następnie w skonfigurowaniu logowania jednokrotnego w aplikacji w chmurze obiektu firmy SAP.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z rozwiązaniem SAP Cloud obiektu biznesowych:

1. W witrynie Azure portal na **obiektu chmury dla oprogramowania SAP Business** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Wybierz opcję logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** strony dla **tryb**, wybierz opcję **opartej na SAML logowania jednokrotnego**.
 
    ![Wybierz opartej na SAML logowania jednokrotnego](./media/sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. W obszarze **SAP Business obiektu chmury domena i adresy URL**, wykonaj następujące czynności:

    1. W **adres URL logowania** wprowadź adres URL z następującym wzorcem: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. W **identyfikator** wprowadź adres URL z następującym wzorcem:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![SAP Business obiektu chmury domena i adresy URL adresów URL](./media/sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Wartości w tych adresów URL są tylko demonstracyjne. Zaktualizuj wartości z rzeczywistych adres URL logowania i adres URL identyfikatora. Aby uzyskać adres URL logowania, skontaktuj się z pomocą [zespołem pomocy technicznej SAP Business obiektu Cloud Client](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/en-US). Adres URL identyfikatora można uzyskać, pobierając metadane obiektu chmury dla oprogramowania SAP Business z poziomu konsoli administracyjnej. Jest to wyjaśnione w dalszej części tego samouczka. 

4. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Wybierz kod XML metadanych](./media/sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Wybierz opcję Zapisz](./media/sapboc-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki internetowej innej należy zalogować się jako administrator do witryny firmy SAP Business obiektu chmury.

7. Wybierz **Menu** > **systemu** > **administracji**.
    
    ![Wybierz opcję Menu, a następnie systemu i Administracja](./media/sapboc-tutorial/config1.png)

8. Na **zabezpieczeń** zaznacz **Edytuj** ikonę (pióra).
    
    ![Na karcie Zabezpieczenia wybierz ikonę edycji](./media/sapboc-tutorial/config2.png)  

9. Aby uzyskać **metodę uwierzytelniania**, wybierz opcję **SAML pojedynczego logowania jednokrotnego (SSO)**.

    ![Wybierz metodę uwierzytelniania SAML logowania jednokrotnego](./media/sapboc-tutorial/config3.png)  

10. Aby pobrać metadane dostawcy usługi (krok 1), zaznacz **Pobierz**. W pliku metadanych, Znajdź i skopiuj **entityID** wartości. W witrynie Azure portal w obszarze **SAP Business obiektu chmury domena i adresy URL**, Wklej wartość w **identyfikator** pole.

    ![Skopiuj i Wklej wartość entityID](./media/sapboc-tutorial/config4.png)  

11. Można przekazać metadane dostawcy usługi (krok 2) w pliku, który został pobrany z witryny Azure portal w obszarze **metadanych przekazywanie dostawcy tożsamości**, wybierz opcję **przekazywanie**.  

    ![W obszarze metadane przekazywanie dostawcy tożsamości Wybieranie pozycji Przekaż](./media/sapboc-tutorial/config5.png)

12. W **atrybutu użytkownika** wybierz atrybut użytkownika (krok 3), który chcesz użyć dla wdrożenia. Ten atrybut użytkownika mapuje do dostawcy tożsamości. Aby wprowadzić niestandardowy atrybut, na stronie użytkownika, należy użyć **mapowania niestandardowej SAML** opcji. Lub możesz wybrać opcję **E-mail** lub **identyfikator użytkownika** jako atrybut użytkownika. W tym przykładzie Wybraliśmy **E-mail** ponieważ możemy zamapowane oświadczenia identyfikatora użytkownika z **userprincipalname** atrybutu w **atrybutów użytkownika** sekcję na platformie Azure Portal. Dzięki temu wiadomości e-mail unikatowych użytkowników, który jest wysyłany do aplikacji w chmurze obiektu firmy SAP w każdej pomyślnej odpowiedzi SAML.

    ![Wybierz atrybut użytkownika](./media/sapboc-tutorial/config6.png)

13. Aby zweryfikować konto za pomocą dostawcy tożsamości (krok 4) w **poświadczenia danych logowania (adres E-mail)** wprowadź adres e-mail użytkownika. Następnie wybierz **Sprawdź konto**. System dodaje poświadczenia logowania do konta użytkownika.

    ![Wprowadź adres e-mail, a następnie wybierz pozycję Sprawdź konto](./media/sapboc-tutorial/config7.png)

14. Wybierz **Zapisz** ikony.

    ![Ikona zapisu](./media/sapboc-tutorial/save.png)

> [!TIP]
> Może odczytywać zwięzłe wersji tych instrukcji w [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji! Po dodaniu aplikacji, wybierając **usługi Active Directory** > **aplikacje dla przedsiębiorstw**, wybierz opcję **logowania jednokrotnego** kartę. Możesz uzyskać dostęp z dokumentacją osadzone w **konfiguracji** sekcji w dolnej części strony. Aby uzyskać więcej informacji, zobacz [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji utworzysz użytkownika testu o nazwie Britta Simon w witrynie Azure portal.

Aby utworzyć użytkownika testowego w usłudze Azure AD:

1. W witrynie Azure portal w menu po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sapboc-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, wybierz **użytkowników i grup**, a następnie wybierz pozycję **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sapboc-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj**.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sapboc-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    1. W **nazwa** wprowadź **BrittaSimon**.

    2. W **nazwa_użytkownika** wprowadź adres e-mail użytkownika Britta Simon.

    3. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    4. Wybierz pozycję **Utwórz**.

        ![Okno dialogowe użytkownika](./media/sapboc-tutorial/create_aaduser_04.png) 

    ![Utwórz użytkownika usługi Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Tworzenie użytkownika testowego obiektu chmury dla oprogramowania SAP Business

Użytkownicy usługi Azure AD musi być obsługiwana w chmurze obiektu firmy SAP, aby zalogować się do obiektu chmury dla oprogramowania SAP Business. W chmurze obiektu firmy SAP Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Aby aprowizować konto użytkownika:

1. Zaloguj się do witryny firmy SAP Business obiektu chmury jako administrator.

2. Wybierz **Menu** > **zabezpieczeń** > **użytkowników**.

    ![Dodawanie pracownika](./media/sapboc-tutorial/user1.png)

3. Na **użytkowników** strony, aby dodać nowe szczegóły użytkownika, wybierz **+**. 

    ![Strona dodawania użytkowników](./media/sapboc-tutorial/user4.png)

    Następnie należy wykonać następujące czynności:

    1. W **identyfikator użytkownika** wprowadź identyfikator użytkownika, takie jak **Britta**.

    2. W **imię** Wprowadź imię użytkownika, takie jak **Britta**.

    3. W **nazwisko** wprowadź nazwisko użytkownika, takie jak **Simon**.

    4. W **nazwę WYŚWIETLANĄ** wprowadź pełną nazwę użytkownika, takie jak **Britta Simon**.

    5. W **E-MAIL** wprowadź adres e-mail użytkownika, takie jak **brittasimon@contoso.com**.

    6. Na **Wybieranie ról** strony, wybierz odpowiednią rolę dla użytkownika, a następnie wybierz pozycję **OK**.

      ![Wybór roli](./media/sapboc-tutorial/user3.png)

    7. Wybierz **Zapisz** ikony.    


### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz zezwolić użytkownikowi Britta Simon, aby korzystać z usługi Azure AD logowania jednokrotnego przez przyznanie dostępu kontu użytkownika do obiektu chmury dla oprogramowania SAP Business.

Aby przypisać Britta Simon do obiektu chmury dla oprogramowania SAP Business:

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **obiektu chmury dla oprogramowania SAP Business**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapboc-tutorial/tutorial_sapboc_app.png) 

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Wybierz użytkowników i grupy][202] 

4. Wybierz pozycję **Dodaj**. Następnie na **Dodaj przydziału** wybierz opcję **użytkowników i grup**.

    ![Na stronie Dodawanie przypisania][203]

5. Na **użytkowników i grup** strony, z listy użytkowników, wybierz opcję **Britta Simon**.

6. Na **użytkowników i grup** wybierz opcję **wybierz**.

7. Na **Dodaj przydziału** wybierz opcję **przypisać**.

![Przypisanie roli użytkownika][200] 
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka SAP Business obiektu chmury w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze aplikacji w chmurze obiektu firmy SAP.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
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

