---
title: 'Samouczek: Konfigurowanie produktu Workday do obsługi użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie użycia produktu Workday jako źródło danych tożsamości dla usługi Active Directory i Azure Active Directory.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2018
ms.author: asmalser
ms.openlocfilehash: 5c2c39db7ab89b06915c014778977915cca15190
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie produktu Workday do inicjowania obsługi użytkowników

Celem tego samouczka jest opisano czynności, które należy wykonać, aby zaimportować osób z produktu Workday do usługi Active Directory i Azure Active Directory, z opcjonalne zapisu pewnych atrybutów do produktu Workday. 



## <a name="overview"></a>Przegląd

[Użytkownika usługi Azure Active Directory świadczenie usługi](active-directory-saas-app-provisioning.md) integruje się z [produktu Workday kadr API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) w celu obsługi administracyjnej kont użytkowników. Usługi Azure AD używa tego połączenia, aby umożliwić następujące przepływów pracy przypisywania użytkowników:

* **Inicjowanie obsługi użytkowników do usługi Active Directory** -synchronizowanie wybranego zestawu użytkowników z produktu Workday w co najmniej jeden lasów usługi Active Directory. 

* **Inicjowanie obsługi administracyjnej tylko na chmurze użytkowników do usługi Azure Active Directory** -hybrydowego użytkowników, którzy istnieje w usłudze Active Directory i Azure Active Directory można udostępnić w ostatnim przy użyciu [AAD Connect](connect/active-directory-aadconnect.md). Jednak użytkownicy, którzy są tylko w chmurze można udostępnić bezpośrednio z produktu Workday do usługi Azure Active Directory przy użyciu użytkownika usługi Azure AD, inicjowania obsługi usługi.

* **Zapisywanie zwrotne e-mail adresów do produktu Workday** -użytkownika usługi Azure AD, świadczenie usługi można zapisu wybrane atrybuty użytkownika usługi Azure AD do pracy, takie jak adres e-mail.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jakie scenariuszy zasobów ludzkich obejmuje on?

Dzień roboczy użytkownika inicjowania obsługi administracyjnej przepływów pracy obsługiwanych przez usługę inicjowania obsługi administracyjnej użytkowników usługi Azure AD automatyzować następujących zasobów ludzkich i scenariusze zarządzania cyklem życia tożsamości:

* **Nowi pracownicy wynajem** — po dodaniu nowych pracowników do produktu Workday, konto użytkownika zostanie automatycznie utworzone w usłudze Active Directory, usługi Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](active-directory-saas-app-provisioning.md), z zapisu adres e-mail z produktu Workday.

* **Aktualizacje atrybutu i profilu pracownika** — po rekordu pracownika jest aktualizowany w pracy (takie jak ich nazwy, tytuł lub manager), ich konta użytkownika zostanie automatycznie zaktualizowana w usłudze Active Directory, usługi Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](active-directory-saas-app-provisioning.md).

* **Liczba przerwanych pracownika** — gdy pracownik zostaje zakończone w pracy, ich konta użytkownika jest automatycznie wyłączana w usłudze Active Directory, usługi Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwany przez platformę Azure AD](active-directory-saas-app-provisioning.md).

* **Pracownik ponownie wynajmuje** — gdy pracownik jest rehired w pracy, ich stare konto może być automatycznie ponownie uaktywnić lub ponownie zainicjowano obsługę administracyjną (w zależności od swoich preferencji) do usługi Active Directory, Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](active-directory-saas-app-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Kto jest rozwiązanie inicjowania obsługi administracyjnej tego użytkownika najlepiej nadaje się do?

Ten użytkownik produktu Workday inicjowania obsługi administracyjnej rozwiązania jest obecnie w wersji zapoznawczej i doskonale nadaje się do:

* Organizacje, które konieczna jest wstępnie przygotowanych, oparte na chmurze rozwiązanie do produktu Workday Inicjowanie obsługi użytkowników

* Organizacje, które wymagają bezpośredniego Inicjowanie obsługi użytkowników z produktu Workday do usługi Active Directory lub Azure Active Directory

* Organizacje, które wymagają użytkownikom udostępniane przy użyciu danych uzyskanych z produktu Workday HCM modułu (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)) 

* Organizacje wymagają dołączenie, przenoszenie, i pozostawienie użytkownikom synchronizowane z jednego lub więcej lasy usługi Active Directory, domeny i jednostki organizacyjne, tylko na podstawie zmienić informacje użytkownika w module HCM produktu Workday (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacji do obsługi poczty e-mail przy użyciu usługi Office 365


## <a name="planning-your-solution"></a>Planowanie rozwiązania

Przed rozpoczęciem integracji z produktem Workday, sprawdź poniższe wymagania wstępne, a następnie przeczytaj następujące wskazówki na temat sposobu zgodny z bieżącym architektury usługi Active Directory i przypisywania wymagania z solution(s) udostępniane przez usługi Azure Active Directory użytkowników.

### <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

* Prawidłową subskrypcję z uprawnieniami administratora globalnego usługi Azure AD Premium P1
* Dzierżawca implementacji produktu Workday do celów testowania i integracji
* Uprawnienia administratora w pracy można utworzyć użytkownika integracji systemu i wprowadzać zmiany do przetestowania danych pracowników do celów testowych
* Inicjowanie obsługi użytkowników do usługi Active Directory, aby uzyskać przyłączonych do domeny serwera z systemem usługi systemu Windows 2012 lub nowszego jest wymagany do hosta [agent synchronizacji lokalnej](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](connect/active-directory-aadconnect.md) synchronizacji od usługi Active Directory i Azure AD

### <a name="solution-architecture"></a>Architektura rozwiązania

Usługa Azure AD zapewnia bogaty zestaw inicjowania obsługi administracyjnej łączników, aby pomóc w określeniu tożsamości i udostępniania Zarządzanie cyklem życia z produktu Workday do usługi Active Directory, usługi Azure AD, aplikacji SaaS i późniejsze. Funkcje użytkownik użyje i sposób konfigurowania rozwiązania będą się różnić w zależności od środowiska i wymagań Twojej organizacji. Pierwszym krokiem wykonać zapasów, o ile poniżej są obecne i wdrożony w organizacji:

* Ile lasy usługi Active Directory są używane?
* Jak wiele domen usługi Active Directory są używane?
* Ile Active Directory jednostek organizacyjnych (OU) są używane?
* Jak wiele dzierżaw usługi Azure Active Directory są używane?
* Istnieją użytkownicy, którzy muszą być przygotowana do usługi Active Directory i Azure Active Directory (np. "hybrydowe" użytkowników)?
* Istnieją użytkownicy, którzy muszą być przygotowana do usługi Azure Active Directory, ale nie Active Directory (np. "tylko w chmurze" użytkowników)?
* Czy adresu e-mail użytkownika muszą być zapisywane z powrotem do produktu Workday?


Po uzyskaniu odpowiedzi na te pytania, możesz zaplanować pracy inicjowania obsługi wdrożenia, wykonując poniższe wskazówki.

#### <a name="using-provisioning-connector-apps"></a>Za pomocą aplikacji łącznika inicjowania obsługi administracyjnej

Azure Active Directory obsługuje wstępnie zintegrowanych łączników inicjowania obsługi administracyjnej dla produktu Workday i wiele innych aplikacji SaaS. 

Pojedynczy łącznik inicjowania obsługi administracyjnej interfejsy przy użyciu interfejsu API systemu jednego źródła i pomaga dane udostępniania system pojedynczym elementem docelowym. Większość inicjowania obsługi administracyjnej łączniki, które obsługuje usługę Azure AD dla pojedynczego systemu źródłowego i docelowego (np. Azure AD do usługi ServiceNow) i można skonfigurować przez dodanie aplikacji w galerii aplikacji usługi Azure AD (np. usługi ServiceNow). 

Jest relacją między inicjowania obsługi administracyjnej wystąpienia łącznika i wystąpień aplikacji w usłudze Azure AD:

| Systemu źródłowego | System docelowy |
| ---------- | ---------- | 
| Dzierżawy usługi Azure AD | Aplikacja SaaS |


Jednak podczas pracy z produktu Workday i usługi Active Directory, istnieje wiele systemów źródłowych i docelowych wziąć pod uwagę:

| Systemu źródłowego | System docelowy | Uwagi |
| ---------- | ---------- | ---------- |
| Dzień roboczy | Las usługi Active Directory | Każdy las jest traktowany jako system docelowy różne |
| Dzień roboczy | Dzierżawy usługi Azure AD | Co jest wymagane dla użytkowników tylko w chmurze |
| Las usługi Active Directory | Dzierżawy usługi Azure AD | Ten przepływ jest obecnie obsługiwany przez AAD Connect |
| Dzierżawy usługi Azure AD | Dzień roboczy | Dla zapisu zwrotnego adresów e-mail |

W celu ułatwienia tych wiele przepływów pracy w wielu systemach źródłowych i docelowych, usługa Azure AD zapewnia wielu inicjowania obsługi administracyjnej aplikacji łącznika, które można dodać z galerii aplikacji Azure AD:

![Galerii aplikacji usługi AAD](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **WORKDAY do udostępniania usługi Active Directory** -ta aplikacja ułatwia konta Inicjowanie obsługi użytkowników z produktu Workday do jednego lasu usługi Active Directory. Jeśli masz wiele lasów, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji Azure AD dla każdego lasu usługi Active Directory, które należy udostępnić do.

* **WORKDAY do usługi Azure AD inicjowania obsługi administracyjnej** — gdy AAD Connect to narzędzie, które mają być używane do synchronizacji usługi Active Directory użytkowników do usługi Azure Active Directory, ta aplikacja może służyć do ułatwienia obsługi tylko w chmurze użytkowników z produktu Workday do pojedynczego Azure Dzierżawy usługi Active Directory.

* **Zapisywanie zwrotne WORKDAY** -ta aplikacja umożliwia zapisywanie zwrotne adresu e-mail użytkownika z usługi Azure Active Directory do produktu Workday.

> [!TIP]
> Regularne aplikacji "Produktu Workday" służy do konfigurowania rejestracji jednokrotnej między produktu Workday i Azure Active Directory. 

Sposób instalowania i konfigurowania tych specjalnych inicjowania obsługi administracyjnej aplikacji łącznika jest przedmiotem pozostałej części tego samouczka. Aplikacje, które użytkownik chce skonfigurować zależy to dzierżawców systemach, w których należy udostępnić, ile lasy usługi Active Directory i Azure AD w środowisku.

![Przegląd](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Skonfiguruj użytkownika integracji systemu w pracy
Typowe wymagania wszystkich łączników inicjowania obsługi administracyjnej produktu Workday jest wymagają podania poświadczeń dla konta integracji systemu produktu Workday do nawiązania połączenia interfejsu API kadr produktu Workday. Ta sekcja zawiera opis sposobu tworzenia konta integrator systemu w pracy.

> [!NOTE]
> Istnieje możliwość pominąć tę procedurę i zamiast tego użyć konta administratora globalnego produktu Workday jako konta integracji systemu. Może działać prawidłowo w przypadku pokazów, ale nie jest zalecane w przypadku wdrożeń produkcyjnych.

### <a name="create-an-integration-system-user"></a>Utwórz użytkownika integracji systemu

**Aby utworzyć użytkownika system integracji:**

1. Zaloguj się do konta administratora dzierżawy produktu Workday. W **Workbench produktu Workday**, wprowadź użytkownika w polu wyszukiwania, a następnie kliknij polecenie **Tworzenie użytkownika System integracji**. 
   
    ![Utwórz użytkownika](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "tworzenia użytkownika")
2. Zakończenie **Tworzenie użytkownika System integracji** zadanie, podając nazwę użytkownika i hasło dla nowego użytkownika System integracji.  
 * Pozostaw **wymagają nowe hasło przy następnej znak** opcja nie jest zaznaczona, ponieważ ten użytkownik będzie logować się programowo. 
 * Pozostaw **minut limitu czasu sesji** z jego wartość domyślna 0, która uniemożliwi sesji użytkownika przedwcześnie limit czasu. 
   
    ![Utwórz użytkownika System integracji](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "utworzyć integracji systemu użytkownika")

### <a name="create-a-security-group"></a>Utwórz grupę zabezpieczeń
Należy utworzyć grupę zabezpieczeń systemu nieograniczonego integracji i przypisz użytkownika do niego.

**Aby utworzyć grupę zabezpieczeń:**

1. Wprowadź Utwórz grupę zabezpieczeń w polu wyszukiwania, a następnie kliknij przycisk **Utwórz grupę zabezpieczeń**. 
   
    ![Grupa CreateSecurity](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity grupy")
2. Zakończenie **Utwórz grupę zabezpieczeń** zadań.  
3. Wybierz **integracji systemu zabezpieczeń grupy (nieograniczone)** z **typ grupy zabezpieczeń dzierżawcza** listy rozwijanej.
4. Utwórz grupę zabezpieczeń, do których członkowie zostaną jawnie dodani. 
   
    ![Grupa CreateSecurity](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity grupy")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Przypisz użytkownika do integracji systemu do grupy zabezpieczeń

**Aby przypisać użytkownika systemu integracji:**

1. Edytuj grupę zabezpieczeń należy wprowadzić w polu wyszukiwania, a następnie kliknij przycisk **Edytuj grupę zabezpieczeń**. 
   
    ![Edytuj grupę zabezpieczeń](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Edytuj grupę zabezpieczeń")
2. Wyszukaj i wybierz nową grupę zabezpieczeń integracji według nazwy. 
   
    ![Edytuj grupę zabezpieczeń](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Edytuj grupę zabezpieczeń")
3. Dodaj nowego użytkownika system integracji do nowej grupy zabezpieczeń. 
   
    ![Grupa zabezpieczeń systemu](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "grupa zabezpieczeń systemu")  

### <a name="configure-security-group-options"></a>Skonfiguruj opcje grupy zabezpieczeń
W tym kroku zostanie Przyznaj zabezpieczeń domeny uprawnienia zasady dla danych procesu roboczego do grupy zabezpieczeń.

**Aby skonfigurować opcje grupy zabezpieczeń:**

1. Wprowadź **zasady zabezpieczeń domeny** w polu wyszukiwania, a następnie kliknij łącze **zasady zabezpieczeń domeny dla Obszar funkcjonalny**.  
   
    ![Zasady zabezpieczeń domeny](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "zasady zabezpieczeń domeny")  
2. Wyszukaj systemu i wybierz **systemu** Obszar funkcjonalny.  Kliknij przycisk **OK**.  
   
    ![Zasady zabezpieczeń domeny](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "zasady zabezpieczeń domeny")  
3. Na liście zasad zabezpieczeń dla Obszar funkcjonalny systemu, rozwiń węzeł **administrowania zabezpieczeniami** i wybierz zasady zabezpieczeń domeny **zewnętrznych Inicjowanie obsługi konta**.  
   
    ![Zasady zabezpieczeń domeny](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "zasady zabezpieczeń domeny")  
4. Kliknij przycisk **Edytuj uprawnienia**, a następnie na **Edytuj uprawnienia** strony okna dialogowego Dodaj nową grupę zabezpieczeń do listy grup zabezpieczeń z **uzyskać** i **Put**  uprawnienia integracji. 
   
    ![Edytuj uprawnienia](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "uprawnienia do edycji")  
    
5. Powtórz kroki 1 – 4 powyżej dla każdej z pozostałych zasady zabezpieczeń:

| Operacja | Zasady zabezpieczeń domeny |
| ---------- | ---------- | 
| GET i Put | Danych procesu roboczego: Raporty publicznego procesu roboczego |
| GET i Put | Danych procesu roboczego: Informacje kontaktowe pracy |
| Get | Danych procesu roboczego: Wszystkie pozycje |
| Get | Danych procesu roboczego: Personel informacje o bieżącej |
| Get | Danych procesu roboczego: Tytuł firm proces roboczy w profilu |
   
    
### <a name="activate-security-policy-changes"></a>Aktywuj zmian zasad zabezpieczeń

**Aby aktywować zmian zasad zabezpieczeń:**

1. Wprowadź aktywować w polu wyszukiwania, a następnie kliknij łącze **aktywować oczekujących zmian zasad zabezpieczeń**. 
   
    ![Aktywuj](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "aktywacji") 
2. Rozpoczęcia zadania aktywować oczekujących zmian zasad zabezpieczeń, wprowadzając komentarz dla inspekcji, a następnie kliknij przycisk **OK**. 
   
    ![Aktywuj oczekujących zabezpieczeń](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "aktywować oczekujących zabezpieczeń")   
3. Ukończenie zadania na następnym ekranie, zaznaczając pole wyboru **Potwierdź**, a następnie kliknij przycisk **OK**. 
   
    ![Aktywuj oczekujących zabezpieczeń](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "aktywować oczekujących zabezpieczeń")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurowanie Inicjowanie obsługi użytkowników z produktu Workday do usługi Active Directory
Wykonaj te instrukcje, aby skonfigurować konto użytkownika, inicjowania obsługi administracyjnej z produktu Workday w każdym lesie usługi Active Directory, wymagające inicjowania obsługi administracyjnej.

### <a name="planning"></a>Planowanie

Przed skonfigurowaniem lesie usługi Active Directory Inicjowanie obsługi użytkowników, należy rozważyć następujące kwestie. Odpowiedzi na te pytania określają sposób mapowania atrybutu i filtry zakresu, na których trzeba ustawić. 

* **Co użytkownicy w pracy muszą być przygotowana do tego lasu usługi Active Directory?**

   * *Przykład: Użytkowników, których atrybut "Firmowe" produktu Workday zawiera wartość "Contoso" i "Regularne" zawiera atrybut "Worker_Type"*

* **Jak użytkownicy są kierowane do jednostki innej organizacji (OU)**

   * *Przykład: Użytkownicy są kierowane do jednostek organizacyjnych, które odpowiadają lokalizacji biura, zgodnie z definicją w produktu Workday "Jednostki administracyjnej" i "Country_Region_Reference" atrybutów*

* **Jak następujące atrybuty powinny zostać wypełnione w usłudze Active Directory?**

   * Nazwa pospolita (cn)
      * *Przykład: Użyj wartości funkcja User_ID produktu Workday, zgodnie z ustawieniami kadr*
      
   * Identyfikator pracownika (identyfikator pracownika)
      * *Przykład: Użyj wartości Worker_ID produktu Workday*
      
   * Nazwa konta SAM (sAMAccountName)
      * *Przykład: Użyj wartości funkcja User_ID produktu Workday, filtrować za pomocą usługi Azure AD inicjowania obsługi administracyjnej wyrażenie Usuń nieprawidłowe znaki*
      
   * Nazwa główna użytkownika (userPrincipalName)
      * *Przykład: Należy użyć wartości funkcja User_ID pracy z usługą Azure AD inicjowania obsługi administracyjnej wyrażenie dołączyć nazwę domeny*

* **Jak użytkownicy powinny być zgodne między produktu Workday i usługi Active Directory?**

  * *Przykład: Użytkownicy z określonym produktem Workday "Worker_ID" wartości są zgodne z użytkowników usługi Active Directory, gdzie "identyfikator pracownika" ma taką samą wartość. Wartość Worker_ID nie zostanie znaleziony w usłudze Active Directory, utworzenie nowego użytkownika.*
  
* **Las usługi Active Directory już zawiera identyfikatory wymagane logiki pasującego do pracy użytkowników?**

  * *Przykład: Jeśli jest to nowe wdrożenie produktu Workday, zdecydowanie zalecane jest czy usługi Active Directory będzie wstępnie wypełniane poprawne wartości Worker_ID produktu Workday (lub unikatowa wartość Identyfikatora wyboru) do zachowania, wystarczy logika dopasowywania.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie inicjowania obsługi administracyjnej aplikacja łącznika i tworzenie połączenia z produktu Workday

**Aby skonfigurować produktu Workday do inicjowania obsługi usługi Active Directory:**

1.  Przejdź do strony <https://portal.azure.com>

2.  Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3.  Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4.  Wybierz **dodać aplikację**i wybierz **wszystkie** kategorii.

5.  Wyszukaj **udostępniania produktu Workday do usługi Active Directory**i Dodaj danej aplikacji w galerii.

6.  Po dodaniu aplikacji i ekran szczegółów aplikacji jest wyświetlane, wybierz pozycję **inicjowania obsługi administracyjnej**

7.  Zmień **inicjowania obsługi administracyjnej** **tryb** do **automatyczne**

8.  Zakończenie **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemu integracji produktu Workday, z dołączoną nazwą domeny dzierżawy. **Powinien wyglądać mniej więcej tak: username@contoso4**

   * **Hasło administratora —** wprowadź hasło do konta produktu Workday integracji systemu

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla dzierżawy. To powinien wyglądać następująco: https://wd3-impl-services1.workday.com/ccx/service/contoso4, gdzie contoso4 jest zastępowany nazwa dzierżawy poprawne i wd3 impl jest zastępowany ciągiem poprawne środowisko.

   * **Lasu usługi Active Directory -** "Name" usługi Active Directory lasu, zwracane przez polecenia powershell Get-ADForest. Zazwyczaj jest to ciąg, takich jak: *contoso.com*

   * **Kontener usługi Active Directory -** wprowadź ciąg kontenera, który zawiera wszystkich użytkowników w lesie usługi AD. Przykład: *OU = użytkowników standardowych, OU = użytkownicy, DC = contoso, DC = test*

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku. Jeśli test połączenia powiedzie się, kliknij przycisk **zapisać** na górze. Jeśli nie powiedzie się, sprawdź, czy poświadczenia produktu Workday są nieprawidłowe w pracy. 

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów 

W tej sekcji skonfigurujesz, jak dane użytkownika wypływających z produktu Workday do usługi Active Directory.

1.  Na karcie inicjowania obsługi administracyjnej w obszarze **mapowania**, kliknij przycisk **zsynchronizować pracowników z produktu Workday do OnPremises**.

2.  W **zakres obiektu źródłowego** pole, można wybrać, które zestawów użytkowników w produktu Workday musi należeć do zakresu na potrzeby inicjowania obsługi administracyjnej AD, definiując zestaw opartych na atrybutach filtrów. Domyślny zakres to "wszyscy użytkownicy w pracy". Przykładowe filtry:

   * Przykład: Zakres dla użytkowników z procesu roboczego identyfikatorów 1000000 i 2000000

      * Atrybut: WorkerID

      * Operator: Dopasowanie wyrażenia REGULARNEGO

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko pracownicy i nie pracowników warunkowe 

      * Atrybut: identyfikator pracownika

      * Operator: Nie ma wartości NULL

3.  W **akcji obiektów docelowych** pole globalnie filtrowania, jakie akcje mogą być wykonywane w usłudze Active Directory. **Utwórz** i **aktualizacji** są najczęściej.

4.  W **mapowania atrybutów** sekcji, można zdefiniować poszczególne produktu Workday atrybuty mapy do atrybutów usługi Active Directory.

5. Kliknij na istniejące mapowanie atrybutu go zaktualizować lub **Dodaj nowe mapowanie** u dołu ekranu, aby dodawać nowe mapowania. Mapowanie atrybutu poszczególnych obsługuje następujące właściwości:

      * **Typ mapowania**

         * **Bezpośrednie** — zapisuje wartość atrybutu produktu Workday w atrybucie AD bez zmian

         * **Stała** -zapisać wartości ciągu statycznych, stałych do atrybutu AD

         * **Wyrażenie** — umożliwia pisanie niestandardowej wartości atrybutu AD na podstawie co najmniej jeden dzień roboczy atrybutów. [Aby uzyskać więcej informacji, zobacz ten artykuł wyrażeń](active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **Atrybut źródłowy** — atrybut użytkownika z produktu Workday. Jeśli nie ma atrybutu, którego szukasz, zobacz [Dostosowywanie listę atrybutów użytkowników produktu Workday](#customizing-the-list-of-workday-user-attributes).

      * **Wartość domyślna** — jest to opcjonalne. Jeśli atrybut źródłowy ma wartość pustą, mapowanie zapisu wtedy tę wartość.
            Najbardziej typowych konfiguracji jest to pole pozostanie puste.

      * **Atrybut TARGET** — atrybut użytkownika w usłudze Active Directory.

      * **Zgodne obiektów przy użyciu tego atrybutu** — czy to mapowanie powinien być używany do jednoznacznego identyfikowania użytkowników między produktu Workday i usługi Active Directory. Jest to zazwyczaj ustawiona w polu Identyfikator procesu roboczego dla produktu Workday, która zwykle jest mapowana na jeden z atrybutów identyfikator pracownika w usłudze Active Directory.

      * **Dopasowywanie pierwszeństwo** — wiele pasujących atrybuty można ustawić. Jeśli dostępnych jest wiele, są oceniane pod w kolejności wynika z tego pola. Jak dopasowania zostanie znaleziony, żadne dodatkowe dopasowania atrybuty są oceniane.

      * **Zastosuj to mapowanie**
       
         * **Zawsze** — Zastosuj to mapowanie na obu Tworzenie użytkownika i aktualizowanie akcji

         * **Tylko podczas tworzenia** -Zastosuj to mapowanie tylko akcje creation użytkownika

6. Aby zapisać mapowania, kliknij przycisk **zapisać** w górnej części sekcji atrybutu mapowania.

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**Poniżej przedstawiono przykładowe Mapowanie atrybutów między produktu Workday i usługi Active Directory, z niektórych typowych wyrażeń**

-   Wyrażenie, które mapy do atrybutu parentDistinguishedName służy do obsługi administracyjnej użytkowników do różnych jednostek organizacyjnych, na podstawie jednego lub więcej atrybutów źródła produktu Workday. W tym przykładzie tutaj umieszcza użytkowników w różnych jednostkach organizacyjnych oparte na jakim mieście znajdują się w.

-   Atrybut userPrincipalName w usłudze Active Directory jest generowana przez połączenie produktu Workday identyfikator użytkownika z sufiksem domeny

-   [Brak dokumentację dotyczącą wyrażeń w tym miejscu](active-directory-saas-writing-expressions-for-attribute-mappings.md). W tym przykłady dotyczące Usuń znaki specjalne.

  
| ATRYBUT WORKDAY | ATRYBUT USŁUGI ACTIVE DIRECTORY |  ZGODNYM IDENTYFIKATOREM? | UTWÓRZ / ZAKTUALIZUJ |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  Identyfikator pracownika | **Tak** | Napisane przy tworzeniu tylko | 
| **Nazwa użytkownika**    |  cn    |   |   Napisane przy tworzeniu tylko |
| **Dołącz ("@", [UserID] "contoso.com")**   | userPrincipalName     |     | Napisane przy tworzeniu tylko 
| **Zastąp (Mid (Zastąp (\[UserID\],, "(\[ \\ \\ / \\ \\ \\ \\ \\ \\ \[ \\\\\]\\\\:\\\\;\\ \\|\\\\=\\\\,\\\\+\\\\\*\\ \\? \\ \\ &lt; \\ \\ &gt; \]) "," ",), 1, 20)," ([\\\\.) \* \$] (file:///\\.) *$)", , "", , )**      |    sAMAccountName            |     |         Napisane przy tworzeniu tylko |
| **Przełącznik (\[Active\],, "0", "True", "1")** |  AccountDisabled      |     | Tworzenie i aktualizowanie |
| **Imię**   | givenName       |     |    Tworzenie i aktualizowanie |
| **Nazwisko**   |   SN   |     |  Tworzenie i aktualizowanie |
| **PreferredNameData**  |  Nazwa wyświetlana |     |   Tworzenie i aktualizowanie |
| **Firmy**         | Firmy   |     |  Tworzenie i aktualizowanie |
| **SupervisoryOrganization**  | Dział  |     |  Tworzenie i aktualizowanie |
| **ManagerReference**   | manager  |     |  Tworzenie i aktualizowanie |
| **BusinessTitle**   |  tytuł     |     |  Tworzenie i aktualizowanie | 
| **AddressLineData**    |  streetAddress  |     |   Tworzenie i aktualizowanie |
| **Jednostki administracyjnej**   |   l   |     | Tworzenie i aktualizowanie |
| **CountryReferenceTwoLetter**      |   co |     |   Tworzenie i aktualizowanie |
| **CountryReferenceTwoLetter**    |  c  |     |         Tworzenie i aktualizowanie |
| **CountryRegionReference** |  st     |     | Tworzenie i aktualizowanie |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Tworzenie i aktualizowanie |
| **PostalCode**  |   postalCode  |     | Tworzenie i aktualizowanie |
| **PrimaryWorkTelephone**  |  TelephoneNumber   |     | Tworzenie i aktualizowanie |
| **Faksów**      | facsimileTelephoneNumber     |     |    Tworzenie i aktualizowanie |
| **Mobile**  |    Telefon komórkowy       |     |       Tworzenie i aktualizowanie |
| **LocalReference** |  preferredLanguage  |     |  Tworzenie i aktualizowanie |                                               
| **Przełącznik (\[jednostki administracyjnej\], "jednostki Organizacyjnej użytkowników standardowych, OU = użytkowników, OU = domyślne, OU = lokalizacjach, DC = = contoso, DC = com", "Dallas", "jednostki Organizacyjnej użytkowników standardowych, OU = użytkowników, OU = Dallas, OU = lokalizacjach, DC = = contoso, DC = com", "Austin", "jednostki Organizacyjnej użytkowników standardowych, OU = Użytkownicy, OU = Austin, OU = lokalizacjach, DC = = contoso, DC = com ","Seattle"," jednostki Organizacyjnej użytkowników standardowych, OU = użytkowników, OU = Seattle, OU = lokalizacjach, DC = = contoso, DC = com ","Londyn"," jednostki Organizacyjnej użytkowników standardowych, OU = użytkowników, OU = Londyn, OU = lokalizacjach, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Tworzenie i aktualizowanie |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Część 3: Konfigurowanie agenta synchronizacji lokalnej

Aby udostępnić, aby lokalne usługi Active Directory, musi być zainstalowany agent na serwerze przyłączonym do domeny w desire lasu usługi Active Directory. Administrator domeny (lub administratorem przedsiębiorstwa) są wymagane poświadczenia, aby zakończyć procedurę.

**[Można pobrać tutaj agent synchronizacji lokalnej](https://go.microsoft.com/fwlink/?linkid=847801)**

Po zainstalowaniu agenta, uruchom poniższe polecenia programu Powershell do konfigurowania agenta w danym środowisku.

**Polecenie #1**

> CD "C:\Program Files\Microsoft Azure AD Connect, inicjowania obsługi administracyjnej Agent\Modules\AADSyncAgent" Agent\\modułów\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect inicjowania obsługi administracyjnej Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**Polecenie #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Wejście: "Nazwa katalogu", wprowadź nazwę lesie usługi Active Directory wprowadzoną w części \#2
* Wejście: Nazwa użytkownika i hasło dla lasu usługi Active Directory

**Polecenie #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Wejście: Nazwa użytkownika administratora globalnego i hasło dla dzierżawy usługi Azure AD

>[!IMPORTANT]
>Obecnie jest to znany problem z poświadczeniami administratora globalnego nie działa, jeśli używają one niestandardową domenę (przykład: admin@contoso.com). Jako obejście, Utwórz i korzystać z domeny onmicrosoft.com konta administratora globalnego (przykład: admin@contoso.onmicrosoft.com)

>[!IMPORTANT]
>Obecnie jest to znany problem z poświadczeniami administratora globalnego nie działa, jeśli mają włączone uwierzytelnianie wieloskładnikowe. Jako obejście Wyłącz uwierzytelnianie wieloskładnikowe dla administratora globalnego.


**Polecenie #4**

> Get-AdSyncAgentProvisioningTasks

* Akcja: Upewnij się, że dane są zwracane. To polecenie automatycznie odnajduje produktu Workday Inicjowanie obsługi aplikacji w dzierżawie usługi Azure AD. Przykładowe dane wyjściowe:

> Nazwa: Moje lesie usługi Active Directory
>
> Włączono: True
>
> DirectoryName: mydomain.contoso.com
>
> Przetwarzający: False
>
> Identifier    : WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Polecenie #5**

> Start-AdSyncAgentSynchronization -Automatic

**Polecenie #6**

> polecenie net stop aadsyncagent

**Polecenie #7**

> polecenie net start aadsyncagent

>[!TIP]
>Oprócz polecenia "net" w programie Powershell, usługi agenta synchronizacji można też uruchomić i zatrzymać przy użyciu **Services.msc**. Jeśli występują błędy podczas uruchamiania polecenia programu Powershell, upewnij się, że **Microsoft Azure AD inicjowania obsługi administracyjnej agenta Connect** działa w **Services.msc**.

![Usługi](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**Dodatkowa konfiguracja dla klientów w Unii Europejskiej**

Jeśli dzierżawy usługi Azure Active Directory znajduje się w jednym z centrów danych Unii Europejskiej, należy wykonać dodatkowe czynności.

1. Otwórz **Services.msc** i Zatrzymaj **Microsoft Azure AD inicjowania obsługi administracyjnej agenta Connect** usługi.
2. Przejdź do folderu instalacji agenta (przykład: C:\Program Files\Microsoft Azure inicjowania obsługi administracyjnej agenta programu AD Connect).
3. Otwórz **SyncAgnt.exe.config** w edytorze tekstów.
4. Zastąp https://manage.hub.syncfabric.windowsazure.com/Management z **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Zastąp https://provision.hub.syncfabric.windowsazure.com/Provisioning z **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Zapisz **SyncAgnt.exe.config** pliku.
7. Otwórz **Services.msc**i uruchom **Microsoft Azure AD inicjowania obsługi administracyjnej agenta Connect** usługi.

**Rozwiązywanie problemów z agentem**

[Dziennika zdarzeń systemu Windows](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) w systemie Windows Server komputerze hostującym agenta zawiera zdarzenia dla wszystkich operacji wykonywanych przez agenta. Aby wyświetlić te zdarzenia:
    
1. Otwórz **Eventvwr.msc**.
2. Wybierz **dzienniki systemu Windows > Aplikacja**.
3. Wyświetl wszystkie zdarzenia rejestrowane w lokalizacji source **AADSyncAgent**. 
4. Sprawdź błędy i ostrzeżenia.

W przypadku problemu uprawnień przy użyciu poświadczeń usługi Active Directory lub Azure Active Directory w poleceń programu Powershell zostanie wyświetlony błąd, taką jak: 
    
![Dzienniki zdarzeń](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Część 4: Uruchom usługę programu
Po zakończeniu części 1-3, można uruchomić usługę inicjowania obsługi administracyjnej w portalu Azure.

1.  W **inicjowania obsługi administracyjnej** ustaw **stan inicjowania obsługi administracyjnej** do **na**.

2. Kliknij pozycję **Zapisz**.

3. Spowoduje to uruchomienie synchronizacji początkowej, jakie mogą mieć różną liczbą godzin, zależnie od liczby użytkowników znajdują się w pracy.

4. W każdej chwili sprawdzić **dzienniki inspekcji** kartę w portalu Azure, aby zobaczyć, jakie akcje wykonał inicjowania obsługi usługi. Dzienniki inspekcji wymieniono wszystkie zdarzenia synchronizacji poszczególnych wykonywane przez usługę inicjowania obsługi administracyjnej, takie jak użytkownicy są poza produktu Workday do odczytu i następnie później dodane lub zaktualizowane do usługi Active Directory. **[W podręczniku obsługi administracyjnej raportowania szczegółowe instrukcje na temat sposobu czytanie dzienników inspekcji](active-directory-saas-provisioning-reporting.md)**

5.  Sprawdź [dziennika zdarzeń systemu Windows](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) na komputerze z serwerem systemu Windows uruchomiony jest agent nowe błędy lub ostrzeżenia. Te zdarzenia są widoczne, uruchamiając **Eventvwr.msc** na serwerze i wybranie **dzienniki systemu Windows > Aplikacja**. Wszystkie wiadomości dotyczące inicjowania obsługi administracyjnej są rejestrowane w lokalizacji source **AADSyncAgent**. 
    

6. Jeden ukończone, będą zapisywane podsumowanie inspekcji **inicjowania obsługi administracyjnej** karcie, jak pokazano poniżej.

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Konfigurowanie Inicjowanie obsługi użytkowników do usługi Azure Active Directory
Jak skonfigurować inicjowania obsługi administracyjnej do usługi Azure Active Directory zależy od wymagania inicjowania obsługi administracyjnej, zgodnie z opisem w poniższej tabeli.

| Scenariusz | Rozwiązanie |
| -------- | -------- |
| **Użytkownicy muszą być przygotowana do usługi Active Directory i Azure AD** | Użyj  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Użytkownicy muszą być aprowizowana tylko do usługi Active Directory** | Użyj  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Użytkownicy muszą być przygotowana do tylko usługi Azure AD (tylko w chmurze)** | Użyj **Workday do inicjowania obsługi usługi Azure Active Directory** aplikacji w galerii aplikacji |

Aby uzyskać instrukcje na temat konfigurowania usługi Azure AD Connect, zobacz [dokumentacji usługi Azure AD Connect](connect/active-directory-aadconnect.md).

W poniższych sekcjach opisano konfigurowanie połączenia między produktu Workday i Azure AD, aby udostępnić użytkownikom tylko w chmurze.

> [!IMPORTANT]
> Wykonaj poniższą procedurę tylko, jeśli użytkownicy tylko w chmurze, które muszą być przygotowana do usługi Azure AD i nie lokalnej usługi Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie aplikacji łącznika inicjowania obsługi usługi Azure AD i tworzenie połączenia z produktu Workday

**Aby skonfigurować produktu Workday do usługi Azure Active Directory inicjowania obsługi administracyjnej dla użytkowników tylko w chmurze:**

1.  Przejdź do pozycji <https://portal.azure.com> (Plik > Nowy > Inny).

2.  Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3.  Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4.  Wybierz **dodać aplikację**, a następnie wybierz **wszystkie** kategorii.

5.  Wyszukaj **produktu Workday do inicjowania obsługi usługi Azure AD**i Dodaj danej aplikacji w galerii.

6.  Po dodaniu aplikacji i ekran szczegółów aplikacji jest wyświetlane, wybierz pozycję **inicjowania obsługi administracyjnej**

7.  Zmień **inicjowania obsługi administracyjnej** **tryb** do **automatyczne**

8.  Zakończenie **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemu integracji produktu Workday, z dołączoną nazwą domeny dzierżawy. Powinien wyglądać mniej więcej tak: username@contoso4

   * **Hasło administratora —** wprowadź hasło do konta produktu Workday integracji systemu

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla dzierżawy. To powinien wyglądać następująco: https://wd3-impl-services1.workday.com/ccx/service/contoso4, gdzie contoso4 jest zastępowany nazwa dzierżawy poprawne i wd3 impl jest zastępowany ciągiem poprawne środowisko. Jeśli ten adres URL nie jest znany, proszę współpracować z produktu Workday integracji partnera lub pomocy technicznej przedstawicielem, aby określić prawidłowy adres URL do użycia.

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku.

   * Jeśli test połączenia powiedzie się, kliknij przycisk **zapisać** na górze. W przypadku niepowodzenia dokładnie sprawdzić, czy produktu Workday adresu URL i poświadczenia są prawidłowe w pracy.


### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów 

W tej sekcji skonfigurujesz, jak dane użytkownika wypływających z produktu Workday do usługi Azure Active Directory dla użytkowników tylko w chmurze.

1.  Na karcie inicjowania obsługi administracyjnej w obszarze **mapowania**, kliknij przycisk **pracowników zsynchronizować z usługą Azure AD**.

2.   W **zakres obiektu źródłowego** pole, można wybrać, które zestawów użytkowników w pracy powinna być w zakresie obsługi do usługi Azure AD, definiując zestaw opartych na atrybutach filtrów. Domyślny zakres to "wszyscy użytkownicy w pracy". Przykładowe filtry:

   * Przykład: Zakres dla użytkowników z procesu roboczego identyfikatorów 1000000 i 2000000

      * Atrybut: WorkerID

      * Operator: Dopasowanie wyrażenia REGULARNEGO

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko warunkowe pracowników i pracowników nie regularne

      * Atrybut: ContingentID

      * Operator: Nie ma wartości NULL

3.  W **akcji obiektów docelowych** pole globalnie filtrowania, jakie akcje mogą wykonać na usługi Azure AD. **Utwórz** i **aktualizacji** są najczęściej.

4.  W **mapowania atrybutów** sekcji, można zdefiniować poszczególne produktu Workday atrybuty mapy do atrybutów usługi Active Directory.

5. Kliknij na istniejące mapowanie atrybutu go zaktualizować lub **Dodaj nowe mapowanie** u dołu ekranu, aby dodawać nowe mapowania. Mapowanie atrybutu poszczególnych obsługuje następujące właściwości:

   * **Typ mapowania**

      * **Bezpośrednie** — zapisuje wartość atrybutu produktu Workday w atrybucie AD bez zmian

      * **Stała** -zapisać wartości ciągu statycznych, stałych do atrybutu AD

      * **Wyrażenie** — umożliwia pisanie niestandardowej wartości atrybutu AD na podstawie co najmniej jeden dzień roboczy atrybutów. [Aby uzyskać więcej informacji, zobacz ten artykuł wyrażeń](active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **Atrybut źródłowy** — atrybut użytkownika z produktu Workday. Jeśli nie ma atrybutu, którego szukasz, zobacz [Dostosowywanie listę atrybutów użytkowników produktu Workday](#customizing-the-list-of-workday-user-attributes).

   * **Wartość domyślna** — jest to opcjonalne. Jeśli atrybut źródłowy ma wartość pustą, mapowanie zapisu wtedy tę wartość.
            Najbardziej typowych konfiguracji jest to pole pozostanie puste.

   * **Atrybut TARGET** — atrybut użytkownika w usłudze Azure AD.

   * **Zgodne obiektów przy użyciu tego atrybutu** — czy to mapowanie powinien być używany do jednoznacznego identyfikowania użytkowników między produktu Workday i Azure AD. Jest to zazwyczaj ustawiona w polu Identyfikator procesu roboczego dla produktu Workday, która zwykle jest mapowana na atrybut rozszerzenia lub atrybutu ID pracownika (nowy) w usłudze Azure AD.

   * **Dopasowywanie pierwszeństwo** — wiele pasujących atrybuty można ustawić. Jeśli dostępnych jest wiele, są oceniane pod w kolejności wynika z tego pola. Jak dopasowania zostanie znaleziony, żadne dodatkowe dopasowania atrybuty są oceniane.

   * **Zastosuj to mapowanie**

     * **Zawsze** — Zastosuj to mapowanie na obu Tworzenie użytkownika i aktualizowanie akcji

     * **Tylko podczas tworzenia** -Zastosuj to mapowanie tylko akcje creation użytkownika

6. Aby zapisać mapowania, kliknij przycisk **zapisać** w górnej części sekcji atrybutu mapowania.

### <a name="part-3-start-the-service"></a>Część 3: Uruchom usługę programu
Po zakończeniu części 1 i 2, można uruchomić usługę inicjowania obsługi administracyjnej.

1.  W **inicjowania obsługi administracyjnej** ustaw **stan inicjowania obsługi administracyjnej** do **na**.

2. Kliknij pozycję **Zapisz**.

3. Spowoduje to uruchomienie synchronizacji początkowej, jakie mogą mieć różną liczbą godzin, zależnie od liczby użytkowników znajdują się w pracy.

4. Synchronizacja poszczególnych zdarzeń można wyświetlać w **dzienników inspekcji** kartę. **[W podręczniku obsługi administracyjnej raportowania szczegółowe instrukcje na temat sposobu czytanie dzienników inspekcji](active-directory-saas-provisioning-reporting.md)**

5. Jeden ukończone, będą zapisywane podsumowanie inspekcji **inicjowania obsługi administracyjnej** karcie, jak pokazano poniżej.


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurowanie funkcji zapisywania zwrotnego adresów e-mail do pracy
Wykonaj te instrukcje, aby skonfigurować zapisywanie zwrotne adresu e-mail użytkownika z usługi Azure Active Directory do produktu Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie inicjowania obsługi administracyjnej aplikacja łącznika i tworzenie połączenia z produktu Workday

**Aby skonfigurować produktu Workday do inicjowania obsługi usługi Active Directory:**

1.  Przejdź do strony <https://portal.azure.com>

2.  Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3.  Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4.  Wybierz **dodać aplikację**, a następnie wybierz pozycję **wszystkie** kategorii.

5.  Wyszukaj **zapisywania zwrotnego produktu Workday**i Dodaj danej aplikacji w galerii.

6.  Po dodaniu aplikacji i ekran szczegółów aplikacji jest wyświetlane, wybierz pozycję **inicjowania obsługi administracyjnej**

7.  Zmień **inicjowania obsługi administracyjnej** **tryb** do **automatyczne**

8.  Zakończenie **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemu integracji produktu Workday, z dołączoną nazwą domeny dzierżawy. Powinien wyglądać mniej więcej tak: username@contoso4

   * **Hasło administratora —** wprowadź hasło do konta produktu Workday integracji systemu

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla dzierżawy. To powinien wyglądać następująco: https://wd3-impl-services1.workday.com/ccx/service/contoso4, gdzie contoso4 jest zastępowany nazwa dzierżawy poprawne i wd3 impl jest zastępowany ciągiem poprawne środowisko (Jeśli to konieczne).

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku. Jeśli test połączenia powiedzie się, kliknij przycisk **zapisać** na górze. W przypadku niepowodzenia dokładnie sprawdzić, czy produktu Workday adresu URL i poświadczenia są prawidłowe w pracy.


### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów 


W tej sekcji skonfigurujesz, jak dane użytkownika wypływających z produktu Workday do usługi Active Directory.

1.  Na karcie inicjowania obsługi administracyjnej w obszarze **mapowania**, kliknij przycisk **synchronizacji Azure AD użytkownikom produktu Workday**.

2.  W **zakres obiektu źródłowego** pole, można również filtrować które zestawów użytkowników w usłudze Azure Active Directory powinny mieć ich adresów e-mail zapisywane z powrotem do produktu Workday. Domyślny zakres to "wszyscy użytkownicy w usłudze Azure AD". 

3.  W **mapowania atrybutów** sekcji, można zdefiniować poszczególne produktu Workday atrybuty mapy do atrybutów usługi Active Directory. Brak mapowania dla adresu e-mail domyślnie. Jednak należy zaktualizować dopasowania Identyfikatora do dopasowania użytkowników w usłudze Azure AD z ich odpowiednie pozycje w pracy. Popularne odpowiadającej metody jest synchronizowane identyfikator pracownika do extensionAttribute1 15 lub identyfikator procesu roboczego produktu Workday w usłudze Azure AD, a następnie użyć tego atrybutu w usłudze Azure AD, aby dopasować użytkowników, po powrocie do produktu Workday.

4.  Aby zapisać mapowania, kliknij przycisk **zapisać** w górnej części sekcji atrybutu mapowania.

### <a name="part-3-start-the-service"></a>Część 3: Uruchom usługę programu
Po zakończeniu części 1 i 2, można uruchomić usługę inicjowania obsługi administracyjnej.

1.  W **inicjowania obsługi administracyjnej** ustaw **stan inicjowania obsługi administracyjnej** do **na**.

2. Kliknij pozycję **Zapisz**.

3. Spowoduje to uruchomienie synchronizacji początkowej, jakie mogą mieć różną liczbą godzin, zależnie od liczby użytkowników znajdują się w pracy.

4. Synchronizacja poszczególnych zdarzeń można wyświetlać w **dzienników inspekcji** kartę. **[W podręczniku obsługi administracyjnej raportowania szczegółowe instrukcje na temat sposobu czytanie dzienników inspekcji](active-directory-saas-provisioning-reporting.md)**

5. Jeden ukończone, będą zapisywane podsumowanie inspekcji **inicjowania obsługi administracyjnej** karcie, jak pokazano poniżej.


## <a name="customizing-the-list-of-workday-user-attributes"></a>Dostosowywanie listę atrybutów użytkowników produktu Workday
Udostępnianie aplikacji dla usługi Active Directory i Azure AD obejmują domyślną listę atrybutów użytkowników z produktu Workday pracy można wybrać z. Jednak te listy nie są wyczerpujące. Dzień roboczy obsługuje wiele setki użytkowników możliwe atrybuty, które mogą być standardowe lub unikatowym dla Twojej dzierżawy produktu Workday. 

Azure AD, świadczenie usługi obsługuje możliwość dostosowania listy lub atrybut produktu Workday do zawiera wszystkie atrybuty w [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operacji interfejsu API zasobów ludzkich.

Aby to zrobić, należy użyć [Studio produktu Workday](https://community.workday.com/studio-download) wyodrębnić wyrażenia XPath, które reprezentują wartości atrybutów, które chcesz użyć, a następnie dodaj je do inicjowania obsługi administracyjnej konfiguracji za pomocą edytora zaawansowane atrybutu w portalu Azure.

**Aby pobrać wyrażenie XPath dla produktu Workday atrybutu użytkownika:**

1. Pobierz i zainstaluj [Studio produktu Workday](https://community.workday.com/studio-download). Konieczne będzie konto społeczności produktu Workday do Instalatora.

2. Pobierz plik WDSL Human_Resources produktu Workday z tego adresu URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Uruchom Workday Studio.

4. Na pasku poleceń Wybierz **produktu Workday > usługi sieci Web testów w Tester** opcji.

5. Wybierz **zewnętrznych**i wybierz plik Human_Resources WSDL pobrany w kroku 2.

    ![WORKDAY Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio1.PNG)

6. Ustaw **lokalizacji** do `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale zastępując "IMPL-CC" rzeczywistymi wystąpienie typu i "Dzierżawa" nazwą Twojej dzierżawy prawdziwe.

7. Ustaw **operacji** do **Get_Workers**

8.  Kliknij małą **skonfigurować** link poniżej okienka żądanie/odpowiedź, aby ustawić poświadczenia dla produktu Workday. Sprawdź **uwierzytelniania**, a następnie wprowadź nazwę użytkownika i hasło dla konta system integracji produktu Workday. Pamiętaj format nazwy użytkownika jako name@tenanti pozostawić **UsernameToken WS-Security** wybraną opcją.

    ![WORKDAY Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio2.PNG)

9. Kliknij przycisk **OK**.

10. **Żądania** okienka, Wklej w kodzie XML poniżej i ustaw **Employee_ID** identyfikator pracownika rzeczywistego użytkownika w dzierżawie produktu Workday. Wybierz użytkownika, który ma atrybut wypełnione chcesz wyodrębnić.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Kliknij przycisk **Wyślij żądanie** (zieloną strzałkę) można wykonać polecenia. Jeśli powodzenia odpowiedzi powinna być widoczna w **odpowiedzi** okienka. Sprawdź odpowiedzi, aby upewnić się, że ma dane wprowadzony identyfikator użytkownika, a nie błąd.

12. Jeśli to się powiedzie, skopiuj plik XML z **odpowiedzi** okienko i zapisz go jako plik XML.

13. W programie polecenia pasek z produktu Workday Studio wybierz **Plik > Otwórz plik...**  , a następnie otwórz zapisany plik XML. Zostanie otwarte go w edytorze XML Studio produktu Workday.

    ![WORKDAY Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio3.PNG)

14. W drzewie pliku nawigowania **/env:Envelope > ENV > wd:Get_Workers_Response > wd:Response_Data > wd:Worker** celu znalezienia danych użytkownika. 

15. W obszarze **wd:Worker**, Znajdź atrybut, który chcesz dodać i zaznacz je.

16. Skopiuj wyrażenie XPath dla wybranych atrybut poza **ścieżka dokumentu** pola.

17. Usuń **/env:Envelope / ENV / wd:Get_Workers_Response / wd:Response_Data /** prefiksu wyrażenia skopiowane. 

18. Jeśli ostatniego elementu w wyrażeniu skopiowanych jest węzłem (przykład: "/ wd:Birth_Date"), następnie dołącz **/text()** na końcu wyrażenia. Nie jest to konieczne, jeśli ostatni element atrybutu (przykład: "/@wd:type").

19. Wynik powinien być podobny do `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Jest to, co spowoduje skopiowanie do portalu Azure.


**Aby dodać niestandardowe atrybut użytkownika produktu Workday do inicjowania obsługi administracyjnej konfiguracji:**

1. Uruchom [portalu Azure](https://portal.azure.com), a następnie przejdź do sekcji inicjowania obsługi administracyjnej dnia roboczego Inicjowanie obsługi administracyjnej aplikacji, zgodnie z opisem we wcześniejszej części tego samouczka.

2. Ustaw **stan inicjowania obsługi administracyjnej** do **poza**i wybierz **zapisać**. Dzięki temu, upewnij się, że zmiany zostały zastosowane tylko wtedy, gdy wszystko będzie gotowe.

3. W obszarze **mapowania**, wybierz pozycję **zsynchronizować pracownikom OnPremises** (lub **pracowników zsynchronizować z usługą Azure AD**).

4. Przewiń do dołu ekranu dalej, a następnie wybierz **Pokaż zaawansowane opcje**.

5. Wybierz **listę atrybutów edycji dla produktu Workday**.

    ![WORKDAY Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Przewiń w dół listę atrybutów, na którym są pól wejściowych.

7. Aby uzyskać **nazwa**, wprowadź nazwę wyświetlaną dla atrybut.

8. Dla **typu**, wybierz typ odpowiadający odpowiednio do atrybutu (**ciąg** jest najbardziej typowych).

9. Aby uzyskać **wyrażenie interfejsu API**, wprowadź wyrażenie XPath, które zostały skopiowane z produktu Workday Studio. Przykład: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wybierz **Dodaj atrybut**.

    ![WORKDAY Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Wybierz **zapisać** powyżej, a następnie **tak** do okna dialogowego. Zamknąć ten ekran mapowania atrybut, jeśli jest nadal otwarty.

12. Ponownie w głównym **inicjowania obsługi administracyjnej** wybierz opcję **zsynchronizować pracownikom OnPremises** (lub **pracowników zsynchronizować z usługą Azure AD**) ponownie.

13. Wybierz **Dodaj nowe mapowanie**.

14. Powinien zostać wyświetlony nowy atrybut **atrybut źródłowy** listy.

15. Dodaj mapowanie atrybutu nowe zgodnie z potrzebami.

16. Po zakończeniu Pamiętaj, aby ustawić **stan inicjowania obsługi administracyjnej** do **na** i Zapisz.


## <a name="known-issues"></a>Znane problemy

* Podczas uruchamiania **ADSyncAgentAzureActiveDirectoryConfiguration Dodaj** poleceń programu Powershell, obecnie jest to znany problem z poświadczeniami administratora globalnego nie działa, jeśli używają one niestandardową domenę (przykład: admin@contoso.com) . Jako obejście, tworzenia i używania konta administratora globalnego w usłudze Azure AD z domeny onmicrosoft.com (przykład: admin@contoso.onmicrosoft.com).

* Poprzedni problem z dziennikami inspekcji nie są widoczne w dzierżaw usługi Azure AD, znajduje się w Unii Europejskiej został rozwiązany. Jednak konfiguracji dodatkowych agenta jest wymagane dla dzierżaw usługi Azure AD w UE. Aby uzyskać więcej informacji, zobacz [część 3: Konfigurowanie agenta synchronizacji lokalnej](#Part 3: Configure the on-premises synchronization agent)

## <a name="gdpr-compliance"></a>GDPR zgodności

[Rozporządzenia ogólne ochrony danych (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) jest prawo ochrona i prywatność danych Unii Europejskiej (UE). GDPR nakłada reguł dla firm, agencji rządowych, z systemem innym niż zysków i innych organizacji, które oferują towarów i usług do osób w UE, lub że zbieranie i analizowanie danych powiązane mieszkańców Unii Europejskiej. 

Inicjowania obsługi usługi Azure AD jest GDPR zgodne wraz z resztą funkcje i usługi firmy Microsoft. Aby dowiedzieć się więcej na temat wątku GDPR firmy Microsoft, zobacz [warunków użytkowania usługi](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Jednak ponieważ produktu Workday rozwiązania inicjowania obsługi administracyjnej dla usługi Active Directory wymaga agent synchronizacji musi zostać zainstalowany na serwerze przyłączonym do domeny, są niektóre zdarzenia, które mają być monitorowanie, aby być na bieżąco GDPR zgodne.
 
Agent tworzy dzienniki w **dziennika zdarzeń systemu Windows**, które zawierają dane osobowe.

Istnieją dwa sposoby pozostać GDPR zgodnych:

1. Na żądanie wyodrębnić dane dla osoby i usuwanie danych z tej osoby z dzienników zdarzeń systemu Windows. 
2. Zachowaj przechowywania dzienników zdarzeń systemu Windows, pochodzących z procesu AADSyncAgent w obszarze 48 godzin.

Aby uzyskać informacje na temat konfigurowania przechowywania danych dzienników zdarzeń systemu Windows, temacie [ustawienia dzienników zdarzeń](https://technet.microsoft.com/en-us/library/cc952132.aspx). Aby uzyskać ogólne informacje w dzienniku zdarzeń systemu Windows, temacie [w tym artykule](https://msdn.microsoft.com/en-us/library/windows/desktop/aa385772.aspx).


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
* [Informacje o sposobie konfigurowania rejestracji jednokrotnej między produktu Workday i Azure Active Directory](active-directory-saas-workday-tutorial.md)
* [Dowiedz się, jak zintegrować innych aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)

