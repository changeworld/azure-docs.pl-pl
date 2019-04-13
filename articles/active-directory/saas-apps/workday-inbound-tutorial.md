---
title: 'Samouczek: Konfigurowanie produktu Workday do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników w produkcie Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: d34bd9d7f80f72b3c6c0821ad48e6be1fd260be9
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524637"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie produktu Workday do automatycznej aprowizacji użytkowników

Celem tego samouczka jest ukazują kroki, które należy wykonać, aby zaimportować profile procesu roboczego z produktu Workday do usługi Active Directory i Azure Active Directory, za pomocą opcjonalnych zapisywania zwrotnego adresu e-mail w produkcie Workday.

## <a name="overview"></a>Omówienie

[Usługa aprowizowania użytkowników w usłudze Azure Active Directory](../manage-apps/user-provisioning.md) integruje się z [interfejsu API zarządzania zasobami ludzkimi Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) w celu dostarczania kont użytkowników. Usługa Azure AD używa tego połączenia, aby włączyć następujący użytkownik przepływy pracy aprowizacji:

* **Inicjowanie obsługi administracyjnej użytkowników do usługi Active Directory** -Provision wybrane zestawy użytkowników z produktu Workday do jednej lub kilku domen usługi Active Directory.

* **Inicjowanie obsługi administracyjnej użytkowników tylko w chmurze, usługą Azure Active Directory** — w scenariuszach, gdzie lokalnej usługi Active Directory nie jest używana, użytkownicy mogą być udostępniane bezpośrednio z produktu Workday do usługi Azure Active Directory za pomocą użytkownika usługi Azure AD, do usługi aprowizacji.

* **Zapisu tylnej stronie adresy e-mail w produkcie Workday** — usługa aprowizowania użytkowników w usłudze Azure AD można zapisywać adresy e-mail użytkowników usługi Azure AD w produkcie Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jakie scenariuszy zarządzania zasobami ludzkimi dotyczy?

Dzień roboczy użytkownika inicjowania obsługi administracyjnej przepływów pracy obsługiwanych przez usługę aprowizacji użytkownika usługi Azure AD pozwalają na automatyzację następujących zasobów ludzkich i scenariuszy zarządzania cyklem życia tożsamości:

* **Zatrudniania nowych pracowników** — po dodaniu nowego pracownika w produkcie Workday, konto użytkownika zostanie automatycznie utworzone w usłudze Active Directory, Azure Active Directory i, opcjonalnie, usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](../manage-apps/user-provisioning.md), przy użyciu zapisywania zwrotnego adresu e-mail w produkcie Workday.

* **Aktualizacje atrybutu i profilu pracownika** — po zaktualizowaniu rekordu pracownika w produkcie Workday (np. ich nazwy, tytułu lub Menedżer), ich konta użytkownika zostaną automatycznie zaktualizowane w usłudze Active Directory, Azure Active Directory i, opcjonalnie, usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](../manage-apps/user-provisioning.md).

* **Liczba przerwanych pracowników** — w przypadku zakończenia pracownika w produkcie Workday, ich konta użytkownika jest automatycznie wyłączana w usługi Active Directory, Azure Active Directory i, opcjonalnie, usługi Office 365 i [innych aplikacji SaaS obsługiwane przez platformę Azure Usługi AD](../manage-apps/user-provisioning.md).

* **Pracownik rehires** — gdy pracownik jest rehired w produkcie Workday, ich starego konta mogą zostać automatycznie ponownie uaktywnione lub ponownie zainicjowano obsługę administracyjną (w zależności od Twojego preferencji) do usługi Active Directory, Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwane przez usługę Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Kto jest ten użytkownik rozwiązanie do aprowizowania najlepsze dostosowane do?

Doskonale nadają się dla tego użytkownika z produktu Workday aprowizacji rozwiązania:

* Organizacje, które pragną wbudowanych, oparte na chmurze rozwiązanie do aprowizowania użytkowników w produkcie Workday

* Organizacje, które wymagają, bezpośrednie Inicjowanie obsługi użytkowników z produktu Workday do usługi Active Directory lub Azure Active Directory

* Organizacje, które wymagają użytkowników do aprowizacji przy użyciu danych uzyskanych z modułu Workday HCM (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacje wymagają dołączania, przenoszenie, i pozostawienie użytkownikom być synchronizowane z usługą co najmniej jeden lasy usługi Active Directory, domen i jednostek organizacyjnych, tylko na podstawie Zmień informacje w module Workday HCM (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacje, do obsługi poczty e-mail przy użyciu usługi Office 365

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji opisano użytkownika end-to-end, inicjowanie obsługi administracyjnej architekturę rozwiązania dla typowych środowisk hybrydowych. Istnieją dwa z procedurami:

* **Przepływ danych HR autorytatywne — z produktu Workday do usługi Active Directory w środowisku lokalnym:** W tym przepływie, zdarzeń procesu roboczego (na przykład nowi pracownicy w przypadku transferów zakończeń) najpierw występują w chmurze dzierżawy produktu Workday HR, a następnie przepływu danych zdarzeń do środowiska lokalnego usługi Active Directory za pomocą usługi Azure AD i aprowizacji agenta. W zależności od zdarzenia może prowadzić do operacji tworzenia/aktualizacji/Włączanie/wyłączanie w AD.
* **Przepływ zapis zwrotny wiadomości e-mail — z lokalnej usługi Active Directory w produkcie Workday:** Po zakończeniu tworzenia konta w usłudze Active Directory jest synchronizowana z usługą Azure AD za pomocą usługi Azure AD Connect, a atrybut poczty e-mail, skąd pochodzi z usługi Active Directory można zapisać zwrotnie w produkcie Workday.

![Omówienie](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Przepływ danych użytkownika end-to-end

1. Zespół działu KADR wykonuje procesu roboczego w transakcji (Łączniki/firmy przeprowadzkowej/Leavers lub nowych pracowników/transfery/zakończeń) w produkcie Workday HCM
2. Usługa Azure AD aprowizacji uruchamia zaplanowanej synchronizacji tożsamości z produktu Workday HR i identyfikuje zmiany, które muszą być przetworzone w celu synchronizacji z usługą Active Directory w środowisku lokalnym.
3. Usługa Azure AD aprowizacji wywołuje w środowisku lokalnym usługi AAD Connect aprowizacji agenta z ładunkiem żądania zawiera operacje tworzenia/aktualizacji/Włączanie/wyłączanie konta usługi AD.
4. Agent programu Azure AD Connect inicjowania obsługi używa konta usługi można dodać/zaktualizować dane konta usługi Active Directory.
5. Program Azure AD Connect / synchronizacja różnicowa w celu ściągania aktualizacji w AD działa aparat synchronizacji usługi AD.
6. Aktualizacje usługi Active Directory są synchronizowane z usługą Azure Active Directory.
7. Jeśli skonfigurowano łącznik produktu Workday funkcji zapisywania zwrotnego, go zapisywanie zwrotne atrybut poczty e-mail w produkcie Workday, na podstawie atrybutu zgodnego używane.

## <a name="planning-your-deployment"></a>Planowanie wdrożenia

Przed rozpoczęciem integracji z produktem Workday, sprawdź poniższe wymagania wstępne, a następnie przeczytaj poniższe wskazówki na temat sposobu spełnić bieżące architektury usługi Active Directory i inicjowania obsługi wymagań przy użyciu rozwiązania dostarczane przez usługę Azure Active Directory użytkownika. Kompleksowy [planu wdrożenia](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) z arkuszami planowania jest również dostępna, które ułatwiają współpracę z Twojego partnera integracji z produktem Workday i HR zainteresowanych stron.

W tej sekcji omówiono następujące aspekty planowania:

* [Wymagania wstępne](#prerequisites)
* [Wybierania inicjowania obsługi administracyjnej aplikacji łącznik do wdrożenia](#selecting-provisioning-connector-apps-to-deploy)
* [Planowanie wdrożenia, Agent programu Azure AD Connect inicjowania obsługi administracyjnej](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integracja z wieloma domenami usługi Active Directory](#integrating-with-multiple-active-directory-domains)
* [Planowanie produktu Workday do mapowania atrybutów użytkownika usługi Active Directory i przekształcenia](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Nieprawidłowa Azure AD Premium P1 lub wyższej subskrypcji przy użyciu dostępu administratora globalnego
* Dzierżawy produktu Workday wdrożenia na potrzeby testowania i integracji
* Uprawnienia administratora w usłudze WORKDAY aplikacja do tworzenia użytkownika integracji systemu i wprowadzić zmiany do przetestowania danych pracownika do celów testowych
* Dla aprowizacji użytkowników w usłudze Active Directory, jest wymagany do hosta serwera z systemem Windows Server 2012 lub nowszej za pomocą środowiska uruchomieniowego .NET 4.7.1+ [agenta inicjowania obsługi administracyjnej lokalnych](https://go.microsoft.com/fwlink/?linkid=847801)
* [Program Azure AD Connect](../hybrid/whatis-hybrid-identity.md) za synchronizowanie użytkowników usługi Active Directory i usługi Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Wybierania inicjowania obsługi administracyjnej aplikacji łącznik do wdrożenia

W celu ułatwienia inicjowania obsługi administracyjnej przepływy pracy między produktem Workday i usługi Active Directory, usługa Azure AD zapewnia wiele inicjowania obsługi administracyjnej aplikacji łącznika, które można dodawać z galerii aplikacji Azure AD:

![Galeria aplikacji usługi AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **WORKDAY użytkownika i zainicjowanie obsługi usługi Active Directory** — ta aplikacja ułatwia aprowizowaniem kont użytkowników z produktu Workday do pojedynczej domeny usługi Active Directory. Jeśli masz wiele domen, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji Azure AD dla każdej domeny usługi Active Directory, które należy aprowizować do.

* **WORKDAY do aprowizacji użytkownika usługi Azure AD** — o ile AAD Connect to narzędzie, które mają być używane do synchronizacji usługi Active Directory, użytkowników do usługi Azure Active Directory, ta aplikacja może służyć do ułatwienia aprowizacji użytkowników tylko w chmurze z produktu Workday do pojedynczej platformy Azure Dzierżawy usługi Active Directory.

* **Zapisywanie zwrotne WORKDAY** — ta aplikacja ułatwia zapisu adresów e-mail użytkowników z usługi Azure Active Directory w produkcie Workday.

> [!TIP]
> Regularne aplikacji "Dzień roboczy" służy do konfigurowania logowania jednokrotnego między produktem Workday i Azure Active Directory.

Schemat blokowy decyzji poniżej umożliwia zidentyfikowanie, Workday inicjowania obsługi administracyjnej aplikacji, które mają zastosowanie do danego scenariusza.
    ![Schemat blokowy decyzji](./media/workday-inbound-tutorial/wday_app_flowchart.png "schemat blokowy decyzji")

Użyj spisu treści, aby przejść do odpowiedniej sekcji tego samouczka.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planowanie wdrożenia, Agent programu Azure AD Connect inicjowania obsługi administracyjnej

> [!NOTE]
> Ta sekcja dotyczy tylko wtedy, gdy planujesz wdrożyć produktu Workday do użytkownika inicjowania obsługi administracyjnej aplikacji usługi Active Directory. To można pominąć, jeśli wdrażasz zapisywania zwrotnego Workday czy produktu Workday do aplikacji aprowizacji użytkownika usługi Azure AD.

Produktu Workday do aprowizacji użytkowników usługi AD rozwiązania wymaga wdrożenia co najmniej jednego agenta inicjowania obsługi na serwerach z systemem Windows 2012 R2 lub nowszego z co najmniej 4 GB pamięci RAM i .NET 4.7.1+ środowiska uruchomieniowego. Następujące zagadnienia dotyczące musi być brana pod uwagę przed zainstalowaniem agenta inicjowania obsługi administracyjnej:

* Upewnij się, że serwer hosta z agentem Aprowizowania ma dostęp do sieci do domeny docelowej usługi AD
* Kreator konfiguracji agenta inicjowania obsługi administracyjnej rejestruje agenta dzierżawy usługi Azure AD i procesu rejestracji wymaga dostępu do *. msappproxy.net za pośrednictwem portu SSL 443. Upewnij się, że reguły zapory dla ruchu wychodzącego w miejscu, pozwalających na korzystanie z tej komunikacji. Agent obsługuje [konfiguracji serwera proxy HTTPS wychodzącego](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Agenta inicjowania obsługi używa konta usługi do komunikacji z lokalnej domeny usługi AD. Przed zainstalowaniem agenta zalecane jest, Utwórz konto usługi przy użyciu uprawnień administratora domeny i hasło, które nie wygasa.  
* Podczas konfigurowania aprowizacji agenta możesz wybrać kontrolerów domeny, które ma obsługiwać żądania udostępniania. Jeśli masz kilka kontrolerów domeny geograficznie rozproszonych, należy zainstalować agenta inicjowania obsługi administracyjnej, w tym samym miejscu co Twoje kontrolerach domeny preferowanych, aby zwiększyć niezawodność i wydajność rozwiązania end-to-end
* Wysoką dostępność, możesz wdrożyć więcej niż jednego agenta aprowizacji i zarejestruj go do obsługi tego samego zestawu w środowisku lokalnym domeny usługi AD.

> [!IMPORTANT]
> W środowiskach produkcyjnych firma Microsoft zaleca, że masz co najmniej 3 agentów aprowizacji skonfigurowany z dzierżawą usługi Azure AD w celu zapewnienia wysokiej dostępności.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integracja z wieloma domenami usługi Active Directory

> [!NOTE]
> Ta sekcja dotyczy tylko wtedy, gdy planujesz wdrożyć produktu Workday do użytkownika inicjowania obsługi administracyjnej aplikacji usługi Active Directory. To można pominąć, jeśli wdrażasz zapisywania zwrotnego Workday czy produktu Workday do aplikacji aprowizacji użytkownika usługi Azure AD.

W zależności od topologii usługi Active Directory należy zdecydować, liczba użytkowników inicjowania obsługi administracyjnej łącznika aplikacji i liczby agentów inicjowania obsługi administracyjnej do skonfigurowania. Poniżej przedstawiono niektóre typowe wzorce wdrażania, których możesz skorzystać podczas planowania wdrożenia.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Scenariusz wdrażania #1: Pojedynczej dzierżawy produktu Workday -> domeny pojedynczej usługi AD

W tym scenariuszu użytkownik ma jednej dzierżawy produktu Workday i chcesz aprowizować użytkowników do pojedynczy element docelowy domeny usługi AD. Poniżej przedstawiono konfigurację produkcyjnych zalecane dla tego wdrożenia.

|   |   |
| - | - |
| Nie. Inicjowanie obsługi agentów do wdrożenia w środowisku lokalnym | 3 (Aby uzyskać wysoką dostępność i tryb failover) |
| Nie. z produktu Workday do użytkownika AD inicjowania obsługi administracyjnej aplikacji, aby skonfigurować w witrynie Azure portal | 1 |

  ![Scenariusz 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Scenariusz wdrożenia #2. Pojedynczej dzierżawy produktu Workday -> wiele domen podrzędnych usługi AD

Ten scenariusz obejmuje Inicjowanie obsługi administracyjnej użytkowników z produktu Workday do wielu domen podrzędnych docelowej usługi AD w lesie. Poniżej przedstawiono konfigurację produkcyjnych zalecane dla tego wdrożenia.

|   |   |
| - | - |
| Nie. Inicjowanie obsługi agentów do wdrożenia w środowisku lokalnym | 3 (Aby uzyskać wysoką dostępność i tryb failover) |
| Nie. z produktu Workday do użytkownika AD inicjowania obsługi administracyjnej aplikacji, aby skonfigurować w witrynie Azure portal | jedna aplikacja na domenę podrzędną |

  ![Scenariusz 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Scenariusz wdrażania #3: Pojedynczej dzierżawy produktu Workday -> lasów usługi AD rozłączne

Ten scenariusz obejmuje Inicjowanie obsługi administracyjnej użytkowników z produktu Workday do domen z rozłącznymi lasów usługi AD. Poniżej przedstawiono konfigurację produkcyjnych zalecane dla tego wdrożenia.

|   |   |
| - | - |
| Nie. Inicjowanie obsługi agentów do wdrożenia w środowisku lokalnym | 3 na rozłączne lasu usługi AD |
| Nie. z produktu Workday do użytkownika AD inicjowania obsługi administracyjnej aplikacji, aby skonfigurować w witrynie Azure portal | jedna aplikacja na domenę podrzędną |

  ![Scenariusz 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planowanie produktu Workday do mapowania atrybutów użytkownika usługi Active Directory i przekształcenia

> [!NOTE]
> Ta sekcja dotyczy tylko wtedy, gdy planujesz wdrożyć produktu Workday do użytkownika inicjowania obsługi administracyjnej aplikacji usługi Active Directory. To można pominąć, jeśli wdrażasz zapisywania zwrotnego Workday czy produktu Workday do aplikacji aprowizacji użytkownika usługi Azure AD.

Przed rozpoczęciem konfigurowania aprowizacji użytkowników do domeny usługi Active Directory, należy wziąć pod uwagę następujące kwestie. Odpowiedzi na te pytania określają, jak Twoje filtrów określania zakresu i mapowania atrybutów należy ustawić.

* **Użytkownicy w usłudze WORKDAY aplikacja musi być przygotowana do tego lasu usługi Active Directory?**

  * *Przykład: Użytkownicy, których atrybut "Firmowe" dzień roboczy zawiera wartość "Contoso", a atrybut "Worker_Type" "Regularne"*

* **Jak użytkownicy są kierowane do innej organizacji jednostek organizacyjnych**

  * *Przykład: Użytkownicy są kierowane do jednostek organizacyjnych, które odnoszą się do lokalizacji pakietu office, zgodnie z definicją w produkcie Workday "Gmina" i "Country_Region_Reference" atrybuty*

* **Jak następujące atrybuty powinny być wypełnione w usłudze Active Directory?**

  * Nazwa pospolita (cn)
    * *Przykład: Użyj wartości Workday User_ID, zgodnie z ustawieniem zarządzania zasobami ludzkimi*

  * Identyfikator pracownika (employeeId)
    * *Przykład: Użyj wartości Workday Worker_ID*

  * Nazwa konta SAM (sAMAccountName)
    * *Przykład: Należy użyć wartości pola User_ID produktu Workday filtrowany za pośrednictwem usługi Azure AD aprowizacji wyrażenie Usuń niedozwolone znaki*

  * Główna nazwa użytkownika (userPrincipalName)
    * *Przykład: Wartość User_ID produktu Workday za pomocą usługi Azure AD aprowizacji wyrażenie Dołącz nazwę domeny*

* **Jak użytkowników powinny być dopasowane między produktem Workday i usługi Active Directory?**

  * *Przykład: Użytkownicy z określonym produktem Workday "Worker_ID" wartości są dopasowywane użytkownikom usługi Active Directory, w którym "employeeID" ma taką samą wartość. Wartość Worker_ID nie zostanie znaleziony w usłudze Active Directory, utworzenie nowego użytkownika.*
  
* **Las usługi Active Directory już zawiera identyfikatory wymaganych do dopasowania logiki pracy użytkowników?**

  * *Przykład: Tej konfiguracji w przypadku nowego wdrożenia produktu Workday, zaleca się, że usługi Active Directory jest wstępnie wypełniony poprawne wartości Workday Worker_ID (lub wartość unikatowego Identyfikatora wybranego) do zachowania pasujące logiki tak proste, jak to możliwe.*

Sposób instalowania i konfigurowania tych specjalnych inicjowania obsługi administracyjnej aplikacji łącznika jest przedmiotem pozostałej części tego samouczka. Aplikacje, które użytkownik chce skonfigurować będzie zależeć od systemów, które należy aprowizować, jak wiele domen usługi Active Directory i Azure AD to dzierżawców w danym środowisku.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurowanie integracji systemu użytkownika w produkcie Workday

Typowym wymogiem wszystkich aprowizacji łączniki produktu Workday jest wymaganych przez nich poświadczeń użytkownika systemu integracji produktu Workday, połączyć się z interfejsu API zarządzania zasobami ludzkimi produktu Workday. W tej sekcji opisano sposób tworzenia integracji systemu użytkownika w produkcie Workday i zawiera następujące sekcje:

* [Tworzenie integracji systemu użytkownika](#creating-an-integration-system-user)
* [Tworzenie grupy zabezpieczeń usługi integracji](#creating-an-integration-security-group)
* [Konfigurowanie uprawnień zasady zabezpieczeń domeny](#configuring-domain-security-policy-permissions)
* [Konfigurowanie uprawnień zasad zabezpieczeń procesów biznesowych](#configuring-business-process-security-policy-permissions)
* [Aktywowanie zmiany zasad zabezpieczeń](#activating-security-policy-changes)

> [!NOTE]
> Istnieje możliwość pominąć tę procedurę i zamiast tego użyć konta administratora globalnego Workday jako konto integracji systemu. Może działać prawidłowo dla pokazów, ale nie jest zalecane w przypadku wdrożeń produkcyjnych.

### <a name="creating-an-integration-system-user"></a>Tworzenie integracji systemu użytkownika

**Aby utworzyć użytkownika systemowego integracji:**

1. Zaloguj się do dzierżawy produktu Workday za pomocą konta administratora. W **Workday aplikacja**, wprowadź użytkownika w polu wyszukiwania, a następnie kliknij polecenie **Create User systemu integracji**.

    ![Utwórz użytkownika](./media/workday-inbound-tutorial/wd_isu_01.png "Utwórz użytkownika")
2. Wykonaj **Create User systemu integracji** zadanie, podając nazwę użytkownika i hasło dla nowego użytkownika systemu integracji.  
  
* Pozostaw **wymagają nowego hasła przy następnym Sign In** opcja nie jest zaznaczone, ponieważ ten użytkownik będzie logować się programowo.
* Pozostaw **czas wygaśnięcia sesji w minutach** o jego wartość domyślna 0, która uniemożliwi sesji użytkownika przedwcześnie limit czasu.
* Wybierz opcję **należy zezwolić na sesjach interfejsu użytkownika** jako zapewnia dodatkową warstwę zabezpieczeń, który uniemożliwia użytkownikiem z hasłem systemu integracji logując się do produktu Workday.

    ![Tworzenie użytkownika systemu integracji](./media/workday-inbound-tutorial/wd_isu_02.png "tworzenie integracji systemu użytkownika")

### <a name="creating-an-integration-security-group"></a>Tworzenie grupy zabezpieczeń usługi integracji

W tym kroku zostanie Utwórz grupę zabezpieczeń systemu integracji z użyciem lub ograniczone w produkcie Workday i przypisz użytkownika systemu integracji, które są utworzone w poprzednim kroku do tej grupy.

**Aby utworzyć grupę zabezpieczeń:**

1. Wprowadź grupy zabezpieczeń w polu wyszukiwania, a następnie kliknij polecenie **Utwórz grupę zabezpieczeń**.

    ![Grupa CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity grupy")
2. Wykonaj **Utwórz grupę zabezpieczeń** zadania. 

   * Istnieją dwa typy grup zabezpieczeń w usłudze WORKDAY aplikacja:
     * **Nieograniczone:** Wszyscy członkowie grupy zabezpieczeń można uzyskać dostęp do wszystkich wystąpień dane chronione przez grupę zabezpieczeń.
     * **Ograniczone:** Wszyscy członkowie grupy zabezpieczeń mają kontekstowych dostęp do podzbioru wystąpień danych (wierszy), które mogą uzyskiwać dostęp do grupy zabezpieczeń.
   * Sprawdź, czy razem ze swoim partnerem integracji produktu Workday wybierz typ grupy zabezpieczeń odpowiednich do integracji.
   * Jeśli znasz już typ grupy, wybierz **integracji systemowa grupa zabezpieczeń (nieograniczony)** lub **integracji systemowa grupa zabezpieczeń (ograniczony)** z **typu grupy zabezpieczeń gośćmi**  listy rozwijanej.

     ![Grupa CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity grupy")

3. Po pomyślnym utworzeniu grupy zabezpieczeń zostanie wyświetlona strona, na której można przypisać członków do grupy zabezpieczeń. Dodawanie nowego użytkownika systemu integracji, utworzony w poprzednim kroku do tej grupy zabezpieczeń. Jeśli używasz *ograniczonego* grupę zabezpieczeń należy również wybrać zakres w organizacji odpowiednie.

    ![Edytuj grupę zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_05.png "Edytuj grupę zabezpieczeń")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurowanie uprawnień zasady zabezpieczeń domeny

W tym kroku będziesz udzielić "zabezpieczenia domeny" uprawnienia zasad dla danych procesu roboczego do grupy zabezpieczeń.

**Aby skonfigurować uprawnienia zasad zabezpieczeń domeny:**

1. Wprowadź **konfiguracji zabezpieczeń domeny** w polu wyszukiwania, a następnie kliknij link **raport dotyczący konfiguracji zabezpieczeń domeny**.  

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_06.png "zasady zabezpieczeń domeny")  
2. W **domeny** polu tekstowym Wyszukaj następujące domeny i dodać je do filtru jedno po drugim.  
   * *Inicjowanie obsługi administracyjnej konta zewnętrzne*
   * *Dane procesu roboczego: Raporty publicznych procesu roboczego*
   * *Danych osobowych: Informacje kontaktowe pracy*
   * *Dane procesu roboczego: Wszystkie stanowiska*
   * *Dane procesu roboczego: Bieżące informacje kadrowe*
   * *Dane procesu roboczego: Tytuł biznesowych na profil pracownika*

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
   | Operacje GET i Put | Dane procesu roboczego: Raporty publicznych procesu roboczego |
   | Operacje GET i Put | Danych osobowych: Informacje kontaktowe pracy |
   | Get | Dane procesu roboczego: Wszystkie stanowiska |
   | Get | Dane procesu roboczego: Bieżące informacje kadrowe |
   | Get | Dane procesu roboczego: Tytuł biznesowych na profil pracownika |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurowanie uprawnień zasad zabezpieczeń procesów biznesowych

W tym kroku będziesz udzielić "zabezpieczenia procesów biznesowych" uprawnienia zasad dla danych procesu roboczego do grupy zabezpieczeń. Ten krok jest wymagany do konfigurowania łącznika produktu Workday funkcji zapisywania zwrotnego w aplikacji.

**Aby skonfigurować uprawnienia zasad zabezpieczeń procesów biznesowych:**

1. Wprowadź **zasad wymagających używania procesów biznesowych** w polu wyszukiwania, a następnie kliknij link **edytować zasady zabezpieczeń procesów biznesowych** zadania.  

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_12.png "zasad zabezpieczeń procesów biznesowych")  

2. W **typ procesu biznesowego** polu tekstowym Wyszukaj *skontaktuj się z pomocą* i wybierz **zmiany skontaktuj się z pomocą** procesów biznesowych i kliknij przycisk **OK**.

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_13.png "zasad zabezpieczeń procesów biznesowych")  

3. Na **edytować zasady zabezpieczeń procesów biznesowych** przewiń do **Obsługa informacje kontaktowe (usługa sieci Web)** sekcji.

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_14.png "zasad zabezpieczeń procesów biznesowych")  

4. Wybierz i Dodaj nową grupę zabezpieczeń systemu integracji do listy grup zabezpieczeń, które można zainicjować żądanie usługi sieci web. Kliknij pozycję **gotowe**. 

    ![Zasady zabezpieczeń procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_15.png "zasad zabezpieczeń procesów biznesowych")  

### <a name="activating-security-policy-changes"></a>Aktywowanie zmiany zasad zabezpieczeń

**Aby aktywować zmiany zasad zabezpieczeń:**

1. Wprowadź aktywować w polu wyszukiwania, a następnie kliknij łącze **aktywować oczekujące zmiany zasad zabezpieczeń**.

    ![Aktywuj](./media/workday-inbound-tutorial/wd_isu_16.png "aktywacji")

1. Rozpoczęcia zadania aktywować oczekujące zmiany zasad zabezpieczeń, wprowadzając komentarz na potrzeby inspekcji, a następnie kliknij przycisk **OK**.
1. Ukończenie zadania na następnym ekranie, zaznaczając pole wyboru **Potwierdź**, a następnie kliknij przycisk **OK**.

    ![Aktywuj oczekujące zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_18.png "aktywować oczekujące zabezpieczeń")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfiguracja aprowizacji użytkowników z produktu Workday do usługi Active Directory

Ta sekcja zawiera instrukcje aprowizowaniem kont użytkowników z produktu Workday do każdej domeny usługi Active Directory, w zakresie integracji usługi.

* [Instalowanie i konfigurowanie agentów inicjowania obsługi administracyjnej w środowisku lokalnym](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Dodawanie obsługi administracyjnej aplikacji łącznika i tworzenia połączenia z produktem Workday](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurowanie mapowania atrybutów](#part-3-configure-attribute-mappings)
* [Włącz i uruchom aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Część 1: Instalowanie i konfigurowanie agentów inicjowania obsługi administracyjnej w środowisku lokalnym

Aby zainicjować obsługę do usługi Active Directory w środowisku lokalnym, na serwerze, który ma strukturę 4.7.1+ .NET i sieci, dostępu do żądanego domeny usługi Active Directory musi być zainstalowany agent.

> [!TIP]
> Sprawdź wersję programu .NET framework, na serwerze wykonując instrukcje podane [tutaj](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Jeśli serwer nie ma .NET 4.7.1 lub nowszy jest zainstalowany, możesz ją pobrać z [tutaj](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Wdrożenie .NET 4.7.1+ można pobrać **[tutaj agenta inicjowania obsługi administracyjnej lokalnych](https://go.microsoft.com/fwlink/?linkid=847801)** i wykonaj czynności podane poniżej, aby ukończyć konfiguracji agenta.

1. Zaloguj się do systemu Windows Server, w którym chcesz zainstalować nowego agenta.
2. Uruchom Instalatora agenta inicjowania obsługi administracyjnej, zaakceptuj warunki i kliknij **zainstalować** przycisku.

   ![Zainstaluj ekranu](./media/workday-inbound-tutorial/pa_install_screen_1.png "zainstalować ekranu")
3. Po zakończeniu instalacji, zostanie uruchomiony Kreator i zostanie wyświetlony **usługi Azure AD Connect** ekranu. Kliknij pozycję **Uwierzytelnij** przycisk, aby nawiązać połączenie z wystąpieniem usługi Azure AD.

   ![Łączenie usługi Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "usługi Azure AD Connect")
1. Uwierzytelnianie do swojego wystąpienia usługi Azure AD przy użyciu poświadczeń administratora globalnego.

   ![Uwierzytelnianie administratora](./media/workday-inbound-tutorial/pa_install_screen_3.png "uwierzytelniania administratora")

> [!NOTE]
> Poświadczenia administratora usługi Azure AD jest używana tylko połączyć się z dzierżawą usługi Azure AD. Agent nie przechowuje poświadczenia lokalnie, jeśli na serwerze.

1. Po pomyślnym uwierzytelnieniu przy użyciu usługi Azure AD, zobaczysz **łączenie usługi Active Directory** ekranu. W tym kroku, wpisz nazwę domeny usługi AD, a następnie kliknij przycisk na **Dodaj katalog** przycisku.

   ![Add Directory](./media/workday-inbound-tutorial/pa_install_screen_4.png "Add Directory")
  
1. Możesz teraz jest monitowany o podanie poświadczeń, trzeba połączyć się z domeną usługi AD. Na jednym ekranie, można użyć **wybierz priorytet kontrolera domeny** do określenia kontrolerów domeny, które powinny być używane przez agenta wysyłania żądania alokacji.

   ![Poświadczenia domeny](./media/workday-inbound-tutorial/pa_install_screen_5.png)
1. Po skonfigurowaniu domeny, Instalator wyświetla listę skonfigurowanych domen. Na tym ekranie można powtórzyć krok #5 i 6 #, aby dodać więcej domen lub kliknąć **dalej** aby przejść do rejestracji agenta.

   ![Skonfigurowane domen](./media/workday-inbound-tutorial/pa_install_screen_6.png "skonfigurowane domen")

   > [!NOTE]
   > Jeśli masz wiele domen AD (np. na.contoso.com, emea.contoso.com), Dodaj każdej domeny indywidualnie do listy. Tylko Dodawanie domeny nadrzędnej (np. contoso.com) jest niewystarczająca. Każda domena podrzędna należy zarejestrować przy użyciu agenta.
1. Przejrzyj szczegóły konfiguracji, a następnie kliknij pozycję **Potwierdź** można zarejestrować agenta.
  
   ![Upewnij się, ekran](./media/workday-inbound-tutorial/pa_install_screen_7.png "potwierdzić ekranu")
1. Kreator konfiguracji Wyświetla postęp rejestracji agenta.
  
   ![Rejestracja agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "rejestracji agenta")
1. Gdy rejestracja agenta zakończy się pomyślnie, możesz kliknąć **wyjść** aby zakończyć pracę kreatora.
  
   ![Zamknąć ekran](./media/workday-inbound-tutorial/pa_install_screen_9.png "zamknąć ekran")
1. Zweryfikuj instalację agenta i upewnij się, że jest uruchomiona przez otwarcie przystawki "Usługi" i Wyszukaj usługę o nazwie "Microsoft Azure AD Connect Agent aprowizacji"
  
   ![Usługi](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 2: Dodawanie obsługi administracyjnej aplikacji łącznika i tworzenia połączenia z produktem Workday

**Aby skonfigurować produktu Workday do inicjowania obsługi usługi Active Directory:**

1. Przejdź do strony <https://portal.azure.com>

2. Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3. Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4. Wybierz **dodać aplikację**i wybierz **wszystkich** kategorii.

5. Wyszukaj **pracy aprowizacji usługi Active Directory**i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i ekran szczegółów aplikacji jest pokazywane, wybierz opcję **aprowizacji**

7. Zmiana **aprowizacji** **tryb** do **automatyczne**

8. Wykonaj **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemowego integracji produktu Workday za pomocą dołączona nazwa domeny dzierżawy. Powinien wyglądać mniej więcej tak: **username\@nazwa_dzierżawy**

   * **Hasło administratora —** wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla Twojej dzierżawy. Ta wartość powinna wyglądać: https://wd3-impl-services1.workday.com/ccx/service/contoso4, gdzie *contoso4* jest zastępowana nazwą dzierżawy poprawne i *wd3 impl* jest zastępowany ciągiem odpowiednie środowisko.

   * **Las usługi Active Directory —** "Name" domeny usługi Active Directory, ponieważ zarejestrowane przy użyciu agenta. Użyj listy rozwijanej, aby wybrać domeny docelowej do inicjowania obsługi. Ta wartość jest zazwyczaj ciągów, takich jak: *contoso.com*

   * **Kontener usługi Active Directory —** wprowadź nazwa Wyróżniająca kontenera, w którym tworzenia kont użytkowników domyślnie agenta.
        Przykład: *OU = użytkownicy standardowi, OU = Users, DC = contoso, DC = test*
     > [!NOTE]
     > To ustawienie pochodzi wyłącznie do gry dla podczas tworzenia kont użytkowników Jeśli *parentDistinguishedName* atrybutu nie skonfigurowano mapowania atrybutów. To ustawienie nie jest używane dla wyszukiwanie użytkowników lub aktualizacji operacji. Drzewo podrzędne całej domeny znajduje się w zakresie operacji wyszukiwania.

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

> [!NOTE]
> Usługa Azure AD aprowizacji wysyła powiadomienie e-mail, jeśli zadanie inicjowania obsługi administracyjnej przechodzi w stan [kwarantanny](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) stanu.

   * Kliknij przycisk **Testuj połączenie** przycisku. Jeśli test połączenia powiedzie się, kliknij przycisk **Zapisz** znajdujący się u góry. Jeśli nie powiedzie się, należy się zapoznać, że poświadczenia dla produktu Workday i poświadczeń usługi AD skonfigurowane na temat instalacji agenta są prawidłowe.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * Gdy poświadczenia zostały zapisane pomyślnie, **mapowania** sekcji spowoduje wyświetlenie domyślnego mapowania **zsynchronizować Workday pracownikom w lokalnej usłudze Active Directory**

### <a name="part-3-configure-attribute-mappings"></a>Część 3: Konfigurowanie mapowania atrybutów

W tej sekcji skonfigurujesz przepływ danych użytkownika z produktu Workday do usługi Active Directory.

1. Na karcie Udostępnianie w obszarze **mapowania**, kliknij przycisk **zsynchronizować Workday pracownikom w lokalnej usłudze Active Directory**.

2. W **zakres obiektów źródłowych** pola, można wybrać, który konfiguruje użytkowników w usłudze WORKDAY aplikacja musi należeć do zakresu, aby aprowizacja w usłudze AD, definiując zestaw opartych na atrybutach filtrów. Domyślny zakres jest "wszyscy użytkownicy w produkcie Workday". Przykładowe filtry:

   * Przykład: Zakres do użytkowników z identyfikatorami procesów roboczych 1000000 i 2000000 (z wyjątkiem 2000000)

      * Atrybut: WorkerID

      * Operator: Dopasowanie wyrażenia REGULARNEGO

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko pracownicy i nie tymczasowi pracownicy

      * Atrybut: EmployeeID

      * Operator: NIE MA WARTOŚCI NULL

> [!TIP]
> Podczas konfigurowania aprowizacji aplikacji po raz pierwszy, należy przetestować i zweryfikować mapowań atrybutów i wyrażenia, aby upewnić się, że go udostępnia Ci oczekiwany rezultat. Firma Microsoft zaleca przy użyciu zakresu filtry w obszarze **zakres obiektów źródłowych** do testowania mapowania przy użyciu kilku użytkowników testowych z produktu Workday. Po upewnieniu się, że mapowania działają, a następnie możesz usunąć filtr lub stopniowo rozwinąć, aby zawierała więcej użytkowników.

1. W **Akcje obiektu docelowego** pole globalnie filtrowania, jakie akcje są wykonywane na usłudze Active Directory. **Tworzenie** i **aktualizacji** występują najczęściej.

1. W **mapowania atrybutów** sekcji można zdefiniować Workday jak poszczególne atrybuty mapowania atrybutów usługi Active Directory.

1. Kliknij na istniejące mapowanie atrybutu go zaktualizować, lub kliknij **Dodaj nowe mapowanie** w dolnej części ekranu, aby dodać nowe mapowania. Mapowanie atrybutu poszczególnych obsługuje następujące właściwości:

      * **Typ mapowania**

         * **Bezpośrednie** — zapisuje wartość atrybutu produktu Workday do atrybutu usługi AD, bez konieczności wprowadzania zmian

         * **Stałe** -zapisu wartość statyczne, stała ciągu atrybutu usługi AD

         * **Wyrażenie** — pozwala na zapis niestandardową wartość do atrybutu usługi AD na podstawie co najmniej jeden dzień roboczy atrybutów. [Aby uzyskać więcej informacji znajduje się w artykule na wyrażeniach](../manage-apps/functions-for-customizing-application-data.md).

      * **Atrybut źródłowy** — atrybut użytkownika z produktu Workday. Jeśli nie ma atrybut, którego szukasz, zobacz [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](#customizing-the-list-of-workday-user-attributes).

      * **Wartość domyślna** — opcjonalne. Jeśli atrybut źródłowy ma wartość pustą, mapowanie zapisu wtedy tę wartość.
            Najbardziej typowa konfiguracja polega na pozostaw to pole puste.

      * **Atrybut docelowy** — atrybut użytkownika w usłudze Active Directory.

      * **Zgodne obiekty korzystające z tego atrybutu** — czy to mapowanie powinien być używany do jednoznacznego identyfikowania użytkowników między produktem Workday i usługi Active Directory. Ta wartość jest zazwyczaj ustawiana na pole Identyfikatora procesu roboczego dla produktu Workday, który zazwyczaj jest mapowany na jeden z atrybutów identyfikator pracownika w usłudze Active Directory.

      * **Pierwszeństwo dopasowania** — wiele pasujące atrybuty można ustawić. Jeśli dostępnych jest wiele, są one obliczane w kolejności, zdefiniowanych przez to pole. Gdy tylko zostanie znalezione dopasowanie, żadne dodatkowe dopasowania atrybuty są oceniane.

      * **Zastosuj to mapowanie**

         * **Zawsze** — Zastosuj to mapowanie na obu tworzenia użytkownika i aktualizowanie działań

         * **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w akcji tworzenia użytkownika

1. Aby zapisać mapowania, kliknij przycisk **Zapisz** w górnej części mapowanie atrybutu.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Poniżej przedstawiono przykładowe mapowania atrybutów między produktem Workday i usługi Active Directory, z niektórych wspólnych wyrażeń

* Wyrażenie, które mapuje *parentDistinguishedName* atrybut jest używany do aprowizacji użytkownika do różnych jednostkach organizacyjnych, oparte na jeden lub więcej atrybutów źródłowych produktu Workday. W tym przykładzie w tym miejscu umieszcza użytkowników w różnych jednostkach organizacyjnych, oparte na jakim mieście znajdują się w.

* *UserPrincipalName* atrybutu w usłudze Active Directory jest generowana z użyciem funkcji deduplikacja [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) , sprawdza istnienie wygenerowaną wartość w domenie docelowej usługi AD i tylko Ustawia ją, jeśli jest on unikatowy.  

* [Dokumentacja na temat pisania wyrażeń w tym miejscu jest](../manage-apps/functions-for-customizing-application-data.md). Ta sekcja zawiera przykłady Usuń znaki specjalne.

| ATRYBUT WORKDAY | ATRYBUT USŁUGI ACTIVE DIRECTORY |  ZGODNYM IDENTYFIKATOREM? | TWORZENIE I AKTUALIZOWANIE |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Tak** | Zapisane podczas tworzenia tylko |
| **PreferredNameData**    |  cn    |   |   Zapisane podczas tworzenia tylko |
| **SelectUniqueValue (Dołącz ("\@", Dołącz (".", \[FirstName\], \[LastName\]), "contoso.com"), Dołącz ("\@", Dołącz do (".", Mid (\[FirstName\], 1, 1 () \[LastName\]), "contoso.com"), Dołącz ("\@", Dołącz (".", Mid (\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Zapisane podczas tworzenia tylko 
| **Zastąp(Mid(Zastąp(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Zapisane podczas tworzenia tylko |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Tworzenie i aktualizowanie |
| **Imię**   | givenName       |     |    Tworzenie i aktualizowanie |
| **Nazwisko**   |   numery seryjne   |     |  Tworzenie i aktualizowanie |
| **PreferredNameData**  |  displayName |     |   Tworzenie i aktualizowanie |
| **Firmy**         | company   |     |  Tworzenie i aktualizowanie |
| **SupervisoryOrganization**  | department  |     |  Tworzenie i aktualizowanie |
| **ManagerReference**   | menedżer  |     |  Tworzenie i aktualizowanie |
| **BusinessTitle**   |  title     |     |  Tworzenie i aktualizowanie | 
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

Po zakończeniu konfiguracji mapowania atrybutów, możesz teraz [Włącz i uruchom usługa aprowizowania użytkowników](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfiguracja aprowizacji użytkowników do usługi Azure AD

W poniższych sekcjach opisano kroki związane z konfigurowaniem aprowizacji użytkowników z produktu Workday do usługi Azure AD w przypadku wdrożeń tylko w chmurze.

* [Dodawanie aplikacji łącznika inicjowania obsługi usługi Azure AD i tworzenia połączenia z produktem Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurowanie mapowania atrybutów Workday i Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Włącz i uruchom aprowizacji użytkowników](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Tylko wykonaj poniższą procedurę, jeśli masz tylko na chmurze użytkowników, którzy muszą być przygotowana do usługi Azure AD i nie lokalnej usługi Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie aplikacji łącznika inicjowania obsługi usługi Azure AD i tworzenia połączenia z produktem Workday

**Aby skonfigurować produktu Workday do usługi Azure Active Directory inicjowania obsługi administracyjnej dla użytkowników tylko w chmurze:**

1. Przejdź do pozycji <https://portal.azure.com> (Plik > Nowy > Inny).

2. Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3. Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4. Wybierz **dodać aplikację**, a następnie wybierz pozycję **wszystkich** kategorii.

5. Wyszukaj **produktu Workday do inicjowania obsługi usługi Azure AD**i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i ekran szczegółów aplikacji jest pokazywane, wybierz opcję **aprowizacji**

7. Zmiana **aprowizacji** **tryb** do **automatyczne**

8. Wykonaj **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemowego integracji produktu Workday za pomocą dołączona nazwa domeny dzierżawy. Powinien wyglądać następująco: username@contoso4

   * **Hasło administratora —** wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla Twojej dzierżawy. Ta wartość powinna wyglądać: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, gdzie *contoso4* jest zastępowana nazwą dzierżawy poprawne i *wd3 impl* jest zastępowany ciągiem odpowiednie środowisko. Jeśli ten adres URL nie jest znany, skontaktuj się z produktem Workday integracji pomocy technicznej lub partner przedstawicielem, aby określić prawidłowy adres URL do użycia.

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku.

   * Jeśli test połączenia powiedzie się, kliknij przycisk **Zapisz** znajdujący się u góry. Jeśli nie powiedzie się, należy upewnić się, że adres URL produktu Workday i poświadczenia są prawidłowe w produkcie Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Część 2: Konfigurowanie mapowania atrybutów Workday i Azure AD

W tej sekcji skonfigurujesz, jak użytkownik przepływu danych z produktu Workday do usługi Azure Active Directory dla użytkowników tylko w chmurze.

1. Na karcie Udostępnianie w obszarze **mapowania**, kliknij przycisk **zsynchronizować pracowników do usługi Azure AD**.

2. W **zakres obiektów źródłowych** pola, można wybrać, który konfiguruje użytkowników w usłudze WORKDAY aplikacja powinna być w zakresie aprowizacji usługi Azure AD, definiując zestaw opartych na atrybutach filtrów. Domyślny zakres jest "wszyscy użytkownicy w produkcie Workday". Przykładowe filtry:

   * Przykład: Zakres do użytkowników z identyfikatorami procesów roboczych 1000000 i 2000000

      * Atrybut: WorkerID

      * Operator: Dopasowanie wyrażenia REGULARNEGO

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko warunkowe pracowników i pracowników nie regularne

      * Atrybut: ContingentID

      * Operator: NIE MA WARTOŚCI NULL

3. W **Akcje obiektu docelowego** pole globalnie filtrowania, jakie akcje są wykonywane w usłudze Azure AD. **Tworzenie** i **aktualizacji** występują najczęściej.

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

   * **Zgodne obiekty korzystające z tego atrybutu** — czy ten atrybut powinien być używany do jednoznacznego identyfikowania użytkowników między produktem Workday i Azure AD. Ta wartość jest zazwyczaj ustawiana na pole Identyfikatora procesu roboczego dla produktu Workday, która jest przeważnie mapowana identyfikator pracownika, atrybut (nowy) lub atrybut rozszerzenia w usłudze Azure AD.

   * **Pierwszeństwo dopasowania** — wiele pasujące atrybuty można ustawić. Jeśli dostępnych jest wiele, są one obliczane w kolejności, zdefiniowanych przez to pole. Gdy tylko zostanie znalezione dopasowanie, żadne dodatkowe dopasowania atrybuty są oceniane.

   * **Zastosuj to mapowanie**

     * **Zawsze** — Zastosuj to mapowanie na obu tworzenia użytkownika i aktualizowanie działań

     * **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w akcji tworzenia użytkownika

6. Aby zapisać mapowania, kliknij przycisk **Zapisz** w górnej części mapowanie atrybutu.

Po zakończeniu konfiguracji mapowania atrybutów, możesz teraz [Włącz i uruchom usługa aprowizowania użytkowników](#enable-and-launch-user-provisioning).

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurowanie funkcji zapisywania zwrotnego adresów e-mail w produkcie Workday

Wykonaj te instrukcje, aby skonfigurować zapisywanie zwrotne adresy e-mail użytkowników z usługi Azure Active Directory w produkcie Workday.

* [Dodawanie funkcji zapisywania zwrotnego aplikacji łącznika i tworzenia połączenia z produktem Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurowanie funkcji zapisywania zwrotnego mapowania atrybutów](#part-2-configure-writeback-attribute-mappings)
* [Włącz i uruchom aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie funkcji zapisywania zwrotnego aplikacji łącznika i tworzenia połączenia z produktem Workday

**Aby skonfigurować łącznik produktu Workday funkcji zapisywania zwrotnego:**

1. Przejdź do strony <https://portal.azure.com>

2. Na pasku nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**

3. Wybierz **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.

4. Wybierz **dodać aplikację**, a następnie wybierz **wszystkich** kategorii.

5. Wyszukaj **zapisywania zwrotnego Workday**i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i ekran szczegółów aplikacji jest pokazywane, wybierz opcję **aprowizacji**

7. Zmiana **aprowizacji** **tryb** do **automatyczne**

8. Wykonaj **poświadczeń administratora** sekcji w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemowego integracji produktu Workday za pomocą dołączona nazwa domeny dzierżawy. Powinien wyglądać mniej więcej tak: *username\@contoso4*

   * **Hasło administratora —** wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL — dzierżawy** wprowadź adres URL punktu końcowego usługi sieci web produktu Workday dla Twojej dzierżawy. Ta wartość powinna wyglądać: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, gdzie *contoso4* jest zastępowana nazwą dzierżawy poprawne i *wd3 impl* jest zastępowany ciągiem odpowiednie środowisko (jeśli jest to konieczne).

   * **Wiadomość E-mail z powiadomieniem —** wprowadź swój adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** przycisku. Jeśli test połączenia powiedzie się, kliknij przycisk **Zapisz** znajdujący się u góry. Jeśli nie powiedzie się, należy upewnić się, że adres URL produktu Workday i poświadczenia są prawidłowe w produkcie Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Część 2: Konfigurowanie funkcji zapisywania zwrotnego mapowania atrybutów

W tej sekcji skonfigurujesz przepływ atrybutów funkcji zapisywania zwrotnego z usługi Azure AD w produkcie Workday.

1. Na karcie Udostępnianie w obszarze **mapowania**, kliknij przycisk **synchronizacji Azure użytkownicy usługi Active Directory w produkcie Workday**.

2. W **zakres obiektów źródłowych** pola, można również filtrować, który zestawów użytkowników w usłudze Azure Active Directory powinny mieć ich adresy e-mail, zapisywane z powrotem w produkcie Workday. Domyślny zakres jest "wszyscy użytkownicy w usłudze Azure AD".

3. W **mapowania atrybutów** sekcji, zaktualizuj identyfikator pasującego do wskazania to atrybut usługi Azure Active Directory, gdzie jest przechowywany identyfikator procesu roboczego Workday lub identyfikator pracownika. Popularne metody dopasowania jest synchronizowane z produktem Workday, identyfikator procesu roboczego lub identyfikator pracownika do extensionAttribute1 15 w usłudze Azure AD, a następnie użyć tego atrybutu w usłudze Azure AD, aby dopasować użytkowników z powrotem w produkcie Workday.

4. Aby zapisać mapowania, kliknij przycisk **Zapisz** w górnej części mapowanie atrybutu.

Po zakończeniu konfiguracji mapowania atrybutów, możesz teraz [Włącz i uruchom usługa aprowizowania użytkowników](#enable-and-launch-user-provisioning). 

## <a name="enable-and-launch-user-provisioning"></a>Włącz i uruchom aprowizacji użytkowników

Po ukończeniu inicjowania obsługi administracyjnej konfiguracji produktu Workday w aplikacji można włączyć usługi aprowizacji w witrynie Azure portal.

> [!TIP]
> Domyślnie po włączeniu usługa aprowizowania będzie inicjował inicjowania obsługi administracyjnej operacje dla wszystkich użytkowników w zakresie. Jeśli wystąpią błędy, problemy danych mapowanie lub Workday, zadanie inicjowania obsługi administracyjnej może zakończyć się niepowodzeniem i przechodzi w stan kwarantanny. Aby tego uniknąć, najlepszym rozwiązaniem, zaleca się skonfigurowanie **zakres obiektów źródłowych** filtru i testowania mapowania atrybutów z kilkoma użytkownikami testów przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po upewnieniu się, że mapowania pracy i zapewniając zakładanych wyników, a następnie możesz usunąć filtr lub stopniowo rozwinąć, aby zawierała więcej użytkowników.

1. W **aprowizacji** kartę, należy ustawić **stanie aprowizacji** do **na**.

2. Kliknij pozycję **Zapisz**.

3. Ta operacja zostanie uruchomione początkowej synchronizacji może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w ramach dzierżawy produktu Workday. 

4. W każdej chwili sprawdzić **dzienniki inspekcji** kartę w witrynie Azure portal, aby zobaczyć, jakie akcje przeprowadził usługi aprowizacji. Dzienniki inspekcji zawiera listę wszystkich zdarzeń indywidualnych synchronizacji, wykonywane przez usługę aprowizacji, np użytkowników ich odczytu z produktu Workday i następnie później dodane lub zaktualizowane w usłudze Active Directory. Zapoznaj się z sekcją rozwiązywanie problemów, aby uzyskać instrukcje, jak przeglądać dzienniki inspekcji i usuń błędy inicjowania obsługi administracyjnej.

5. Po ukończeniu synchronizacji początkowej zapisze raport z podsumowaniem inspekcji **aprowizacji** karty, jak pokazano poniżej.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

* **Pytania dotyczące możliwości rozwiązania**
  * [Podczas przetwarzania nowego zatrudnienia z produktu Workday, jak to rozwiązanie ustawić hasło dla nowego konta użytkownika w usłudze Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Czy rozwiązanie obsługuje wysyłanie powiadomień e-mail po zainicjowaniu obsługi administracyjnej zakończenie operacji](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Jak zarządzać dostarczania haseł dla nowych pracowników i bezpiecznie udostępniają mechanizm do zresetowania swojego hasła?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Czy rozwiązanie będzie buforować Workday profilów użytkowników w chmurze usługi Azure AD lub warstwie aprowizacji agenta?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Przypisywanie obsługę rozwiązań lokalnych grup usługi AD dla użytkownika?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Które interfejsy API z produktu Workday rozwiązanie używa do zapytania i aktualizację profilów roboczych Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Czy można skonfigurować mojej dzierżawy produktu Workday HCM, za pomocą dwóch dzierżaw usługi Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Dlaczego użytkownik "Workday do usługi Azure AD" Inicjowanie obsługi administracyjnej aplikacji nie jest obsługiwana, jeśli po wdrożeniu usługi Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Jak sugeruje ulepszenia lub poprosić o nowe funkcje związane z integracją z produktu Workday i Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Inicjowanie obsługi administracyjnej pytania agenta**
  * [Co to jest wersją GA usługi aprowizacji agenta?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Jak sprawdzić wersję mojego aprowizacji agenta?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Aktualizacji agenta inicjowania obsługi administracyjnej jest automatycznie wypchnie firmy Microsoft?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Inicjowanie obsługi administracyjnej Agent może być instalowany na tym samym serwerze z narzędziem AAD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Jak skonfigurować agenta inicjowania obsługi, Użyj serwera proxy dla komunikacji wychodzącej HTTP?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Jak zagwarantować, że aprowizacji agenta jest w stanie komunikować się z dzierżawy usługi Azure AD i zapory nie blokują porty wymagane przez agenta?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Jak anulowanie rejestracji domeny skojarzone z moich aprowizacji agenta?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Jak odinstalować agenta inicjowania obsługi administracyjnej?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Dzień roboczy pytania mapowania i konfiguracji atrybutu usługi AD**
  * [Jak utworzyć kopię zapasową lub wyeksportować funkcjonalną kopię mojego produktu Workday aprowizacji atrybutu mapowania i schematu](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Atrybutów niestandardowych, które masz w produkcie Workday i usługi Active Directory. Jak skonfigurować rozwiązanie do pracy z moich atrybutów niestandardowych?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Czy mogę uaktywnić zdjęcia użytkownika z produktu Workday do usługi Active Directory](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Jak zsynchronizować numery telefonów komórkowych z produktu Workday w oparciu o zgodę użytkownika na użycie publicznego?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Jak sformatować nazw wyświetlanych w AD zgodnie z atrybutów Dział/kraj/miasta i odchylenia regionalnych dojście?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Jak używać SelectUniqueValue do generowania unikatowych wartości dla atrybutu samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Jak usunąć znaków i znaków diakrytycznych i przekonwertować je na normalne litery angielskie?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Pytania dotyczące możliwości rozwiązania

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Podczas przetwarzania nowego zatrudnienia z produktu Workday, jak to rozwiązanie ustawić hasło dla nowego konta użytkownika w usłudze Active Directory?

Gdy lokalna agenta inicjowania obsługi administracyjnej pobiera żądanie, aby utworzyć nowe konto usługi AD, automatycznie generuje losowe hasło złożone zaprojektowanych do spełniania wymagań dotyczących złożoności haseł, które zostały zdefiniowane przez serwer AD i ustawia to obiektu user. To hasło nie jest rejestrowana w dowolnym miejscu.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Czy rozwiązanie obsługuje wysyłanie powiadomień e-mail po zainicjowaniu obsługi administracyjnej zakończenie operacji

Nie, wysyłanie powiadomień e-mail po zakończeniu operacji udostępniania nie jest obsługiwana w bieżącej wersji.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Jak zarządzać dostarczania haseł dla nowych pracowników i bezpiecznie udostępniają mechanizm do zresetowania swojego hasła?

Jednym z ostatnim etapy aprowizowania nowego konta usługi AD jest dostarczanie tymczasowego hasła, które są przypisane do konta usługi AD. Wiele firm nadal korzystać z tradycyjne podejście dostarczania menedżerem użytkownika, który następnie przekazuje hasło do nowego zatrudnienia/dorywczy hasło tymczasowe. Ten proces ma lukę w zabezpieczeniach nieprzerwaną pracę i jest opcją dostępną w celu zaimplementowania lepszym rozwiązaniem, korzystając z możliwości usługi Azure AD.

W ramach procesu zatrudniania zespoły HR zwykle Uruchom sprawdzanie tła i Zweryfikuj numer telefonu komórkowego nowego zatrudnienia. Z aplikacją Workday do integracji aprowizacji użytkownika usługi AD możesz tworzyć na podstawie ten fakt i wprowadzania hasła Samoobsługowe Resetowanie możliwości dla użytkownika na 1. dnia. Jest to osiągane przez propagowanie atrybutu "Numer telefonu komórkowego" nowego zatrudnienia z produktu Workday do usługi AD, a następnie z usługi AD z usługą Azure AD przy użyciu programu AAD Connect. Po "Numer telefonu komórkowego" znajduje się w usłudze Azure AD, można włączyć [samoobsługowego resetowania haseł (SSPR)](../authentication/howto-sspr-authenticationdata.md) dla konta użytkownika, więc ten na 1 dzień, nowego zatrudnienia numer telefonu komórkowego zarejestrowanych i zweryfikowane do uwierzytelniania można używać.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Czy rozwiązanie będzie buforować Workday profilów użytkowników w chmurze usługi Azure AD lub warstwie aprowizacji agenta?

Nie, to rozwiązanie nie zachowuje pamięć podręczną profile użytkowników. Usługa aprowizowania usługi Azure AD po prostu działa jako podmiot przetwarzający dane firma odczytywanie danych z produktu Workday i zapisywanie do docelowej usługi Active Directory lub Azure AD. Zobacz sekcję [zarządzanie danymi osobowymi](#managing-personal-data) Aby uzyskać szczegółowe informacje dotyczące utrzymania ochrony prywatności i danych użytkowników.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Przypisywanie obsługę rozwiązań lokalnych grup usługi AD dla użytkownika?

Ta funkcja nie jest obecnie obsługiwana. Zalecaną praktyką jest, aby wdrożyć skrypt programu PowerShell, który odpytuje punkt końcowy interfejsu API programu Graph usługi Azure AD, za dane dziennika inspekcji i używać go do wyzwolenia scenariuszy, takich jak przypisanie do grupy. Ten skrypt programu PowerShell, można dołączyć do harmonogramu zadań i wdrożone na tym samym komputerze z agentem aprowizowania.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Które interfejsy API z produktu Workday rozwiązanie używa do zapytania i aktualizację profilów roboczych Workday?

Rozwiązanie używa obecnie następujące interfejsy API z produktu Workday:

* Get_Workers (v21.1) do pobierania informacji procesu roboczego
* Maintain_Contact_Information (v26.1) dla funkcji zapisywania zwrotnego E-mail pracy

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Czy można skonfigurować mojej dzierżawy produktu Workday HCM, za pomocą dwóch dzierżaw usługi Azure AD?

Tak, ta konfiguracja jest obsługiwana. Poniżej przedstawiono kroki wysokiego poziomu konfigurowania tego scenariusza:

* Wdróż agenta inicjowania obsługi administracyjnej #1 i zarejestrowanie go za pomocą dzierżawy usługi Azure AD #1.
* Wdróż agenta inicjowania obsługi administracyjnej #2 i zarejestrowanie go za pomocą dzierżawy usługi Azure AD #2.
* Oparte na "domen podrzędnych" każdego agenta inicjowania obsługi służącej do zarządzania, należy skonfigurować każdego agenta z domeny. Jeden agent może obsługiwać wiele domen.
* W witrynie Azure portal należy zainstalować produktu Workday do aplikacji aprowizacji użytkownika usługi AD w każdej dzierżawy i skonfigurować go za pomocą odpowiednich domenach.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Dlaczego użytkownik "Workday do usługi Azure AD" Inicjowanie obsługi administracyjnej aplikacji nie jest obsługiwana, jeśli po wdrożeniu usługi Azure AD Connect?

Usługa Azure AD jest używana w trybie hybrydowym (gdzie go zawiera mieszankę cloud + użytkowników lokalnych), koniecznie wyraźne określenie "źródło własności". Zwykle hybrydowe scenariusze wymagają od wdrożenia programu Azure AD Connect. Po wdrożeniu usługi Azure AD Connect lokalne usługi AD jest źródło własności. Wprowadzenie do produktu Workday do usługi Azure AD łącznika do mieszanki może doprowadzić do sytuacji, gdy wartości atrybutów Workday potencjalnie może spowodować zastąpienie wartości ustawionych przez program Azure AD Connect. Dlatego korzystanie z aplikacji aprowizacji "Produktu Workday do usługi Azure AD" nie jest obsługiwana, gdy program Azure AD Connect jest włączona. W takich przypadkach zaleca się używanie "Produktu Workday do usługi AD User" aprowizacji aplikacji dla użytkowników w lokalnej usługi AD i synchronizowanie ich w usłudze Azure AD za pomocą usługi Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Jak sugeruje ulepszenia lub poprosić o nowe funkcje związane z integracją z produktu Workday i Azure AD?

Twoja opinia jest bardzo ważnych, ponieważ pomoże nam to Określanie kierunku dla przyszłych wersji i ulepszeń. Firma Microsoft Zapraszamy wszystkich opinii i zachęcamy do przesyłania propozycji pomysł lub ulepszenia w [forum opinii platformy Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Opinie są powiązane z integracji z produktem Workday, wybierz kategorię *aplikacji SaaS* i wyszukiwania według słów kluczowych *Workday* można znaleźć istniejącego opinie związane z produktu Workday.

![Aplikacje SaaS w usłudze UserVoice](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![Dzień roboczy UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Gdy sugerowanie nowy pomysł, sprawdź ktoś ma już sugerowana podobnych funkcji. W takim przypadku można nawet głosować funkcję lub ulepszenie żądania. Można także pozostawić komentarze dotyczące specyficznego przypadku użycia Pokaż zainteresowanie pomysł i pokazują, jak ta funkcja będzie przydatna w przypadku zbyt.

### <a name="provisioning-agent-questions"></a>Inicjowanie obsługi administracyjnej pytania agenta

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Co to jest wersją GA usługi aprowizacji agenta?

* Wersja GA agenta inicjowania obsługi administracyjnej jest 1.1.30 i nowsze wersje.
* Jeśli wersja agenta jest mniejsza niż 1.1.30 używasz wersji publicznej wersji zapoznawczej, a jej zostaną automatycznie zaktualizowane do wersji ogólnie dostępnej wersji, jeśli na serwerze uruchomiony jest agent, .NET 4.7.1 środowiska uruchomieniowego.
  * Możesz [zapoznaj się z wersją .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na serwerze jest zainstalowany. Jeśli serwer nie jest uruchomiona .NET 4.7.1, możesz to zrobić [pobrać i zainstalować program .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Aprowizacji agenta zostanie automatycznie zaktualizowane do wersji Ogólnodostępnej, po zainstalowaniu platformy .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Jak sprawdzić wersję mojego aprowizacji agenta?

* Zaloguj się do serwera Windows, w którym jest zainstalowany Agent inicjowania obsługi administracyjnej.
* Przejdź do **Panelu sterowania** -> **Odinstaluj lub zmień Program** menu
* Wyszukaj wersję odpowiadający wpis **agenta Microsoft Azure AD Connect inicjowania obsługi administracyjnej**

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Aktualizacji agenta inicjowania obsługi administracyjnej jest automatycznie wypchnie firmy Microsoft?

Tak, firma Microsoft automatycznie aktualizuje agenta inicjowania obsługi administracyjnej. Można wyłączyć aktualizacji automatycznych, zatrzymując usługę Windows **Microsoft Azure AD Connect Agent Updater**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>Inicjowanie obsługi administracyjnej Agent może być instalowany na tym samym serwerze z narzędziem AAD Connect?

Tak, można zainstalować agenta inicjowania obsługi administracyjnej na tym samym serwerze, który jest uruchamiany program AAD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>W czasie konfiguracji agenta inicjowania obsługi administracyjnej monituje o podanie poświadczeń administratora usługi Azure AD. Czy Agent przechowywane poświadczenia lokalnie na serwerze?

Podczas konfigurowania agenta inicjowania obsługi administracyjnej monituje o podanie poświadczeń administratora usługi Azure AD tylko połączyć się z dzierżawą usługi Azure AD. Nie były przechowywane poświadczenia lokalnie na serwerze. Jednak przechowywać poświadczenia używane do łączenia z *lokalnej domeny usługi Active Directory* w lokalnym magazynie hasła Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Jak skonfigurować agenta inicjowania obsługi, Użyj serwera proxy dla komunikacji wychodzącej HTTP?

Inicjowanie obsługi administracyjnej Agent obsługuje korzystanie z serwera proxy ruchu wychodzącego. Możesz ją skonfigurować, edytując plik konfiguracji agenta **C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\AADConnectProvisioningAgent.exe.config**. Dodaj następujące wiersze do niego, pod koniec pliku przed zamykającym `</configuration>` tagu.
Zmienne [serwer proxy] i [port serwera proxy] Zamień na nazwę serwera proxy i port wartości.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Jak zagwarantować, że aprowizacji agenta jest w stanie komunikować się z dzierżawy usługi Azure AD i zapory nie blokują porty wymagane przez agenta?

Możesz również sprawdzić, czy masz wszystkie wymagane porty otwarte, otwierając [narzędzie Test porty łącznika](https://aadap-portcheck.connectorporttest.msappproxy.net/) z siecią lokalną. Więcej zielone znaczniki wyboru oznacza większą odporność.

Aby upewnić się, że narzędzie zapewnia odpowiednich wyników, należy koniecznie:

* Otwórz narzędzie w przeglądarce z serwera, na którym zainstalowano agenta inicjowania obsługi administracyjnej.
* Upewnij się, czy wszystkie serwery proxy lub zapory mające zastosowanie do aprowizacji agenta są również stosowane do tej strony. Można to zrobić w programie Internet Explorer, przechodząc do **Ustawienia -> Opcje internetowe -> połączeń -> Ustawienia sieci LAN**. Na tej stronie zobaczysz pola "Użyj serwera Proxy serwera dla Twojej sieci LAN". Zaznacz to pole, a następnie umieścić adres serwera proxy w polu "Address".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Jednego agenta inicjowania obsługi administracyjnej można skonfigurować do obsługi administracyjnej wielu domen AD?

Tak, można skonfigurować do obsługi wielu domen AD tak długo, jak agent ma linii wzroku do kontrolerów domeny odpowiednich jednego agenta inicjowania obsługi administracyjnej. Firma Microsoft zaleca skonfigurowanie grupy 3 aprowizacja agentów, obsługujący ten sam zestaw domeny usługi AD, aby zapewnić wysoką dostępność i udostępniać awarii za pośrednictwem pomocy technicznej.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Jak anulowanie rejestracji domeny skojarzone z moich aprowizacji agenta?

* W witrynie Azure portal, Pobierz *identyfikator dzierżawy* dzierżawy usługi Azure AD.
* Zaloguj się do serwera Windows z agentem Aprowizowania.
* Otwórz program powershell jako Administrator Windows.
* Przejdź do katalogu zawierającego skryptów rejestracji i uruchom następujące polecenia, zastępując \[identyfikator dzierżawy\] parametru z wartością identyfikatora dzierżawy.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Na liście agentów, które pojawiają się — kopiowania wartości "ID" pole tego zasobu którego *resourceName* równa się do swojej nazwy domeny usługi AD.
* Wklej identyfikator do tego polecenia, a następnie uruchomić go w programie Powershell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Uruchom ponownie Kreatora konfiguracji agenta.
* Wszelkich innych agentów, które wcześniej zostały przypisane do tej domeny musi zostać ponownie skonfigurowany.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Jak odinstalować agenta inicjowania obsługi administracyjnej?

* Zaloguj się do serwera Windows, w którym jest zainstalowany Agent inicjowania obsługi administracyjnej.
* Przejdź do **Panelu sterowania** -> **Odinstaluj lub zmień Program** menu
* Odinstaluj następujące programy:
  * Microsoft Azure AD Connect agenta inicjowania obsługi administracyjnej
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect Agent pakietu Aprowizacyjnego

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Dzień roboczy pytania mapowania i konfiguracji atrybutu usługi AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Jak utworzyć kopię zapasową lub wyeksportować funkcjonalną kopię mojego produktu Workday aprowizacji atrybutu mapowania i schematu

Aby wyeksportować konfigurację aprowizacji użytkowników w produkcie Workday, można użyć interfejsu API Microsoft Graph. Skorzystaj z procedury opisanej w sekcji [eksportowanie i importowanie konfiguracji produktu Workday aprowizacji atrybutu mapowania użytkowników](#exporting-and-importing-your-configuration) Aby uzyskać szczegółowe informacje.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Atrybutów niestandardowych, które masz w produkcie Workday i usługi Active Directory. Jak skonfigurować rozwiązanie do pracy z moich atrybutów niestandardowych?

Rozwiązanie obsługuje atrybutów niestandardowych z produktu Workday i usługi Active Directory. Aby dodać swoje niestandardowe atrybuty do schematu mapowania, otwórz **mapowanie atrybutu** bloku i przewiń w dół do rozwiń sekcję **Pokaż opcje zaawansowane**. 

![Edytuj listę atrybutów](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Aby dodać swoje niestandardowe atrybuty Workday, wybierz opcję *Edytuj listę atrybutów dla produktu Workday* i aby dodać swoje niestandardowe atrybuty usługi AD, wybierz opcję *Edytuj listę atrybutów dla na lokalnej usługi Active Directory*.

Zobacz też:

* [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Jak skonfigurować rozwiązanie, aby aktualizować tylko atrybuty usługi AD w oparciu o zmiany produktu Workday i nie tworzyć nowych kont usługi AD?

Tę konfigurację można osiągnąć przez ustawienie **Akcje obiektu docelowego** w **mapowania atrybutów** bloku, jak pokazano poniżej:

![Aktualizuj akcję](./media/workday-inbound-tutorial/wd_target_update_only.png)

Zaznacz pole wyboru "Aktualizuj" dla tylko operacje aktualizacji mogą przepływać z produktu Workday do usługi AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Czy mogę uaktywnić zdjęcia użytkownika z produktu Workday do usługi Active Directory

Rozwiązanie nie jest obecnie obsługiwane ustawienie atrybutów binarnych, takich jak *thumbnailphoto usługa* i *jpegPhoto* w usłudze Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Jak zsynchronizować numery telefonów komórkowych z produktu Workday w oparciu o zgodę użytkownika na użycie publicznego?

* Przejdź bloku "Aprowizowanie" dzień roboczy inicjowania obsługi administracyjnej aplikacji.
* Kliknij pozycję mapowania atrybutów 
* W obszarze **mapowania**, wybierz opcję **zsynchronizować Workday pracownikom w lokalnej usłudze Active Directory** (lub **zsynchronizować pracowników z produktu Workday do usługi Azure AD**).
* Na stronie mapowania atrybutów przewiń w dół, a następnie zaznacz pole "Pokaż opcje zaawansowane".  Kliknij pozycję **Edytuj listę atrybutów dla produktu Workday**
* W otwartym bloku zlokalizować atrybutu "wyraz Mobile", a następnie kliknij polecenie w wierszu, aby można było edytować **wyrażenie interfejsu API** ![Mobile RODO](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Zastąp **wyrażenie interfejsu API** następujące nowe wyrażenie, które pobiera numer telefonu komórkowego pracy tylko wtedy, gdy "Publiczny Flaga użycia" jest ustawiona na wartość "True" w produkcie Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Zapisz listę atrybutów.
* Zapisz mapowanie atrybutu.
* Wyczyść bieżący stan, a następnie ponownie uruchom pełną synchronizację.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Jak sformatować nazw wyświetlanych w AD zgodnie z atrybutów Dział/kraj/miasta i odchylenia regionalnych dojście?

Jest typowym wymogiem do skonfigurowania *displayName* atrybutu w AD, tak aby zawiera także informacje o kraju i działu użytkownika. Dla np. Jeśli Jan Kowalski pracuje w dziale marketingu, w Stanach Zjednoczonych, możesz zechcieć jego *displayName* pojawienie się jako *Smith, John (Marketing-pl)*.

Oto jak można obsługiwać takie wymagania do konstruowania *CN* lub *displayName* obejmujący atrybuty, takie jak firmy, jednostki biznesowe, miasta lub kraju.

* Każdy atrybut produktu Workday są pobierane przy użyciu podstawowego wyrażenia XPATH interfejsu API, które można skonfigurować w **mapowanie atrybutu -> Zaawansowane sekcji -> Edytuj listę atrybutów dla produktu Workday**. Oto domyślne wyrażenie XPATH interfejsu API dla produktu Workday *PreferredFirstName*, *PreferredLastName*, *firmy* i *SupervisoryOrganization* atrybutów.

     [!div class="mx-tdCol2BreakAll"]
     | Atrybut WORKDAY | Wyrażenie XPATH interfejsu API |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Firma | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD:Worker / wd:Worker_Data / wd:Organization_Data / wd:Worker_Organization_Data / wd:Organization_Data [wd:Organization_Type_Reference / wd:ID [@wd:type= "Organization_Type_ID"] = 'Nadzoru'] /wd:Organization_Name/text() |
  
   Potwierdzenia otrzymania pracy zespołu produktu Workday, że powyższe wyrażenie interfejsu API jest prawidłowy dla danej konfiguracji dzierżawy produktu Workday. Jeśli to konieczne, można je edytować zgodnie z opisem w sekcji [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](#customizing-the-list-of-workday-user-attributes).

* Podobnie kraju informacji zawartych w produkcie Workday są pobierane przy użyciu następujących XPATH: *wd:Worker / wd:Worker_Data / wd:Employment_Data / wd:Position_Data / wd:Business_Site_Summary_Data / wd:Address_Data / wd:Country_Reference*

     Istnieje 5 związane z kraju atrybutów, które są dostępne w sekcji lista atrybutów produktu Workday.

     | Atrybut WORKDAY | Wyrażenie XPATH interfejsu API |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Upewnij się z zespołem produktu Workday, że powyższe wyrażeń interfejsu API są prawidłowe dla danej konfiguracji dzierżawy produktu Workday. Jeśli to konieczne, można je edytować zgodnie z opisem w sekcji [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](#customizing-the-list-of-workday-user-attributes).

* Aby skompilować wyrażenia mapowanie atrybutu prawo, zidentyfikuj atrybut, którego produktu Workday "autorytatywnie" reprezentuje użytkownika imię, ostatnia nazwa, kraju i działu. Załóżmy, że atrybuty są *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* i *SupervisoryOrganization* odpowiednio. Umożliwia to tworzenie wyrażenia dla usług AD *displayName* atrybutu w następujący sposób, aby ustalić nazwę wyświetlaną, takich jak *Smith, John (Marketing-pl)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Po utworzeniu wyrażenie prawej krawędzi, Edytuj tabelę mapowań atrybutów i modyfikować *displayName* Mapowanie atrybutów, jak pokazano poniżej:   ![Mapowanie DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Rozszerzanie w powyższym przykładzie, możemy powiedzieć, które chcesz przekonwertować nazwy miast przychodzących z produktu Workday do wartości skrótu, a następnie użyć go do tworzenia nazw wyświetlanych takich jak *Smith, John (CHI)* lub *Doe, Magdalena (NYC)*, następnie ten wynik można osiągnąć przy użyciu wyrażenie Switch z aplikacją Workday *gmina* atrybutu jako decydującym zmiennej.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Zobacz też:
  * [Składnia funkcji przełącznika](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Dołącz do składnia funkcji](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Dołącz Składnia funkcji](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Jak używać SelectUniqueValue do generowania unikatowych wartości dla atrybutu samAccountName?

Załóżmy, że chcesz wygenerować unikatowe wartości *samAccountName* atrybutu przy użyciu kombinacji *FirstName* i *LastName* atrybuty z produktu Workday. Podane poniżej to wyrażenie, które można uruchomić za pomocą:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
)
```

Jak działa powyższym wyrażeniu: Jeśli użytkownik jest Jan Kowalski, najpierw próbuje wygenerować JSmith, jeśli JSmith już istnieje, a następnie generuje JoSmith, który istnienia generuje JohSmith. Wyrażenie gwarantuje również, że wartość generowane spełnia ograniczenia długości i znaki specjalne ograniczeń skojarzone z *samAccountName*.

Zobacz też:

* [MID Składnia funkcji](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Zastąp Składnia funkcji](../manage-apps/functions-for-customizing-application-data.md#replace)
* [Składnia funkcji SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Jak usunąć znaków i znaków diakrytycznych i przekonwertować je na normalne litery angielskie?

Użyj funkcji [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) Aby usunąć znaki specjalne w imię i nazwisko użytkownika, podczas konstruowania adresu e-mail lub nazwy Pospolitej wartość dla użytkownika.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

W tej części podano dokładne wskazówki dotyczące sposobu rozwiązywania problemów ze inicjowania obsługi problemów za pomocą usługi integracji z produktem Workday za pomocą dzienników inspekcji usługi AD platformy Azure i dzienniki Podglądu zdarzeń systemu Windows Server. Opiera się na górze ogólne kroki rozwiązywania problemów i pojęcia przechwycone w [samouczka: Raporty dotyczące aprowizacja kont użytkowników](../manage-apps/check-status-user-account-provisioning.md)

W tej sekcji omówiono następujące aspekty rozwiązywania problemów:

* [Konfigurowanie podglądu zdarzeń Windows dla agenta, rozwiązywania problemów](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Konfigurowanie w witrynie Azure portal. dzienniki inspekcji Rozwiązywanie problemów z usługą](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Operacje tworzenia dzienników zrozumienia dla konta użytkownika usługi AD](#understanding-logs-for-ad-user-account-create-operations)
* [Opis Dzienniki Menedżera operacje aktualizacji](#understanding-logs-for-manager-update-operations)
* [Rozwiązywanie często napotkała błędy](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Konfigurowanie podglądu zdarzeń Windows dla agenta, rozwiązywania problemów

* Zaloguj się do maszyny systemu Windows Server, których jest wdrażany Agent inicjowania obsługi administracyjnej
* Otwórz **Podglądzie zdarzeń systemu Windows Server** aplikacji klasycznej.
* Wybierz **Dzienniki Windows > Aplikacja**.
* Użyj **Filtruj bieżący dziennik...** opcję, aby wyświetlić wszystkie zdarzenia zarejestrowane w lokalizacji source **usługi AAD. Connect.ProvisioningAgent** i wykluczyć zdarzenia o identyfikatorze zdarzenia "5" przez określenie filtru "-5", jak pokazano poniżej.

  ![Podgląd zdarzeń Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Kliknij przycisk **OK** i sortowanie widok wyników przez **daty i godziny** kolumny.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Konfigurowanie w witrynie Azure portal. dzienniki inspekcji Rozwiązywanie problemów z usługą

* Uruchom [witryny Azure portal](https://portal.azure.com)i przejdź do **dzienniki inspekcji** część pracy inicjowania obsługi administracyjnej aplikacji.
* Użyj **kolumn** przycisk na stronie dzienniki inspekcji, aby wyświetlić następujące kolumny w widoku (daty, działania, stan, Przyczyna stanu). Ta konfiguracja gwarantuje, można skupić się tylko na danych, która jest odpowiednia przy rozwiązywaniu problemów.

  ![Kolumny dziennika inspekcji](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Użyj **docelowej** i **zakres dat** parametry do filtrowania widoku zapytania. 
  * Ustaw **docelowej** zapytania parametr "Identyfikator procesu roboczego" lub "Identyfikator pracownika" dzień roboczy obiektu procesu roboczego.
  * Ustaw **zakres dat** do odpowiedniego okresu, nad którym chcesz zbadać błędy lub problemy z jego obsługi.

  ![Filtry dziennika inspekcji](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Operacje tworzenia dzienników zrozumienia dla konta użytkownika usługi AD

Po wykryciu nowego zatrudnienia w produkcie Workday (Załóżmy, identyfikatorze pracowników *21023*), programu Azure AD, inicjowanie obsługi administracyjnej prób usługi do tworzenia nowego konta użytkownika usługi AD dla pracownika i proces tworzy 4 rekordów dziennika inspekcji, zgodnie z poniższym opisem:

  [![Dziennik inspekcji tworzenia platformy ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Po kliknięciu poszczególnych rekordów dziennika inspekcji, **Szczeg** otwarcie strony. Oto, co **Szczeg** stronie są wyświetlane dla każdego typu rekordu dziennika.

* **Importuj WORKDAY** rekord: Tego rekordu dziennika zawiera informacje procesów roboczych pobranego z produktu Workday. Użyj informacji w *dodatkowe szczegóły* sekcji rekord dziennika, aby rozwiązać problemy z pobieranie danych z produktu Workday. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami na temat sposobu interpretacji każdego pola.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Importuj AD** rekordu: Tego rekordu dziennika wyświetla informacje o rachunku pobrane z usługi AD. Podczas tworzenia początkowej użytkownika jest nie konta usługi AD *przyczyny stanu działania* wskaże, że żadne konto z wartością atrybutu identyfikator dopasowania został znaleziony w usłudze Active Directory. Użyj informacji w *dodatkowe szczegóły* sekcji rekord dziennika, aby rozwiązać problemy z pobieranie danych z produktu Workday. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami na temat sposobu interpretacji każdego pola.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Aby znaleźć rekordy dziennika agenta inicjowania obsługi administracyjnej odpowiadający tej operacji importowania AD, otwórz dzienniki Podglądu zdarzeń Windows i użyj **Znajdź...** opcję menu, aby znaleźć wpisy dziennika, zawierający wartość atrybutu dopasowania właściwości Identyfikatora/przyłączanie (w tym przypadku *21023*).

  ![Znajdowanie](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Wyszukaj wpis o *zdarzenie o identyfikatorze = 9*, który zapewni LDAP wyszukiwania filtra używanego przez agenta można pobrać konta usługi AD. Aby sprawdzić, czy filtr wyszukiwania prawo do pobierania wpisów unikatowych użytkowników.

  ![Wyszukiwanie LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Rekord, który poprzedza ją za pomocą *zdarzenie o identyfikatorze = 2* przechwytuje wynik operacji wyszukiwania i jeśli jego zwróciło żadnych wyników.

  ![Wyniki LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Akcja reguły synchronizacji** rekord: Ten rekord dziennika wyświetla wyniki są reguły mapowania atrybutów i skonfigurować filtrów określania zakresu, wraz z akcji aprowizacji, która zostaną podjęte w celu przetwarzania zdarzenia przychodzącego produktu Workday. Użyj informacji w *dodatkowe szczegóły* sekcji rekord dziennika, aby rozwiązać problemy z akcji synchronizacji. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami na temat sposobu interpretacji każdego pola.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Jeśli występują problemy z wyrażenia mapowanie atrybutu lub dane przychodzące Workday występują problemy z (na przykład: pusty, lub wartość null dla wymaganych atrybutów), wówczas można zauważyć błąd na tym etapie kod błędu:, który udostępnia szczegółowe informacje o awarii.

* **Eksportowanie AD** rekordu: Ten rekord dziennika wyświetla wynik operacji tworzenia konta usługi AD wraz z wartości atrybutów, które zostały określone w procesie. Użyj informacji w *dodatkowe szczegóły* operacji tworzenia sekcji rekord dziennika, aby rozwiązać problemy z kontem. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami na temat sposobu interpretacji każdego pola. W sekcji "Szczegóły dodatkowe", "EventName" jest ustawiona na "EntryExportAdd", "JoiningProperty" ma ustawioną wartość atrybutu ID dopasowania, "SourceAnchor" jest ustawiona na WorkdayID (WID) skojarzony z rekordem i ma wartość "TargetAnchor" wartość "ObjectGuid" AD atrybut nowo utworzonego użytkownika. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Aby znaleźć rekordy dziennika agenta inicjowania obsługi administracyjnej odpowiadający tej operacji eksportowania usługi AD, otwórz dzienniki Podglądu zdarzeń Windows i użyj **Znajdź...** opcję menu, aby znaleźć wpisy dziennika, zawierający wartość atrybutu dopasowania właściwości Identyfikatora/przyłączanie (w tym przypadku *21023*).  

  Wyszukaj żądania HTTP POST rekord odpowiadający sygnaturę czasową operacji eksportowania za pomocą *zdarzenie o identyfikatorze = 2*. Ten rekord będzie zawierać wartości atrybutów wysyłane przez usługę aprowizacji agenta inicjowania obsługi administracyjnej.

  [![Dodaj Standard SCIM](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Natychmiast po powyższych przypadkach należy innego zdarzenia, które przechwytuje odpowiedź Utwórz operacji na koncie usługi AD. To zdarzenie zwraca nowy objectGuid, utworzone w AD i ustawiono go jako atrybut TargetAnchor w usłudze aprowizowania.

  [![Dodaj Standard SCIM](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Opis Dzienniki Menedżera operacje aktualizacji

Atrybut menedżera jest atrybut odwołania w AD. Usługa aprowizowania nie atrybut manager w ramach operacji tworzenia użytkownika. Zamiast atrybutu menedżera jest ustawiona jako część *aktualizacji* operacji po utworzeniu konta usługi AD dla użytkownika. Rozszerzając powyższy przykład, załóżmy, że nowego zatrudnienia identyfikatorem pracowników "21451" została aktywowana w produkcie Workday i zatrudnienia nowego Menedżera (*21023*) ma już konto usługi AD. W tym scenariuszu przeszukiwanie dzienników inspekcji dla użytkownika 21451 wyświetlane wpisy 5.

  [![Menedżer aktualizacji](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

4 pierwsze rekordy są, takie jak te, które rozważyliśmy użytkownika w ramach operacji tworzenia. 5. rekord jest eksportu skojarzone z Menedżera atrybutu aktualizacji. Rekord dziennika wyświetla wynik operacji aktualizacji Menedżera konta usługi AD, która jest wykonywane przy użyciu Menedżera *objectGuid* atrybutu.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Rozwiązywanie często napotkała błędy

W tej sekcji opisano często spotykanych problemów z aprowizowaniem użytkowników w produkcie Workday i sposobie jego rozwiązania. Błędy są grupowane w następujący sposób:

* [Błędy aprowizacji agenta](#provisioning-agent-errors)
* [Błędy łączności](#connectivity-errors)
* [Błędy tworzenia konta użytkownika usługi AD](#ad-user-account-creation-errors)
* [Błędy aktualizacji konta użytkownika usługi AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Błędy aprowizacji agenta

|#|Błąd scenariusza |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Wystąpił błąd podczas instalowania agenta inicjowania obsługi administracyjnej z komunikatem o błędzie:  *Usługa "Agenta Microsoft Azure AD Connect aprowizacji" (AADConnectProvisioningAgent) nie powiodło się. Sprawdź, czy masz wystarczające uprawnienia do uruchomienia systemu.* | Ten błąd zazwyczaj pojawia się, jeśli chcesz zainstalować agenta inicjowania obsługi administracyjnej na kontrolerze domeny i zasad grupy uniemożliwia uruchomienie usługi.  Również pojawia się, jeśli masz poprzednią wersję agenta uruchomionego i nie ma go odinstalować przed rozpoczęciem nowej instalacji.| Na serwerze-DC, należy zainstalować agenta inicjowania obsługi administracyjnej. Upewnij się, że poprzednie wersje agenta zostaną odinstalowane przed zainstalowaniem nowego agenta.|
|2.| Usługa Windows "Agenta Microsoft Azure AD Connect aprowizacji" znajduje się w *od* stanu i przełącza się na *systemem* stanu. | W ramach instalacji, Kreator agent tworzy konta lokalnego (**usługi NT\\AADConnectProvisioningAgent**) na serwerze i to jest **logowanie** konto używane do uruchamiania Usługa. Jeśli zasady zabezpieczeń na serwerze Windows uniemożliwia uruchamianie usług kont lokalnych, wystąpi błąd. | Otwórz *konsoli usługi*. Kliknij prawym przyciskiem myszy w usłudze Windows "Agenta Microsoft Azure AD Connect aprowizacji", i w karcie Logowanie Określ konto administratora domeny, aby uruchomić usługę. Uruchom ponownie usługę. |
|3.| Podczas konfigurowania agenta inicjowania obsługi administracyjnej z Twoją domeną usługi AD w kroku *łączenie usługi Active Directory*, Kreator długi czas ładowania schematu usługi AD i w końcu przekroczy limit czasu. | Ten błąd jest zazwyczaj wyświetlany, jeśli kreator nie może skontaktować się z serwerem kontrolera domeny usługi AD ze względu na problemy z zaporą. | Na *łączenie usługi Active Directory* ekranie kreatora przy jednoczesnym zapewnieniu poświadczenia dla Twojej domeny usługi AD, jest dostępna opcja o nazwie *wybierz priorytet kontrolera domeny*. Ta opcja umożliwia wybranie kontrolera domeny, który znajduje się w tej samej lokacji co serwer agenta i upewnij się, że nie istnieją żadne reguły zapory blokuje komunikację. |

#### <a name="connectivity-errors"></a>Błędy związane z łącznością

Jeśli usługa aprowizowania jest w stanie połączyć się z produktem Workday lub usługi Active Directory, może to spowodować, inicjowanie obsługi administracyjnej przejść w stan poddane kwarantannie. W poniższej tabeli umożliwiają rozwiązywanie problemów z łącznością.

|#|Błąd scenariusza |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Po kliknięciu **Testuj połączenie**, zostanie wyświetlony komunikat o błędzie: *Wystąpił błąd podczas nawiązywania połączenia z usługi Active Directory. Upewnij się, że jest uruchomiony Agent inicjowania obsługi administracyjnej lokalnych i jest ono skonfigurowane przy użyciu poprawnej domeny usługi Active Directory.* | Ten błąd zazwyczaj wyświetlane if aprowizacji agenta nie jest uruchomiona lub jest Zapora blokuje komunikację między usługą Azure AD i agenta inicjowania obsługi administracyjnej. Ten błąd może być też widoczny, jeśli domena nie jest skonfigurowany w Kreatorze agenta. | Otwórz *usług* konsolę na serwerze Windows, aby upewnić się, że agent jest uruchomiony. Otwórz Kreatora inicjowania obsługi agenta i upewnij się, że właściwej domeny jest zarejestrowana przy użyciu agenta.  |
|2.| Zadanie inicjowania obsługi administracyjnej przechodzi w stan kwarantanny podczas weekendów (PT-SO) i uzyskujemy wiadomość e-mail z powiadomieniem że jest błąd synchronizacji. | Częstą przyczyną występowania tego błędu są planowane przestoje produktu Workday. Jeśli korzystasz z dzierżawy implementacji produktu Workday, pamiętaj, że ten produkt ma w weekendy zaplanowane przestoje dzierżaw implementacji (zwykle od wieczora w piątek do rana w sobotę). W tym czasie aplikacje aprowizacji produktu Workday mogą przechodzić w stan kwarantanny, ponieważ nie mogą nawiązać połączenia z produktem Workday. Normalny stan zostaje przywrócony po ponownym przejściu w tryb online dzierżawy implementacji produktu Workday. W rzadkich przypadkach ten błąd może być także wyświetlany, jeśli zmieniło się hasło użytkownika systemu integracji z powodu odświeżenia dzierżawy lub jeśli konto zostało zablokowane bądź wygasło. | Zapytaj administratora produktu Workday lub partnera integracji o czas planowanych przestojów produktu Workday, aby wiedzieć, które komunikaty o alertach można zignorować, i móc potwierdzić dostępność wystąpienia produktu Workday po jego ponownym przejściu w tryb online.  |


#### <a name="ad-user-account-creation-errors"></a>Błędy tworzenia konta użytkownika usługi AD

|#|Błąd scenariusza |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Eksportuj niepowodzeń operacji w dzienniku inspekcji z komunikatem *błąd: OperationsError-SvcErr: Wystąpił błąd operacji. Nie skonfigurowano odwołania wyższego poziomu dla usługi katalogowej. Usługa katalogowa jest w związku z tym nie może przydzielić odwołań do obiektów poza tym lesie.* | Ten błąd jest zwykle pojawia się Jeśli *kontener usługi Active Directory* jednostki Organizacyjnej nie jest ustawiona poprawnie, lub jeśli występują problemy z mapowania wyrażenie używane dla *parentDistinguishedName*. | Sprawdź *kontener usługi Active Directory* jednostki Organizacyjnej parametr literówki. Jeśli korzystasz z elementu *parentDistinguishedName* podczas mapowania atrybutów, upewnij się, że zawsze wynikiem jego oceny jest znany kontener w ramach domeny usługi AD. Sprawdź *wyeksportować* dzienniki zdarzeń w dzienniku inspekcji, aby zobaczyć wygenerowaną wartość. |
|2.| Eksportuj niepowodzeń operacji w dzienniku inspekcji, kod błędu: *SystemForCrossDomainIdentityManagementBadResponse* i komunikat *błąd: ConstraintViolation AtrErr: Wartość w żądaniu jest nieprawidłowa. Wartość atrybutu nie dopuszczalny zakres wartości. Szczegóły \nError: CONSTRAINT_ATT_TYPE - firmy*. | Ten błąd jest specyficzne dla *firmy* atrybutu, może zostać wyświetlony ten błąd dla innych atrybutów, takich jak *CN* także. Ten błąd pojawia się ze względu na ograniczenia schematu AD wymuszane. Domyślnie, takich jak atrybuty *firmy* i *CN* AD mieć maksymalnie 64 znaki. Jeśli wartość pochodzące z produktu Workday jest więcej niż 64 znaki, spowoduje zobacz ten komunikat o błędzie. | Sprawdź *wyeksportować* zdarzeń w dziennikach inspekcji, aby wyświetlić wartość dla atrybutu zgłoszonych w komunikacie o błędzie. Należy wziąć pod uwagę obcinanie wartości pochodzące z produktu Workday za pomocą [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) funkcji lub zmiana mapowań do atrybutu usługi AD, który nie ma podobnych ograniczeń długości.  |

#### <a name="ad-user-account-update-errors"></a>Błędy aktualizacji konta użytkownika usługi AD

Podczas procesu aktualizacji konta użytkownika usługi AD usługi aprowizacji odczytuje informacje z produktu Workday i AD działa atrybut reguły mapowania i określa, jeśli jakiekolwiek zmiany potrzeb zaczęły obowiązywać. W związku z tym zdarzeniem aktualizacji zostanie wywołany. Jeśli dowolny z tych kroków napotka błąd, wartość jest rejestrowane w dziennikach inspekcji. W poniższej tabeli umożliwiają Rozwiązywanie typowych problemów aktualizacji.

|#|Błąd scenariusza |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Błędy Akcja reguły synchronizacji w dzienniku inspekcji z komunikatem *EventName = EntrySynchronizationError i kod błędu = EndpointUnavailable*. | Ten błąd pojawia się, czy nie można pobrać danych profilu użytkownika z usługi Active Directory z powodu błędu przetwarzania napotykanych przez lokalną usługę aprowizacji agenta inicjowania obsługi administracyjnej. | Sprawdź dzienniki aprowizacji agenta programu Podgląd zdarzeń dla zdarzenia błędów, które wskazują na problemy z operacji odczytu (filtrowanie według Identyfikatora zdarzenia 2). |
|2.| Atrybut menedżera w AD nie zostanie zaktualizowany dla niektórych użytkowników w AD. | Najbardziej prawdopodobną przyczyną wystąpienia tego błędu jest, jeśli używane są reguły określania zakresów i menedżerem użytkownika nie jest częścią zakresu. Można również uruchamiać ten problem występuje Jeśli Menedżera pasujący atrybut ID (np. EmployeeID) nie został odnaleziony w lokalizacji docelowej domeny usługi AD lub nie ustawiono prawidłową wartość. | Przejrzyj filtru określania zakresu i Dodaj użytkownika menedżera w zakresie. Sprawdź profil Menedżera w AD, aby upewnij się, że wartość atrybutu ID dopasowania. |

## <a name="managing-your-configuration"></a>Zarządzanie konfiguracją

W tej sekcji opisano, jak można dodatkowo rozszerzyć, dostosowywanie i zarządzanie konfiguracją aprowizacji użytkowników oparte na dzień roboczy. Obejmuje następujące tematy:

* [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](#customizing-the-list-of-workday-user-attributes)  
* [Eksportowanie i importowanie konfiguracji](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Dostosowywanie listy atrybutów użytkownika w produkcie Workday

Dzień roboczy aprowizacji aplikacji dla usługi Active Directory i usługi Azure AD oferują domyślną listę atrybutów użytkowników z produktu Workday możesz wybrać z. Jednak te listy nie są wyczerpujące. Dzień roboczy obsługuje wiele setki użytkowników możliwe atrybuty, które mogą być standardowe lub unikatowa dla dzierżawy produktu Workday.

Usługa aprowizacji usługi Azure AD obsługuje możliwość dostosowania listy lub atrybut produktu Workday do uwzględnienia wszelkie atrybuty ujawnione w [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operacji interfejsu API zarządzania zasobami ludzkimi.

Aby wykonać tę zmianę, należy użyć [Workday Studio](https://community.workday.com/studio-download) można wyodrębnić wyrażenia XPath, które reprezentują atrybutów, o których chcesz użyć, a następnie dodaj je do konfiguracji aprowizacji za pomocą edytora zaawansowanego atrybutu w witrynie Azure portal .

**Aby pobrać wyrażenie XPath dla produktu Workday atrybutu użytkownika:**

1. Pobierz i zainstaluj [Workday Studio](https://community.workday.com/studio-download). Konieczne będzie Workday społeczności dostęp konta do Instalatora.

2. Pobierz plik Workday Human_Resources WSDL spod tego adresu URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Uruchom program Workday Studio.

4. Na pasku poleceń Wybierz **Workday > Test usługi sieci Web w Tester** opcji.

5. Wybierz **zewnętrznych**i wybierz pobrany plik Human_Resources WSDL w kroku 2.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Ustaw **lokalizacji** pole `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale zastąpieniu "IMPL-CC" faktycznego typu wystąpienia, a "DZIERŻAWCA" nazwą rzeczywistego dzierżawy.

7. Ustaw **operacji** do **Get_Workers**

8.  Kliknij małą **skonfigurować** link poniżej okienka żądanie/odpowiedź, aby ustawić poświadczenia dla produktu Workday. Sprawdź **uwierzytelniania**, a następnie wprowadź nazwę użytkownika i hasło dla konta system integracji produktu Workday. Pamiętaj sformatować nazwę użytkownika, jak nazwa\@dzierżawy i pozostaw **UsernameToken WS-Security** wybraną opcją.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Kliknij przycisk **OK**.

10. W **żądania** okienku wklejanie w XML poniżej i ustaw **Employee_ID** identyfikator pracownika rzeczywistego użytkownika w ramach dzierżawy produktu Workday. Wybierz użytkownika, który ma atrybut wypełnione chcesz wyodrębnić.

    ```xml
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

13. W programu polecenie paska dla produktu Workday Studio, wybierz **Plik > Otwórz plik...**  , a następnie otwórz plik XML został zapisany. Ta akcja spowoduje Otwórz plik w edytorze XML programu Studio produktu Workday.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. W drzewie pliku nawigowania   **/ENV: Koperta > env: Treść > wd:Get_Workers_Response > wd:Response_Data > wd: Proces roboczy** wyszukiwania danych użytkownika.

15. W obszarze **wd: Proces roboczy**Znajdź atrybut, który chcesz dodać i wybierz ją.

16. Skopiuj wyrażenie XPath dla Twojej wybrany atrybut poza **ścieżka dokumentu** pola.

17. Usuń **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data /** prefiksu kopiowane wyrażenia.

18. Jeśli ostatni element w wyrażeniu skopiowany jest węzłem (przykład: "/ wd: Birth_Date"), następnie dołącz **/text()** na końcu wyrażenia. Nie jest to konieczne, jeśli ostatni element jest atrybutem (przykład: "/@wd: typu").

19. Wynik powinien być podobny do `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Ta wartość jest o tym, co zostanie skopiowany do witryny Azure portal.

**Aby dodać swoje niestandardowy atrybut użytkownika produktu Workday do inicjowania obsługi administracyjnej konfiguracji:**

1. Uruchom [witryny Azure portal](https://portal.azure.com), a następnie przejdź do sekcji Provisioning dnia roboczego Inicjowanie obsługi administracyjnej aplikacji, zgodnie z opisem we wcześniejszej części tego samouczka.

2. Ustaw **stanie aprowizacji** do **poza**i wybierz **Zapisz**. Ten krok pomoże, upewnij się, że zmiany zostały zastosowane tylko wtedy, gdy wszystko będzie gotowe.

3. W obszarze **mapowania**, wybierz opcję **zsynchronizować Workday pracownikom w lokalnej usłudze Active Directory** (lub **zsynchronizować pracowników z produktu Workday do usługi Azure AD**).

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

12. Po powrocie na główny **aprowizacji** zaznacz **zsynchronizować Workday pracownikom w lokalnej usłudze Active Directory** (lub **zsynchronizować pracowników do usługi Azure AD**) ponownie.

13. Wybierz **Dodaj nowe mapowanie**.

14. Nowy atrybut powinien zostać wyświetlony na **atrybut źródłowy** listy.

15. Dodaj mapowanie dla użytkownika nowego atrybutu zgodnie z potrzebami.

16. Gdy skończysz, pamiętaj, aby ustawić **stanie aprowizacji** do **na** i Zapisz.

### <a name="exporting-and-importing-your-configuration"></a>Eksportowanie i importowanie konfiguracji

W tej sekcji opisano, jak za pomocą interfejsu API Microsoft Graph i programu Graph Explorer wyeksportować schemat i inicjowania obsługi produktu Workday mapowania atrybutów do pliku JSON i zaimportować z powrotem do usługi Azure AD.

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>Krok 1: Pobieranie usługi Workday inicjowania obsługi administracyjnej aplikacji identyfikator jednostki usługi (identyfikator obiektu:)

1. Uruchom [witryny Azure portal](https://portal.azure.com), a następnie przejdź do sekcji właściwości dnia roboczego Inicjowanie obsługi administracyjnej aplikacji.
1. W sekcji właściwości inicjowania obsługi administracyjnej aplikacji skopiuj wartość identyfikatora GUID skojarzony z *obiektu o identyfikatorze* pola. Ta wartość jest również nazywany **ServicePrincipalId** aplikacji i będą używane w operacjach Graph Explorer.

   ![Identyfikator jednostki usługi aplikacji w produkcie WORKDAY](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Zaloguj się do programu Microsoft Graph Explorer

1. Uruchom [programu Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Kliknij przycisk "Zaloguj się przy użyciu Microsoft" i zaloguj się przy użyciu poświadczeń platformy Azure AD administratora globalnego lub administratora aplikacji.

    ![Wykres logowania](./media/workday-inbound-tutorial/wd_export_02.png)

1. Po pomyślnym zalogowaniu widoczne będą szczegóły konta użytkownika w okienku po lewej stronie.

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>Krok 3: Pobieranie Identyfikatora zadania inicjowania obsługi administracyjnej Workday inicjowania obsługi administracyjnej aplikacji

W programie Microsoft Graph Explorer, uruchom następujące zapytanie GET, zastępując [servicePrincipalId], przy użyciu **ServicePrincipalId** wyodrębnione z [kroku 1](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Otrzymasz odpowiedzi, jak pokazano poniżej. Skopiuj atrybut"id" w odpowiedzi. Ta wartość jest **ProvisioningJobId** i będą używane do pobierania odpowiednie metadane schematu.

   [![Inicjowanie obsługi administracyjnej identyfikator zadania](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>Krok 4: Pobierz schemat inicjowania obsługi administracyjnej

W programie Microsoft Graph Explorer uruchom następujące zapytania GET, zastępując [servicePrincipalId] i [ProvisioningJobId] z ServicePrincipalId i ProvisioningJobId pobranego w poprzednich krokach.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Skopiuj obiekt JSON z odpowiedzi i zapisz go w pliku, aby utworzyć kopię zapasową schematu.

#### <a name="step-5-import-the-provisioning-schema"></a>Krok 5. Zaimportuj schemat inicjowania obsługi administracyjnej

> [!CAUTION]
> Ten krok należy wykonać tylko wtedy, gdy trzeba zmodyfikować schemat konfiguracji, której nie można zmienić za pomocą witryny Azure portal lub jeśli trzeba przywrócić konfigurację z wcześniej kopii zapasowej plików z prawidłowym oraz pracy schematu.

W programie Microsoft Graph Explorer, należy skonfigurować przy użyciu ServicePrincipalId następujące zapytania PUT, zastępując [servicePrincipalId] i [ProvisioningJobId] i ProvisioningJobId pobranego w poprzednich krokach.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na karcie "Treść żądania" skopiuj zawartość pliku schematu JSON.

   [![Treść żądania](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

Na karcie "Nagłówki żądania" Dodaj atrybut nagłówka Content-Type o wartości "application/json"

   [![Nagłówki żądania](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

Kliknij przycisk "Uruchom kwerendę", aby zaimportować nowy schemat.

## <a name="managing-personal-data"></a>Zarządzanie danymi osobowymi

Workday, inicjowanie obsługi administracyjnej rozwiązania dla usługi Active Directory wymaga agenta inicjowania obsługi administracyjnej, należy zainstalować na serwerze Windows w środowisku lokalnym, a ten agent tworzy dzienniki w dzienniku zdarzeń Windows, który może zawierać dane osobowe, w zależności od Twojego produktu Workday do atrybutu usługi AD mapowania. Aby spełnia zobowiązania do zachowania użytkownika, należy upewnić się, że żadne dane nie są przechowywane w przypadku dzienników poza 48 godzin, konfigurując Windows zaplanowane zadanie, aby wyczyścić dziennik zdarzeń.

Usługa aprowizacji usługi Azure AD należy do **przetwarzający dane** Kategoria klasyfikacji RODO. Jako potok przetwarzający dane usługa zapewnia usług przetwarzania danych dla klucza partnerów i użytkowników końcowych. Usługi Azure AD z usługą aprowizacji nie generuje dane użytkownika i nie ma niezależnego kontroli nad jakie dane osobowe są zbierane i sposobie ich użycia. Pobieranie danych, agregacji, analizy i raportowania w usłudze Azure AD z usługą aprowizacji są oparte na istniejących danych przedsiębiorstwa.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

W odniesieniu do przechowywania danych usługi Azure AD usługi aprowizacji nie Generowanie raportów, przeprowadzanie analiz lub zapewniają wgląd, po upływie 30 dni. W związku z tym usługi Azure AD z usługą aprowizacji nie przechowujących, przetwarzających lub przechowywać żadnych danych, po upływie 30 dni. Ten projekt jest zgodna z rozporządzenia RODO, przepisy dotyczące zgodności ochrony prywatności firmy Microsoft i zasady przechowywania danych usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować logowanie jednokrotne między produktem Workday i Azure Active Directory](workday-tutorial.md)
* [Dowiedz się, jak zintegrować innych aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak używać interfejsów API programu Microsoft Graph do zarządzania konfiguracjami inicjowania obsługi administracyjnej](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
