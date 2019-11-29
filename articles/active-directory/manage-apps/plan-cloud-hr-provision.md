---
title: Planowanie aplikacji w chmurze w celu Azure Active Directory aprowizacji użytkowników
description: W tym artykule opisano proces wdrażania integracji systemów kadr w chmurze, takich jak Workday i Sucessfactors z Azure Active Directory. Integracja usługi Azure AD z systemem zarządzania KADRą w chmurze skutkuje ukończeniem całego cyklu życia tożsamości.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 5d55aafc29b3b022d1023077d2d8f459b0608ae7
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555654"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planowanie aplikacji w chmurze w celu Azure Active Directory aprowizacji użytkowników

W przeszłości pracownicy IT korzystali z ręcznych metod tworzenia, aktualizowania i usuwania pracowników, takich jak przekazywanie plików CSV, lub skryptów niestandardowych do synchronizowania danych pracownika. Te procesy aprowizacji są podatne na błędy, niezabezpieczone i trudne do zarządzania.

Aby bezproblemowo zarządzać kompleksowym cyklem życia tożsamości pracowników, dostawców lub procesów roboczych, [usługa Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) zapewnia integrację z aplikacjami ludzkimi opartymi na chmurze, takimi jak Workday lub SuccessFactors.

Usługa Azure AD używa tej integracji w celu włączenia następujących przepływów pracy aplikacji w chmurze (aplikacji):

- **Inicjowanie obsługi administracyjnej użytkowników** w usłudze AD-wybrane zestawy użytkowników z aplikacji kadrowej w chmurze do co najmniej jednej domeny Active Directory (AD).
- **Inicjowanie obsługi administracyjnej użytkowników tylko w chmurze do usługi Azure AD** — w scenariuszach, w których Usługa AD nie jest używana, udostępniaj użytkownikom bezpośrednio z aplikacji kadr w chmurze do usługi Azure AD.
- **Wróć do aplikacji w chmurze.** — Napisz adresy e-mail i atrybuty nazwy użytkownika z usługi Azure AD z powrotem do aplikacji w chmurze.

> [!NOTE]
> Ten plan wdrożenia przedstawia sposób wdrażania przepływów pracy aplikacji KADRowych w chmurze przy użyciu aprowizacji użytkowników usługi Azure AD. Informacje o wdrażaniu automatycznej aprowizacji użytkowników w aplikacjach SaaS można znaleźć w [zaplanowaniu wdrożenia automatycznego aprowizacji użytkowników](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Włączone scenariusze kadr

Usługa aprowizacji użytkowników w usłudze Azure AD umożliwia automatyzację następujących scenariuszy zarządzania cyklem życia tożsamości opartych na usłudze HR:

- **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do aplikacji w chmurze, konto użytkownika jest automatycznie tworzone w usługach AD i Azure AD z opcją zapisania adresu e-mail i atrybutów nazwy użytkownika w aplikacji w chmurze.
- **Aktualizacje atrybutu pracownika i profilu** — gdy rekord pracownika zostanie zaktualizowany w aplikacji kadrowej w chmurze (takiej jak nazwa, tytuł lub Menedżer), konto użytkownika zostanie automatycznie zaktualizowane w usługach AD i Azure AD.
- **Zakończenie zatrudnienia pracownika** — gdy pracownik zostanie zakończony w aplikacji kadrowej w chmurze, jego konto użytkownika zostanie automatycznie wyłączone w usługach AD i Azure AD.
- **Pracownicy** przedziałów — gdy pracownik jest ponownie zatrudniany w aplikacji kadrowej w chmurze, jego stare konto można automatycznie ponownie uaktywnić lub zainicjować ich obsługę w usługach AD i Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Dla kogo jest to integracja najlepiej dopasowanej?

Integracja aplikacji w chmurze z obsługą administracyjną użytkowników w usłudze Azure AD doskonale nadaje się dla organizacji, które:

- Zażądaj wstępnie skompilowanego rozwiązania opartego na chmurze na potrzeby aprowizacji użytkowników w chmurze
- Wymagaj bezpośredniej aprowizacji użytkowników z aplikacji KADRowej w chmurze do usługi AD lub Azure AD
- Wymagaj aprowizacji użytkowników przy użyciu danych uzyskanych z aplikacji KADRowej w chmurze
- Wymagaj synchronizacji, przeniesienia i opuszczenia użytkowników do synchronizowania z co najmniej jednym lasów, domenami i jednostkami organizacyjnymi usługi AD na podstawie informacji o zmianach wykrytych w aplikacji w chmurze
- Użyj pakietu Office 365 do obsługi poczty e-mail

## <a name="learn"></a>Nauka

Inicjowanie obsługi użytkowników tworzy podstawę do ciągłego zarządzania tożsamościami i zwiększa jakość procesów biznesowych, które opierają się na autorytatywnych danych tożsamości.

### <a name="terms"></a>Warunki

W tym artykule są stosowane następujące warunki:

- **System źródłowy** — repozytorium użytkowników, z których pochodzą postanowienia usługi Azure AD (na przykład aplikacja do obsługi kadr w chmurze, taka jak Workday i SuccessFactors).
- **System docelowy** — repozytorium użytkowników, których dotyczą zasady usługi Azure AD (na przykład AD, Azure AD, Office 365 lub innych aplikacji SaaS).
- **Sprzęganie — proces opuszczania firmy przeprowadzkowej** — termin używany do nowych zatrudniania, transferów i kończenia pracy przy użyciu aplikacji kadrowej w chmurze jako systemu rekordów. Proces kończy się po pomyślnym zainicjowaniu przez usługę niezbędnych atrybutów do systemu docelowego.

### <a name="key-benefits"></a>Najważniejsze korzyści

Ta funkcja aprowizacji IT oparta na usłudze kadr oferuje znaczące korzyści biznesowe, jak wymieniono poniżej:

- **Zwiększ produktywność** — teraz możesz zautomatyzować przypisanie kont użytkowników, licencji usługi Office 365 i zapewnić dostęp do grup kluczy. Automatyzacja przypisań pozwala nowym zatrudniać natychmiastowy dostęp do swoich narzędzi do zadań i zwiększyć produktywność.
- **Zarządzanie ryzykiem** — można zwiększyć bezpieczeństwo, automatyzując zmiany w zależności od stanu pracownika lub członkostwa w grupach przy użyciu danych przepływających z aplikacji w chmurze. Automatyzacja zmian gwarantuje, że tożsamości użytkowników i dostęp do najważniejszych aplikacji są aktualizowane automatycznie, gdy użytkownicy przechodzą lub opuściją organizację.
- **Zgodność i zarządzanie adresami** — usługa Azure AD obsługuje natywne dzienniki inspekcji dla żądań aprowizacji użytkowników wykonywanych przez aplikacje zarówno w systemie źródłowym, jak i docelowym. Inspekcja umożliwia śledzenie użytkowników, którzy mają dostęp do aplikacji z jednego ekranu.
- **Zarządzanie kosztami** — Automatyczna obsługa administracyjna zmniejsza koszty, unikając nieefektywności i błędu ludzkiego związanego z obsługą ręczną. Zmniejsza to potrzebę tworzenia niestandardowych rozwiązań aprowizacji użytkowników utworzonych w czasie z użyciem starszych i nieaktualnych platform.

### <a name="licensing"></a>Licencjonowanie

Aby skonfigurować aplikację kadr w chmurze do integracji aprowizacji użytkowników usługi Azure AD, musisz mieć ważną [licencję Azure AD — wersja Premium](https://azure.microsoft.com/pricing/details/active-directory/) i licencję dla aplikacji kadr w chmurze, taką jak Workday lub SuccessFactors.

Potrzebna jest również ważna licencja subskrypcyjna na Azure AD — wersja Premium P1 lub nowsza dla każdego użytkownika, który zostanie pożądany przez aplikację kadr w chmurze i zostanie zainicjowany do usługi AD lub Azure AD. Niewłaściwa liczba licencji należących do aplikacji w chmurze w usłudze Cloud Personal może prowadzić do błędów podczas aprowizacji użytkownika.

### <a name="prerequisites"></a>Wymagania wstępne

- Dostęp administratora globalnego usługi Azure AD w celu skonfigurowania agenta aprowizacji Azure AD Connect.
- Wystąpienie testowe i produkcyjne aplikacji w chmurze.
- Uprawnienia administratora w aplikacji KADRowej w chmurze umożliwiającej tworzenie użytkownika integracji systemu i wprowadzanie zmian w celu przetestowania danych pracownika na potrzeby testowania.
- W przypadku aprowizacji użytkowników do usługi AD serwer, na którym działa system Windows Server 2012 lub nowszy z platformą .NET 4.7.1 + Runtime, jest wymagany do hostowania [agenta aprowizacji Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) synchronizowania użytkowników między usługami AD i Azure AD.

### <a name="training-resources"></a>Zasoby szkoleniowe

| **Zasoby** | **Link i opis** |
|:-|:-|
| Filmy | [Co to jest inicjowanie obsługi użytkowników w usłudze Active Directory systemu Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Jak wdrożyć Inicjowanie obsługi użytkowników w usłudze Active Directory systemu Azure?](https://youtu.be/pKzyts6kfrw) |
| Samouczki | Zapoznaj się z [listą samouczków dotyczących integrowania aplikacji SaaS z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [Samouczek: Konfigurowanie produktu Workday do automatycznego aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| Często zadawane pytania | [Automatyczne Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#frequently-asked-questions) |
| | [Inicjowanie obsługi z poziomu produktu Workday do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architektura rozwiązania

Poniższy przykład opisuje kompleksową architekturę rozwiązania obsługi użytkowników dla typowych środowisk hybrydowych i obejmuje:

- **Autorytatywny przepływ danych HR — od aplikacji w chmurze do usługi AD** — w tym przepływie jest inicjowane zdarzenie HR (dołączanie — proces opuszczania i opuszczanie) w ramach dzierżawy aplikacji w chmurze. Usługa Azure AD Provisioning i Agent aprowizacji Azure AD Connect udostępniają dane użytkownika z dzierżawy aplikacji kadr w chmurze do usługi AD. W zależności od zdarzenia może to prowadzić do tworzenia/aktualizowania/włączania/wyłączania operacji w usłudze AD.
- **Synchronizacja z usługą Azure AD i zapisywanie zwrotne poczty e-mail i nazwy użytkownika z lokalnej usługi AD do aplikacji w chmurze w usłudze Cloud** — po zaktualizowaniu kont w usłudze AD jest ona synchronizowana z usługą Azure AD za pośrednictwem Azure AD Connect, a adresy e-mail i atrybuty nazwy użytkownika można zapisywać z powrotem do dzierżawy aplikacji w chmurze.

![Diagram przepływu pracy](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Opis przepływu pracy

Kluczowe kroki wskazane na diagramie są następujące:  

1. **Zespół kadr** wykonuje transakcje w dzierżawie aplikacji kadrowej w chmurze.
2. **Usługa Azure AD Provisioning** uruchamia zaplanowane cykle z dzierżawy aplikacji kadr w chmurze i identyfikuje zmiany, które muszą zostać przetworzone w celu synchronizacji z usługą AD.
3. **Usługa Azure AD Provisioning** wywołuje Azure AD Connect agenta aprowizacji z ładunkiem żądania zawierającym funkcję tworzenia/aktualizowania/włączania/wyłączania konta usługi AD.
4. **Azure AD Connect Agent aprowizacji** używa konta usługi do zarządzania danymi kont usługi AD.
5. **Azure AD Connect** uruchamia [synchronizację](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) Delta w celu ściągnięcia aktualizacji w usłudze AD.
6. Aktualizacje **usługi AD** są synchronizowane z usługą Azure AD.
7. **Usługa aprowizacji usługi Azure AD** zastornowania atrybutu poczty e-mail i nazwy użytkownika z usługi Azure AD do dzierżawy aplikacji kadrowej w chmurze.

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożenia

Podczas określania strategii tego wdrożenia w danym środowisku należy wziąć pod uwagę potrzeby organizacyjne.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnością oczekiwań, rezultatów i obowiązków. Aby uniknąć tych pułapek, [należy się upewnić, że angażuje się w odpowiednie osoby zainteresowane](https://aka.ms/deploymentplans) i że role udziałowców w projekcie są zrozumiałe w celu udokumentowania uczestników projektu i ich danych wejściowych i accountabilities.

Musisz dołączyć przedstawiciela z organizacji KADRy, który może zapewnić dane wejściowe dla istniejących procesów biznesowych usługi kadr oraz wymagania dotyczące przetwarzania danych przez proces roboczy.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Należy aktywnie komunikować się z użytkownikami, w jaki sposób ich środowisko zostanie zmienione, gdy ulegnie zmianie, oraz jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Integrowanie procesów biznesowych i przepływów pracy tożsamości z aplikacji w chmurze w systemie docelowym wymaga dużej ilości walidacji danych, transformacji danych, czyszczenia danych i kompleksowego testowania przed wdrożeniem rozwiązania w środowisku produkcyjnym.

Zalecamy, aby początkowa konfiguracja była uruchamiana w [środowisku pilotażowym](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) przed przeskalowaniem go do wszystkich użytkowników w produkcji.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Wybierz aplikacje łącznika usługi Cloud Personal

Aby ułatwić przepływy pracy inicjowania obsługi usługi Azure AD między aplikacją KADRową w chmurze a usługą AD, istnieje wiele aplikacji łączników aprowizacji, które można dodać z galerii aplikacji usługi Azure AD:

- **Aplikacja Cloud kadr z obsługą administracyjną użytkowników w usłudze AD** — ta aplikacja łącznika aprowizacji ułatwia Inicjowanie obsługi konta użytkownika z poziomu aplikacji w chmurze w ramach jednej domeny usługi AD. Jeśli masz wiele domen, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji usługi Azure AD dla każdej domeny usługi AD, dla której ma zostać wdrożona.
- **Obsługa administracyjna aplikacji w chmurze w usłudze Azure AD** — gdy Azure AD Connect jest narzędziem, które ma być używane do synchronizowania użytkowników usługi AD z usługą Azure AD, ta aplikacja łącznika aprowizacji może być używana do ułatwienia aprowizacji użytkowników korzystających tylko z chmury z aplikacji w chmurze usługi Azure AD.
- **Zapisywanie zwrotne aplikacji w chmurze** — ta aplikacja łącznika aprowizacji ułatwia zapisywanie adresów e-mail użytkowników z usługi Azure AD do aplikacji w chmurze.

Na przykład na poniższej ilustracji przedstawiono aplikacje łącznika produktu Workday dostępne w galerii aplikacji usługi Azure AD.

![Galeria aplikacji portalu Azure Active Directory](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Schemat blokowy decyzji

Użyj wykresu przepływu decyzji poniżej, aby określić, które aplikacje w chmurze dotyczące aprowizacji są odpowiednie dla Twojego scenariusza.

![Schemat blokowy decyzji](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Zaprojektowanie topologii wdrażania agenta aprowizacji Azure AD Connect

Integracja między aplikacją KADRową w chmurze a usługą AD wymaga następujących czterech składników:

- Dzierżawa aplikacji KADRowej w chmurze
- Aplikacja łącznika aprowizacji
- Azure AD Connect agenta aprowizacji
- Domena usługi AD

Azure AD Connect topologia wdrażania agenta aprowizacji jest zależna od liczby dzierżawców aplikacji w chmurze i domen podrzędnych usługi AD, które planujesz zintegrować. Jeśli masz wiele domen usługi AD, zależą od tego, czy domeny usługi AD są ciągłe, czy [rozłączone](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

Na podstawie decyzji wybierz jeden z scenariuszy wdrażania:

- Dzierżawa pojedynczej aplikacji w chmurze — > docelowej jednej lub wielu domen podrzędnych usługi AD w lesie zaufanym
- Dzierżawa pojedynczej aplikacji w chmurze — > cel wielu domen podrzędnych w odłączonym lesie usługi AD

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>Dzierżawa pojedynczej aplikacji w chmurze — > jedną lub wiele docelowych domen podrzędnych usługi AD w lesie zaufanym

Zalecamy zastosowanie następującej konfiguracji produkcyjnej:

|Wymaganie|Zalecenie|
|:-|:-|
|Liczba Azure AD Connect agentów aprowizacji do wdrożenia|2 (w przypadku wysokiej dostępności i przełączenia w tryb failover)
|Liczba aplikacji łącznika aprowizacji do skonfigurowania|Jedna aplikacja na domenę podrzędną|
|Host serwera dla Azure AD Connect agenta aprowizacji|Windows 2012 R2 + z wierszem wglądu w geograficznie zlokalizowane kontrolery domeny usługi AD</br>Może współistnieć z usługą Azure AD Connect|

![Przepływ do agentów lokalnych](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>Dzierżawa pojedynczej aplikacji w chmurze — > cel wielu domen podrzędnych w odłączonym lesie usługi AD

Ten scenariusz obejmuje Inicjowanie obsługi użytkowników z aplikacji w chmurze w przypadku domen w odłączonych lasach usługi AD.

Zalecamy zastosowanie następującej konfiguracji produkcyjnej:

|Wymaganie|Zalecenie|
|:-|:-|
|Liczba Azure AD Connect agentów aprowizacji do wdrożenia w środowisku lokalnym|2 na odłączony Las usługi AD|
|Liczba aplikacji łącznika aprowizacji do skonfigurowania|Jedna aplikacja na domenę podrzędną|
|Host serwera dla Azure AD Connect agenta aprowizacji|Windows 2012 R2 + z wierszem wglądu w geograficznie zlokalizowane kontrolery domeny usługi AD</br>Może współistnieć z usługą Azure AD Connect|

![Dzierżawa usługi AD z jedną chmurą aplikacji KADRowej](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect wymagania dotyczące agenta aprowizacji

Rozwiązanie do aprowizacji aplikacji w chmurze dla użytkowników usługi AD wymaga wdrożenia co najmniej jednego Azure AD Connect agentów aprowizacji na serwerach z systemem Windows 2012 R2 lub nowszym oraz z co najmniej 4 GB pamięci RAM i środowiskiem .NET 4.7.1 + Runtime. Upewnij się, że serwer hosta ma dostęp sieciowy do docelowej domeny usługi AD.

Aby przygotować środowisko Premium, Kreator konfiguracji agenta aprowizacji Azure AD Connect zarejestruje agenta za pomocą dzierżawy usługi Azure AD, [otwiera porty](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [zezwala na dostęp do adresów URL](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)i obsługuje [konfigurację wychodzącego serwera proxy HTTPS](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Agent aprowizacji używa konta usługi do komunikacji z domenami usługi AD. Przed zainstalowaniem agenta zalecamy utworzenie konta usługi w Użytkownicy i komputery usługi AD, które spełniają następujące wymagania:

- Hasło, które nie wygasa
- Delegowane uprawnienia kontroli do odczytu, tworzenia, usuwania i zarządzania kontami użytkowników

Można wybrać kontrolery domeny, które powinny obsługiwać żądania aprowizacji. Jeśli masz kilka rozproszonych geograficznie kontrolerów domeny, Zainstaluj agenta aprowizacji w tej samej lokacji co preferowane kontrolery domeny, aby zwiększyć niezawodność i wydajność kompleksowego rozwiązania.

Aby zapewnić wysoką dostępność, można wdrożyć więcej niż jednego Azure AD Connect agenta aprowizacji i zarejestrować go w celu obsługi tego samego zestawu lokalnych domen usługi AD.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planowanie filtrów zakresu i mapowania atrybutów

Po włączeniu obsługi administracyjnej z poziomu aplikacji w chmurze w usłudze AD lub usłudze Azure AD Azure Portal kontroluje wartości atrybutów za pośrednictwem mapowania atrybutu.

### <a name="define-scoping-filters"></a>Definiowanie filtrów określania zakresu

[Filtry zakresu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) umożliwiają definiowanie reguł opartych na atrybutach, które określają, którzy użytkownicy powinni być obsługiwani z aplikacji kadrowej w chmurze do usługi AD lub Azure AD.

Po zainicjowaniu procesu przyłączania należy zebrać następujące wymagania:

- Czy aplikacja usługi Cloud kadr służy do założenia pracowników i pracowników warunkowych?
- Czy planujesz używać aplikacji w chmurze do aprowizacji użytkowników usługi Azure AD do zarządzania pracownikami i pracownikami warunkowymi?
- Czy planujesz wdrożyć aplikację kadr w chmurze na potrzeby aprowizacji użytkowników usługi Azure AD tylko dla podzbioru użytkowników aplikacji w chmurze (na przykład tylko pracownicy)?

W zależności od wymagań, podczas konfigurowania mapowań atrybutów można ustawić pole **Zakres obiektu źródłowego** , aby wybrać, które zestawy użytkowników w aplikacji kadrowej w chmurze powinny znajdować się w zakresie dla aprowizacji do usługi AD. Zapoznaj się z samouczkiem dotyczącym usługi Cloud kadr dla często używanych filtrów określania zakresu.

### <a name="determine-matching-attributes"></a>Określ pasujące atrybuty

Dzięki aprowizacji można dopasować istniejące konta między systemem źródłowym a docelowym. Po zintegrowaniu aplikacji KADRowej w chmurze z usługą Azure AD Provisioning można [skonfigurować Mapowanie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) , aby określić, jakie dane użytkownika powinny być przepływać z aplikacji kadrowej w chmurze do usługi AD lub Azure AD.

Po zainicjowaniu procesu przyłączania należy zebrać następujące wymagania:

- Jaki jest unikatowy identyfikator w tej aplikacji usługi Cloud kadr, który jest używany do identyfikowania poszczególnych użytkowników?
- Jak można obsłużyć się w perspektywie cyklu życia tożsamości? Czy pracownicy przezatrudniają swój stary identyfikator pracownika?
- Czy chcesz przetwarzać w przyszłości Zatrudnienie i tworzyć konta usługi AD z wyprzedzeniem?
- W ramach perspektywy cyklu życia tożsamości, w jaki sposób można obsłużyć proces konwersji pracownika na warunkowe lub w inny sposób?
- Czy przekonwertowani użytkownicy zachowują swoje stare konto usługi AD, czy otrzymują nowe?

W zależności od wymagań usługa Azure AD obsługuje mapowanie między atrybutami i atrybutami, zapewniając wartości stałe lub [pisząc wyrażenia dla mapowań atrybutów](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Ta elastyczność zapewnia ostateczną kontrolę nad tym, co zostanie wypełnione w atrybucie aplikacji Target. Za pomocą [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) i Eksploratora grafów można wyeksportować mapowania atrybutów aprowizacji użytkowników i schemat do pliku JSON, a następnie zaimportować je z powrotem do usługi Azure AD.

**Domyślnie**atrybut w aplikacji kadrowej w chmurze reprezentujący unikatowy identyfikator pracownika jest używany jako pasujący atrybut *mapowany na unikatowy atrybut w usłudze AD.* Na przykład w scenariuszu aplikacji Workday atrybut *Workday* *WorkerID* jest mapowany na atrybut AD *IDPracownika* .

Można ustawić wiele pasujących atrybutów i przypisać priorytet pasujący. Są one oceniane w oparciu o pasujące pierwszeństwo. Po znalezieniu dopasowania nie są oceniane żadne dalsze pasujące atrybuty.

Można również [dostosować domyślne mapowania atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) , takie jak zmiana lub usuwanie istniejących mapowań atrybutów, lub utworzyć nowe mapowania atrybutów zgodnie z potrzebami biznesowymi. Zapoznaj się z samouczkiem dotyczącym usługi Cloud Kadr (na przykład [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)), aby uzyskać listę atrybutów niestandardowych, które mają być mapowane.

### <a name="determine-user-account-status"></a>Określanie stanu konta użytkownika

Domyślnie aplikacja łącznika aprowizacji mapuje **stan profilu użytkownika usługi HR** na **stan konta użytkownika** w usłudze AD/Azure AD w celu ustalenia, czy należy włączyć lub wyłączyć konto użytkownika.

Po zainicjowaniu procesu przyłączania/opuszczania należy zebrać następujące wymagania:

| Proces | Wymagania |
| - | - |
| **Sprzężenia** | Jak można obsłużyć się w perspektywie cyklu życia tożsamości? Czy pracownicy przezatrudniają swój stary identyfikator pracownika? |
| | Czy możesz przetwarzać zatrudnienie w przyszłości i tworzyć konta usługi AD z wyprzedzeniem? Czy te konta są tworzone w stanie włączony/wyłączony? |
| | W ramach perspektywy cyklu życia tożsamości, w jaki sposób można obsłużyć proces konwersji pracownika na warunkowe lub w inny sposób? |
| | Czy przekonwertowani użytkownicy zachowują swoje stare konto usługi AD, czy otrzymują nowe? |
| **Wyjściu** | Czy zakończenia są obsługiwane inaczej w przypadku pracowników i pracowników warunkowych w usłudze AD? |
| | Jakie daty efektywne są brane pod uwagę w przypadku przetwarzania zakończenia użytkownika? |
| | Jak pracownicy i warunkowe konwersje procesów roboczych wpływają na istniejące konta usługi AD? |
| | Jak przetworzyć operację "odwołaj" w usłudze AD? Operacje unieważniania muszą być obsługiwane, jeśli w usłudze AD zostaną utworzone przyszłe zatrudnienie w ramach procesu łącznika. |

W zależności od wymagań można dostosować logikę mapowania przy użyciu [wyrażeń usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) , aby konto usługi AD zostało włączone lub wyłączone na podstawie kombinacji punktów danych.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Mapowanie aplikacji KADRowej w chmurze na atrybuty użytkownika usługi AD

Każda aplikacja KADRa w chmurze jest dostarczana z domyślną aplikacją w chmurze do mapowań usługi AD.

Po zainicjowaniu procesu joins/przeprowadzki/opuszczania należy zebrać następujące wymagania:

| Proces | Wymagania |
| - | - |
| **Sprzężenia** | Czy proces tworzenia konta usługi AD jest ręczny, zautomatyzowany czy częściowo zautomatyzowany? |
| | Czy planowane jest propagowanie atrybutów niestandardowych z aplikacji w chmurze do usługi AD? |
| **DM** | Jakie atrybuty chcesz przetworzyć za każdym razem, gdy w aplikacji w chmurze jest wykonywana operacja "przeprowadzki"? |
| | Czy wykonujesz wszystkie określone walidacje atrybutów w momencie aktualizacji użytkownika? Jeśli tak, podaj szczegóły. |
| **Wyjściu** | Czy zakończenia są obsługiwane inaczej w przypadku pracowników i pracowników warunkowych w usłudze AD? |
| | Jakie daty efektywne są brane pod uwagę w przypadku przetwarzania zakończenia użytkownika? |
| | Jak pracownicy i warunkowe konwersje procesów roboczych wpływają na istniejące konta usługi AD? |

W zależności od wymagań można zmodyfikować mapowania, aby spełniały cele integracji. Zapoznaj się z samouczkiem dotyczącym konkretnej aplikacji w chmurze (na przykład [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)), aby uzyskać listę atrybutów niestandardowych, które mają być mapowane.

### <a name="generate-unique-attribute-value"></a>Generuj unikatową wartość atrybutu

Po zainicjowaniu procesu przyłączania może być konieczne wygenerowanie unikatowych wartości atrybutów podczas ustawiania atrybutów, takich jak CN, samAccountName i UPN, które mają unikatowe ograniczenia.

Funkcja Azure AD [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) oblicza każdą regułę, a następnie sprawdza wartość wygenerowaną w celu zapewnienia unikatowości w systemie docelowym. Zobacz przykład [Generuj unikatową wartość atrybutu userPrincipalName (UPN)](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Ta funkcja jest obecnie obsługiwana tylko w przypadku "Workday Active Directory aprowizacji użytkowników". Nie można jej używać z innymi aplikacjami aprowizacji.

### <a name="configure-ad-ou-container-assignment"></a>Konfigurowanie przypisania kontenera jednostki organizacyjnej AD

Typowym wymaganiem jest umieszczenie kont użytkowników usługi AD w kontenerach w oparciu o jednostki biznesowe, lokalizacje i działy. Po zainicjowaniu procesu firmy przeprowadzkowej, jeśli istnieje zmiana organizacji nadzorczej, może być konieczne przeniesienie użytkownika z jednej jednostki organizacyjnej do innej w usłudze AD.

Funkcja [Switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) służy do konfigurowania logiki biznesowej dla przypisania jednostki organizacyjnej i mapowania jej do atrybutu AD *parentDistinguishedName*.

Na przykład jeśli chcesz utworzyć użytkowników w jednostce organizacyjnej na podstawie atrybutu HR "gmina", możesz użyć poniższego wyrażenia.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

W tym wyrażeniu, jeśli wartość gminy to Dallas, Austin, Seattle lub Londyn, konto użytkownika zostanie utworzone w odpowiedniej jednostce organizacyjnej. Jeśli nie ma dopasowania, konto zostanie utworzone w domyślnej jednostce organizacyjnej.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planowanie dostarczania haseł dla nowych kont użytkowników

Po zainicjowaniu procesu przyłączania należy ustawić i dostarczyć tymczasowe hasło nowych kont użytkowników. Za pomocą usługi Azure Personal do aprowizacji użytkowników w chmurze możesz wdrożyć funkcję [samoobsługowego resetowania hasła](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) usługi Azure AD dla użytkownika w dniu 1.

SSPR to prosty środek dla administratorów IT umożliwiający użytkownikom Resetowanie swoich haseł lub odblokowywanie ich kont. Możesz udostępnić atrybut **numeru komórkowego** z aplikacji w chmurze w usłudze AD i zsynchronizować ją z usługą Azure AD. Gdy atrybut **numer telefonu komórkowego** znajduje się w usłudze Azure AD, możesz włączyć SSPR dla konta użytkownika, tak aby w dniu 1 nowi użytkownicy mogli używać zarejestrowanego i zweryfikowanego numeru komórkowego na potrzeby uwierzytelniania.

## <a name="plan-for-initial-cycle"></a>Planowanie cyklu początkowego

Gdy usługa Azure AD Provisioning jest uruchamiana po raz pierwszy, wykonuje [początkowy cykl](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning) względem aplikacji kadrowej w chmurze, aby utworzyć migawkę wszystkich obiektów użytkownika w aplikacji w chmurze. Czas trwania cykli początkowej zależy od liczby użytkowników w systemie źródłowym. Cykl początkowy dla niektórych dzierżawców aplikacji usługi Cloud kadr z ponad 100 000 użytkownikami może zająć dużo czasu.

W **przypadku dużych dzierżawców aplikacji w chmurze (> 30 000) zaleca** się uruchomienie początkowego cyklu w stopniowych etapach i Uruchamianie aktualizacji przyrostowych dopiero po zweryfikowaniu, że poprawne atrybuty są ustawione w usłudze AD dla różnych scenariuszy aprowizacji użytkowników. Postępuj zgodnie z poniższą kolejnością:

1. Uruchom cykl początkowy tylko dla ograniczonego zestawu użytkowników, ustawiając [Filtr określania zakresu](#plan-scoping-filters-and-attribute-mapping).
2. Sprawdź poprawność aprowizacji konta usługi AD i wartości atrybutów ustawione dla użytkowników wybranych do pierwszego uruchomienia. Jeśli wynik spełnia Twoje oczekiwania, rozwiń filtr określania zakresu, aby stopniowo obejmować więcej użytkowników i zweryfikować wyniki drugiego przebiegu.

Po spełnieniu wyników cyklu początkowego dla użytkowników testowych można uruchomić [Aktualizacje przyrostowe](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planowanie testowania i zabezpieczeń

Na każdym etapie wdrożenia od początkowej wersji pilotażowej przez włączenie aprowizacji użytkowników upewnij się, że testy zostały przeprowadzone zgodnie z oczekiwaniami, i Przeprowadź inspekcję cykli aprowizacji.

### <a name="plan-testing"></a>Testowanie planu

Po skonfigurowaniu aplikacji do obsługi administracyjnej w chmurze w usłudze Azure AD należy uruchomić przypadki testowe, aby sprawdzić, czy to rozwiązanie spełnia wymagania organizacji.

|Scenariusze|Oczekiwane wyniki|
|:-|:-|
|Nowe zatrudnienie pracowników w aplikacji KADRowej w chmurze| — Konto użytkownika jest inicjowane w usłudze AD.</br>— Użytkownicy mogą logować się do aplikacji domenowych w domenie i wykonywać odpowiednie działania.</br>— Jeśli skonfigurowano synchronizację z usługą AAD Connect, konto użytkownika również zostanie utworzone w usłudze Azure AD.
|Użytkownik zostanie przerwany w aplikacji KADRowej w chmurze|— Konto użytkownika jest wyłączone w usłudze AD.</br>— Użytkownik nie może zalogować się do żadnych aplikacji dla przedsiębiorstw chronionych przez usługi AD.
|Organizacja nadzorcza użytkownika została zaktualizowana w aplikacji KADRowej w chmurze|Na podstawie mapowania atrybutów konto użytkownika przenosi się z jednej jednostki organizacyjnej na inną w usłudze AD.|
|Menedżer użytkownika aktualizacji usługi HR w aplikacji KADRowej w chmurze|Pole kierownika w usłudze AD jest aktualizowane w celu odzwierciedlenia nazwy nowego menedżera.|
|HR zatrudnia pracownika w nowej roli.|Zachowanie zależy od konfiguracji aplikacji KADRowej w chmurze w celu generowania identyfikatorów pracowników:</br>— Jeśli identyfikator starego pracownika jest ponownie używany w przypadku ponownego zatrudniania, łącznik spowoduje włączenie istniejącego konta usługi AD dla użytkownika.</br>— Jeśli ponowne zatrudnianie uzyska nowy identyfikator pracownika, łącznik utworzy nowe konto usługi AD dla tego użytkownika.|
|HR konwertuje pracownika na pracownika kontraktowe lub odwrotnie|Nowe konto usługi AD zostanie utworzone dla nowej osoby, a stare konto zostanie wyłączone przy dacie wejścia w życie konwersji.|

Skorzystaj z powyższych wyników, aby określić sposób przejścia implementacji automatycznej aprowizacji użytkowników do środowiska produkcyjnego na podstawie ustalonych osi czasu.

> [!TIP]
> Zalecamy używanie takich technik jak zmniejszenie ilości danych i szybka Kontrola danych podczas odświeżania środowiska testowego z danymi produkcyjnymi w celu usunięcia/zamaskowania poufnych danych osobowych (informacji osobistych) w celu zapewnienia zgodności ze standardami ochrony prywatności i zabezpieczeń.

### <a name="plan-security"></a>Planowanie zabezpieczeń

W ramach wdrażania nowej usługi często wymagane jest sprawdzenie zabezpieczeń. Jeśli Przegląd zabezpieczeń jest wymagany lub nie został jeszcze przeprowadzony, przejrzyj wiele oficjalnych [dokumentów usługi Azure AD,](https://www.microsoft.com/download/details.aspx?id=36391) które zawierają omówienie tożsamości jako usługi.

### <a name="plan-rollback"></a>Planowanie wycofywania

Jeśli implementacja aprowizacji użytkownika usługi Cloud prod nie działa zgodnie z potrzebami w środowisku produkcyjnym, poniższe kroki wycofywania mogą pomóc w powracaniu do poprzedniego znanego dobrego stanu:

1. Zapoznaj się z [raportem podsumowania aprowizacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#getting-provisioning-reports-from-the-azure-management-portal) i [dziennikami aprowizacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#provisioning-audit-logs) (zobacz [Zarządzanie obsługą administracyjną aplikacji w chmurze](#manage-your-configuration)), aby określić, jakie operacje zostały wykonane na odpowiednich użytkownikach i/lub w grupach.
2. Ostatni znany dobry stan użytkowników i/lub grup, których to dotyczy, można określić za pomocą dzienników inspekcji aprowizacji lub przeglądając systemy docelowe (Azure AD lub AD).
3. Współpracuj z właścicielem aplikacji, aby zaktualizować użytkowników i/lub grupy dotknięte bezpośrednio w aplikacji przy użyciu ostatnich znanych wartości stanu.

## <a name="deploy-the-cloud-hr-app"></a>Wdróż aplikację usługi Cloud kadr

Wybierz aplikację kadr w chmurze, która jest wyrównana do wymagań dotyczących rozwiązania.

Samouczek dotyczący usługi **Workday** [: Skonfiguruj usługę Workday dla automatycznej aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) , aby wykonać kroki wymagane do zaimportowania profilów procesów roboczych z produktu Workday do usługi AD i usługi Azure AD, z opcjonalnym zapisem zwrotnym adresu e-mail i nazwy użytkownika w dniach roboczych.

## <a name="manage-your-configuration"></a>Zarządzanie konfiguracją

Usługa Azure AD może zapewnić dodatkowe informacje dotyczące użycia i kondycji operacyjnej w organizacji za pomocą dzienników inspekcji i raportów.

### <a name="gain-insights-from-reports-and-logs"></a>Uzyskiwanie szczegółowych informacji z raportów i dzienników

Po pomyślnym [cyklu początkowym](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning)usługa Azure AD Provisioning będzie nadal uruchamiać aktualizacje przyrostowe z powrotem do tyłu, w odstępach czasu zdefiniowanych w samouczkach dotyczących poszczególnych aplikacji, dopóki nie wystąpi jedno z następujących zdarzeń:

- Usługa została zatrzymana ręcznie i nowy początkowy cykl wyzwalany przy użyciu [Azure Portal](https://portal.azure.com/) lub przy użyciu odpowiedniego polecenia [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) .
- Nowy cykl początkowy jest wyzwalany z powodu zmiany mapowań atrybutów lub filtrów określania zakresu.
- Proces aprowizacji jest kierowany do kwarantanny ze względu na wysoki współczynnik błędów i pozostaje w kwarantannie przez ponad cztery tygodnie, w których zostanie automatycznie wyłączone.

Aby przejrzeć te zdarzenia i wszystkie inne działania wykonywane przez usługę aprowizacji, [Dowiedz się, jak przejrzeć dzienniki i uzyskać raporty dotyczące działań aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Wszystkie działania wykonywane przez usługę aprowizacji są rejestrowane w **dziennikach inspekcji usługi Azure AD**. Dzienniki inspekcji usługi Azure AD można kierować do Azure Monitor dzienników w celu dalszej analizy. **Dzienniki Azure monitor (znane również jako log Analytics obszar roboczy)** umożliwiają wykonywanie zapytań dotyczących danych w celu znajdowania zdarzeń, analizowanie trendów i wykonywanie korelacji w różnych źródłach danych. Obejrzyj ten [film wideo](https://youtu.be/MP5IaCTwkQg) , aby poznać zalety korzystania z dzienników Azure monitor na potrzeby dzienników usługi Azure AD w praktycznych scenariuszach użytkownika.

[Widoki usługi log Analytics dla dzienników aktywności usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) można zainstalować, aby uzyskać dostęp do [wstępnie skompilowanych raportów](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) dotyczących zdarzeń aprowizacji w Twoim środowisku.

Aby uzyskać więcej informacji, zobacz [jak analizować dzienniki aktywności usługi Azure AD za pomocą dzienników Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>Zarządzanie danymi osobowymi

Agent aprowizacji Azure AD Connect zainstalowany w systemie Windows Server tworzy dzienniki w dzienniku zdarzeń systemu Windows, które mogą zawierać dane osobowe w zależności od aplikacji w chmurze do mapowań atrybutów usługi AD. Aby zachować zgodność z zobowiązaniami dotyczącymi prywatności użytkowników, można skonfigurować zaplanowane zadanie systemu Windows, aby wyczyścić dziennik zdarzeń i upewnić się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Usługa Azure AD Provisioning nie generuje raportów, nie przeprowadza analizy ani nie udostępnia szczegółowych informacji dłużej niż 30 dni. W związku z tym usługa Azure AD Provisioning nie przechowuje, nie przetwarza ani nie zachowuje żadnych danych dłużej niż 30 dni. 

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Skorzystaj z poniższych linków, aby rozwiązać wszelkie problemy, które mogą zostać włączone podczas aprowizacji:

- [Wystąpił problem podczas konfigurowania aprowizacji użytkowników w aplikacji z galerii usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Synchronizowanie atrybutu z Active Directory lokalnego z usługą Azure AD w celu aprowizacji aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [Inicjowanie obsługi administracyjnej użytkowników w aplikacji z galerii usługi Azure AD trwa kilka godzin](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Wystąpił problem podczas zapisywania poświadczeń administratora podczas konfigurowania aprowizacji użytkowników w aplikacji Galerii Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [Nie zainicjowano obsługi użytkowników w aplikacji z galerii usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [W aplikacji z galerii usługi Azure AD jest inicjowany niewłaściwy zestaw użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Konfigurowanie Podgląd zdarzeń systemu Windows na potrzeby rozwiązywania problemów z agentem](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Konfigurowanie dzienników inspekcji Azure Portal na potrzeby rozwiązywania problemów z usługą](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Omówienie dzienników dla operacji tworzenia konta użytkownika usługi AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [Omówienie dzienników dla operacji aktualizacji Menedżera](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Rozwiązywanie najczęściej występujących błędów](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Następne kroki

- [Pisanie wyrażeń do mapowania atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Omówienie interfejsu API synchronizacji usługi Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Azure AD Connect agenta aprowizacji: historia wersji](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
