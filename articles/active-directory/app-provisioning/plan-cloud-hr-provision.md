---
title: Planowanie aprowizacji użytkowników usługi Azure Active Directory do obsługi administracyjnej w chmurze
description: W tym artykule opisano proces wdrażania integracji systemów hr w chmurze, takich jak Workday i SuccessFactors, z usługą Azure Active Directory. Integracja usługi Azure AD z systemem hr w chmurze powoduje pełny system zarządzania cyklem życia tożsamości.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522436"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planowanie aprowizacji użytkowników usługi Azure Active Directory do obsługi administracyjnej w chmurze

W przeszłości pracownicy działu IT polegali na ręcznych metodach tworzenia, aktualizowania i usuwania pracowników. Używali metod, takich jak przekazywanie plików CSV lub skryptów niestandardowych do synchronizacji danych pracowników. Te procesy inicjowania obsługi administracyjnej są podatne na błędy, niezabezpieczone i trudne do zarządzania.

Aby zarządzać cyklami życia tożsamości pracowników, dostawców lub pracowników warunkowych, [usługa inicjowania obsługi administracyjnej użytkowników usługi Azure Active Directory (Azure AD)](../app-provisioning/user-provisioning.md) oferuje integrację z aplikacjami opartymi na chmurze zasobami ludzkimi (HR). Przykłady aplikacji obejmują Workday lub SuccessFactors.

Usługa Azure AD używa tej integracji, aby włączyć następujące przepływy pracy aplikacji usługi cloud HR (aplikacji):

- **Aprowizuj użytkowników do usługi Active Directory:** Aprowizuj wybrane zestawy użytkowników z aplikacji cloud HR na co najmniej jedną domenę usługi Active Directory.
- **Aprowizuj użytkowników tylko w chmurze do usługi Azure AD:** W scenariuszach, w których usługa Active Directory nie jest używana, aprowizuj użytkowników bezpośrednio z aplikacji usługi Cloud HR do usługi Azure AD.
- **Write back to the cloud HR app:** Zapisz adresy e-mail i atrybuty nazwy użytkownika z usługi Azure AD z powrotem do aplikacji cloud HR.

> [!NOTE]
> Ten plan wdrażania pokazuje, jak wdrożyć przepływy pracy aplikacji hr w chmurze za pomocą inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać informacje na temat wdrażania automatycznej inicjowania obsługi administracyjnej aplikacji dla użytkowników jako usługi (SaaS), zobacz [Planowanie automatycznego wdrażania inicjowania obsługi administracyjnej użytkowników](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Włączone scenariusze hr

Usługa inicjowania obsługi administracyjnej użytkowników usługi Azure AD umożliwia automatyzację następujących scenariuszy zarządzania cyklem życia tożsamości opartych na usłudze HR:

- **Zatrudnianie nowych pracowników:** Po dodaniu nowego pracownika do aplikacji cloud HR konto użytkownika jest automatycznie tworzone w usłudze Active Directory i usłudze Azure AD z opcją odpisania atrybutów adresu e-mail i nazwy użytkownika w aplikacji cloud HR.
- **Aktualizacje atrybutów i profilu pracownika:** Po zaktualizowaniu rekordu pracownika, takiego jak nazwa, tytuł lub menedżer w aplikacji cloud HR, ich konto użytkownika jest automatycznie aktualizowane w usłudze Active Directory i usłudze Azure AD.
- **Wypowiedzenia pracowników:** Po zakończeniu pracy pracownika w aplikacji cloud HR jego konto użytkownika jest automatycznie wyłączane w usłudze Active Directory i usłudze Azure AD.
- **Pracownik ponownie zatrudnia:** Gdy pracownik jest ponownie zatrudniony w aplikacji cloud HR, jego stare konto może być automatycznie ponownie aktywowane lub ponownie aprowiowane w usłudze Active Directory i usłudze Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Do kogo najlepiej nadaje się ta integracja?

Integracja aplikacji hr w chmurze z inicjowania obsługi administracyjnej użytkowników usługi Azure AD jest idealnie nadaje się dla organizacji, które:

- Potrzebujesz wstępnie utworzonego, opartego na chmurze rozwiązania do inicjowania obsługi administracyjnej użytkowników usług w chmurze.
- Wymagaj bezpośredniego inicjowania obsługi administracyjnej przez użytkowników z aplikacji usługi cloud HR do usługi Active Directory lub usługi Azure AD.
- Wymagaj, aby użytkownicy były aprowiowane przy użyciu danych uzyskanych z aplikacji cloud HR.
- Wymagaj dołączania, przenoszenia i pozostawiania użytkowników do synchronizacji z co najmniej jednym lasem, domenami i procesorami operacyjnymi usługi Active Directory na podstawie informacji o zmianach wykrytych w aplikacji cloud HR.
- Używanie usługi Office 365 do obsługi poczty e-mail.

## <a name="learn"></a>Informacje

Inicjowanie obsługi administracyjnej użytkowników tworzy podstawę dla bieżącego zarządzania tożsamościami. Poprawia jakość procesów biznesowych, które opierają się na autorytatywnych danych tożsamości.

### <a name="terms"></a>Warunki

W tym artykule użyto następujących terminów:

- **System źródłowy:** repozytorium użytkowników, od których można za pomocą przepisów usługi Azure AD. Przykładem jest aplikacja hr w chmurze, takich jak Workday lub SuccessFactors.
- **System docelowy:** repozytorium użytkowników, które zapozytorzyją usługi Azure AD. Przykładami są usługa Active Directory, usługa Azure AD, Office 365 lub inne aplikacje SaaS.
- **Proces Joiners-Movers-Leavers:** Termin używany dla nowych pracowników, transferów i zakończenia pracy przy użyciu aplikacji cloud HR jako systemu rekordów. Proces kończy się, gdy usługa pomyślnie apowiizuje niezbędne atrybuty do systemu docelowego.

### <a name="key-benefits"></a>Najważniejsze korzyści

Ta możliwość obsługi administracyjnej działu IT opartej na zasobach ludzkich oferuje następujące znaczące korzyści biznesowe:

- **Zwiększ produktywność:** Teraz możesz zautomatyzować przypisywanie kont użytkowników i licencji usługi Office 365 oraz zapewnić dostęp do kluczowych grup. Automatyzacja zadań zapewnia nowym pracownikom natychmiastowy dostęp do narzędzi pracy i zwiększa produktywność.
- **Zarządzanie ryzykiem:** Możesz zwiększyć bezpieczeństwo, automatyzując zmiany na podstawie stanu pracownika lub członkostwa w grupach z danymi płynącymi z aplikacji hr w chmurze. Automatyzacja zmian gwarantuje, że tożsamości użytkowników i dostęp do kluczowych aplikacji są aktualizowane automatycznie, gdy użytkownicy przechodzą lub opuszczają organizację.
- **Zgodność z przepisami i zarządzanie:** Usługa Azure AD obsługuje natywne dzienniki inspekcji dla żądań inicjowania obsługi administracyjnej użytkowników wykonywanych przez aplikacje systemów źródłowych i docelowych. Dzięki inspekcji możesz śledzić, kto ma dostęp do aplikacji na jednym ekranie.
- **Zarządzanie kosztami:** Automatyczne inicjowanie obsługi administracyjnej zmniejsza koszty, unikając nieefektywności i błędów ludzkich związanych z ręczną inicjowania obsługi administracyjnej. Zmniejsza to potrzebę niestandardowych rozwiązań inicjowania obsługi administracyjnej użytkowników utworzonych w czasie przy użyciu starszych i przestarzałych platform.

### <a name="licensing"></a>Licencjonowanie

Aby skonfigurować aplikację cloud HR do integracji inicjowania obsługi administracyjnej użytkowników usługi Azure AD, potrzebujesz ważnej [licencji usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) i licencji dla aplikacji hr w chmurze, takiej jak Workday lub SuccessFactors.

Potrzebujesz również ważnej licencji usługi Azure AD Premium P1 lub wyższej subskrypcji dla każdego użytkownika, który będzie pozyskiwany z aplikacji cloud HR i aprowizować do usługi Active Directory lub usługi Azure AD. Każda niewłaściwa liczba licencji posiadanych w aplikacji cloud HR może prowadzić do błędów podczas inicjowania obsługi administracyjnej użytkownika.

### <a name="prerequisites"></a>Wymagania wstępne

- Dostęp administratora globalnego usługi Azure AD do konfigurowania agenta inicjowania obsługi administracyjnej usługi Azure AD Connect.
- Wystąpienie testowe i produkcyjne aplikacji cloud HR.
- Uprawnienia administratora w aplikacji cloud HR do tworzenia użytkownika integracji systemu i wprowadzania zmian w celu testowania danych pracowników do celów testowych.
- W przypadku inicjowania obsługi administracyjnej usługi Active Directory serwer z systemem Windows Server 2012 lub nowszym ze środowiskiem uruchomieniowym .NET 4.7.1+ jest wymagany do [hostowania agenta inicjowania obsługi administracyjnej usługi Azure AD Connect.](https://go.microsoft.com/fwlink/?linkid=847801)
- [Usługa Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) do synchronizowania użytkowników między usługą Active Directory a usługą Azure AD.

### <a name="training-resources"></a>Zasoby szkoleniowe

| **Zasoby** | **Link i opis** |
|:-|:-|
| Filmy wideo | [Co to jest inicjowanie obsługi administracyjnej użytkowników w usłudze Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Jak wdrożyć aprowizacji użytkowników w usłudze Active Azure Directory](https://youtu.be/pKzyts6kfrw) |
| Samouczki | [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure AD](../saas-apps/tutorial-list.md) |
| | [Samouczek: Konfigurowanie workday do automatycznego inicjowania obsługi administracyjnej przez użytkowników](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Najczęściej zadawane pytania | [Automatyczne inicjowanie obsługi administracyjnej użytkowników](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Inicjowanie obsługi administracyjnej z dnia roboczego na usługę Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architektura rozwiązania

W poniższym przykładzie opisano architekturę rozwiązania inicjowania obsługi administracyjnej użytkownika końcowego dla typowych środowisk hybrydowych i obejmuje:

- **Autorytatywne przepływ danych HR z aplikacji cloud HR do usługi Active Directory.** W tym przepływie zdarzenie HR (proces Joiners-Movers-Leavers) jest inicjowane w dzierżawie aplikacji hr w chmurze. Usługa inicjowania obsługi administracyjnej usługi Azure AD i agent aprowizujący usługi Azure AD Connect aprowizują dane użytkownika z dzierżawy aplikacji hr w chmurze do usługi Active Directory. W zależności od zdarzenia może to prowadzić do tworzenia, aktualizowania, włączania i wyłączania operacji w usłudze Active Directory.
- **Synchronizuj z usługą Azure AD i odpisuj wiadomości e-mail i nazwę użytkownika z lokalnej usługi Active Directory do aplikacji cloud HR.** Po zaktualizowaniu kont w usłudze Active Directory jest synchronizowana z usługą Azure AD za pośrednictwem usługi Azure AD Connect. Adresy e-mail i atrybuty nazwy użytkownika można zapisać z powrotem do dzierżawy aplikacji usługi hr w chmurze.

![Diagram przepływu pracy](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Opis przepływu pracy

Na diagramie przedstawiono następujące kluczowe kroki:  

1. **Zespół HR** wykonuje transakcje w dzierżawie aplikacji hr w chmurze.
2. **Usługa inicjowania obsługi administracyjnej usługi Azure AD** uruchamia zaplanowane cykle z dzierżawy aplikacji hr w chmurze i identyfikuje zmiany, które muszą zostać przetworzone w celu synchronizacji z usługą Active Directory.
3. **Usługa inicjowania obsługi administracyjnej usługi** Azure AD wywołuje agenta inicjowania obsługi administracyjnej usługi Azure AD Connect z ładunkiem żądań zawierającym konto usługi Active Directory— umożliwia tworzenie, aktualizowanie, włączanie i wyłączanie operacji.
4. **Agent inicjowania obsługi administracyjnej usługi Azure AD Connect** używa konta usługi do zarządzania danymi konta usługi Active Directory.
5. **Usługa Azure AD Connect** uruchamia [synchronizację różnicową](../hybrid/how-to-connect-sync-whatis.md) w celu ściągania aktualizacji w usłudze Active Directory.
6. Aktualizacje **usługi Active Directory** są synchronizowane z usługą Azure AD.
7. **Usługa inicjowania obsługi administracyjnej usługi Azure AD** odpisuje atrybut e-mail i nazwę użytkownika z usługi Azure AD do dzierżawy aplikacji hr w chmurze.

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożeniowego

Należy wziąć pod uwagę potrzeby organizacyjne podczas określania strategii dla tego wdrożenia w twoim środowisku.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich interesariuszy

Gdy projekty technologiczne nie powiodą się, zazwyczaj robią to ze względu na niedopasowane oczekiwania dotyczące wpływu, wyników i odpowiedzialności. Aby uniknąć tych pułapek, [upewnij się, że angażujesz odpowiednie zainteresowane strony.](https://aka.ms/deploymentplans) Upewnij się również, że role interesariuszy w projekcie są dobrze poznane. Dokumentowanie zainteresowanych stron oraz ich wkładu i odpowiedzialności w ramach projektu.

Dołącz przedstawiciela organizacji HR, który może dostarczyć danych wejściowych na temat istniejących procesów biznesowych HR i tożsamości pracownika oraz wymagań dotyczących przetwarzania danych zadań.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla powodzenia każdej nowej usługi. Proaktywnie komunikuj się z użytkownikami o tym, kiedy i jak zmieni się ich środowisko. Poinformuj ich, jak zdobyć wsparcie, jeśli wystąpią problemy.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Integracja procesów biznesowych hr i przepływów pracy tożsamości z aplikacji cloud HR do systemów docelowych wymaga znacznej ilości sprawdzania poprawności danych, przekształcania danych, oczyszczania danych i kompleksowych testów, zanim będzie można wdrożyć rozwiązanie w proces produkcyjny.

Uruchom konfigurację początkową w [środowisku pilotażowym](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) przed skalowaniem do wszystkich użytkowników w środowisku produkcyjnym.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Wybieranie aplikacji łącznika aprowizacji zasobów ludzkich w chmurze

Aby ułatwić przepływy pracy inicjowania obsługi administracyjnej usługi Azure AD między aplikacją cloud HR i usługą Active Directory, można dodać wiele aplikacji łącznika inicjowania obsługi administracyjnej z galerii aplikacji usługi Azure AD:

- **Aplikacja Cloud HR do inicjowania obsługi administracyjnej użytkowników usługi Active Directory:** Ta aplikacja łącznika inicjowania obsługi administracyjnej ułatwia inicjowanie obsługi administracyjnej konta użytkownika z aplikacji cloud HR do jednej domeny usługi Active Directory. Jeśli masz wiele domen, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji usługi Azure AD dla każdej domeny usługi Active Directory, którą musisz udostępnić.
- **Aplikacja Cloud HR do inicjowania obsługi administracyjnej użytkowników usługi Azure AD:** Podczas gdy usługa Azure AD Connect jest narzędziem, które powinno być używane do synchronizowania użytkowników usługi Active Directory z usługą Azure AD, ta aplikacja łącznika inicjowania obsługi administracyjnej może służyć do ułatwiania inicjowania obsługi administracyjnej użytkowników tylko w chmurze z aplikacji cloud HR do jednej dzierżawy usługi Azure AD.
- **Odpisy z aplikacji Cloud HR:** Ta aplikacja łącznika inicjowania obsługi administracyjnej ułatwia odpis adresów e-mail użytkownika z usługi Azure AD do aplikacji cloud HR.

Na przykład na poniższej ilustracji wymieniono aplikacje łącznika Workday, które są dostępne w galerii aplikacji usługi Azure AD.

![Galeria aplikacji portalu usługi Azure Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Schemat blokowy decyzji

Użyj następującego schematu blokowego decyzji, aby określić, które aplikacje do inicjowania obsługi zasobów ludzkich w chmurze są odpowiednie dla danego scenariusza.

![Schemat blokowy decyzji](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Projektowanie topologii wdrażania agenta inicjowania obsługi administracyjnej usługi Azure AD Connect

Integracja inicjowania obsługi administracyjnej między aplikacją cloud HR a usługą Active Directory wymaga czterech składników:

- Dzierżawa aplikacji Cloud HR
- Aplikacja łącznika inicjowania obsługi administracyjnej
- Agent inicjowania obsługi administracyjnej usługi Azure AD Connect
- Domena Active Directory

Topologia wdrażania agenta inicjowania obsługi administracyjnej usługi Azure AD Connect zależy od liczby dzierżaw aplikacji hr w chmurze i domen podrzędnych usługi Active Directory, które mają być integrujące. Jeśli masz wiele domen usługi Active Directory, zależy to od tego, czy domeny usługi Active Directory są ciągłe, czy [rozłączne.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)

Na podstawie decyzji wybierz jeden ze scenariuszy wdrażania:

- Dzierżawa aplikacji hr w jednej chmurze — > docelowa pojedynczych lub wielu domen podrzędnych usługi Active Directory w zaufanym lesie
- Dzierżawa aplikacji hr w jednej chmurze — > kierowana na wiele domen podrzędnych w rozłącznym lesie usługi Active Directory

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Dzierżawa aplikacji hr w jednej chmurze — > docelowa pojedynczych lub wielu domen podrzędnych usługi Active Directory w zaufanym lesie

Zaleca się następującą konfigurację produkcji:

|Wymaganie|Zalecenie|
|:-|:-|
|Liczba agentów inicjowania obsługi administracyjnej usługi Azure AD Connect do wdrożenia|Dwa (dla wysokiej dostępności i pracy awaryjnej)
|Liczba aplikacji łącznika inicjowania obsługi administracyjnej do skonfigurowania|Jedna aplikacja na domenę podrzędną|
|Host serwera dla agenta inicjowania obsługi administracyjnej usługi Azure AD Connect|Windows 2012 R2+ z linią wzroku do kontrolerów domeny usługi Active Directory z geograficznie</br>Możliwość współistnienia z usługą Azure AD Connect|

![Przepływ do agentów lokalnych](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Dzierżawa aplikacji hr w jednej chmurze — > kierowana na wiele domen podrzędnych w rozłącznym lesie usługi Active Directory

W tym scenariuszu obejmuje inicjowanie obsługi administracyjnej użytkowników z aplikacji cloud HR do domen w różnych lasach usługi Active Directory.

Zaleca się następującą konfigurację produkcji:

|Wymaganie|Zalecenie|
|:-|:-|
|Liczba agentów inicjowania obsługi administracyjnej usługi Azure AD Connect do wdrożenia lokalnego|Dwa na rozłączny las usługi Active Directory|
|Liczba aplikacji łącznika inicjowania obsługi administracyjnej do skonfigurowania|Jedna aplikacja na domenę podrzędną|
|Host serwera dla agenta inicjowania obsługi administracyjnej usługi Azure AD Connect|Windows 2012 R2+ z linią wzroku do kontrolerów domeny usługi Active Directory z geograficznie</br>Możliwość współistnienia z usługą Azure AD Connect|

![Dzierżawa aplikacji hr w jednej chmurze rozłączny las usługi Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Wymagania agenta inicjowania obsługi administracyjnej usługi Azure AD Connect

Aplikacja chmury HR do rozwiązania inicjowania obsługi administracyjnej użytkowników usługi Active Directory wymaga wdrożenia co najmniej jednego agenta inicjowania obsługi administracyjnej usługi Azure AD Connect na serwerach z systemem Windows 2012 R2 lub większym. Serwery muszą mieć co najmniej 4 GB pamięci RAM i środowiska uruchomieniowego .NET 4.7.1+. Upewnij się, że serwer hosta ma dostęp sieciowy do docelowej domeny usługi Active Directory.

Aby przygotować środowisko lokalne, kreator konfiguracji agenta inicjowania obsługi administracyjnej usługi Azure AD Connect rejestruje agenta w dzierżawie usługi Azure AD, [otwiera porty,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [umożliwia dostęp do adresów URL](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)i obsługuje [wychodzącą konfigurację serwera proxy HTTPS.](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)

Agent inicjowania obsługi administracyjnej używa konta usługi do komunikowania się z domenami usługi Active Directory. Przed zainstalowaniem agenta utwórz konto usługi w usłudze Użytkownicy i komputery usługi Active Directory, które spełnia następujące wymagania:

- Hasło, które nie wygasa
- Delegowane uprawnienia kontroli do odczytu, tworzenia, usuwania i zarządzania kontami użytkowników

Można wybrać kontrolery domeny, które powinny obsługiwać żądania inicjowania obsługi administracyjnej. Jeśli masz geograficznie rozproszone kontrolery domeny, zainstaluj agenta inicjowania obsługi administracyjnej w tej samej lokacji co preferowane kontrolery domeny. To pozycjonowanie zwiększa niezawodność i wydajność kompleksowego rozwiązania.

Aby uzyskać wysoką dostępność, można wdrożyć więcej niż jednego agenta inicjowania obsługi administracyjnej usługi Azure AD Connect. Zarejestruj agenta, aby obsłużyć ten sam zestaw lokalnych domen usługi Active Directory.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planowanie filtrów zakresu i mapowania atrybutów

Po włączeniu inicjowania obsługi administracyjnej z aplikacji usługi cloud HR do usługi Active Directory lub usługi Azure AD, portal Azure kontroluje wartości atrybutów za pomocą mapowania atrybutów.

### <a name="define-scoping-filters"></a>Definiowanie filtrów zakresu

Użyj [filtrów zakresu,](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) aby zdefiniować reguły oparte na atrybutach, które określają, którzy użytkownicy powinni być aprowicyjni z aplikacji cloud HR do usługi Active Directory lub usługi Azure AD.

Podczas inicjowania procesu Dołączanie, zebrać następujące wymagania:

- Czy aplikacja cloud HR służy do wprowadzania na pokład zarówno pracowników, jak i pracowników warunkowych?
- Czy zamierzasz używać aplikacji cloud HR do inicjowania obsługi administracyjnej użytkowników usługi Azure AD do zarządzania zarówno pracownikami, jak i pracownikami warunkowymi?
- Czy planujesz wdrożyć aplikację cloud HR do inicjowania obsługi administracyjnej użytkowników usługi Azure AD tylko dla podzbioru użytkowników aplikacji hr w chmurze? Przykładem mogą być tylko pracownicy.

W zależności od wymagań podczas konfigurowania mapowań atrybutów można ustawić pole **Zakres obiektu źródłowego,** aby wybrać zestawy użytkowników w aplikacji hr w chmurze powinny być w zakresie inicjowania obsługi administracyjnej usługi Active Directory. Aby uzyskać więcej informacji, zobacz samouczek aplikacji cloud HR dla często używanych filtrów zakresu.

### <a name="determine-matching-attributes"></a>Określanie pasujących atrybutów

Za pomocą inicjowania obsługi administracyjnej można dopasować istniejące konta między systemem źródłowym i docelowym. Po zintegrowaniu aplikacji cloud HR z usługą inicjowania obsługi administracyjnej usługi Azure AD można [skonfigurować mapowanie atrybutów,](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) aby określić, jakie dane użytkownika powinny przepływać z aplikacji cloud HR do usługi Active Directory lub usługi Azure AD.

Podczas inicjowania procesu Dołączanie, zebrać następujące wymagania:

- Jaki jest unikatowy identyfikator w tej aplikacji cloud HR, która służy do identyfikacji każdego użytkownika?
- Z punktu widzenia cyklu życia tożsamości, jak obsługiwać rehires? Czy rehires zachować swoje stare identyfikatory pracowników?
- Czy przetwarzasz przyszłe kontrakty na wynajem i tworzysz dla nich konta usługi Active Directory z wyprzedzeniem?
- Z punktu widzenia cyklu życia tożsamości, jak radzić sobie z konwersją pracownika warunkowego lub w inny sposób?
- Czy przekonwertowani użytkownicy zachowują swoje stare konta usługi Active Directory, czy też otrzymują nowe?

W zależności od wymagań usługa Azure AD obsługuje bezpośrednie mapowanie atrybutów do atrybutów, zapewniając stałe wartości lub [zapisując wyrażenia dla mapowań atrybutów.](../app-provisioning/functions-for-customizing-application-data.md) Ta elastyczność zapewnia najwyższą kontrolę nad tym, co jest wypełniane w docelowym atrybucie aplikacji. Za pomocą [interfejsu API programu Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md) i Eksploratora wykresów można wyeksportować mapowania atrybutów i schematu inicjowania obsługi administracyjnej użytkownika do pliku JSON i zaimportować go z powrotem do usługi Azure AD.

Domyślnie atrybut w aplikacji cloud HR reprezentujący unikatowy identyfikator pracownika jest używany jako pasujący atrybut *mapowany na unikatowy atrybut w usłudze Active Directory.* Na przykład w scenariuszu aplikacji Workday atrybut **Workday** **WorkerID** jest mapowany do atrybutu **EmployeeID usługi** Active Directory.

Można ustawić wiele pasujących atrybutów i przypisać pasowanie pierwszeństwo. Są one oceniane na pasujące pierwszeństwo. Jak tylko zostanie znaleziony dopasowania, nie dalsze pasujące atrybuty są oceniane.

Można również [dostosować domyślne mapowania atrybutów,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)takie jak zmienianie lub usuwanie istniejących mapowań atrybutów. Można również tworzyć nowe mapowania atrybutów zgodnie z potrzebami biznesowymi. Aby uzyskać więcej informacji, zobacz samouczek aplikacji hr w chmurze (na przykład [Workday),](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)aby uzyskać listę atrybutów niestandardowych do mapowania.

### <a name="determine-user-account-status"></a>Określanie stanu konta użytkownika

Domyślnie aplikacja łącznika inicjowania obsługi administracyjnej mapuje stan profilu użytkownika DZIAŁU KADR na stan konta użytkownika w usłudze Active Directory lub usłudze Azure AD w celu określenia, czy włączyć lub wyłączyć konto użytkownika.

Podczas inicjowania procesu Joiners-Leavers, zebrać następujące wymagania.

| Proces | Wymagania |
| - | - |
| **Stolarzy** | Z punktu widzenia cyklu życia tożsamości, jak obsługiwać rehires? Czy rehires zachować swoje stare identyfikatory pracowników? |
| | Czy przetwarzasz przyszłe kontrakty na wynajem i tworzysz dla nich konta usługi Active Directory z wyprzedzeniem? Czy te konta są tworzone w stanie włączonym lub wyłączonym? |
| | Z punktu widzenia cyklu życia tożsamości, jak radzić sobie z konwersją pracownika warunkowego lub w inny sposób? |
| | Czy przekonwertowani użytkownicy zachowują swoje stare konta usługi Active Directory, czy też otrzymują nowe? |
| **Urlopy** | Czy rozwiązania są traktowane inaczej dla pracowników i pracowników warunkowych w usłudze Active Directory? |
| | Jakie daty wejścia w życie są brane pod uwagę w przypadku przetwarzania rozwiązania umowy przez użytkownika? |
| | Jak konwersje pracowników i pracowników warunkowych wpływają na istniejące konta usługi Active Directory? |
| | Jak przetworzyć operację odstąpienia od ceń w usłudze Active Directory? Operacje odstąpienia muszą być obsługiwane, jeśli przyszłe przestarzałe hires są tworzone w usłudze Active Directory w ramach procesu skomola. |

W zależności od wymagań można dostosować logikę mapowania przy użyciu [wyrażeń usługi Azure AD,](../app-provisioning/functions-for-customizing-application-data.md) tak aby konto usługi Active Directory było włączone lub wyłączone na podstawie kombinacji punktów danych.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mapowanie aplikacji cloud HR do atrybutów użytkowników usługi Active Directory

Każda aplikacja cloud HR jest dostarczana z domyślną aplikacją cloud HR do mapowań usługi Active Directory.

Podczas inicjowania procesu Joiners-Movers-Leavers, zebrać następujące wymagania.

| Proces | Wymagania |
| - | - |
| **Stolarzy** | Czy proces tworzenia konta usługi Active Directory jest ręczny, zautomatyzowany lub częściowo zautomatyzowany? |
| | Czy planujesz propagować atrybuty niestandardowe z aplikacji cloud HR do usługi Active Directory? |
| **Przeprowadzki międzynarodowe** | Jakie atrybuty chcesz przetwarzać za każdym razem, gdy operacja Movers odbywa się w aplikacji cloud HR? |
| | Czy w czasie aktualizacji użytkownika przeprowadzasz określone weryfikacje atrybutów? Jeśli tak, podaj szczegóły. |
| **Urlopy** | Czy rozwiązania są traktowane inaczej dla pracowników i pracowników warunkowych w usłudze Active Directory? |
| | Jakie daty wejścia w życie są brane pod uwagę w przypadku przetwarzania rozwiązania umowy przez użytkownika? |
| | Jak konwersje pracowników i pracowników warunkowych wpływają na istniejące konta usługi Active Directory? |

W zależności od wymagań można zmodyfikować mapowania, aby osiągnąć swoje cele integracji. Aby uzyskać więcej informacji, zobacz przykład samouczka aplikacji hr w chmurze (na przykład [Workday),](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)aby uzyskać listę atrybutów niestandardowych do mapowania.

### <a name="generate-a-unique-attribute-value"></a>Generowanie unikatowej wartości atrybutu

Podczas inicjowania procesu Dołączanie, może być konieczne wygenerowanie unikatowych wartości atrybutów podczas ustawiania atrybutów, takich jak CN, samAccountName i UPN, który ma unikatowe ograniczenia.

Funkcja usługi Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) ocenia każdą regułę, a następnie sprawdza wartość wygenerowaną pod kątem unikatowości w systemie docelowym. Na przykład zobacz [Generowanie unikatowej wartości atrybutu userPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Ta funkcja jest obecnie obsługiwana tylko w przypadku inicjowania obsługi administracyjnej użytkowników usługi Workday to Active Directory. Nie można go używać z innymi aplikacjami do inicjowania obsługi administracyjnej.

### <a name="configure-active-directory-ou-container-assignment"></a>Konfigurowanie przypisania kontenera jednostki organizacyjnej usługi Active Directory

Częstym wymaganiem jest umieszczanie kont użytkowników usługi Active Directory w kontenerach na podstawie jednostek biznesowych, lokalizacji i działów. Podczas inicjowania procesu przeprowadzki i zmiany organizacji nadzorczej może być konieczne przeniesienie użytkownika z jednej organizacji organizacyjnej do innej w usłudze Active Directory.

Funkcja [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) służy do konfigurowania logiki biznesowej przypisania jednostki organizacyjnej i mapowania jej na **atrybut ParentDistinguishedName**.

Na przykład, jeśli chcesz utworzyć użytkowników w urzędzie organizacyjnym na podstawie atrybutu HR **Gmina,** można użyć następującego wyrażenia:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

W tym wyrażeniu wartość gminy jest Dallas, Austin, Seattle lub Londyn, konto użytkownika zostanie utworzony w odpowiedniej ou. Jeśli nie ma dopasowania, konto jest tworzone w domyślnej oi.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planowanie dostarczania haseł dla nowych kont użytkowników

Podczas inicjowania procesu Dołączanie, należy ustawić i dostarczyć tymczasowe hasło nowych kont użytkowników. Dzięki inicjowaniu obsługi administracyjnej użytkowników usługi Azure AD w chmurze można wdrożyć funkcję [samoobsługowego resetowania haseł](../authentication/quickstart-sspr.md) usługi Azure AD (SSPR) dla użytkownika pierwszego dnia.

SSPR jest prostym środkiem dla administratorów IT, aby umożliwić użytkownikom zresetowanie haseł lub odblokowanie ich kont. Możesz aprowizować atrybut **Numer telefonu komórkowego** z aplikacji usługi cloud HR do usługi Active Directory i zsynchronizować go z usługą Azure AD. Po tym, jak atrybut **Numer telefonu komórkowego** znajduje się w usłudze Azure AD, można włączyć wiele samoużycie dla konta użytkownika. Pierwszego dnia nowy użytkownik może użyć zarejestrowanego i zweryfikowanego numeru telefonu komórkowego do uwierzytelniania.

## <a name="plan-for-initial-cycle"></a>Zaplanuj cykl początkowy

Gdy usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchamiana po raz pierwszy, wykonuje [początkowy cykl](../app-provisioning/how-provisioning-works.md#initial-cycle) względem aplikacji cloud HR, aby utworzyć migawkę wszystkich obiektów użytkownika w aplikacji cloud HR. Czas przeznaczony dla początkowych cykli zależy bezpośrednio od liczby użytkowników obecnych w systemie źródłowym. Początkowy cykl dla niektórych dzierżaw aplikacji hr w chmurze z ponad 100 000 użytkowników może zająć dużo czasu.

**W przypadku dzierżawców aplikacji hr w dużych chmurze (>30 000 użytkowników)** uruchom początkowy cykl w etapach progresywnych. Uruchom aktualizacje przyrostowe dopiero po sprawdzeniu, czy w usłudze Active Directory są ustawione poprawne atrybuty dla różnych scenariuszy inicjowania obsługi administracyjnej użytkowników. Postępuj zgodnie z kolejnością tutaj.

1. Uruchom cykl początkowy tylko dla ograniczonego zestawu użytkowników, ustawiając [filtr zakresu](#plan-scoping-filters-and-attribute-mapping).
2. Sprawdź inicjowanie obsługi administracyjnej kont usługi Active Directory i wartości atrybutów ustawionych dla użytkowników wybranych dla pierwszego uruchomienia. Jeśli wynik spełnia Twoje oczekiwania, rozwiń filtr zakresu, aby stopniowo uwzględniać więcej użytkowników i weryfikuj wyniki drugiego uruchomienia.

Po spełnieniu wyników początkowego cyklu dla użytkowników testowych uruchom [aktualizacje przyrostowe](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planowanie testowania i bezpieczeństwa

Na każdym etapie wdrażania od początkowego pilotażu do włączania inicjowania obsługi administracyjnej użytkownika, upewnij się, że testujesz, że wyniki są zgodnie z oczekiwaniami i inspekcji cykli inicjowania obsługi administracyjnej.

### <a name="plan-testing"></a>Planowanie testowania

Po skonfigurowaniu aplikacji cloud HR do inicjowania obsługi administracyjnej użytkowników usługi Azure AD uruchom przypadki testowe, aby sprawdzić, czy to rozwiązanie spełnia wymagania organizacji.

|Scenariusze|Oczekiwane wyniki|
|:-|:-|
|Nowy pracownik jest zatrudniony w aplikacji hr w chmurze.| - Konto użytkownika jest aprowidywne w usłudze Active Directory.</br>- Użytkownik może zalogować się do aplikacji domeny Usługi Active Directory i wykonać żądane akcje.</br>— Jeśli skonfigurowano synchronizację usługi Azure AD Connect, konto użytkownika zostanie również utworzone w usłudze Azure AD.
|Użytkownik jest kończony w aplikacji cloud HR.|- Konto użytkownika jest wyłączone w usłudze Active Directory.</br>- Użytkownik nie może zalogować się do żadnych aplikacji korporacyjnych chronionych przez usługą Active Directory.
|Organizacja nadzoru nad użytkownikami jest aktualizowana w aplikacji cloud HR.|Na podstawie mapowania atrybutów konto użytkownika przechodzi z jednej grupy organizacyjnej do drugiej w usłudze Active Directory.|
|Hr aktualizuje menedżera użytkownika w aplikacji hr w chmurze.|Pole menedżera w usłudze Active Directory zostanie zaktualizowane w celu odzwierciedlenia nazwy nowego menedżera.|
|HR ponownie zatrudnia pracownika na nową rolę.|Zachowanie zależy od konfiguracji aplikacji cloud HR do generowania identyfikatorów pracowników:</br>- Jeśli stary identyfikator pracownika jest ponownie odtwarzany dla ponownego zatrudnienia, łącznik włącza istniejące konto usługi Active Directory dla użytkownika.</br>- Jeśli ponowne użycie zostanie aktywowane, łącznik utworzy nowe konto usługi Active Directory dla użytkownika.|
|Hr konwertuje pracownika na pracownika kontraktowego lub odwrotnie.|Dla nowej persony zostanie utworzone nowe konto usługi Active Directory, a stare konto zostanie wyłączone w dniu wejścia w życie konwersji.|

Użyj poprzednich wyników, aby określić, jak przejść implementację automatycznego inicjowania obsługi administracyjnej użytkownika do środowiska produkcyjnego na podstawie ustalonych terminów.

> [!TIP]
> Podczas odświeżania środowiska testowego danymi produkcyjnymi należy używać technik, takich jak ograniczanie danych i czyszczenie danych, w celu usunięcia lub zamaskowania poufnych danych osobowych w celu zapewnienia zgodności ze standardami prywatności i zabezpieczeń. 

### <a name="plan-security"></a>Zaplanuj bezpieczeństwo

Często jest to wymagane przegląd zabezpieczeń w ramach wdrażania nowej usługi. Jeśli przegląd zabezpieczeń jest wymagane lub nie zostały przeprowadzone, zobacz wiele [oficjalnych dokumentów](https://www.microsoft.com/download/details.aspx?id=36391) usługi Azure AD, które zapewniają omówienie tożsamości jako usługi.

### <a name="plan-rollback"></a>Planowanie wycofywania

Implementacja inicjowania obsługi administracyjnej użytkowników zasobów w chmurze może zakończyć się niepowodzeniem w razie potrzeby w środowisku produkcyjnym. Jeśli tak, następujące kroki wycofywania może pomóc w wróceniu do poprzedniego znanego dobrego stanu.

1. Przejrzyj [raport podsumowujący inicjowanie obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) i [dzienniki inicjowania obsługi administracyjnej,](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) aby określić, jakie niepoprawne operacje zostały wykonane na użytkowników lub grupach, których dotyczy problem. Aby uzyskać więcej informacji na temat raportu podsumowania inicjowania obsługi administracyjnej i dzienników, zobacz [Zarządzanie inicjowania obsługi administracyjnej użytkowników aplikacji w chmurze.](#manage-your-configuration)
2. Ostatni znany dobry stan użytkowników lub grup, których dotyczy problem, można określić za pomocą dzienników inspekcji inicjowania obsługi administracyjnej lub przeglądając systemy docelowe (usługa Azure AD lub usługa Active Directory).
3. Współpracuj z właścicielem aplikacji, aby zaktualizować użytkowników lub grupy, których dotyczy problem bezpośrednio w aplikacji, przy użyciu ostatnich znanych wartości stanu.

## <a name="deploy-the-cloud-hr-app"></a>Wdrażanie aplikacji cloud HR

Wybierz aplikację cloud HR, która jest zgodna z wymaganiami rozwiązania.

**Dzień roboczy:** Aby zaimportować profile procesów roboczych z dnia roboczego do usługi Active Directory i usługi Azure AD, zobacz [Samouczek: Konfigurowanie dnia roboczego do automatycznego inicjowania obsługi administracyjnej przez użytkowników](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Opcjonalnie można odpisać adres e-mail i nazwę użytkownika do Workday.

## <a name="manage-your-configuration"></a>Zarządzanie konfiguracją

Usługa Azure AD może zapewnić dodatkowe informacje na temat użycia obsługi administracyjnej użytkowników w organizacji i kondycji operacyjnej za pośrednictwem dzienników inspekcji i raportów.

### <a name="gain-insights-from-reports-and-logs"></a>Uzyskiwanie szczegółowych informacji z raportów i dzienników

Po pomyślnym [cyklu początkowym](../app-provisioning/how-provisioning-works.md#initial-cycle)usługa inicjowania obsługi administracyjnej usługi Azure AD nadal uruchamia aktualizacje przyrostowe z powrotem w nieskończoność, w odstępach czasu zdefiniowanych w samouczkach specyficznych dla każdej aplikacji, dopóki nie wystąpi jedno z następujących zdarzeń:

- Usługa zostanie zatrzymana ręcznie. Nowy cykl początkowy jest wyzwalany przy użyciu [portalu Azure](https://portal.azure.com/) lub odpowiedniego polecenia interfejsu API programu [Microsoft Graph.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- Nowy cykl początkowy jest wyzwalany z powodu zmiany mapowań atrybutów lub filtrów zakresu.
- Proces inicjowania obsługi administracyjnej przechodzi do kwarantanny z powodu wysokiego poziomu błędu. Pozostaje w kwarantannie przez ponad cztery tygodnie, w tym czasie jest automatycznie wyłączona.

Aby przejrzeć te zdarzenia i wszystkie inne działania wykonywane przez usługę inicjowania obsługi administracyjnej, [dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące działań inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Wszystkie działania wykonywane przez usługę inicjowania obsługi administracyjnej są rejestrowane w dziennikach inspekcji usługi Azure AD. Dzienniki inspekcji usługi Azure AD można rozsyłać do dzienników usługi Azure Monitor w celu dalszej analizy. Za pomocą dzienników usługi Azure Monitor (znany również jako obszar roboczy usługi Log Analytics), można wyszukiwać dane, aby znaleźć zdarzenia, analizować trendy i wykonywać korelację w różnych źródłach danych. Obejrzyj ten [klip wideo,](https://youtu.be/MP5IaCTwkQg) aby dowiedzieć się, jakie korzyści płynące z używania dzienników usługi Azure Monitor dla dzienników usługi Azure AD są w praktycznych scenariuszach użytkownika.

Zainstaluj [widoki analizy dziennika dla dzienników aktywności usługi Azure AD,](../reports-monitoring/howto-install-use-log-analytics-views.md) aby uzyskać dostęp do [wstępnie utworzonych raportów](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) dotyczących inicjowania obsługi administracyjnej zdarzeń w twoim środowisku.

Aby uzyskać więcej informacji, zobacz jak [analizować dzienniki aktywności usługi Azure AD za pomocą dzienników usługi Azure Monitor.](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)

### <a name="manage-personal-data"></a>Zarządzanie danymi osobowymi

Agent inicjowania obsługi administracyjnej usługi Azure AD Connect zainstalowany na serwerze Windows tworzy dzienniki w dzienniku zdarzeń systemu Windows, które mogą zawierać dane osobowe w zależności od aplikacji usługi HR w chmurze do mapowań atrybutów usługi Active Directory. Aby spełnić obowiązki użytkowników dotyczące prywatności, należy skonfigurować zaplanowane zadanie systemu Windows, aby wyczyścić dziennik zdarzeń i upewnić się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Usługa inicjowania obsługi administracyjnej usługi Azure AD nie generuje raportów, nie przeprowadza analiz ani nie dostarcza szczegółowych informacji po upływie 30 dni, ponieważ usługa nie przechowuje, nie przetwarza ani nie przechowuje żadnych danych dłużej niż 30 dni.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać wszelkie problemy, które mogą pojawić się podczas inicjowania obsługi administracyjnej, zobacz następujące artykuły:

- [Problem z konfigurowaniem inicjowania obsługi administracyjnej przez użytkownika aplikacji usługi Azure AD Gallery](application-provisioning-config-problem.md)
- [Synchronizowanie atrybutu z lokalnej usługi Active Directory z usługą Azure AD w celu inicjowania obsługi administracyjnej aplikacji](user-provisioning-sync-attributes-for-mapping.md)
- [Inicjowanie obsługi administracyjnej aplikacji usługi Azure AD Gallery trwa wiele godzin lub więcej](application-provisioning-when-will-provisioning-finish.md)
- [Problem z zapisywaniem poświadczeń administratora podczas konfigurowania inicjowania obsługi administracyjnej aplikacji usługi Azure Active Directory Gallery](application-provisioning-config-problem-storage-limit.md)
- [Żaden użytkownik nie jest aprowiany do aplikacji usługi Azure AD Gallery](application-provisioning-config-problem-no-users-provisioned.md)
- [Niewłaściwy zestaw użytkowników jest aprowiany do aplikacji usługi Azure AD Gallery](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Konfigurowanie Podglądu zdarzeń systemu Windows do rozwiązywania problemów z agentami](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Konfigurowanie dzienników inspekcji portalu Azure do rozwiązywania problemów z usługami](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Opis dzienników dla operacji tworzenia konta użytkownika usługi AD](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Opis dzienników operacji aktualizacji programu Manager](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Rozwiązywanie często napotkanych błędów](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Następne kroki

- [Pisanie wyrażeń dla mapowań atrybutów](functions-for-customizing-application-data.md)
- [Interfejs API synchronizacji usługi Azure AD — omówienie](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](skip-out-of-scope-deletions.md)
- [Agent inicjowania obsługi administracyjnej usługi Azure AD Connect: historia wydania wersji wersji](provisioning-agent-release-version-history.md)
