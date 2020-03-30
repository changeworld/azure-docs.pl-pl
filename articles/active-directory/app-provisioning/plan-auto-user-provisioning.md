---
title: Planowanie wdrożenia automatycznego inicjowania obsługi administracyjnej usługi Azure Active Directory
description: Wskazówki dotyczące planowania i wykonywania automatycznego inicjowania obsługi administracyjnej użytkowników
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522414"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planowanie wdrożenia automatycznego aprowizowania użytkowników

Wiele organizacji polega na aplikacjach typu software as a service (SaaS), takich jak ServiceNow, Zscaler i Slack dla wydajności użytkowników końcowych. W przeszłości pracownicy IT polegali na ręcznych metodach inicjowania obsługi administracyjnej, takich jak przekazywanie plików CSV lub używanie skryptów niestandardowych do bezpiecznego zarządzania tożsamościami użytkowników w każdej aplikacji SaaS. Procesy te są podatne na błędy, niebezpieczne i trudne do zarządzania.

Automatyczne inicjowanie obsługi administracyjnej usługi Azure Active Directory (Azure AD) upraszcza ten proces, bezpiecznie automatyzując tworzenie, konserwację i usuwanie tożsamości użytkowników w aplikacjach SaaS na podstawie reguł biznesowych. Ta automatyzacja umożliwia efektywne skalowanie systemów zarządzania tożsamościami w środowiskach tylko w chmurze i w środowiskach hybrydowych, gdy rozszerzasz ich zależność od rozwiązań opartych na chmurze.

Zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory,](../app-provisioning/user-provisioning.md) aby lepiej zrozumieć te funkcje.

## <a name="learn"></a>Informacje

Inicjowanie obsługi administracyjnej użytkowników tworzy podstawę dla bieżącego zarządzania tożsamościami i zwiększa jakość procesów biznesowych, które opierają się na autorytatywnych danych tożsamości.

### <a name="key-benefits"></a>Najważniejsze korzyści

Najważniejsze zalety włączania automatycznego inicjowania obsługi administracyjnej użytkowników to:

* **Zwiększona produktywność**. Tożsamości użytkowników można zarządzać w aplikacjach SaaS za pomocą interfejsu zarządzania inicjowania obsługi administracyjnej jednego użytkownika. Ten interfejs ma jeden zestaw zasad inicjowania obsługi administracyjnej.

* **Zarządzanie ryzykiem**. Można zwiększyć bezpieczeństwo, automatyzując zmiany na podstawie stanu pracownika lub członkostwa w grupach, które definiują role i/lub dostęp.

* **Adres zgodności i zarządzania**. Usługa Azure AD obsługuje natywne dzienniki inspekcji dla każdego żądania inicjowania obsługi administracyjnej użytkownika. Żądania są wykonywane zarówno w systemach źródłowych, jak i docelowych. Dzięki temu można śledzić, kto ma dostęp do aplikacji z jednego ekranu.

* **Zmniejsz koszty**. Automatyczne inicjowanie obsługi administracyjnej użytkowników zmniejsza koszty, unikając nieefektywności i błędów ludzkich związanych z ręczną inicjowania obsługi administracyjnej. Zmniejsza to potrzebę niestandardowych rozwiązań inicjowania obsługi administracyjnej użytkowników, skryptów i dzienników inspekcji.

### <a name="licensing"></a>Licencjonowanie

Usługa Azure AD zapewnia samoobsługową integrację dowolnej aplikacji przy użyciu szablonów w menu galerii aplikacji. Aby uzyskać pełną listę wymagań dotyczących licencji, zobacz [stronę licencjonowania usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licencjonowanie aplikacji

Potrzebne będą odpowiednie licencje dla aplikacji, które mają być automatycznie aprowizowane. Omów z właścicielami aplikacji, czy użytkownicy przypisani do aplikacji mają odpowiednie licencje dla swoich ról aplikacji. Jeśli usługa Azure AD zarządza automatyczną inicjowania obsługi administracyjnej na podstawie ról, role przypisane w usłudze Azure AD musi wyrównać do licencji aplikacji. Nieprawidłowe licencje posiadane w aplikacji może prowadzić do błędów podczas inicjowania obsługi administracyjnej/aktualizacji użytkownika.

### <a name="terms"></a>Warunki

W tym artykule użyto następujących terminów:

* Operacje CRUD — akcje podejmowane na kontach użytkowników: Tworzenie, odczyt, aktualizacja, Usuwanie.

* Logowanie jednokrotne (Logowanie jednokrotne) — możliwość logowania użytkownika raz i dostępu do wszystkich aplikacji obsługujących jednokrotne logowanie. W kontekście inicjowania obsługi administracyjnej użytkownika, SSO jest wynikiem użytkowników posiadających jedno konto, aby uzyskać dostęp do wszystkich systemów, które używają automatycznego inicjowania obsługi administracyjnej użytkownika.

* System źródłowy — repozytorium użytkowników, od których można zauzyć usługi Azure AD. Usługa Azure AD jest systemem źródłowym dla większości wstępnie zintegrowanych łączników inicjowania obsługi administracyjnej. Istnieją jednak pewne wyjątki dla aplikacji w chmurze, takich jak SAP, Workday i AWS. Na przykład zobacz [Inicjowanie obsługi administracyjnej użytkownika z dnia roboczego do usługi AD](../saas-apps/workday-inbound-tutorial.md).

* System docelowy — repozytorium użytkowników, które azure ad przepisów do. System docelowy jest zazwyczaj aplikacją SaaS, taką jak ServiceNow, Zscaler i Slack. System docelowy może być również systemem lokalnym, takim jak AD.

* [System zarządzania tożsamościami między domenami (SCIM)](https://aka.ms/scimoverview) — otwarty standard, który umożliwia automatyzację inicjowania obsługi administracyjnej użytkowników. Scim komunikuje dane tożsamości użytkownika między dostawcami tożsamości, takimi jak Microsoft, a dostawcami usług, takimi jak Salesforce lub inne aplikacje SaaS, które wymagają informacji o tożsamości użytkownika.

### <a name="training-resources"></a>Zasoby szkoleniowe

| Resources| Łącze i opis |
| - | - |
| Seminaria internetowe na żądanie| [Zarządzanie aplikacjami dla przedsiębiorstw za pomocą usługi Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Dowiedz się, jak usługa Azure AD może pomóc w osiągnięciu usługi SSO w aplikacjach SaaS w przedsiębiorstwie i najlepszych praktyk w zakresie kontrolowania dostępu. |
| Filmy wideo| [Co to jest inicjowanie obsługi administracyjnej użytkowników w usłudze Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Jak wdrożyć inicjowanie obsługi administracyjnej użytkowników w usłudze Active Azure Directory?](https://youtu.be/pKzyts6kfrw) <br> [Integrowanie salesforce z usługą Azure AD: jak zautomatyzować inicjowanie obsługi administracyjnej użytkowników](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Kursy online| SkillUp Online: [Zarządzanie tożsamościami](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Dowiedz się, jak zintegrować usługę Azure AD z wieloma aplikacjami SaaS i zabezpieczyć dostęp użytkowników do tych aplikacji. |
| Książki| [Nowoczesne uwierzytelnianie za pomocą usługi Azure Active Directory for Web Applications (Reference) dla deweloperów 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Jest to autorytatywny, głęboki przewodnik po tworzeniu rozwiązań uwierzytelniania usługi Active Directory dla tych nowych środowisk. |
| Samouczki| Zobacz [listę samouczków dotyczących integrowania aplikacji SaaS z usługą Azure AD](../saas-apps/tutorial-list.md). |
| Najczęściej zadawane pytania| [Często zadawane pytania](../app-provisioning/user-provisioning.md) dotyczące automatycznego inicjowania obsługi administracyjnej użytkowników |

### <a name="solution-architectures"></a>Architektury rozwiązań

Usługa inicjowania obsługi administracyjnej usługi Azure AD aprowizacji użytkowników do aplikacji SaaS i innych systemów, łącząc się z punktami końcowymi interfejsu API zarządzania użytkownikami dostarczonych przez każdego dostawcę aplikacji. Te punkty końcowe interfejsu API zarządzania użytkownikami umożliwiają programowe tworzenie, aktualizowanie i usuwanie użytkowników w usłudze Azure AD.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatyczne inicjowanie obsługi administracyjnej dla przedsiębiorstw hybrydowych

W tym przykładzie użytkownicy i lub grupy są tworzone w bazie danych ZASOBÓW połączonych z katalogiem lokalnym. Usługa inicjowania obsługi administracyjnej usługi Azure AD zarządza automatyczną inicjowania obsługi administracyjnej dla aplikacji SaaS docelowej.

 ![inicjowanie obsługi administracyjnej przez użytkowników](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Opis przepływu pracy:**

1. Użytkownicy/grupy są tworzone w lokalnej aplikacji/systemie hr, takiej jak SAP. 

1. **Agent usługi Azure AD Connect** uruchamia zaplanowane synchronizacje tożsamości (użytkowników i grup) z lokalnej usługi AD na usługę Azure AD.

1. **Usługa inicjowania obsługi administracyjnej usługi Azure AD** rozpoczyna [początkowy cykl](../app-provisioning/user-provisioning.md) względem systemu źródłowego i systemu docelowego. 

1. **Usługa inicjowania obsługi administracyjnej usługi Azure AD** wysyła zapytania do systemu źródłowego dla wszystkich użytkowników i grup zmienionych od początkowego cyklu i wypycha zmiany [w cyklach przyrostowych](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatyczne inicjowanie obsługi administracyjnej dla przedsiębiorstw tylko w chmurze

W tym przykładzie tworzenie użytkownika odbywa się w usłudze Azure AD, a usługa inicjowania obsługi administracyjnej usługi Azure AD zarządza automatyczną inicjowania obsługi administracyjnej aplikacji docelowych (SaaS).

![Zdjęcie 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Opis przepływu pracy:**

1. Użytkownicy/grupy są tworzone w usłudze Azure AD.

1. **Usługa inicjowania obsługi administracyjnej usługi Azure AD** rozpoczyna [początkowy cykl](../app-provisioning/user-provisioning.md) względem systemu źródłowego i systemu docelowego. 

1. **Usługa inicjowania obsługi administracyjnej usługi Azure AD** wysyła zapytania do systemu źródłowego dla wszystkich użytkowników i grup zaktualizowanych od początkowego cyklu i wykonuje wszelkie [cykle przyrostowe.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatyczne inicjowanie obsługi administracyjnej dla aplikacji hr w chmurze 

W tym przykładzie użytkownicy i lub grupy są tworzone w aplikacji hr chmury, takich jak Workday i SuccessFactors. Usługa inicjowania obsługi administracyjnej usługi Azure AD i agent aprowizacji usługi Azure AD Connect aprowizacji aprowizacji danych użytkownika z dzierżawy aplikacji hr w chmurze do usługi AD. Po zaktualizowaniu kont w usłudze AD jest synchronizowana z usługą Azure AD Za pośrednictwem usługi Azure AD Connect, a adresy e-mail i atrybuty nazwy użytkownika mogą być zapisywane z powrotem do dzierżawy aplikacji usługi HR w chmurze.

![Zdjęcie 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Zespół HR** wykonuje transakcje w dzierżawie aplikacji hr w chmurze.
2.  **Usługa inicjowania obsługi administracyjnej usługi Azure AD** uruchamia zaplanowane cykle z dzierżawy aplikacji hr w chmurze i identyfikuje zmiany, które muszą być przetwarzane w celu synchronizacji z usługą AD.
3.  **Usługa inicjowania obsługi administracyjnej usługi** Azure AD wywołuje agenta inicjowania obsługi administracyjnej usługi Azure AD Connect z ładunkiem żądań zawierającym operacje tworzenia/aktualizacji/włączania/wyłączania konta usługi AD.
4.  **Agent inicjowania obsługi administracyjnej usługi Azure AD Connect** używa konta usługi do zarządzania danymi konta usługi AD.
5.  **Usługa Azure AD Connect** uruchamia synchronizację różnicową w celu ściągania aktualizacji w usłudze AD.
6.  Aktualizacje **usługi AD** są synchronizowane z usługą Azure AD. 
7.  **Usługa inicjowania obsługi administracyjnej usługi Azure** ad zapisuje dane z atrybutu wiadomości e-mail i nazwy użytkownika z usługi Azure AD do dzierżawy aplikacji hr w chmurze.

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożeniowego

Należy wziąć pod uwagę potrzeby organizacyjne, aby określić strategię wdrażania inicjowania obsługi administracyjnej użytkowników w twoim środowisku.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich interesariuszy

Gdy projekty technologiczne nie powiodą się, zazwyczaj z powodu niedopasowanych oczekiwań dotyczących wpływu, wyników i obowiązków. Aby uniknąć tych pułapek, upewnij się, że [angażujesz odpowiednie zainteresowane strony](https://aka.ms/deploymentplans) i że role interesariuszy w projekcie są dobrze zrozumiałe, dokumentując zainteresowane strony oraz ich wkład i odpowiedzialność w projekcie.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla powodzenia każdej nowej usługi. Proaktywnie komunikuj się z użytkownikami, jak zmieni się ich środowisko, kiedy się zmieni i jak uzyskać wsparcie, jeśli wystąpią problemy.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Zaleca się, aby początkowa konfiguracja automatycznego inicjowania obsługi administracyjnej użytkowników była w środowisku testowym z małym podzbiorem użytkowników przed skalowaniem go do wszystkich użytkowników w środowisku produkcyjnym. Zobacz [najlepsze rozwiązania dotyczące](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) uruchamiania programu pilotażowego.

#### <a name="best-practices-for-a-pilot"></a>Najlepsze praktyki dla pilota  

Pilot umożliwia testowanie z małą grupą przed wdrożeniem możliwości dla wszystkich. Upewnij się, że w ramach testowania każdy przypadek użycia w organizacji jest dokładnie testowany.

W pierwszej fali kieruj się na it, użyteczność i innych odpowiednich użytkowników, którzy mogą testować i przekazywać opinie. Skorzystaj z tej opinii, aby dalej rozwijać komunikację i instrukcje wysyłane do użytkowników oraz ukazywać informacje na temat rodzajów problemów, które mogą widzieć pracownicy pomocy technicznej.

Poszerzenie wdrożenia na większe grupy użytkowników, zwiększając zakres docelowych grup. Można to zrobić za pomocą [dynamicznego członkostwa w grupie](../users-groups-roles/groups-dynamic-membership.md)lub ręcznie dodając użytkowników do grup docelowych.

## <a name="plan-application-connections-and-administration"></a>Planowanie połączeń i administrowania aplikacjami

Użyj portalu usługi Azure AD do wyświetlania i zarządzania wszystkimi aplikacjami obsługującymi inicjowanie obsługi administracyjnej. Zobacz [Znajdowanie aplikacji w portalu](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Określanie typu złącza, którego chcesz użyć

Rzeczywiste kroki wymagane do włączenia i skonfigurowania automatycznego inicjowania obsługi administracyjnej różnią się w zależności od aplikacji. Jeśli aplikacja, którą chcesz automatycznie aprowizować, znajduje się w [galerii aplikacji Usługi Azure AD SaaS,](../saas-apps/tutorial-list.md)należy wybrać [samouczek integracji specyficzne dla aplikacji,](../saas-apps/tutorial-list.md) aby skonfigurować wstępnie zintegrowany łącznik inicjowania obsługi administracyjnej użytkownika.

Jeśli nie, wykonaj poniższe czynności:

1. [Utwórz żądanie](../develop/howto-app-gallery-listing.md) wstępnie zintegrowanego łącznika inicjowania obsługi administracyjnej użytkownika. Nasz zespół będzie współpracować z Tobą i deweloperem aplikacji, aby przywłkować aplikację do naszej platformy, jeśli obsługuje scim.

1. Użyj obsługi obsługi administracyjnej użytkownika [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) dla aplikacji. Jest to wymóg dla usługi Azure AD do inicjowania obsługi administracyjnej użytkowników do aplikacji bez wstępnie zintegrowanego łącznika inicjowania obsługi administracyjnej.

1. Jeśli aplikacja jest w stanie korzystać ze złącza BYOA SCIM, należy zapoznać się z [samouczkiem integracji BYOA SCIM,](../app-provisioning/use-scim-to-provision-users-and-groups.md) aby skonfigurować łącznik BYOA SCIM dla aplikacji.

Aby uzyskać więcej informacji, zobacz [Jakie aplikacje i systemy można używać z automatyczną inicjowania obsługi administracyjnej usługi Azure AD?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Zbieranie informacji w celu autoryzacji dostępu do aplikacji

Konfigurowanie automatycznego inicjowania obsługi administracyjnej użytkowników jest procesem dla aplikacji. Dla każdej aplikacji należy podać [poświadczenia administratora,](../app-provisioning/configure-automatic-user-provisioning-portal.md) aby połączyć się z punktem końcowym zarządzania użytkownikami systemu docelowego.

Na poniższej ilustracji przedstawiono jedną wersję wymaganych poświadczeń administratora:

![Ekran inicjowania obsługi administracyjnej w celu zarządzania ustawieniami inicjowania obsługi administracyjnej konta użytkownika](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Podczas gdy niektóre aplikacje wymagają nazwy użytkownika administratora i hasła, inne mogą wymagać tokenu nośnika.

## <a name="plan-user-and-group-provisioning"></a>Planowanie inicjowania obsługi administracyjnej użytkowników i grup

Jeśli włączysz inicjowanie obsługi administracyjnej użytkowników dla aplikacji dla przedsiębiorstw, [portal Azure](https://portal.azure.com/) kontroluje swoje wartości atrybutów za pomocą mapowania atrybutów.

### <a name="determine-operations-for-each-saas-app"></a>Określanie operacji dla każdej aplikacji SaaS

Każda aplikacja może mieć unikatowe atrybuty użytkownika lub grupy, które muszą być mapowane do atrybutów w usłudze Azure AD. Aplikacja może mieć tylko podzbiór operacji CRUD dostępne.

Dla każdego wniosku należy udokumentować następujące informacje:

* Crud inicjowania obsługi administracyjnej operacji, które mają być wykonywane na użytkownika i lub Group obiektów dla systemów docelowych. Na przykład każdy właściciel firmy aplikacji SaaS może nie chcieć wszystkich możliwych operacji.

* Atrybuty dostępne w systemie źródłowym

* Atrybuty dostępne w systemie docelowym

* Mapowanie atrybutów między systemami.

### <a name="choose-which-users-and-groups-to-provision"></a>Wybieranie użytkowników i grup do aprowienia

Przed wdrożeniem automatycznego inicjowania obsługi administracyjnej użytkowników, należy określić użytkowników i grup, które mają być aprowizowane do aplikacji.

* Użyj [filtrów zakresu,](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) aby zdefiniować reguły oparte na atrybutach, które określają, którzy użytkownicy są aprowizowani do aplikacji.

* Następnie użyj [przypisania użytkowników i grup,](../manage-apps/assign-user-or-group-access-portal.md) zgodnie z potrzebami, aby uzyskać dodatkowe filtrowanie.

### <a name="define-user-and-group-attribute-mapping"></a>Definiowanie mapowania atrybutów użytkowników i grup

Aby zaimplementować automatyczne inicjowanie obsługi administracyjnej użytkowników, należy zdefiniować atrybuty użytkownika i grupy, które są potrzebne dla aplikacji. Istnieje wstępnie skonfigurowany zestaw atrybutów i [mapowań atrybutów](../app-provisioning/configure-automatic-user-provisioning-portal.md) między obiektami użytkowników usługi Azure AD i obiektami użytkownika każdej aplikacji SaaS. Nie wszystkie aplikacje SaaS włączają atrybuty grupy.

Usługa Azure AD obsługuje przez bezpośrednie mapowanie atrybutów do atrybutów, dostarczanie stałych wartości lub [zapisywanie wyrażeń dla mapowań atrybutów.](../app-provisioning/functions-for-customizing-application-data.md) Ta elastyczność zapewnia doskonałą kontrolę nad tym, co zostanie wypełnione w atrybucie systemu docelowego. Za pomocą [interfejsu API programu Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md) i Eksploratora wykresów można wyeksportować mapowania atrybutów i schematu inicjowania obsługi administracyjnej użytkownika do pliku JSON i zaimportować go z powrotem do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowania atrybutów inicjowania obsługi administracyjnej dla aplikacji SaaS w usłudze Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Szczególne uwagi dotyczące inicjowania obsługi administracyjnej przez użytkowników

Należy wziąć pod uwagę następujące kwestie, aby zmniejszyć problemy po wdrożeniu:

* Upewnij się, że atrybuty używane do mapowania obiektów użytkownika/grupy między aplikacjami źródłowymi i docelowymi są odporne. Nie powinny one powodować użytkowników/grup, które mają być aprowizacji niepoprawnie, jeśli atrybuty zmiany (na przykład użytkownik przenosi się do innej części firmy).

* Aplikacje mogą mieć określone ograniczenia i/lub wymagania, które muszą być spełnione, aby inicjowanie administracyjne użytkownika działało poprawnie. Na przykład Slack obcina wartości dla niektórych atrybutów. Zapoznaj się z [samouczkami automatycznego inicjowania obsługi administracyjnej specyficznymi](../saas-apps/tutorial-list.md) dla każdej aplikacji.

* Potwierdź spójność schematu między systemami źródłowymi i docelowymi. Typowe problemy obejmują atrybuty, takie jak upn lub poczta nie pasujące. Na przykład upn w usłudze Azure AD ustawiony *john_smith@contoso.com* *jsmith@contoso.com*jako i w aplikacji, to . Aby uzyskać więcej informacji, zobacz [Odwołanie do schematu użytkownika i grupy](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Planowanie testowania i bezpieczeństwa

Na każdym etapie wdrażania upewnij się, że testujesz, że wyniki są zgodnie z oczekiwaniami i inspekcji cykli inicjowania obsługi administracyjnej.

### <a name="plan-testing"></a>Planowanie testowania

Po skonfigurowaniu automatycznego inicjowania obsługi administracyjnej dla aplikacji, należy uruchomić przypadków testowych, aby sprawdzić, czy to rozwiązanie spełnia wymagania organizacji.

| Scenariusze| Oczekiwane wyniki |
| - | - |
| Użytkownik jest dodawany do grupy przypisanej do systemu docelowego | Obiekt użytkownika jest aprowizowana w systemie docelowym. <br>Użytkownik może zalogować się do systemu docelowego i wykonać żądane akcje. |
| Użytkownik jest usuwany z grupy przypisanej do systemu docelowego | Obiekt użytkownika jest wyrejestrowany w systemie docelowym.<br>Użytkownik nie może zalogować się do systemu docelowego. |
| Informacje o użytkowniku są aktualizowane w usłudze Azure AD za pomocą dowolnej metody | Zaktualizowane atrybuty użytkownika są odzwierciedlane w systemie docelowym po cyklu przyrostowym |
| Użytkownik jest poza zakresem | Obiekt użytkownika jest wyłączony lub usunięty. <br>Uwaga: To zachowanie jest zastępowane w przypadku [inicjowania obsługi administracyjnej workday.](skip-out-of-scope-deletions.md) |

### <a name="plan-security"></a>Zaplanuj bezpieczeństwo

Często jest to wymagane przegląd zabezpieczeń w ramach wdrożenia. Jeśli potrzebujesz przeglądu zabezpieczeń, zobacz wiele [oficjalnych dokumentów](https://www.microsoft.com/download/details.aspx?id=36391) usługi Azure AD, który zawiera omówienie tożsamości jako usługi.

### <a name="plan-rollback"></a>Planowanie wycofywania

Jeśli implementacja automatycznego inicjowania obsługi administracyjnej użytkownika nie działa zgodnie z oczekiwaniami w środowisku produkcyjnym, poniższe kroki wycofywania mogą pomóc w wróceniu do poprzedniego znanego dobrego stanu:

1. Przejrzyj [raport podsumowujący inicjowanie obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md) i [dzienniki inicjowania obsługi administracyjnej,](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) aby ustalić, jakie niepoprawne operacje wystąpiły na użytkowników i/lub grupach, których dotyczy problem.

1. Użyj dzienników inspekcji inicjowania obsługi administracyjnej, aby określić ostatni znany dobry stan użytkowników i/lub grup, których to dotyczy. Przejrzyj również systemy źródłowe (Usługi Azure AD lub AD).

1. Praca z właścicielem aplikacji, aby zaktualizować użytkowników i/lub grup, których dotyczy bezpośrednio w aplikacji przy użyciu ostatnich znanych wartości dobrego stanu.

## <a name="deploy-automatic-user-provisioning-service"></a>Wdrażanie usługi automatycznego inicjowania obsługi administracyjnej użytkowników

Wybierz kroki, które są zgodne z wymaganiami rozwiązania.

### <a name="prepare-for-the-initial-cycle"></a>Przygotowanie do cyklu początkowego

Gdy usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchamiana po raz pierwszy, początkowy cykl względem systemu źródłowego i systemów docelowych tworzy migawkę wszystkich obiektów użytkownika dla każdego systemu docelowego.

Po włączeniu automatycznego inicjowania obsługi administracyjnej dla aplikacji, początkowy cykl może trwać od 20 minut do kilku godzin. Czas trwania zależy od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie inicjowania obsługi administracyjnej. Zobacz [Jak poprawić wydajność inicjowania obsługi administracyjnej](../app-provisioning/application-provisioning-when-will-provisioning-finish.md).

Usługa inicjowania obsługi administracyjnej przechowuje stan obu systemów po początkowym cyklu, poprawiając wydajność kolejnych cykli przyrostowych.

### <a name="configure-automatic-user-provisioning"></a>Konfigurowanie automatycznej aprowizacji użytkowników

Użyj [witryny Azure Portal](https://portal.azure.com/) do zarządzania automatyczną inicjowania obsługi administracyjnej konta użytkownika i usuwania obsługi administracyjnej dla aplikacji, które go obsługują. Wykonaj kroki opisane w [jak skonfigurować automatyczne inicjowanie obsługi administracyjnej aplikacji?](../app-provisioning/user-provisioning.md)

Usługę inicjowania obsługi administracyjnej użytkowników usługi Azure AD można również skonfigurować i zarządzać za pomocą [interfejsu API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Zarządzanie automatyczną inicjowania obsługi administracyjnej użytkowników

Po wdrożeniu należy zarządzać rozwiązaniem.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorowanie kondycji operacji inicjowania obsługi administracyjnej użytkownika

Po pomyślnym [cyklu początkowym](../app-provisioning/user-provisioning.md)usługa inicjowania obsługi administracyjnej usługi Azure AD będzie uruchamiać aktualizacje przyrostowe przez czas nieokreślony, w odstępach czasu specyficznych dla każdej aplikacji, dopóki nie wystąpi jedno z następujących zdarzeń:

* Usługa jest ręcznie zatrzymywany, a nowy cykl początkowy jest wyzwalany przy użyciu [portalu Azure](https://portal.azure.com/)portal lub przy użyciu odpowiedniego polecenia interfejsu API programu [Microsoft Graph.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* Nowy cykl początkowy jest wyzwalany przez zmianę mapowań atrybutów lub filtrów zakresu.

* Proces inicjowania obsługi administracyjnej przechodzi do kwarantanny z powodu wysokiego poziomu błędu i pozostaje w kwarantannie przez ponad cztery tygodnie, gdy zostanie automatycznie wyłączona.

Aby przejrzeć te zdarzenia i wszystkie inne działania wykonywane przez usługę inicjowania obsługi administracyjnej, zapoznaj się z [dziennikami inicjowania obsługi administracyjnej](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)usługi Azure AD.

Aby zrozumieć, jak długo trwa cykle inicjowania obsługi administracyjnej i monitorować postęp zadania inicjowania obsługi [administracyjnej, można sprawdzić stan inicjowania obsługi administracyjnej użytkownika](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Uzyskiwanie szczegółowych informacji z raportów

Usługa Azure AD może zapewnić [dodatkowe informacje na temat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) użycia obsługi administracyjnej użytkowników w organizacji i kondycji operacyjnej za pośrednictwem dzienników inspekcji i raportów.

Administratorzy powinni sprawdzić raport podsumowujący inicjowanie obsługi administracyjnej, aby monitorować kondycję operacyjną zadania inicjowania obsługi administracyjnej. Wszystkie działania wykonywane przez usługę inicjowania obsługi administracyjnej są rejestrowane w dziennikach inspekcji usługi Azure AD. Zobacz [Samouczek: Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

Zaleca się, aby przejąć na własność i zużywają te raporty na rytm, który spełnia wymagania organizacji. Usługa Azure AD zachowuje większość danych inspekcji przez 30 dni.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Zapoznaj się z poniższymi łączami, aby rozwiązać wszelkie problemy, które mogą pojawić się podczas inicjowania obsługi administracyjnej:

* [Problem z konfigurowaniem inicjowania obsługi administracyjnej przez użytkownika aplikacji usługi Azure AD Gallery](../app-provisioning/application-provisioning-config-problem.md)

* [Synchronizowanie atrybutu z lokalnej usługi Active Directory z usługą Azure AD w celu inicjowania obsługi administracyjnej aplikacji](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Inicjowanie obsługi administracyjnej aplikacji usługi Azure AD Gallery trwa wiele godzin lub więcej](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Problem z zapisywaniem poświadczeń administratora podczas konfigurowania inicjowania obsługi administracyjnej aplikacji usługi Azure Active Directory Gallery](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Żaden użytkownik nie jest aprowiany do aplikacji usługi Azure AD Gallery](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Niewłaściwy zestaw użytkowników jest aprowiany do aplikacji usługi Azure AD Gallery](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Pomocna dokumentacja

* [Pisanie wyrażeń dla mapowań atrybutów](../app-provisioning/functions-for-customizing-application-data.md)

* [Interfejs API synchronizacji usługi Azure AD — omówienie](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](skip-out-of-scope-deletions.md)

* [Agent inicjowania obsługi administracyjnej usługi Azure AD Connect: historia wydania wersji wersji](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Resources

* [Przekazywanie opinii dotyczących produktów](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Bądź na bieżąco z nowościami w usłudze Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Przepełnienie stosu usługi Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie automatycznego inicjowania obsługi administracyjnej użytkowników](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Eksportowanie lub importowanie konfiguracji inicjowania obsługi administracyjnej przy użyciu interfejsu API programu Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md)

* [Pisanie wyrażeń mapowań atrybutów w usłudze Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)
