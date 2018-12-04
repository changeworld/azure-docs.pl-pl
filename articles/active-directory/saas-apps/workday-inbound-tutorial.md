---
title: 'Samouczek: Konfigurowanie produktu Workday do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników w produkcie Workday.
services: active-directory
author: cmmdesai
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
ms.author: chmutali
ms.openlocfilehash: 754c3278cb01e010718fa4d3cb257acf6ffe99c9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849857"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Samouczek: Konfigurowanie produktu Workday do inicjowania obsługi administracyjnej użytkowników (wersja zapoznawcza)

Celem tego samouczka jest Wam czynności, które należy wykonać, aby zaimportować profile procesu roboczego z produktu Workday do usługi Active Directory i Azure Active Directory, za pomocą opcjonalnych zapisywania zwrotnego adresu e-mail w produkcie Workday.

## <a name="overview"></a>Przegląd

[Usługa aprowizowania użytkowników w usłudze Azure Active Directory](../manage-apps/user-provisioning.md) integruje się z [interfejsu API zarządzania zasobami ludzkimi Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) w celu dostarczania kont użytkowników. Usługa Azure AD używa tego połączenia, aby włączyć następujący użytkownik przepływy pracy aprowizacji:

* **Inicjowanie obsługi administracyjnej użytkowników do usługi Active Directory** — Synchronizuj wybrane zestawy użytkowników z produktu Workday do jednej lub kilku domen usługi Active Directory.

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

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji opisano użytkownika end-to-end, inicjowanie obsługi administracyjnej architekturę rozwiązania dla typowych środowisk hybrydowych. Istnieją dwa z procedurami:

* **Autorytatywny przepływ danych HR — z produktu Workday do usługi Active Directory w środowisku lokalnym:** w tym przepływie, zdarzeń procesu roboczego (na przykład nowi pracownicy w przypadku transferów zakończeń) najpierw występują w chmurze dzierżawy produktu Workday HR, a następnie dane zdarzeń są przesyłane aktywny w środowisku lokalnym Katalogu za pomocą usługi Azure AD i agenta inicjowania obsługi administracyjnej. W zależności od zdarzenia może prowadzić do operacji tworzenia/aktualizacji/Włączanie/wyłączanie w AD.
* **Wiadomości e-mail przepływu zapisywanie zwrotne — z lokalnej usługi Active Directory w produkcie Workday:** po zakończeniu tworzenia konta w usłudze Active Directory jest synchronizowana z usługą Azure AD za pomocą usługi Azure AD Connect i atrybut poczty e-mail, skąd pochodzi z usługi Active Directory mogą być zapisywane z powrotem w produkcie Workday.

![Przegląd](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Przepływ danych użytkownika end-to-end

1. Zespół działu KADR wykonuje procesu roboczego w transakcji (Łączniki/firmy przeprowadzkowej/Leavers lub nowych pracowników/transfery/zakończeń) w produkcie Workday HCM
2. Usługa Azure AD aprowizacji uruchamia zaplanowanej synchronizacji tożsamości z produktu Workday HR i identyfikuje zmiany, które muszą być przetworzone w celu synchronizacji z usługą Active Directory w środowisku lokalnym.
3. Usługa Azure AD aprowizacji wywołuje w środowisku lokalnym usługi AAD Connect aprowizacji agenta z ładunkiem żądania zawiera operacje tworzenia/aktualizacji/Włączanie/wyłączanie konta usługi AD.
4. Agent programu Azure AD Connect inicjowania obsługi używa konta usługi można dodać/zaktualizować dane konta usługi Active Directory.
5. Program Azure AD Connect / synchronizacja różnicowa w celu ściągania aktualizacji w AD działa aparat synchronizacji usługi AD.
6. Aktualizacje usługi Active Directory są synchronizowane z usługą Azure Active Directory.
7. Jeśli skonfigurowano łącznik produktu Workday funkcji zapisywania zwrotnego, go zapisywanie zwrotne atrybut poczty e-mail w produkcie Workday, na podstawie atrybutu zgodnego używane.


## <a name="planning-your-deployment"></a>Planowanie wdrożenia

Przed rozpoczęciem integracji z produktem Workday, sprawdź poniższe wymagania wstępne, a następnie przeczytaj poniższe wskazówki na temat sposobu spełnić bieżące architektury usługi Active Directory i inicjowania obsługi wymagań przy użyciu rozwiązania dostarczane przez usługę Azure Active Directory użytkownika.

### <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Ważną subskrypcją usługi Azure AD Premium P1 uprawnienia dostępu administratora globalnego
* Dzierżawy produktu Workday wdrożenia na potrzeby testowania i integracji
* Uprawnienia administratora w usłudze WORKDAY aplikacja do tworzenia użytkownika integracji systemu i wprowadzić zmiany do przetestowania danych pracownika do celów testowych
* Do aprowizowania użytkowników w usłudze Active Directory, serwer z systemem Windows Server 2012 lub nowszej za pomocą platformy .NET 4.7 środowiska wykonawczego jest wymagana do hosta [agenta inicjowania obsługi administracyjnej lokalnych](https://go.microsoft.com/fwlink/?linkid=847801)
* [Program Azure AD Connect](../hybrid/whatis-hybrid-identity.md) synchronizacji od usługi Active Directory a usługą Azure AD

### <a name="planning-considerations"></a>Zagadnienia dotyczące planowania

Usługa Azure AD zapewnia bogaty zestaw aprowizacji łączników, aby pomóc w określeniu inicjowania obsługi administracyjnej i cyklu życia zarządzania tożsamościami z produktu Workday do usługi Active Directory, usługi Azure AD aplikacji SaaS i nie tylko. Która obejmuje będzie używać i jak skonfigurować rozwiązanie różnią się w zależności od środowiska i wymagań swojej organizacji. Pierwszym krokiem wykonaj zasobów jak wiele z następujących czynności są dostępne i wdrożone w Twojej organizacji:

* Ile lasy usługi Active Directory są używane?
* Jak wiele domen usługi Active Directory są używane?
* Ile Active Directory jednostki organizacyjne (OU) są używane?
* Jak wiele dzierżaw usługi Azure Active Directory są używane?
* Czy istnieją użytkownicy, którzy muszą być przygotowana do usługi Active Directory i Azure Active Directory (na przykład "hybrydowe" użytkowników)?
* Czy istnieją użytkownicy, którzy muszą być przygotowana do usługi Azure Active Directory, ale nie Active Directory (na przykład "chmura tylko do" użytkowników)?
* Czy adresy e-mail użytkowników muszą być zapisywane z powrotem w produkcie Workday?

Po uzyskaniu odpowiedzi na te pytania, możesz zaplanować pracy inicjowania obsługi wdrożenia, wykonując poniższe wskazówki.

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>Planowanie wdrożenia usługi AAD Connect aprowizacji agenta

Produktu Workday do aprowizacji użytkowników usługi AD rozwiązania wymaga wdrożenia co najmniej jednego agenta inicjowania obsługi na serwerach z systemem Windows 2012 R2 lub nowszego z co najmniej 4 GB pamięci RAM i platformy .NET 4.7 środowiska uruchomieniowego. Następujące zagadnienia dotyczące musi być brana pod uwagę przed zainstalowaniem agenta inicjowania obsługi administracyjnej:

* Upewnij się, że serwer hosta z agentem Aprowizowania ma dostęp do sieci do domeny docelowej usługi AD
* Kreator konfiguracji agenta inicjowania obsługi administracyjnej rejestruje agenta dzierżawy usługi Azure AD i procesu rejestracji wymaga dostępu do *. msappproxy.net w porcie 8082. Upewnij się, że reguły zapory dla ruchu wychodzącego w miejscu, pozwalających na korzystanie z tej komunikacji.
* Agenta inicjowania obsługi używa konta usługi do komunikacji z lokalnej domeny usługi AD. Przed zainstalowaniem agenta zalecane jest, Utwórz konto usługi przy użyciu uprawnienia odczytu/zapisu właściwości użytkownika i hasło, które nie wygasa.  
* Podczas konfigurowania aprowizacji agenta możesz wybrać kontrolerów domeny, które ma obsługiwać żądania udostępniania. Jeśli masz kilka kontrolerów domeny geograficznie rozproszonych, należy zainstalować agenta inicjowania obsługi administracyjnej, w tym samym miejscu co Twoje kontrolerach domeny preferowanych, aby zwiększyć niezawodność i wydajność rozwiązania end-to-end
* Wysoką dostępność, możesz wdrożyć więcej niż jednego agenta aprowizacji i zarejestruj go do obsługi tego samego zestawu w środowisku lokalnym domeny usługi AD.

> [!IMPORTANT]
> W środowiskach produkcyjnych firma Microsoft zaleca, że masz co najmniej 3 agentów aprowizacji skonfigurowany z dzierżawą usługi Azure AD w celu zapewnienia wysokiej dostępności.

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Wybierania inicjowania obsługi administracyjnej aplikacji łącznik do wdrożenia

Podczas integracji z produktem Workday i usługi Active Directory, istnieje wiele systemów źródłowych i docelowych, aby zostały uznane za:

| System źródłowy | System docelowy | Uwagi |
| ---------- | ---------- | ---------- |
| Dzień roboczy | Domeny usługi Active Directory | Każda domena jest traktowany jako systemu docelowego odrębne |
| Dzień roboczy | Dzierżawy usługi Azure AD | Zgodnie z wymaganiami dla użytkowników tylko w chmurze |
| Las usługi Active Directory | Dzierżawy usługi Azure AD | Ten przepływ jest obecnie obsługiwane przez AAD Connect |
| Dzierżawy usługi Azure AD | Dzień roboczy | Dla zapisu adresy e-mail |

W celu ułatwienia inicjowania obsługi administracyjnej przepływy pracy między produktem Workday i usługi Active Directory, usługa Azure AD zapewnia wiele inicjowania obsługi administracyjnej aplikacji łącznika, które można dodawać z galerii aplikacji Azure AD:

![Galeria aplikacji usługi AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **WORKDAY do udostępniania usługi Active Directory** — ta aplikacja ułatwia aprowizowaniem kont użytkowników z produktu Workday do pojedynczej domeny usługi Active Directory. Jeśli masz wiele domen, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji Azure AD dla każdej domeny usługi Active Directory, które należy aprowizować do.

* **WORKDAY do usługi Azure AD aprowizacji** — o ile AAD Connect to narzędzie, które mają być używane do synchronizacji usługi Active Directory, użytkowników do usługi Azure Active Directory, ta aplikacja może służyć do ułatwienia aprowizacji użytkowników tylko w chmurze z produktu Workday do pojedynczej platformy Azure Dzierżawy usługi Active Directory.

* **Zapisywanie zwrotne WORKDAY** — ta aplikacja ułatwia zapisu adresów e-mail użytkowników z usługi Azure Active Directory w produkcie Workday.

> [!TIP]
> Regularne aplikacji "Dzień roboczy" służy do konfigurowania logowania jednokrotnego między produktem Workday i Azure Active Directory. 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Określić produktu Workday do mapowania atrybutów użytkownika usługi AD i przekształcenia

Przed rozpoczęciem konfigurowania aprowizacji użytkowników do domeny usługi Active Directory, należy wziąć pod uwagę następujące kwestie. Odpowiedzi na te pytania określają, jak Twoje filtrów określania zakresu i mapowania atrybutów należy ustawić.

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



Sposób instalowania i konfigurowania tych specjalnych inicjowania obsługi administracyjnej aplikacji łącznika jest przedmiotem pozostałej części tego samouczka. Aplikacje, które użytkownik chce skonfigurować będzie zależeć od systemów, które należy aprowizować, jak wiele domen usługi Active Directory i Azure AD to dzierżawców w danym środowisku.



## <a name="configure-integration-system-user-in-workday"></a>Konfigurowanie integracji systemu użytkownika w produkcie Workday

Typowym wymogiem wszystkich łączników inicjowania obsługi administracyjnej produktu Workday jest one wymagają poświadczeń dla konta integracji systemu Workday nawiązać połączenie z interfejsu API zarządzania zasobami ludzkimi produktu Workday. Ta sekcja zawiera opis sposobu tworzenia integracji systemu użytkownika w produkcie Workday.

> [!NOTE]
> Istnieje możliwość pominąć tę procedurę i zamiast tego użyć konta administratora globalnego Workday jako konto integracji systemu. Może działać prawidłowo dla pokazów, ale nie jest zalecane w przypadku wdrożeń produkcyjnych.

### <a name="create-an-integration-system-user"></a>Utworzenie użytkownika usługi integracji systemu

**Aby utworzyć użytkownika systemowego integracji:**

1. Zaloguj się do dzierżawy produktu Workday za pomocą konta administratora. W **Workday aplikacja**, wprowadź użytkownika w polu wyszukiwania, a następnie kliknij polecenie **Create User systemu integracji**.

    ![Utwórz użytkownika](./media/workday-inbound-tutorial/wd_isu_01.png "Utwórz użytkownika")
2. Wykonaj **Create User systemu integracji** zadanie, podając nazwę użytkownika i hasło dla nowego użytkownika systemu integracji.  
 * Pozostaw **wymagają nowego hasła przy następnym Sign In** opcja nie jest zaznaczone, ponieważ ten użytkownik będzie logować się programowo.
 * Pozostaw **czas wygaśnięcia sesji w minutach** o jego wartość domyślna 0, która uniemożliwi sesji użytkownika przedwcześnie limit czasu.
 * Wybierz opcję **należy zezwolić na sesjach interfejsu użytkownika** jako zapewnia dodatkową warstwę zabezpieczeń, który uniemożliwia użytkownikiem z hasłem systemu integracji logując się do produktu Workday. 

    ![Tworzenie użytkownika systemu integracji](./media/workday-inbound-tutorial/wd_isu_02.png "tworzenie integracji systemu użytkownika")

### <a name="create-a-security-group"></a>Utwórz grupę zabezpieczeń
W tym kroku zostanie tworzenie nieograniczonego integracji systemowa grupa zabezpieczeń w usłudze WORKDAY aplikacja i przypisz użytkownika systemu integracji, które są utworzone w poprzednim kroku do tej grupy.

**Aby utworzyć grupę zabezpieczeń:**

1. Wprowadź grupy zabezpieczeń w polu wyszukiwania, a następnie kliknij polecenie **Utwórz grupę zabezpieczeń**.

    ![Grupa CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity grupy")
2. Wykonaj **Utwórz grupę zabezpieczeń** zadania.  
   * Wybierz **integracji systemowa grupa zabezpieczeń (nieograniczony)** z **typu grupy zabezpieczeń gośćmi** listy rozwijanej.

    ![Grupa CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity grupy")

3. Po pomyślnym utworzeniu grupy zabezpieczeń zostanie wyświetlona strona, na której można przypisać członków do grupy zabezpieczeń. Dodaj nowego użytkownika systemu integracji do tej grupy zabezpieczeń i wybierz zakres w organizacji odpowiednie.
![Edytuj grupę zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_05.png "Edytuj grupę zabezpieczeń")
 
### <a name="configure-domain-security-policy-permissions"></a>Skonfiguruj uprawnienia zasad zabezpieczeń domeny
W tym kroku będziesz udzielić "zabezpieczenia domeny" uprawnienia zasad dla danych procesu roboczego do grupy zabezpieczeń.

**Aby skonfigurować uprawnienia zasad zabezpieczeń domeny:**

1. Wprowadź **konfiguracji zabezpieczeń domeny** w polu wyszukiwania, a następnie kliknij link **raport dotyczący konfiguracji zabezpieczeń domeny**.  

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_06.png "zasady zabezpieczeń domeny")  
2. W **domeny** polu tekstowym Wyszukaj następujące domeny i dodać je do filtru jedno po drugim.  
   * *Inicjowanie obsługi administracyjnej konta zewnętrzne*
   * *Danych procesu roboczego: Raporty publicznych procesu roboczego*
   * *Danych osobowych: Informacje kontaktowe pracy*
   * *Danych procesu roboczego: Wszystkie pozycje*
   * *Dane procesu roboczego: Bieżące zaoszczędziła informacji*
   * *Danych procesu roboczego: Tytuł firm profil procesu roboczego*
 
    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_07.png "zasady zabezpieczeń domeny")  

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_08.png "zasady zabezpieczeń domeny") 

    Kliknij przycisk **OK**.

3. W raporcie zostaną wyświetlone, wybierz wielokropek (...), która pojawia się obok **zewnętrznej konta** i kliknij opcję menu **domeny -> Edytuj uprawnienia zasad zabezpieczeń**

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_09.png "zasady zabezpieczeń domeny")  

4. Na **Edytuj uprawnienia zasad zabezpieczeń domeny** strony, przewiń w dół do sekcji **uprawnienia integracji**. Kliknij znak "+", aby dodać grupę systemu integracji do listy grup zabezpieczeń z **uzyskać** i **umieścić** uprawnienia integracji.

    ![Uprawnienia do edycji](./media/workday-inbound-tutorial/wd_isu_10.png "uprawnienia do edycji")  

5. Kliknij znak "+", aby dodać grupę systemu integracji do listy grup zabezpieczeń z **uzyskać** i **umieścić** uprawnienia integracji.

    ![Uprawnienia do edycji](./media/workday-inbound-tutorial/wd_isu_11.png "uprawnienia do edycji")  

6. Powtórz kroki 3 – 5 powyżej dla każdej z tych pozostałych zasad zabezpieczeń:

   | Operacja | Zasady zabezpieczeń domeny |
   | ---------- | ---------- | 
   | Operacje GET i Put | Danych procesu roboczego: Raporty publicznych procesu roboczego |
   | Operacje GET i Put | Danych osobowych: Informacje kontaktowe pracy |
   | Get | Danych procesu roboczego: Wszystkie pozycje |
   | Get | Dane procesu roboczego: Bieżące zaoszczędziła informacji |
   | Get | Danych procesu roboczego: Tytuł firm profil procesu roboczego |

### <a name="configure-business-process-security-policy-permissions"></a>Skonfiguruj uprawnienia zasad zabezpieczeń procesów biznesowych
W tym kroku będziesz udzielić "zabezpieczenia procesów biznesowych" uprawnienia zasad dla danych procesu roboczego do grupy zabezpieczeń. Jest to wymagane do konfigurowania łącznika produktu Workday funkcji zapisywania zwrotnego w aplikacji. 

**Aby skonfigurować uprawnienia zasad zabezpieczeń procesów biznesowych:**

1. Wprowadź **zasad wymagających używania procesów biznesowych** w polu wyszukiwania, a następnie kliknij link **edytować zasady zabezpieczeń procesów biznesowych** zadania.  

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_12.png "zasad zabezpieczeń procesów biznesowych")  

2. W **typ procesu biznesowego** polu tekstowym Wyszukaj *skontaktuj się z pomocą* i wybierz **zmiany skontaktuj się z pomocą** procesów biznesowych i kliknij przycisk **OK**.

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_13.png "zasad zabezpieczeń procesów biznesowych")  

3. Na **edytować zasady zabezpieczeń procesów biznesowych** przewiń do **Obsługa informacje kontaktowe (usługa sieci Web)** sekcji.

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_14.png "zasad zabezpieczeń procesów biznesowych")  

4. Wybierz i Dodaj nową grupę zabezpieczeń systemu integracji do listy grup zabezpieczeń, które można zainicjować żądanie usługi sieci web. Kliknij pozycję **gotowe**. 

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_15.png "zasad zabezpieczeń procesów biznesowych")  

 
### <a name="activate-security-policy-changes"></a>Aktywuj zmiany zasad zabezpieczeń

**Aby aktywować zmiany zasad zabezpieczeń:**

1. Wprowadź aktywować w polu wyszukiwania, a następnie kliknij łącze **aktywować oczekujące zmiany zasad zabezpieczeń**.

    ![Aktywuj](./media/workday-inbound-tutorial/wd_isu_16.png "aktywacji") 
2. Rozpoczęcia zadania aktywować oczekujące zmiany zasad zabezpieczeń, wprowadzając komentarz na potrzeby inspekcji, a następnie kliknij przycisk **OK**. 

    ![Aktywuj oczekujące zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_17.png "aktywować oczekujące zabezpieczeń")  
1. Ukończenie zadania na następnym ekranie, zaznaczając pole wyboru **Potwierdź**, a następnie kliknij przycisk **OK**.

    ![Aktywuj oczekujące zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_18.png "aktywować oczekujące zabezpieczeń")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfiguracja aprowizacji użytkowników z produktu Workday do usługi Active Directory

Wykonaj te instrukcje, aby skonfigurować konto użytkownika z produktu Workday aprowizację każdej domeny usługi Active Directory, w zakresie integracji usługi.

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Część 1: Instalowanie i konfigurowanie agentów inicjowania obsługi administracyjnej w środowisku lokalnym

Aby zainicjować obsługę do usługi Active Directory w środowisku lokalnym, agent musi być zainstalowany na serwerze z platformy .NET 4.7 Framework i dostępu do sieci do żądanego domeny usługi Active Directory.

> [!TIP]
> Sprawdź wersję programu .NET framework, na serwerze wykonując instrukcje podane [tutaj](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Jeśli serwer nie ma programu .NET 4.7 lub nowszej jest zainstalowane, możesz ją pobrać z [tutaj](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).  

Wdrożenie platformy .NET 4.7 można pobrać **[tutaj agenta inicjowania obsługi administracyjnej lokalnych](https://go.microsoft.com/fwlink/?linkid=847801)** i wykonaj czynności podane poniżej, aby ukończyć konfiguracji agenta.

1. Zaloguj się do systemu Windows Server, w którym chcesz zainstalować nowego agenta.
2. Uruchom Instalatora agenta inicjowania obsługi administracyjnej, zaakceptuj warunki i kliknij **zainstalować** przycisku.
![Zainstaluj ekranu](./media/workday-inbound-tutorial/pa_install_screen_1.png "zainstalować ekranu")

3. Po zakończeniu instalacji, zostanie uruchomiony Kreator i zostanie wyświetlony **usługi Azure AD Connect** ekranu. Kliknij pozycję **Uwierzytelnij** przycisk, aby nawiązać połączenie z wystąpieniem usługi Azure AD.
![Łączenie usługi Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "usługi Azure AD Connect")

4. Uwierzytelnianie do swojego wystąpienia usługi Azure AD przy użyciu poświadczeń administratora globalnego. 
![Uwierzytelnianie administratora](./media/workday-inbound-tutorial/pa_install_screen_3.png "uwierzytelniania administratora")

5. Po pomyślnym uwierzytelnieniu przy użyciu usługi Azure AD, zobaczysz **łączenie usługi Active Directory** ekranu. W tym kroku, wprowadź nazwę domeny usługi AD, a następnie kliknij przycisk na **Dodaj katalog** przycisku.
![Dodaj katalog](./media/workday-inbound-tutorial/pa_install_screen_4.png "Dodaj katalog")

6. Możesz teraz jest monitowany o podanie poświadczeń, trzeba połączyć się z domeną usługi AD. Na jednym ekranie, można użyć **wybierz priorytet kontrolera domeny** do określenia kontrolerów domeny, które powinny być używane przez agenta wysyłania żądania alokacji.
![Poświadczenia domeny](./media/workday-inbound-tutorial/pa_install_screen_5.png "poświadczenia domeny")

7. Po skonfigurowaniu domeny, Instalator wyświetla listę skonfigurowanych domen. Na tym ekranie można powtórzyć krok #5 i 6 #, aby dodać więcej domen lub kliknąć **dalej** aby przejść do rejestracji agenta. 
![Skonfigurowane domen](./media/workday-inbound-tutorial/pa_install_screen_6.png "skonfigurowane domen")

   > [!NOTE]
   > Jeśli masz wiele domen AD (np. na.contoso.com, emea.contoso.com), Dodaj każdej domeny indywidualnie do listy. Tylko Dodawanie domeny nadrzędnej (np. contoso.com) nie jest wystarczająca i zaleca się zarejestrować każda domeny podrzędnej przy użyciu agenta. 

8. Przejrzyj szczegóły konfiguracji, a następnie kliknij pozycję **Potwierdź** można zarejestrować agenta. 
![Upewnij się, ekran](./media/workday-inbound-tutorial/pa_install_screen_7.png "potwierdzić ekranu")

9. Kreator konfiguracji Wyświetla postęp rejestracji agenta.
![Rejestracja agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "rejestracji agenta")

10. Gdy rejestracja agenta zakończy się pomyślnie, możesz kliknąć **wyjść** aby zakończyć pracę kreatora. 
![Zamknąć ekran](./media/workday-inbound-tutorial/pa_install_screen_9.png "zamknąć ekran")

11. Zweryfikuj instalację agenta, upewnij się, że jest uruchomiona przez otwarcie przystawki "Usługi" i poszukaj usługa o nazwie "Microsoft Azure AD Connect Agent aprowizacji" ![usług](./media/workday-inbound-tutorial/services.png)  


**Rozwiązywanie problemów z agentem**

[Dziennika zdarzeń Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) w systemie Windows Server maszyny hostującej agenta zawiera zdarzenia dla wszystkich operacji wykonywanych przez agenta. Aby wyświetlić te zdarzenia:
    
1. Otwórz **Eventvwr.msc**.
2. Wybierz **Dzienniki Windows > Aplikacja**.
3. Wyświetl wszystkie zdarzenia zarejestrowane w lokalizacji source **usługi AAD. Connect.ProvisioningAgent**. 
4. Sprawdź, czy błędy i ostrzeżenia.

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 2: Dodawanie obsługi administracyjnej aplikacji łącznika i tworzenia połączenia z produktem Workday

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

   * **Las usługi Active Directory —** "Name" domeny usługi Active Directory, ponieważ zarejestrowane przy użyciu agenta. Zazwyczaj jest to ciąg, takich jak: *contoso.com*

   * **Kontener usługi Active Directory —** wprowadź nazwa Wyróżniająca kontenera, w którym tworzenia kont użytkowników domyślnie agenta. 
        Przykład: *OU = użytkownicy standardowi, OU = Users, DC = contoso, DC = test*
> [!NOTE]
> To ustawienie pochodzi wyłącznie do gry dla podczas tworzenia kont użytkowników Jeśli *parentDistinguishedName* atrybutu nie skonfigurowano mapowania atrybutów. To ustawienie nie jest używane dla wyszukiwanie użytkowników lub aktualizacji operacji. Drzewo podrzędne całej domeny znajduje się w zakresie operacji wyszukiwania.
   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".
> [!NOTE]
> Usługa Azure AD aprowizacji wysyła powiadomienie e-mail, jeśli zadanie inicjowania obsługi administracyjnej przechodzi w stan [kwarantanny](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) stanu.

   * Kliknij przycisk **Testuj połączenie** przycisku. Jeśli test połączenia powiedzie się, kliknij przycisk **Zapisz** znajdujący się u góry. Jeśli nie powiedzie się, należy się zapoznać, że poświadczenia dla produktu Workday i poświadczeń usługi AD skonfigurowane na temat instalacji agenta są prawidłowe.

![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

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
| **Zastąp(Mid(Zastąp(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Zapisane podczas tworzenia tylko |
| **Przełącznik (\[Active\],, "0", "True", "1", "Fałsz")** |  accountDisabled      |     | Tworzenie i aktualizowanie |
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

**Aby skonfigurować łącznik produktu Workday funkcji zapisywania zwrotnego:**

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

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Ustaw **lokalizacji** pole `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale zastąpieniu "IMPL-CC" faktycznego typu wystąpienia, a "DZIERŻAWCA" nazwą rzeczywistego dzierżawy.

7. Ustaw **operacji** do **Get_Workers**

8.  Kliknij małą **skonfigurować** link poniżej okienka żądanie/odpowiedź, aby ustawić poświadczenia dla produktu Workday. Sprawdź **uwierzytelniania**, a następnie wprowadź nazwę użytkownika i hasło dla konta system integracji produktu Workday. Pamiętaj format nazwy użytkownika jako name@tenanti pozostawić **UsernameToken WS-Security** wybraną opcją.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Kliknij przycisk **OK**.

10. **Żądania** okienku wklejanie w XML poniżej i ustaw **Employee_ID** identyfikator pracownika rzeczywistego użytkownika w ramach dzierżawy produktu Workday. Wybierz użytkownika, który ma atrybut wypełnione chcesz wyodrębnić.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
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

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio3.png)

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

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Przewiń w dół listy atrybutów, aby wskazać pól wejściowych.

7. Aby uzyskać **nazwa**, wprowadź nazwę wyświetlaną dla atrybutu użytkownika.

8. Dla **typu**, wybierz typ odpowiadający odpowiednio atrybut (**ciąg** przeważnie).

9. Aby uzyskać **wyrażenie interfejsu API**, wprowadź skopiowany z produktu Workday Studio wyrażenie XPath. Przykład: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wybierz **Dodaj atrybut**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Wybierz **Zapisz** powyżej, a następnie **tak** do okna dialogowego. Zamknij ekran mapowanie atrybutu, jeśli jest nadal otwarty.

12. Po powrocie na główny **aprowizacji** zaznacz **zsynchronizować pracownikom opłacanie** (lub **zsynchronizować pracowników do usługi Azure AD**) ponownie.

13. Wybierz **Dodaj nowe mapowanie**.

14. Nowy atrybut powinien zostać wyświetlony na **atrybut źródłowy** listy.

15. Dodaj mapowanie dla użytkownika nowego atrybutu zgodnie z potrzebami.

16. Gdy skończysz, pamiętaj, aby ustawić **stanie aprowizacji** do **na** i Zapisz.

## <a name="known-issues"></a>Znane problemy

* Zapisywanie danych do atrybutu thumbnailphoto usługa użytkownika w usłudze Active Directory w środowisku lokalnym nie jest obecnie obsługiwane.

* Łącznik "Workday do usługi Azure AD" nie jest obecnie obsługiwane w przypadku dzierżaw usługi Azure AD, w której włączona jest narzędziem AAD Connect.  

## <a name="managing-personal-data"></a>Zarządzanie danymi osobowymi

Workday, inicjowanie obsługi administracyjnej rozwiązania dla usługi Active Directory wymaga agenta synchronizacji, należy zainstalować na serwerze przyłączonym do domeny, a ten agent tworzy dzienniki w dzienniku zdarzeń Windows, które mogą zawierać dane osobowe.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować logowanie jednokrotne między produktem Workday i Azure Active Directory](workday-tutorial.md)
* [Dowiedz się, jak zintegrować innych aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)

