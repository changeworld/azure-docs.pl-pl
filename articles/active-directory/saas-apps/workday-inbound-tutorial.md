---
title: 'Samouczek: Konfigurowanie produktu Workday do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników w produkcie Workday.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: asmalser
ms.openlocfilehash: 917baa4b0d983df858c64cd0fa5b697b0fbb316c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298272"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Samouczek: Konfigurowanie produktu Workday do inicjowania obsługi administracyjnej użytkowników (wersja zapoznawcza)

Celem tego samouczka jest Wam czynności, które należy wykonać, aby zaimportować osób z produktu Workday do usługi Active Directory i Azure Active Directory za pomocą opcjonalnych funkcji zapisywania zwrotnego niektórych atrybutów w produkcie Workday.

## <a name="overview"></a>Przegląd

[Usługa aprowizowania użytkowników w usłudze Azure Active Directory](../manage-apps/user-provisioning.md) integruje się z [interfejsu API zarządzania zasobami ludzkimi Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) w celu dostarczania kont użytkowników. Usługa Azure AD używa tego połączenia, aby włączyć następujący użytkownik przepływy pracy aprowizacji:

* **Inicjowanie obsługi administracyjnej użytkowników do usługi Active Directory** — Synchronizuj wybrane zestawy użytkowników z produktu Workday do przynajmniej jednej lasów usługi Active Directory.

* **Inicjowanie obsługi administracyjnej użytkowników tylko w chmurze, usługą Azure Active Directory** — w scenariuszach, gdzie lokalnej usługi Active Directory nie jest używana, użytkownicy mogą być udostępniane bezpośrednio z produktu Workday do usługi Azure Active Directory za pomocą użytkownika usługi Azure AD, do usługi aprowizacji. 

* **Zapis zwrotny wiadomości e-mail, adresy w produkcie Workday** — usługa aprowizowania użytkowników w usłudze Azure AD można zapisywać adresy e-mail użytkowników usługi Azure AD w produkcie Workday. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jakie scenariuszy zarządzania zasobami ludzkimi dotyczy?

Dzień roboczy użytkownika inicjowania obsługi administracyjnej przepływów pracy obsługiwanych przez usługę aprowizacji użytkownika usługi Azure AD pozwalają na automatyzację następujących zasobów ludzkich i scenariuszy zarządzania cyklem życia tożsamości:

* **Zatrudniania nowych pracowników** — po dodaniu nowego pracownika w produkcie Workday, konto użytkownika zostanie automatycznie utworzone w usłudze Active Directory, Azure Active Directory i, opcjonalnie, usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](../manage-apps/user-provisioning.md), przy użyciu zapisywania zwrotnego adresu e-mail w produkcie Workday.

* **Aktualizacje atrybutu i profilu pracownika** — po zaktualizowaniu rekordu pracownika w produkcie Workday (np. ich nazwy, tytułu lub Menedżer), ich konta użytkownika zostaną automatycznie zaktualizowane w usłudze Active Directory, Azure Active Directory i, opcjonalnie, usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](../manage-apps/user-provisioning.md).

* **Liczba przerwanych pracowników** — w przypadku zakończenia pracownika w produkcie Workday, ich konta użytkownika jest automatycznie wyłączana w usługi Active Directory, Azure Active Directory i, opcjonalnie, usługi Office 365 i [innych aplikacji SaaS obsługiwane przez platformę Azure Usługi AD](../manage-apps/user-provisioning.md).

* **Pracownik ponownie zatrudnienie** — gdy pracownik jest rehired w produkcie Workday, ich starego konta mogą zostać automatycznie ponownie uaktywnione lub ponownie zainicjowano obsługę administracyjną (w zależności od Twojego preferencji) do usługi Active Directory, Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Kto jest ten użytkownik rozwiązanie do aprowizowania najlepsze dostosowane do?

Tego użytkownika z produktu Workday aprowizacji rozwiązania jest obecnie w publicznej wersji zapoznawczej i doskonale nadaje się do:

* Organizacje, które pragną wbudowanych, oparte na chmurze rozwiązanie do aprowizowania użytkowników w produkcie Workday

* Organizacje, które wymagają, bezpośrednie Inicjowanie obsługi użytkowników z produktu Workday do usługi Active Directory lub Azure Active Directory

* Organizacje, które wymagają użytkowników do aprowizacji przy użyciu danych uzyskanych z modułu Workday HCM (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacje wymagają dołączania, przenoszenie, i pozostawienie użytkownikom być synchronizowane z usługą co najmniej jeden lasy usługi Active Directory, domen i jednostek organizacyjnych, tylko na podstawie Zmień informacje w module Workday HCM (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacje, do obsługi poczty e-mail przy użyciu usługi Office 365

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="planning-your-solution"></a>Planowanie rozwiązania

Przed rozpoczęciem integracji z produktem Workday, sprawdź poniższe wymagania wstępne, a następnie przeczytaj poniższe wskazówki na temat sposobu spełnić bieżące architektury usługi Active Directory i inicjowania obsługi wymagań przy użyciu rozwiązania dostarczane przez usługę Azure Active Directory użytkownika.

### <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Ważną subskrypcją usługi Azure AD Premium P1 uprawnienia dostępu administratora globalnego
* Dzierżawy produktu Workday wdrożenia na potrzeby testowania i integracji
* Uprawnienia administratora w usłudze WORKDAY aplikacja do tworzenia użytkownika integracji systemu i wprowadzić zmiany do przetestowania danych pracownika do celów testowych
* Przypisywanie użytkowników do usługi Active Directory, aby uzyskać przyłączone do domeny serwerze z systemem Windows usługi, 2012 lub nowszej jest wymagany do hosta [lokalnego agenta synchronizacji](https://go.microsoft.com/fwlink/?linkid=847801)
* [Program Azure AD Connect](../hybrid/whatis-hybrid-identity.md) synchronizacji od usługi Active Directory a usługą Azure AD

### <a name="solution-architecture"></a>Architektura rozwiązania

Usługa Azure AD zapewnia bogaty zestaw aprowizacji łączników, aby pomóc w określeniu inicjowania obsługi administracyjnej i cyklu życia zarządzania tożsamościami z produktu Workday do usługi Active Directory, usługi Azure AD aplikacji SaaS i nie tylko. Która obejmuje będzie używać i jak skonfigurować rozwiązanie różnią się w zależności od środowiska i wymagań swojej organizacji. Pierwszym krokiem wykonaj zasobów jak wiele z następujących czynności są dostępne i wdrożone w Twojej organizacji:

* Ile lasy usługi Active Directory są używane?
* Jak wiele domen usługi Active Directory są używane?
* Ile Active Directory jednostki organizacyjne (OU) są używane?
* Jak wiele dzierżaw usługi Azure Active Directory są używane?
* Czy istnieją użytkownicy, którzy muszą być przygotowana do usługi Active Directory i Azure Active Directory (na przykład "hybrydowe" użytkowników)?
* Czy istnieją użytkownicy, którzy muszą być przygotowana do usługi Azure Active Directory, ale nie Active Directory (na przykład "chmura tylko do" użytkowników)?
* Czy adresy e-mail użytkowników muszą być zapisywane z powrotem w produkcie Workday?

Po uzyskaniu odpowiedzi na te pytania, możesz zaplanować pracy inicjowania obsługi wdrożenia, wykonując poniższe wskazówki.

#### <a name="using-provisioning-connector-apps"></a>Za pomocą inicjowania obsługi administracyjnej łącznika aplikacji

Usługa Azure Active Directory obsługuje wstępnie zintegrowanych łączniki inicjowania obsługi administracyjnej dla produktu Workday i wielu innych aplikacji SaaS.

Pojedynczy łącznik inicjowania obsługi administracyjnej interfejsy przy użyciu interfejsu API w systemu pojedyncze źródło i pomaga dane udostępniania systemu pojedynczy element docelowy. Większość inicjowania obsługi administracyjnej łączników, które obsługuje usługę Azure AD dla pojedynczego systemu źródłowym i docelowym (na przykład usługi Azure AD do usługi ServiceNow) i można skonfigurować przez dodanie aplikacji w danym z galerii aplikacji Azure AD (na przykład usługi ServiceNow).

Istnieje bezpośredni związek pomiędzy inicjowania obsługi administracyjnej wystąpienia łącznika i wystąpień aplikacji w usłudze Azure AD:

| System źródłowy | System docelowy |
| ---------- | ---------- |
| Dzierżawy usługi Azure AD | Aplikacja SaaS |

Podczas pracy z produktem Workday i usługi Active Directory, istnieją jednak wiele systemów źródłowych i docelowych, aby zostały uznane za:

| System źródłowy | System docelowy | Uwagi |
| ---------- | ---------- | ---------- |
| Dzień roboczy | Las usługi Active Directory | Każdego lasu jest traktowany jako odrębne funkcjonalnego |
| Dzień roboczy | Dzierżawy usługi Azure AD | Zgodnie z wymaganiami dla użytkowników tylko w chmurze |
| Las usługi Active Directory | Dzierżawy usługi Azure AD | Ten przepływ jest obecnie obsługiwane przez AAD Connect |
| Dzierżawy usługi Azure AD | Dzień roboczy | Aby funkcja zapisywania zwrotnego adresów e-mail |

Aby ułatwić wprowadzenie tych wiele przepływów pracy na wiele systemów źródłowych i docelowych, usługa Azure AD zapewnia wiele inicjowania obsługi administracyjnej aplikacji łącznika, które można dodawać z galerii aplikacji Azure AD:

![Galeria aplikacji usługi AAD](./media/workday-inbound-tutorial/WD_Gallery.PNG)

* **WORKDAY do udostępniania usługi Active Directory** — ta aplikacja ułatwia aprowizowaniem kont użytkowników z produktu Workday do jednego lasu usługi Active Directory. Jeśli masz wiele lasów, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji Azure AD dla każdego lasu usługi Active Directory, które należy aprowizować do.

* **WORKDAY do usługi Azure AD aprowizacji** — o ile AAD Connect to narzędzie, które mają być używane do synchronizacji usługi Active Directory, użytkowników do usługi Azure Active Directory, ta aplikacja może służyć do ułatwienia aprowizacji użytkowników tylko w chmurze z produktu Workday do pojedynczej platformy Azure Dzierżawy usługi Active Directory.

* **Zapisywanie zwrotne WORKDAY** — ta aplikacja umożliwia zapisywanie zwrotne adresy e-mail użytkowników z usługi Azure Active Directory w produkcie Workday.

> [!TIP]
> Regularne aplikacji "Dzień roboczy" służy do konfigurowania logowania jednokrotnego między produktem Workday i Azure Active Directory. 

Sposób instalowania i konfigurowania tych specjalnych inicjowania obsługi administracyjnej aplikacji łącznika jest przedmiotem pozostałej części tego samouczka. Aplikacje, które użytkownik chce skonfigurować będzie zależeć od systemów, które należy aprowizować, ile lasy usługi Active Directory a usługą Azure AD to dzierżawców w danym środowisku.

![Przegląd](./media/workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Konfigurowanie użytkownika integracji systemu w produkcie Workday
Typowym wymogiem wszystkich łączników inicjowania obsługi administracyjnej produktu Workday jest one wymagają poświadczeń dla konta integracji systemu Workday nawiązać połączenie z interfejsu API zarządzania zasobami ludzkimi produktu Workday. W tej sekcji opisano sposób tworzenia konta integrator systemu w usłudze WORKDAY aplikacja.

> [!NOTE]
> Istnieje możliwość pominąć tę procedurę i zamiast tego użyć konta administratora globalnego Workday jako konto integracji systemu. Może działać prawidłowo dla pokazów, ale nie jest zalecane w przypadku wdrożeń produkcyjnych.

### <a name="create-an-integration-system-user"></a>Utworzenie użytkownika usługi integracji systemu

**Aby utworzyć użytkownika systemowego integracji:**

1. Zaloguj się do dzierżawy produktu Workday za pomocą konta administratora. W **Workday Workbench**, wprowadź użytkownika w polu wyszukiwania, a następnie kliknij polecenie **Create User systemu integracji**.

    ![Utwórz użytkownika](./media/workday-inbound-tutorial/IC750979.png "Utwórz użytkownika")
2. Wykonaj **Create User systemu integracji** zadanie, podając nazwę użytkownika i hasło dla nowego użytkownika systemu integracji.  
 * Pozostaw **wymagają nowego hasła przy następnym Sign In** opcja nie jest zaznaczone, ponieważ ten użytkownik będzie logować się programowo.
 * Pozostaw **czas wygaśnięcia sesji w minutach** o jego wartość domyślna 0, która uniemożliwi sesji użytkownika przedwcześnie limit czasu.

    ![Tworzenie użytkownika systemu integracji](./media/workday-inbound-tutorial/IC750980.png "tworzenie integracji systemu użytkownika")

### <a name="create-a-security-group"></a>Utwórz grupę zabezpieczeń
Należy utworzyć grupę zabezpieczeń usługi integracji nieograniczonego systemu, a następnie przypisz użytkownika do niego.

**Aby utworzyć grupę zabezpieczeń:**

1. Wprowadź grupy zabezpieczeń w polu wyszukiwania, a następnie kliknij polecenie **Utwórz grupę zabezpieczeń**.

    ![Grupa CreateSecurity](./media/workday-inbound-tutorial/IC750981.png "CreateSecurity grupy")
2. Wykonaj **Utwórz grupę zabezpieczeń** zadania.  
3. Wybierz **integracji systemowa grupa zabezpieczeń (nieograniczony)** z **typu grupy zabezpieczeń gośćmi** listy rozwijanej.
4. Utwórz grupę zabezpieczeń, do którego będzie można dodać elementów członkowskich jawnie.

    ![Grupa CreateSecurity](./media/workday-inbound-tutorial/IC750982.png "CreateSecurity grupy")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Przypisz użytkownika do integracji systemu do grupy zabezpieczeń

**Aby przypisać użytkownika do integracji systemu:**

1. Edytuj grupę zabezpieczeń należy wprowadzić w polu wyszukiwania, a następnie kliknij przycisk **Edytuj grupę zabezpieczeń**.

    ![Edytuj grupę zabezpieczeń](./media/workday-inbound-tutorial/IC750983.png "Edytuj grupę zabezpieczeń")
1. Wyszukaj i wybierz nową grupę zabezpieczeń integracji według nazwy.

    ![Edytuj grupę zabezpieczeń](./media/workday-inbound-tutorial/IC750984.png "Edytuj grupę zabezpieczeń")
2. Dodaj nowego użytkownika systemu integracji do nowej grupy zabezpieczeń. 

    ![Systemowa grupa zabezpieczeń](./media/workday-inbound-tutorial/IC750985.png "systemowa grupa zabezpieczeń")  

### <a name="configure-security-group-options"></a>Skonfiguruj opcje grupy zabezpieczeń
W tym kroku będziesz udzielić zabezpieczeń domeny uprawnienia zasad dla danych procesu roboczego do grupy zabezpieczeń.

**Aby skonfigurować opcje grupy zabezpieczeń:**

1. Wprowadź **zasady zabezpieczeń domeny** w polu wyszukiwania, a następnie kliknij link **domeny zasad zabezpieczeń dla obszaru funkcjonalnego**.  

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/IC750986.png "zasady zabezpieczeń domeny")  
2. Wyszukaj systemu i wybierz **systemu** obszaru funkcjonalnego.  Kliknij przycisk **OK**.  

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/IC750987.png "zasady zabezpieczeń domeny")  
3. Na liście zasad zabezpieczeń dla obszaru funkcjonalnego systemu rozwiń **administrowanie zabezpieczeniami** i wybierz pozycję zasady zabezpieczeń domeny **zewnętrznej konta**.  

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/IC750988.png "zasady zabezpieczeń domeny")  
1. Kliknij przycisk **Edytuj uprawnienia**, a następnie na **Edytuj uprawnienia** strony okna dialogowego Dodaj nową grupę zabezpieczeń do listy grup zabezpieczeń z **uzyskać** i **Put**  uprawnienia integracji.

    ![Uprawnienia do edycji](./media/workday-inbound-tutorial/IC750989.png "uprawnienia do edycji")  

1. Powtórz kroki 1 – 4 powyżej dla każdej z tych pozostałych zasad zabezpieczeń:

| Operacja | Zasady zabezpieczeń domeny |
| ---------- | ---------- | 
| Operacje GET i Put | Danych procesu roboczego: Raporty publicznych procesu roboczego |
| Operacje GET i Put | Danych procesu roboczego: Informacje kontaktowe pracy |
| Get | Danych procesu roboczego: Wszystkie pozycje |
| Get | Dane procesu roboczego: Bieżące zaoszczędziła informacji |
| Get | Danych procesu roboczego: Tytuł firm profil procesu roboczego |


### <a name="activate-security-policy-changes"></a>Aktywuj zmiany zasad zabezpieczeń

**Aby aktywować zmiany zasad zabezpieczeń:**

1. Wprowadź aktywować w polu wyszukiwania, a następnie kliknij łącze **aktywować oczekujące zmiany zasad zabezpieczeń**.

    ![Aktywuj](./media/workday-inbound-tutorial/IC750992.png "aktywacji") 
2. Rozpoczęcia zadania aktywować oczekujące zmiany zasad zabezpieczeń, wprowadzając komentarz na potrzeby inspekcji, a następnie kliknij przycisk **OK**. 

    ![Aktywuj oczekujące zabezpieczeń](./media/workday-inbound-tutorial/IC750993.png "aktywować oczekujące zabezpieczeń")  
1. Ukończenie zadania na następnym ekranie, zaznaczając pole wyboru **Potwierdź**, a następnie kliknij przycisk **OK**.

    ![Aktywuj oczekujące zabezpieczeń](./media/workday-inbound-tutorial/IC750994.png "aktywować oczekujące zabezpieczeń")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfiguracja aprowizacji użytkowników z produktu Workday do usługi Active Directory
Wykonaj te instrukcje, aby skonfigurować konto użytkownika aprowizacji z produktu Workday w każdym lesie usługi Active Directory, wymagające zainicjowania obsługi administracyjnej.

### <a name="planning"></a>Planowanie

Przed rozpoczęciem konfigurowania aprowizacji użytkowników w lesie usługi Active Directory, należy wziąć pod uwagę następujące kwestie. Odpowiedzi na te pytania określają, jak Twoje filtrów określania zakresu i mapowania atrybutów należy ustawić. 

* **Użytkownicy w usłudze WORKDAY aplikacja musi być przygotowana do tego lasu usługi Active Directory?**

   * *Przykład: Użytkownicy, których atrybut "Firmowe" dzień roboczy zawiera wartość "Contoso", a atrybut "Worker_Type" "Regularne"*

* **Jak użytkownicy są kierowane do innej organizacji jednostek organizacyjnych**

   * *Przykład: Użytkownicy są kierowane do jednostek organizacyjnych, które odnoszą się do lokalizacji pakietu office, zgodnie z definicją w produkcie Workday "Gmina" i "Country_Region_Reference" atrybuty*

* **Jak następujące atrybuty powinny być wypełnione w usłudze Active Directory?**

   * Nazwa pospolita (cn)
      * *Przykład: Użyj wartość Workday User_ID, zgodnie z ustawieniem zarządzania zasobami ludzkimi*
      
   * Identyfikator pracownika (employeeId)
      * *Przykład: Użyj wartości Workday Worker_ID*
      
   * Nazwa konta SAM (sAMAccountName)
      * *Przykład: Użyj wartość Workday User_ID filtrowany za pośrednictwem usługi Azure AD aprowizacji wyrażenie Usuń niedozwolone znaki*
      
   * Główna nazwa użytkownika (userPrincipalName)
      * *Przykład: Użyj wartość User_ID produktu Workday za pomocą usługi Azure AD aprowizacji wyrażenie Dołącz nazwę domeny*

* **Jak użytkowników powinny być dopasowane między produktem Workday i usługi Active Directory?**

  * *Przykład: Użytkownicy z określonym produktem Workday "Worker_ID" wartości są dopasowywane użytkownikom usługi Active Directory, w którym "employeeID" ma taką samą wartość. Wartość Worker_ID nie zostanie znaleziony w usłudze Active Directory, utworzenie nowego użytkownika.*
  
* **Las usługi Active Directory już zawiera identyfikatory wymaganych do dopasowania logiki pracy użytkowników?**

  * *Przykład: Jeśli jest to nowe wdrożenie produktu Workday, zdecydowanie zalecane jest, usługi Active Directory jest wstępnie wypełniony poprawne wartości Workday Worker_ID (lub wartość unikatowego Identyfikatora wybranego) do zachowania pasujące logiki tak proste, jak to możliwe.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie obsługi administracyjnej aplikacji łącznika i tworzenia połączenia z produktem Workday

**Aby skonfigurować produktu Workday do inicjowania obsługi usługi Active Directory:**

1.  Przejdź do strony <https://portal.azure.com>

2.  Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3.  Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4.  Wybierz **dodać aplikację**i wybierz **wszystkich** kategorii.

5.  Wyszukaj **pracy aprowizacji usługi Active Directory**i Dodaj tę aplikację z galerii.

6.  Po dodaniu aplikacji i ekran szczegółów aplikacji jest pokazywane, wybierz opcję **aprowizacji**

7.  Zmiana **aprowizacji** **tryb** do **automatyczne**

8.  Wykonaj **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemowego integracji produktu Workday za pomocą dołączona nazwa domeny dzierżawy. **Powinien wyglądać następująco: username@contoso4**

   * **Hasło administratora —** wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla Twojej dzierżawy. Powinno to wyglądać podobnie do: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, gdzie contoso4 jest zastępowana nazwą dzierżawy poprawne i wd3 impl jest zastępowany ciągiem odpowiednie środowisko.

   * **Las usługi Active Directory —** "Name" usługi Active Directory lasu, w postaci zwracanej przez polecenie commandlet programu powershell Get-ADForest. Zazwyczaj jest to ciąg, takich jak: *contoso.com*

   * **Kontener usługi Active Directory —** wprowadź ciąg kontenera, który zawiera wszystkich użytkowników w lesie usługi AD. Przykład: *OU = użytkownicy standardowi, OU = Users, DC = contoso, DC = test*

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku. Jeśli test połączenia powiedzie się, kliknij przycisk **Zapisz** znajdujący się u góry. Jeśli nie powiedzie się, należy upewnić się, że poświadczenia dla produktu Workday są prawidłowe w produkcie Workday. 

![Azure Portal](./media/workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowania atrybutów 

W tej sekcji skonfigurujesz przepływ danych użytkownika z produktu Workday do usługi Active Directory.

1.  Na karcie Udostępnianie w obszarze **mapowania**, kliknij przycisk **zsynchronizować Workday pracownikom opłacanie**.

2.  W **zakres obiektów źródłowych** pola, można wybrać, który konfiguruje użytkowników w usłudze WORKDAY aplikacja musi należeć do zakresu, aby aprowizacja w usłudze AD, definiując zestaw opartych na atrybutach filtrów. Domyślny zakres jest "wszyscy użytkownicy w produkcie Workday". Przykładowe filtry:

   * Przykład: Zakres użytkowników z identyfikatorami procesów roboczych między 1000000 i 2000000

      * Atrybut: WorkerID

      * Operator: Dopasowanie wyrażenia REGULARNEGO

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko pracowników i nie tymczasowi pracownicy 

      * Atrybut: EmployeeID

      * Operator: Nie ma wartości NULL

3.  W **Akcje obiektu docelowego** pole globalnie filtrowania, jakie akcje mogą być wykonane na usłudze Active Directory. **Tworzenie** i **aktualizacji** występują najczęściej.

4.  W **mapowania atrybutów** sekcji można zdefiniować Workday jak poszczególne atrybuty mapowania atrybutów usługi Active Directory.

5. Kliknij na istniejące mapowanie atrybutu go zaktualizować, lub kliknij **Dodaj nowe mapowanie** w dolnej części ekranu, aby dodać nowe mapowania. Mapowanie atrybutu poszczególnych obsługuje następujące właściwości:

      * **Typ mapowania**

         * **Bezpośrednie** — zapisuje wartość atrybutu produktu Workday do atrybutu usługi AD, bez konieczności wprowadzania zmian

         * **Stałe** -zapisu wartość statyczne, stała ciągu atrybutu usługi AD

         * **Wyrażenie** — pozwala na zapis niestandardową wartość do atrybutu usługi AD na podstawie co najmniej jeden dzień roboczy atrybutów. [Aby uzyskać więcej informacji znajduje się w artykule na wyrażeniach](../manage-apps/functions-for-customizing-application-data.md).

      * **Atrybut źródłowy** — atrybut użytkownika z produktu Workday. Jeśli nie ma atrybut, którego szukasz, zobacz [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](#customizing-the-list-of-workday-user-attributes).

      * **Wartość domyślna** — opcjonalne. Jeśli atrybut źródłowy ma wartość pustą, mapowanie zapisu wtedy tę wartość.
            Najbardziej typowa konfiguracja polega na pozostaw to pole puste.

      * **Atrybut docelowy** — atrybut użytkownika w usłudze Active Directory.

      * **Zgodne obiekty korzystające z tego atrybutu** — czy to mapowanie powinien być używany do jednoznacznego identyfikowania użytkowników między produktem Workday i usługi Active Directory. Jest to zazwyczaj ustawiana na pole Identyfikatora procesu roboczego dla produktu Workday, który zazwyczaj jest mapowany na jeden z atrybutów identyfikator pracownika w usłudze Active Directory.

      * **Pierwszeństwo dopasowania** — wiele pasujące atrybuty można ustawić. Jeśli dostępnych jest wiele, są one obliczane w kolejności, zdefiniowanych przez to pole. Gdy tylko zostanie znalezione dopasowanie, żadne dodatkowe dopasowania atrybuty są oceniane.

      * **Zastosuj to mapowanie**
       
         * **Zawsze** — Zastosuj to mapowanie na obu tworzenia użytkownika i aktualizowanie działań

         * **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w akcji tworzenia użytkownika

6. Aby zapisać mapowania, kliknij przycisk **Zapisz** w górnej części mapowanie atrybutu.

![Azure Portal](./media/workday-inbound-tutorial/WD_2.PNG)

**Poniżej przedstawiono przykładowe mapowania atrybutów między produktem Workday i usługi Active Directory, z niektórych wspólnych wyrażeń**

-   Wyrażenie, które mapuje do atrybutu parentDistinguishedName jest używany do inicjowania obsługi użytkowników na różnych jednostkach organizacyjnych, oparte na jeden lub więcej atrybutów źródłowych produktu Workday. W tym przykładzie w tym miejscu umieszcza użytkowników w różnych jednostkach organizacyjnych, oparte na jakim mieście znajdują się w.

-   Atrybut userPrincipalName w usłudze Active Directory jest generowana przez połączenie Identyfikatora użytkownika Workday sufiksu domeny

-   [Dokumentacja na temat pisania wyrażeń w tym miejscu jest](../manage-apps/functions-for-customizing-application-data.md). Obejmuje to przykłady Usuń znaki specjalne.

  
| ATRYBUT WORKDAY | ATRYBUT USŁUGI ACTIVE DIRECTORY |  ZGODNYM IDENTYFIKATOREM? | TWORZENIE I AKTUALIZOWANIE |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  employeeID | **Tak** | Zapisane podczas tworzenia tylko |
| **Identyfikator użytkownika**    |  cn    |   |   Zapisane podczas tworzenia tylko |
| **Dołącz do ("@", [UserID] "contoso.com")**   | userPrincipalName     |     | Zapisane podczas tworzenia tylko 
| **Zastąp (Mid (Zastąp (\[UserID\],, "(\[ \\ \\ / \\ \\ \\ \\ \\ \\ \[ \\\\\]\\\\:\\\\;\\ \\|\\\\=\\\\,\\\\+\\\\\*\\ \\? \\ \\ &lt; \\ \\ &gt; \]) "," ",), 1, 20)," ([\\\\.) \* \$] (file:///\\.) *$)", , "", , )**      |    sAMAccountName            |     |         Zapisane podczas tworzenia tylko |
| **Przełącznik (\[Active\],, "0", "True", "1")** |  accountDisabled      |     | Tworzenie i aktualizowanie |
| **Imię**   | givenName       |     |    Tworzenie i aktualizowanie |
| **Nazwisko**   |   numery seryjne   |     |  Tworzenie i aktualizowanie |
| **PreferredNameData**  |  displayName |     |   Tworzenie i aktualizowanie |
| **Firmy**         | Firmy   |     |  Tworzenie i aktualizowanie |
| **SupervisoryOrganization**  | Dział  |     |  Tworzenie i aktualizowanie |
| **ManagerReference**   | menedżer  |     |  Tworzenie i aktualizowanie |
| **BusinessTitle**   |  tytuł     |     |  Tworzenie i aktualizowanie | 
| **AddressLineData**    |  Adres  |     |   Tworzenie i aktualizowanie |
| **Jednostki administracyjnej**   |   l   |     | Tworzenie i aktualizowanie |
| **CountryReferenceTwoLetter**      |   co |     |   Tworzenie i aktualizowanie |
| **CountryReferenceTwoLetter**    |  c  |     |         Tworzenie i aktualizowanie |
| **CountryRegionReference** |  St     |     | Tworzenie i aktualizowanie |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Tworzenie i aktualizowanie |
| **PostalCode**  |   postalCode  |     | Tworzenie i aktualizowanie |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Tworzenie i aktualizowanie |
| **Faksów**      | facsimileTelephoneNumber     |     |    Tworzenie i aktualizowanie |
| **Mobile**  |    Telefon komórkowy       |     |       Tworzenie i aktualizowanie |
| **LocalReference** |  preferredLanguage  |     |  Tworzenie i aktualizowanie |                                               
| **Przełącznik (\[gmina\], "jednostki Organizacyjnej użytkownicy standardowi, OU = = jednostka Organizacyjna użytkowników = Default, jednostki Organizacyjnej lokalizacji, DC = = contoso, DC = com", "Dallas", "jednostki Organizacyjnej użytkownicy standardowi, OU = = jednostka Organizacyjna użytkowników Dallas, OU = lokalizacji, DC = = contoso, DC = com", "Austin", "jednostki Organizacyjnej użytkownicy standardowi, OU = = Jednostka Organizacyjna użytkowników Austin, OU = lokalizacji, DC = = contoso, DC = com ","Seattle"," jednostki Organizacyjnej użytkowników standardowych, OU = = jednostka Organizacyjna użytkowników Seattle, OU = lokalizacji, DC = = contoso, DC = com ","Londyn"," jednostki Organizacyjnej użytkownicy standardowi, OU = = jednostka Organizacyjna użytkowników Londyn, OU = lokalizacji, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Tworzenie i aktualizowanie |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Część 3: Konfigurowanie lokalnego agenta synchronizacji

Aby można było aprowizować do usługi Active Directory w środowisku lokalnym, na serwerze przyłączonym do domeny w wymaganą lasu usługi Active Directory musi być zainstalowany agent. Administrator domeny (lub administratora przedsiębiorstwa) są wymagane poświadczenia, aby ukończyć tę procedurę.

**[Można pobrać tutaj agenta synchronizacji w środowisku lokalnym](https://go.microsoft.com/fwlink/?linkid=847801)**

Po zainstalowaniu agenta, uruchom poniższe polecenia programu Powershell można skonfigurować agenta w danym środowisku.

**Polecenie #1**

> Agent "C:\Program Files\Microsoft Azure AD Connect, inicjowanie obsługi administracyjnej Agent\Modules\AADSyncAgent" CD\\modułów\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**Polecenie #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Dane wejściowe: "Nazwa katalogu", wprowadź nazwę lasu usługi AD, tak jak zostały wprowadzone w części \#2
* Dane wejściowe: Nazwę i hasło administratora dla lasu usługi Active Directory

>[!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie "nie można nawiązać relacji między domenę podstawową i zaufaną domenę", to, że komputer lokalny znajduje się w środowisku wielu lasów usługi Active Directory lub domeny są skonfigurowane, oraz co najmniej jedna skonfigurowana zaufania Relacja jest, ile niepowodzeniem lub nie działa. Aby rozwiązać ten problem, Popraw lub Usuń relację zerwanie relacji zaufania.

**Polecenie #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Dane wejściowe: Nazwa użytkownika administratora globalnego i hasło dla dzierżawy usługi Azure AD

>[!IMPORTANT]
>Obecnie jest to znany problem przy użyciu poświadczeń administratora globalnego nie działa, jeśli korzystają z domeny niestandardowej (przykład: admin@contoso.com). Jako obejście, Utwórz i użyć konta administratora globalnego z domeny onmicrosoft.com (przykład: admin@contoso.onmicrosoft.com)

>[!IMPORTANT]
>Obecnie jest to znany problem przy użyciu poświadczeń administratora globalnego nie działa, jeśli mają włączonego uwierzytelniania wieloskładnikowego. Jako obejście Wyłącz uwierzytelnianie wieloskładnikowe dla administratora globalnego.

**Polecenie #4**

> Get-AdSyncAgentProvisioningTasks

* Akcja: Upewnij się, że dane są zwracane. To polecenie automatycznie odnajduje Workday inicjowania obsługi administracyjnej aplikacji w dzierżawie usługi Azure AD. Przykładowe dane wyjściowe:

> Nazwa: Mojego lasu usługi AD
>
> Włączono: True
>
> DirectoryName: mydomain.contoso.com
>
> Dysponujący: False
>
> Identyfikator: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Polecenie #5**

> Start-AdSyncAgentSynchronization -Automatic

**Polecenie #6**

> polecenie net stop aadsyncagent

**Polecenie #7**

> polecenie net start aadsyncagent

>[!TIP]
>Oprócz polecenia "net", w programie Powershell, usługi agenta synchronizacji można też uruchomić i zatrzymać przy użyciu **Services.msc**. Jeśli występują błędy podczas uruchamiania polecenia programu Powershell, upewnij się, że **agenta Microsoft Azure AD Connect aprowizacji** działa w **Services.msc**.

![Usługi](./media/workday-inbound-tutorial/Services.png)  

**Dodatkowa konfiguracja dla klientów w Unii Europejskiej**

Jeśli Twoja dzierżawa usługi Azure Active Directory znajduje się w jednej z centrów danych Unii Europejskiej, należy wykonać dodatkowe czynności.

1. Otwórz **Services.msc**i Zatrzymaj **agenta Microsoft Azure AD Connect aprowizacji** usługi.
2. Przejdź do folderu instalacji agenta (przykład: C:\Program Files\Microsoft Azure aprowizacji agenta programu AD Connect).
3. Otwórz **SyncAgnt.exe.config** w edytorze tekstów.
4. Zastąp https://manage.hub.syncfabric.windowsazure.com/Management z **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Zastąp https://provision.hub.syncfabric.windowsazure.com/Provisioning z **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Zapisz **SyncAgnt.exe.config** pliku.
7. Otwórz **Services.msc**i Rozpocznij **agenta Microsoft Azure AD Connect aprowizacji** usługi.

**Rozwiązywanie problemów z agentem**

[Dziennika zdarzeń Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) w systemie Windows Server maszyny hostującej agenta zawiera zdarzenia dla wszystkich operacji wykonywanych przez agenta. Aby wyświetlić te zdarzenia:
    
1. Otwórz **Eventvwr.msc**.
2. Wybierz **Dzienniki Windows > Aplikacja**.
3. Wyświetl wszystkie zdarzenia zarejestrowane w lokalizacji source **AADSyncAgent**. 
4. Sprawdź, czy błędy i ostrzeżenia.

W przypadku problemu uprawnień przy użyciu poświadczeń usługi Active Directory lub Azure Active Directory, podany w poleceniach programu Powershell zostanie wyświetlony błąd, taką jak ta: 
    
![Dzienniki zdarzeń](./media/workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Część 4: Uruchom usługę
Po ukończeniu części 1 – 3 można uruchomić usługi aprowizacji w witrynie Azure portal.

1.  W **aprowizacji** kartę, należy ustawić **stanie aprowizacji** do **na**.

2. Kliknij pozycję **Zapisz**.

3. Spowoduje to uruchomienie synchronizacji początkowej, co może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w produkcie Workday.

4. W każdej chwili sprawdzić **dzienniki inspekcji** kartę w witrynie Azure portal, aby zobaczyć, jakie akcje przeprowadził usługi aprowizacji. Dzienniki inspekcji zawiera listę wszystkich zdarzeń indywidualnych synchronizacji, wykonywane przez usługę aprowizacji, np użytkowników ich odczytu z produktu Workday i następnie później dodane lub zaktualizowane w usłudze Active Directory. **[Zobacz Przewodnik po raportowaniu inicjowania obsługi administracyjnej szczegółowe instrukcje dotyczące sposobu odczytywania dzienników inspekcji](../manage-apps/check-status-user-account-provisioning.md)**

1.  Sprawdź [dziennika zdarzeń Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) na komputerze z serwerem Windows uruchomiony jest agent pod kątem nowych błędów lub ostrzeżeń. Te zdarzenia są widoczne, uruchamiając **Eventvwr.msc** na serwerze i wybierając polecenie **Dzienniki Windows > Aplikacja**. Wszystkie wiadomości dotyczące inicjowania obsługi administracyjnej są rejestrowane w lokalizacji source **AADSyncAgent**.

6. Jeden ukończone, będą zapisywane podsumowania raport dotyczący inspekcji **aprowizacji** karty, jak pokazano poniżej.

![Azure Portal](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Konfigurowaniem aprowizowania użytkowników w usłudze Azure Active Directory
Jak skonfigurować aprowizację usługi Azure Active Directory będzie zależeć od wymagań dotyczących inicjowania obsługi administracyjnej, zgodnie z opisem w poniższej tabeli.

| Scenariusz | Rozwiązanie |
| -------- | -------- |
| **Użytkownicy muszą być przygotowana do usługi Active Directory a usługą Azure AD** | Użyj  **[program AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Użytkownicy muszą być przygotowana do usługi Active Directory tylko** | Użyj  **[program AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Użytkownicy muszą być przygotowana do usługi Azure AD tylko (tylko w chmurze)** | Użyj **Workday do inicjowania obsługi usługi Azure Active Directory** aplikacji w galerii aplikacji |

Aby uzyskać instrukcje na temat konfigurowania usługi Azure AD Connect, zobacz [dokumentacja usługi Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

W poniższych sekcjach opisano konfigurowanie połączenia między produktem Workday i Azure AD, inicjowania obsługi użytkowników tylko w chmurze.

> [!IMPORTANT]
> Tylko wykonaj poniższą procedurę, jeśli masz tylko na chmurze użytkowników, którzy muszą być przygotowana do usługi Azure AD i nie lokalnej usługi Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie aplikacji łącznika inicjowania obsługi usługi Azure AD i tworzenia połączenia z produktem Workday

**Aby skonfigurować produktu Workday do usługi Azure Active Directory inicjowania obsługi administracyjnej dla użytkowników tylko w chmurze:**

1.  Przejdź do pozycji <https://portal.azure.com> (Plik > Nowy > Inny).

2.  Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3.  Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4.  Wybierz **dodać aplikację**, a następnie wybierz pozycję **wszystkich** kategorii.

5.  Wyszukaj **produktu Workday do inicjowania obsługi usługi Azure AD**i Dodaj tę aplikację z galerii.

6.  Po dodaniu aplikacji i ekran szczegółów aplikacji jest pokazywane, wybierz opcję **aprowizacji**

7.  Zmiana **aprowizacji** **tryb** do **automatyczne**

8.  Wykonaj **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemowego integracji produktu Workday za pomocą dołączona nazwa domeny dzierżawy. Powinien wyglądać następująco: username@contoso4

   * **Hasło administratora —** wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla Twojej dzierżawy. Powinno to wyglądać podobnie do: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, gdzie contoso4 jest zastępowana nazwą dzierżawy poprawne i wd3 impl jest zastępowany ciągiem odpowiednie środowisko. Jeśli ten adres URL nie jest znany, skontaktuj się z produktem Workday integracji pomocy technicznej lub partner przedstawicielem, aby określić prawidłowy adres URL do użycia.

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku.

   * Jeśli test połączenia powiedzie się, kliknij przycisk **Zapisz** znajdujący się u góry. Jeśli nie powiedzie się, należy upewnić się, że adres URL produktu Workday i poświadczenia są prawidłowe w produkcie Workday.

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowania atrybutów 

W tej sekcji skonfigurujesz, jak użytkownik przepływu danych z produktu Workday do usługi Azure Active Directory dla użytkowników tylko w chmurze.

1. Na karcie Udostępnianie w obszarze **mapowania**, kliknij przycisk **zsynchronizować pracowników do usługi Azure AD**.

2. W **zakres obiektów źródłowych** pola, można wybrać, który konfiguruje użytkowników w usłudze WORKDAY aplikacja powinna być w zakresie aprowizacji usługi Azure AD, definiując zestaw opartych na atrybutach filtrów. Domyślny zakres jest "wszyscy użytkownicy w produkcie Workday". Przykładowe filtry:

   * Przykład: Zakres użytkowników z identyfikatorami procesów roboczych między 1000000 i 2000000

      * Atrybut: WorkerID

      * Operator: Dopasowanie wyrażenia REGULARNEGO

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko tymczasowi pracownicy i nie stałych pracowników

      * Atrybut: ContingentID

      * Operator: Nie ma wartości NULL

3. W **Akcje obiektu docelowego** pole globalnie filtrowania, jakie akcje mogą być wykonywane w usłudze Azure AD. **Tworzenie** i **aktualizacji** występują najczęściej.

4. W **mapowania atrybutów** sekcji można zdefiniować Workday jak poszczególne atrybuty mapowania atrybutów usługi Active Directory.

5. Kliknij na istniejące mapowanie atrybutu go zaktualizować, lub kliknij **Dodaj nowe mapowanie** w dolnej części ekranu, aby dodać nowe mapowania. Mapowanie atrybutu poszczególnych obsługuje następujące właściwości:

   * **Typ mapowania**

      * **Bezpośrednie** — zapisuje wartość atrybutu produktu Workday do atrybutu usługi AD, bez konieczności wprowadzania zmian

      * **Stałe** -zapisu wartość statyczne, stała ciągu atrybutu usługi AD

      * **Wyrażenie** — pozwala na zapis niestandardową wartość do atrybutu usługi AD na podstawie co najmniej jeden dzień roboczy atrybutów. [Aby uzyskać więcej informacji znajduje się w artykule na wyrażeniach](../manage-apps/functions-for-customizing-application-data.md).

   * **Atrybut źródłowy** — atrybut użytkownika z produktu Workday. Jeśli nie ma atrybut, którego szukasz, zobacz [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](#customizing-the-list-of-workday-user-attributes).

   * **Wartość domyślna** — opcjonalne. Jeśli atrybut źródłowy ma wartość pustą, mapowanie zapisu wtedy tę wartość.
            Najbardziej typowa konfiguracja polega na pozostaw to pole puste.

   * **Atrybut docelowy** — atrybut użytkownika w usłudze Azure AD.

   * **Zgodne obiekty korzystające z tego atrybutu** — czy to mapowanie powinien być używany do jednoznacznego identyfikowania użytkowników między produktem Workday i Azure AD. Jest to zazwyczaj ustawiana na pole Identyfikatora procesu roboczego dla produktu Workday, która jest przeważnie mapowana identyfikator pracownika, atrybut (nowy) lub atrybut rozszerzenia w usłudze Azure AD.

   * **Pierwszeństwo dopasowania** — wiele pasujące atrybuty można ustawić. Jeśli dostępnych jest wiele, są one obliczane w kolejności, zdefiniowanych przez to pole. Gdy tylko zostanie znalezione dopasowanie, żadne dodatkowe dopasowania atrybuty są oceniane.

   * **Zastosuj to mapowanie**

     * **Zawsze** — Zastosuj to mapowanie na obu tworzenia użytkownika i aktualizowanie działań

     * **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w akcji tworzenia użytkownika

6. Aby zapisać mapowania, kliknij przycisk **Zapisz** w górnej części mapowanie atrybutu.

### <a name="part-3-start-the-service"></a>Część 3: Rozpoczęcie usługi
Po ukończeniu części 1 – 2 można uruchomić usługi aprowizacji.

1. W **aprowizacji** kartę, należy ustawić **stanie aprowizacji** do **na**.

2. Kliknij pozycję **Zapisz**.

3. Spowoduje to uruchomienie synchronizacji początkowej, co może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w produkcie Workday.

4. Synchronizuj poszczególne zdarzenia mogą być wyświetlane w **dzienników inspekcji** kartę. **[Zobacz Przewodnik po raportowaniu inicjowania obsługi administracyjnej szczegółowe instrukcje dotyczące sposobu odczytywania dzienników inspekcji](../manage-apps/check-status-user-account-provisioning.md)**

5. Jeden ukończone, będą zapisywane podsumowania raport dotyczący inspekcji **aprowizacji** karty, jak pokazano poniżej.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurowanie funkcji zapisywania zwrotnego adresów e-mail w produkcie Workday
Wykonaj te instrukcje, aby skonfigurować zapisywanie zwrotne adresy e-mail użytkowników z usługi Azure Active Directory w produkcie Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie obsługi administracyjnej aplikacji łącznika i tworzenia połączenia z produktem Workday

**Aby skonfigurować produktu Workday do inicjowania obsługi usługi Active Directory:**

1. Przejdź do strony <https://portal.azure.com>

2. Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3. Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4. Wybierz **dodać aplikację**, a następnie wybierz **wszystkich** kategorii.

5. Wyszukaj **zapisywania zwrotnego Workday**i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i ekran szczegółów aplikacji jest pokazywane, wybierz opcję **aprowizacji**

7. Zmiana **aprowizacji** **tryb** do **automatyczne**

8. Wykonaj **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemowego integracji produktu Workday za pomocą dołączona nazwa domeny dzierżawy. Powinien wyglądać następująco: username@contoso4

   * **Hasło administratora —** wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla Twojej dzierżawy. Powinno to wyglądać podobnie do: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, gdzie contoso4 jest zastępowana nazwą dzierżawy poprawne i wd3 impl jest zastępowany ciągiem odpowiednie środowisko (jeśli jest to konieczne).

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku. Jeśli test połączenia powiedzie się, kliknij przycisk **Zapisz** znajdujący się u góry. Jeśli nie powiedzie się, należy upewnić się, że adres URL produktu Workday i poświadczenia są prawidłowe w produkcie Workday.

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowania atrybutów 

W tej sekcji skonfigurujesz przepływ danych użytkownika z produktu Workday do usługi Active Directory.

1. Na karcie Udostępnianie w obszarze **mapowania**, kliknij przycisk **synchronizować użytkowników platformy Azure AD w produkcie Workday**.

2. W **zakres obiektów źródłowych** pola, można również filtrować które zestawów użytkowników w usłudze Azure Active Directory powinny mieć ich adresy e-mail zapisywane z powrotem w produkcie Workday. Domyślny zakres jest "wszyscy użytkownicy w usłudze Azure AD". 

3. W **mapowania atrybutów** sekcji, zaktualizuj identyfikator pasującego do wskazania to atrybut usługi Azure Active Directory, gdzie jest przechowywany identyfikator procesu roboczego Workday lub identyfikator pracownika. Popularne metody dopasowania jest synchronizowane z produktem Workday, identyfikator procesu roboczego lub identyfikator pracownika do extensionAttribute1 15 w usłudze Azure AD, a następnie użyć tego atrybutu w usłudze Azure AD, aby dopasować użytkowników z powrotem w produkcie Workday. 

4. Aby zapisać mapowania, kliknij przycisk **Zapisz** w górnej części mapowanie atrybutu.

### <a name="part-3-start-the-service"></a>Część 3: Rozpoczęcie usługi
Po ukończeniu części 1 – 2 można uruchomić usługi aprowizacji.

1. W **aprowizacji** kartę, należy ustawić **stanie aprowizacji** do **na**.

2. Kliknij pozycję **Zapisz**.

3. Spowoduje to uruchomienie synchronizacji początkowej, co może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w produkcie Workday.

4. Synchronizuj poszczególne zdarzenia mogą być wyświetlane w **dzienników inspekcji** kartę. **[Zobacz Przewodnik po raportowaniu inicjowania obsługi administracyjnej szczegółowe instrukcje dotyczące sposobu odczytywania dzienników inspekcji](../manage-apps/check-status-user-account-provisioning.md)**

5. Jeden ukończone, będą zapisywane podsumowania raport dotyczący inspekcji **aprowizacji** karty, jak pokazano poniżej.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Dostosowywanie listy atrybutów użytkownika w produkcie Workday
Dzień roboczy aprowizacji aplikacji dla usługi Active Directory i usługi Azure AD oferują domyślną listę atrybutów użytkowników z produktu Workday możesz wybrać z. Jednak te listy nie są wyczerpujące. Dzień roboczy obsługuje wiele setki użytkowników możliwe atrybuty, które mogą być standardowe lub unikatowa dla dzierżawy produktu Workday. 

Usługa aprowizacji usługi Azure AD obsługuje możliwość dostosowania listy lub atrybut produktu Workday do uwzględnienia wszelkie atrybuty ujawnione w [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operacji interfejsu API zarządzania zasobami ludzkimi.

Aby to zrobić, należy użyć [Workday Studio](https://community.workday.com/studio-download) można wyodrębnić wyrażenia XPath, które reprezentują atrybutów, o których chcesz użyć, a następnie dodaj je do konfiguracji aprowizacji za pomocą edytora zaawansowanego atrybutu w witrynie Azure portal.

**Aby pobrać wyrażenie XPath dla produktu Workday atrybutu użytkownika:**

1. Pobierz i zainstaluj [Workday Studio](https://community.workday.com/studio-download). Konieczne będzie Workday społeczności dostęp konta do Instalatora.

2. Pobierz plik produktu Workday Human_Resources WDSL spod tego adresu URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Uruchom program Workday Studio.

4. Na pasku poleceń Wybierz **Workday > Test usługi sieci Web w Tester** opcji.

5. Wybierz **zewnętrznych**i wybierz pobrany plik Human_Resources WSDL w kroku 2.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio1.PNG)

6. Ustaw **lokalizacji** pole `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale zastąpieniu "IMPL-CC" faktycznego typu wystąpienia, a "DZIERŻAWCA" nazwą rzeczywistego dzierżawy.

7. Ustaw **operacji** do **Get_Workers**

8.  Kliknij małą **skonfigurować** link poniżej okienka żądanie/odpowiedź, aby ustawić poświadczenia dla produktu Workday. Sprawdź **uwierzytelniania**, a następnie wprowadź nazwę użytkownika i hasło dla konta system integracji produktu Workday. Pamiętaj format nazwy użytkownika jako name@tenanti pozostawić **UsernameToken WS-Security** wybraną opcją.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio2.PNG)

9. Kliknij przycisk **OK**.

10. **Żądania** okienku wklejanie w XML poniżej i ustaw **Employee_ID** identyfikator pracownika rzeczywistego użytkownika w ramach dzierżawy produktu Workday. Wybierz użytkownika, który ma atrybut wypełnione chcesz wyodrębnić.

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
 
11. Kliknij przycisk **Wyślij żądanie** (zielonej strzałki) można wykonać polecenia. Jeśli operacja się powiedzie, odpowiedź powinna zostać wyświetlona w **odpowiedzi** okienka. Sprawdź odpowiedzi, upewnij się, że dane wprowadzony identyfikator użytkownika, a nie błąd.

12. Jeśli to się powiedzie, skopiuj plik XML z **odpowiedzi** okienka i zapisz go jako plik XML.

13. W programu polecenie paska dla produktu Workday Studio, wybierz **Plik > Otwórz plik...**  , a następnie otwórz plik XML został zapisany. To otwiera go w edytorze XML programu Studio produktu Workday.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio3.PNG)

14. W drzewie pliku nawigowania **/ENV: koperty > env: treść > wd:Get_Workers_Response > wd:Response_Data > wd: proces roboczy** wyszukiwania danych użytkownika. 

15. W obszarze **wd: proces roboczy**Znajdź atrybut, który chcesz dodać i wybierz ją.

16. Skopiuj wyrażenie XPath dla Twojej wybrany atrybut poza **ścieżka dokumentu** pola.

1. Usuń **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data /** prefiksu kopiowane wyrażenia.

18. Jeśli ostatni element w wyrażeniu skopiowany jest węzłem (przykład: "/ wd: Birth_Date"), następnie dołącz **/text()** na końcu wyrażenia. Nie jest to konieczne, jeśli ostatni element jest atrybutem (przykład: "/@wd: typu").

19. Wynik powinien być podobny do `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Jest to, co zostanie skopiowany do witryny Azure portal.


**Aby dodać swoje niestandardowy atrybut użytkownika produktu Workday do inicjowania obsługi administracyjnej konfiguracji:**

1. Uruchom [witryny Azure portal](https://portal.azure.com), a następnie przejdź do sekcji Provisioning dnia roboczego Inicjowanie obsługi administracyjnej aplikacji, zgodnie z opisem we wcześniejszej części tego samouczka.

2. Ustaw **stanie aprowizacji** do **poza**i wybierz **Zapisz**. Dzięki temu, upewnij się, że zmiany zostały zastosowane tylko wtedy, gdy wszystko będzie gotowe.

3. W obszarze **mapowania**, wybierz opcję **zsynchronizować pracownikom opłacanie** (lub **zsynchronizować pracowników do usługi Azure AD**).

4. Przewiń w dół następnego ekranu, a następnie wybierz pozycję **Pokaż opcje zaawansowane**.

5. Wybierz **Edytuj listę atrybutów dla produktu Workday**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Przewiń w dół listy atrybutów, aby wskazać pól wejściowych.

7. Aby uzyskać **nazwa**, wprowadź nazwę wyświetlaną dla atrybutu użytkownika.

8. Dla **typu**, wybierz typ odpowiadający odpowiednio atrybut (**ciąg** przeważnie).

9. Aby uzyskać **wyrażenie interfejsu API**, wprowadź skopiowany z produktu Workday Studio wyrażenie XPath. Przykład: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wybierz **Dodaj atrybut**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Wybierz **Zapisz** powyżej, a następnie **tak** do okna dialogowego. Zamknij ekran mapowanie atrybutu, jeśli jest nadal otwarty.

12. Po powrocie na główny **aprowizacji** zaznacz **zsynchronizować pracownikom opłacanie** (lub **zsynchronizować pracowników do usługi Azure AD**) ponownie.

13. Wybierz **Dodaj nowe mapowanie**.

14. Nowy atrybut powinien zostać wyświetlony na **atrybut źródłowy** listy.

15. Dodaj mapowanie dla użytkownika nowego atrybutu zgodnie z potrzebami.

16. Gdy skończysz, pamiętaj, aby ustawić **stanie aprowizacji** do **na** i Zapisz.

## <a name="known-issues"></a>Znane problemy

* Podczas uruchamiania **ADSyncAgentAzureActiveDirectoryConfiguration Dodaj** polecenia programu Powershell, obecnie jest to znany problem przy użyciu poświadczeń administratora globalnego nie działa, jeśli korzystają z domeny niestandardowej (przykład: admin@contoso.com) . Jako obejście, Utwórz i użyć konta administratora globalnego w usłudze Azure AD z domeny onmicrosoft.com (przykład: admin@contoso.onmicrosoft.com).

* Zapisywanie danych do atrybutu thumbnailphoto usługa użytkownika w usłudze Active Directory w środowisku lokalnym nie jest obecnie obsługiwane.

* Łącznik "Workday do usługi Azure AD" nie jest obecnie obsługiwane w przypadku dzierżaw usługi Azure AD, w której włączona jest narzędziem AAD Connect.  

* Poprzedni problem z dziennikami inspekcji, które nie są widoczne w dzierżawach usługi Azure AD na terenie Unii Europejskiej został rozwiązany. Jednak jest wymagana w przypadku dzierżaw usługi Azure AD w UE Konfiguracja Dodatkowi agenci. Aby uzyskać więcej informacji, zobacz [część 3: Konfigurowanie lokalnego agenta synchronizacji](#Part 3: Configure the on-premises synchronization agent)

## <a name="managing-personal-data"></a>Zarządzanie danymi osobowymi

Workday, inicjowanie obsługi administracyjnej rozwiązania dla usługi Active Directory wymaga agenta synchronizacji, należy zainstalować na serwerze przyłączonym do domeny, a ten agent tworzy dzienniki w dzienniku zdarzeń Windows, które mogą zawierać dane osobowe.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować logowanie jednokrotne między produktem Workday i Azure Active Directory](workday-tutorial.md)
* [Dowiedz się, jak zintegrować innych aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)

