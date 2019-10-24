---
title: Zaplanuj automatyczne wdrożenie aprowizacji użytkowników dla Azure Active Directory
description: Wskazówki dotyczące planowania i wykonywania automatycznej aprowizacji użytkowników
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab1caeb59cf7fc0a6baef5ba0001e734a75fccd2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72758236"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planowanie automatycznego wdrożenia aprowizacji użytkowników

Wiele organizacji korzysta z aplikacji typu oprogramowanie jako usługa (SaaS), takich jak usługi ServiceNow, rozwiązania Zscaler i zapasowy, na potrzeby produktywności użytkowników końcowych. W przeszłości pracownicy IT korzystali z ręcznych metod inicjowania obsługi, takich jak przekazywanie plików CSV lub używanie skryptów niestandardowych do bezpiecznego zarządzania tożsamościami użytkowników w każdej aplikacji SaaS. Procesy te są podatne na błędy, niezabezpieczone i trudne do zarządzania.

Usługa Azure Active Directory (Azure AD) automatyczne Inicjowanie obsługi użytkowników upraszcza ten proces, zapewniając bezpieczne automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS na podstawie reguł firmy. Ta Automatyzacja pozwala efektywnie skalować systemy zarządzania tożsamościami w środowiskach tylko w chmurze i hybrydowych w miarę rozszerzania ich zależności od rozwiązań opartych na chmurze.

Zapoznaj się [z automatyzacją aprowizacji użytkowników i cofaj obsługę administracyjną, aby SaaS aplikacje za pomocą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) w celu lepszego zrozumienia funkcjonalności.

## <a name="learn"></a>Nauka

Inicjowanie obsługi użytkowników tworzy podstawę do ciągłego zarządzania tożsamościami i zwiększa jakość procesów biznesowych, które opierają się na autorytatywnych danych tożsamości.

### <a name="key-benefits"></a>Najważniejsze korzyści

Najważniejsze zalety włączenia automatycznego aprowizacji użytkowników są następujące:

* **Zwiększona produktywność**. Tożsamościami użytkowników można zarządzać w aplikacjach SaaS za pomocą jednego interfejsu zarządzania aprowizacji użytkownika. Ten interfejs ma jeden zestaw zasad aprowizacji.

* **Zarządzanie ryzykiem**. Aby zwiększyć bezpieczeństwo, można zautomatyzować zmiany w zależności od stanu pracownika lub członkostwa w grupach, które definiują role i/lub dostęp.

* **Adresowanie zgodności i zarządzania**. Usługa Azure AD obsługuje natywne dzienniki inspekcji dla każdego żądania aprowizacji użytkownika. Żądania są wykonywane zarówno w systemie źródłowym, jak i docelowym. Dzięki temu można śledzić, kto ma dostęp do aplikacji z jednego ekranu.

* **Obniżyć koszty**. Automatyczne Inicjowanie obsługi użytkowników zmniejsza koszty, unikając nieefektywności i błędu ludzkiego związanego z obsługą ręczną. Zmniejsza to potrzebę niestandardowo rozwiniętych rozwiązań użytkowników, skryptów i dzienników inspekcji.

### <a name="licensing"></a>Licencjonowanie

Usługa Azure AD zapewnia samoobsługową integrację dowolnej aplikacji przy użyciu szablonów dostępnych w menu galerii aplikacji. Aby uzyskać pełną listę wymagań dotyczących licencji, zobacz [stronę licencjonowania usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licencjonowanie aplikacji

Wymagane są odpowiednie licencje dla aplikacji, które mają być automatycznie inicjowane. Należy omówić z właścicielami aplikacji, czy użytkownicy przypisani do aplikacji mają odpowiednie licencje dla ich ról aplikacji. Jeśli usługa Azure AD zarządza automatyczną obsługą administracyjną opartą na rolach, role przypisane w usłudze Azure AD muszą być wyrównane do licencji aplikacji. Nieprawidłowe licencje należące do aplikacji mogą prowadzić do błędów podczas aprowizacji/aktualizowania użytkownika.

### <a name="terms"></a>Warunki

W tym artykule są stosowane następujące warunki:

* Operacje CRUD — akcje podejmowane na kontach użytkowników: Tworzenie, odczytywanie, aktualizowanie i usuwanie.

* Logowanie jednokrotne (SSO) — umożliwia użytkownikowi Logowanie jednokrotne i dostęp do wszystkich aplikacji obsługujących Logowanie jednokrotne. W kontekście aprowizacji użytkowników Logowanie jednokrotne jest wynikiem użytkowników, którzy mają pojedyncze konto, aby uzyskać dostęp do wszystkich systemów korzystających z automatycznego aprowizacji użytkowników.

* System źródłowy — repozytorium użytkowników, z których pochodzą postanowienia usługi Azure AD. Usługa Azure AD to system źródłowy dla większości wstępnie zintegrowanych łączników aprowizacji. Istnieją jednak pewne wyjątki dotyczące aplikacji w chmurze, takich jak SAP, Workday i AWS. Na przykład zapoznaj [się z tematem Inicjowanie obsługi użytkowników z produktu Workday do usługi AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial).

* System docelowy — repozytorium użytkowników, do których mają prawa usługi Azure AD. System docelowy jest zazwyczaj aplikacją SaaS, taką jak usługi ServiceNow, rozwiązania Zscaler i zapasowy. System docelowy może być również systemem lokalnym, takim jak usługi AD.

* [System do zarządzania tożsamościami między domenami (standard scim)](http://www.simplecloud.info/)) — Otwarty standard, który umożliwia automatyzację aprowizacji użytkowników. Standard scim komunikuje dane tożsamości użytkowników między dostawcami tożsamości, takimi jak firma Microsoft, i dostawcami usług, takimi jak Salesforce lub innymi aplikacjami SaaS, które wymagają informacji o tożsamości użytkownika.

### <a name="training-resources"></a>Zasoby szkoleniowe

| Zasoby| Link i opis |
| - | - |
| Seminaria internetowe na żądanie| [Zarządzanie aplikacjami przedsiębiorstwa za pomocą usługi Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Dowiedz się, jak usługa Azure AD może pomóc Ci w osiągnięciu rejestracji jednokrotnej w aplikacjach SaaS w przedsiębiorstwie i najlepszych rozwiązaniach dotyczących kontroli dostępu. |
| Filmy| [Co to jest inicjowanie obsługi użytkowników w usłudze Active Directory systemu Azure?](https://youtu.be/_ZjARPpI6NI) <br> [Jak wdrożyć Inicjowanie obsługi użytkowników w usłudze Active Directory systemu Azure?](https://youtu.be/pKzyts6kfrw) <br> [Integrowanie usług Salesforce z usługą Azure AD: jak zautomatyzować Inicjowanie obsługi użytkowników](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Kursy online| SkillUp online: [Zarządzanie tożsamościami](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Dowiedz się, jak zintegrować usługę Azure AD z wieloma aplikacjami SaaS i zabezpieczyć dostęp użytkowników do tych aplikacji. |
| Książki| [Nowoczesne uwierzytelnianie za pomocą Azure Active Directory dla aplikacji sieci Web (Dokumentacja dla deweloperów) wersja 1](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Jest to autorytatywny, głęboki szczegółowe Przewodnik dotyczący tworzenia rozwiązań uwierzytelniania Active Directory dla tych nowych środowisk. |
| Samouczki| Zapoznaj się z [listą samouczków dotyczących integrowania aplikacji SaaS z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list). |
| Często zadawane pytania| [Często zadawane pytania](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) dotyczące automatycznej aprowizacji użytkowników |

### <a name="solution-architectures"></a>Architektury rozwiązań

Usługa Azure AD Provisioning udostępnia użytkownikom SaaS aplikacje i inne systemy, łącząc się z punktami końcowymi interfejsu API zarządzania użytkownikami udostępnionymi przez poszczególnych dostawców aplikacji. Te punkty końcowe interfejsu API zarządzania użytkownikami umożliwiają usłudze Azure AD Programistyczne tworzenie, aktualizowanie i usuwanie użytkowników.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatyczne Inicjowanie obsługi użytkowników dla firm hybrydowych

W tym przykładzie użytkownicy i grupy są tworzone w bazie danych kadr połączonej z katalogiem lokalnym. Usługa Azure AD Provisioning zarządza automatyczną obsługą użytkowników do docelowych aplikacji SaaS.

 ![Inicjowanie obsługi użytkowników](media/auto-user-provision-dp/hybridprovisioning.png)

**Opis przepływu pracy:**

1. Użytkownicy/grupy są tworzone w lokalnej aplikacji/systemie kadr, takiej jak SAP. 

1. Agent Azure AD Connect uruchamia zaplanowane synchronizacje tożsamości (użytkowników i grup) z lokalnej usługi AD do usługi Azure AD.

1. Usługa Azure AD Provisioning rozpoczyna [cykl początkowy](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) względem systemu źródłowego i systemu docelowego. 

1. Usługa Azure AD Provisioning sprawdza system źródłowy dla wszystkich użytkowników i grup, które uległy zmianie od pierwszego cyklu i wypycha zmiany w [cyklach przyrostowych](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatyczne Inicjowanie obsługi użytkowników dla przedsiębiorstw korzystających tylko z chmury

W tym przykładzie tworzenie użytkowników odbywa się w usłudze Azure AD, a usługa Azure AD Provisioning zarządza automatyczną obsługą użytkowników do aplikacji docelowych (SaaS):

![Obraz 2](media/auto-user-provision-dp/cloudprovisioning.png)

**Opis przepływu pracy:**

1. Użytkownicy/grupy są tworzone w usłudze Azure AD.

1. Usługa Azure AD Provisioning rozpoczyna [cykl początkowy](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) względem systemu źródłowego i systemu docelowego. 

1. Usługa Azure AD Provisioning jest kwerendą systemu źródłowego dla wszystkich użytkowników i grup zaktualizowanych od pierwszego cyklu i wykonuje wszystkie [cykle przyrostowe](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatyczne Inicjowanie obsługi administracyjnej użytkowników dla aplikacji usługi Cloud kadr 

W tym przykładzie użytkownicy i grupy są tworzone w aplikacji KADRowej w chmurze, takiej jak Workday.

![Obraz 2](media/auto-user-provision-dp/workdayprovisioning.png)

1. Konta utworzone w systemie kadr w chmurze
1. Dane są przesyłane w lokalnej usłudze AD za pomocą usługi Azure AD Provisioning i agenta aprowizacji.
1. Azure AD Connect synchronizuje dane z usługą Azure AD
1. Atrybut poczty e-mail i nazwy użytkownika można zapisać z powrotem do aplikacji w chmurze.

Aby uzyskać więcej informacji o architekturze i wdrażaniu rozwiązań, zobacz [Samouczek: Konfigurowanie produktu Workday do automatycznego aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial).

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożenia

Należy wziąć pod uwagę potrzebę organizacji, aby określić strategię wdrażania aprowizacji użytkowników w danym środowisku.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

W przypadku niepowodzenia projektów technologicznych zazwyczaj wynika to z niezgodności zaoczekiwań, wyników i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że interesują Cię odpowiednie osoby zainteresowane](https://aka.ms/deploymentplans) i że role udziałowców w projekcie są zrozumiałe przez udokumentowanie uczestników projektu oraz ich wejścia i wyjścia.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Proaktywnie Komunikuj się z użytkownikami, w jaki sposób ich środowisko zostanie zmienione, gdy ulegnie zmianie, i jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Zaleca się, aby początkowa konfiguracja automatycznego aprowizacji użytkowników była w środowisku testowym z małym podzbiorem użytkowników przed ich przeskalowaniem do wszystkich użytkowników w produkcji.

#### <a name="best-practices-for-a-pilot"></a>Najlepsze rozwiązania dla pilotażu  

Pilotaż umożliwia testowanie z małą grupą przed wdrożeniem możliwości dla wszystkich użytkowników. Upewnij się, że w ramach testowania każdy przypadek użycia w organizacji zostanie dokładnie przetestowany.

W pierwszej fazie, kierowanie do nich, użyteczność i innych odpowiednich użytkowników, którzy mogą testować i dostarczać Opinie. Skorzystaj z tej opinii, aby dodatkowo opracować komunikację i instrukcje wysyłane do użytkowników oraz uzyskać wgląd w typy problemów, które pracownicy pomocy technicznej mogą zobaczyć.

Poszerzanie wdrożenia do większych grup użytkowników przez zwiększenie zakresu grup przeznaczonych do użycia. Można to zrobić za pomocą [dynamicznej przynależności do grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)lub ręcznie dodając użytkowników do grup przeznaczonych do grupy.

## <a name="plan-application-connections-and-administration"></a>Planowanie połączeń i administrowania aplikacjami

Za pomocą portalu usługi Azure AD można wyświetlać i zarządzać wszystkimi aplikacjami, które obsługują obsługę administracyjną. Zobacz [Znajdowanie aplikacji w portalu](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal).

### <a name="determine-the-type-of-connector-to-use"></a>Określ typ łącznika do użycia

Rzeczywiste kroki wymagane do włączenia i skonfigurowania automatycznej aprowizacji różnią się w zależności od aplikacji. Jeśli aplikacja, którą chcesz automatycznie udostępnić, jest wymieniona w [galerii aplikacji usługi Azure AD SaaS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), należy wybrać [samouczek integracji dla aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) , aby skonfigurować jego wstępnie zintegrowany łącznik aprowizacji użytkowników.

Jeśli nie, wykonaj następujące czynności:

1. [Utwórz żądanie](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing) dla wstępnie zintegrowanego łącznika aprowizacji użytkowników. Nasz zespół będzie współpracować z ty i deweloperem aplikacji, aby dołączyć swoją aplikację do naszej platformy, jeśli obsługuje standard scim.

1. Użyj [BYOA Standard scim](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) ogólnej obsługi administracyjnej użytkowników dla aplikacji. Jest to wymaganie, aby usługa Azure AD mogła udostępnić użytkownikom aplikację bez wstępnie zintegrowanego łącznika aprowizacji.

1. Jeśli aplikacja jest w stanie korzystać z łącznika BYOA Standard scim, zapoznaj się z [samouczkiem integracji BYOA Standard scim](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) , aby skonfigurować łącznik BYOA Standard scim dla aplikacji.

Aby uzyskać więcej informacji, zobacz [aplikacje i systemy, których można używać z funkcją automatycznej aprowizacji użytkowników usługi Azure AD?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

### <a name="collect-information-to-authorize-application-access"></a>Zbieranie informacji w celu autoryzowania dostępu do aplikacji

Konfigurowanie automatycznego aprowizacji użytkowników jest procesem dla poszczególnych aplikacji. Dla każdej aplikacji należy podać [poświadczenia administratora](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) , aby połączyć się z punktem końcowym zarządzania użytkownikami systemu docelowego.

Na poniższej ilustracji przedstawiono jedną wersję wymaganych poświadczeń administratora:

![Ekran aprowizacji do zarządzania ustawieniami aprowizacji konta użytkownika](media/auto-user-provision-dp/userprovisioning-admincredentials.png)

Niektóre aplikacje wymagają nazwy użytkownika i hasła administratora, inne mogą wymagać tokenu okaziciela.

## <a name="plan-user-and-group-provisioning"></a>Planowanie aprowizacji użytkowników i grup

Po włączeniu aprowizacji użytkowników dla aplikacji dla przedsiębiorstw [Azure Portal](https://portal.azure.com/) kontroluje wartości atrybutów za pomocą mapowania atrybutów.

### <a name="determine-operations-for-each-saas-app"></a>Określanie operacji dla każdej aplikacji SaaS

Każda aplikacja może mieć unikatowe atrybuty użytkownika lub grupy, które muszą być mapowane na atrybuty w usłudze Azure AD. W aplikacji może być dostępnych tylko podzbiór CRUD operacji.

Dla każdej aplikacji należy udokumentować następujące informacje:

* CRUD operacje inicjowania obsługi na użytkownikach i grupach obiektów w systemach docelowych. Na przykład każdy właściciel firmy aplikacji SaaS może nie chcieć wszystkich możliwych operacji.

* Atrybuty dostępne w systemie źródłowym

* Atrybuty dostępne w systemie docelowym

* Mapowanie atrybutów między systemami.

### <a name="choose-which-users-and-groups-to-provision"></a>Wybieranie użytkowników i grup do aprowizacji

Przed zaimplementowaniem automatycznej aprowizacji użytkowników należy określić użytkowników i grupy, które mają być obsługiwane dla aplikacji.

* [Filtry zakresu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) umożliwiają definiowanie reguł opartych na atrybutach, które określają, którzy użytkownicy są obsługiwani do aplikacji.

* Następnie użyj [przypisań użytkowników i grup](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) w miarę potrzeb w celu dodatkowego filtrowania.

### <a name="define-user-and-group-attribute-mapping"></a>Definiowanie mapowania atrybutów użytkowników i grup

Aby zaimplementować automatyczne Inicjowanie obsługi użytkowników, należy zdefiniować atrybuty użytkownika i grupy, które są potrzebne dla aplikacji. Istnieje wstępnie skonfigurowany zestaw atrybutów i [mapowań atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) między obiektami użytkowników usługi Azure AD i obiektami użytkownika aplikacji SaaS. Nie wszystkie aplikacje SaaS włączają atrybuty grupy.

Usługa Azure AD obsługuje mapowanie między atrybutami i atrybutami, zapewniając wartości stałe lub [pisząc wyrażenia dla mapowań atrybutów](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Ta elastyczność zapewnia kontrolę nad tym, co zostanie wypełnione w atrybucie systemu wskazanego. Za pomocą [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) i Eksploratora grafów można wyeksportować mapowania atrybutów aprowizacji użytkowników i schemat do pliku JSON, a następnie zaimportować je z powrotem do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowania atrybutu aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

### <a name="special-considerations-for-user-provisioning"></a>Specjalne zagadnienia dotyczące aprowizacji użytkowników

Aby zmniejszyć liczbę problemów po wdrożeniu, należy wziąć pod uwagę następujące kwestie:

* Upewnij się, że atrybuty używane do mapowania obiektów użytkownika/grupy między aplikacjami źródłową i docelową są odporne na błędy. Nie powinny one spowodować, że użytkownicy/grupy mają być nieprawidłowo inicjowane, jeśli zmienią się atrybuty (na przykład użytkownik przejdzie do innej części firmy).

* Aplikacje mogą mieć określone ograniczenia i/lub wymagania, które należy spełnić w celu poprawnego działania aprowizacji użytkowników. Na przykład zapasowy obcina wartości dla niektórych atrybutów. Zapoznaj się z [automatycznymi samouczkami aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) specyficznymi dla poszczególnych aplikacji.

* Potwierdź spójność schematu między systemem źródłowym a docelowym. Typowe problemy obejmują atrybuty, takie jak nazwa UPN lub poczta, która nie jest zgodna. Na przykład nazwa UPN w usłudze Azure AD ustawiona jako *john_smith@contoso.com* i w aplikacji jest *jsmith@contoso.com* . Aby uzyskać więcej informacji, zobacz [Informacje o schemacie użytkownika i grupy](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups).

## <a name="plan-testing-and-security"></a>Planowanie testowania i zabezpieczeń

Na każdym etapie wdrożenia upewnij się, że testy są określone zgodnie z oczekiwaniami, i Przeprowadź inspekcję cykli aprowizacji.

### <a name="plan-testing"></a>Testowanie planu

Po skonfigurowaniu automatycznej aprowizacji użytkowników dla aplikacji należy uruchomić przypadki testowe, aby sprawdzić, czy to rozwiązanie spełnia wymagania organizacji.

| Scenariusze| Oczekiwane wyniki |
| - | - |
| Użytkownik jest dodawany do grupy przypisanej do systemu docelowego | Obiekt użytkownika jest zainicjowany w systemie docelowym. <br>Użytkownik może zalogować się do systemu docelowego i wykonać odpowiednie działania. |
| Użytkownik zostanie usunięty z grupy, która jest przypisana do systemu docelowego | Obiekt użytkownika jest cofany w systemie docelowym.<br>Użytkownik nie może zalogować się do systemu docelowego. |
| Informacje o użytkowniku zostały zaktualizowane w usłudze Azure AD za pomocą dowolnej metody | Zaktualizowane atrybuty użytkownika są odzwierciedlane w systemie docelowym po przyrostowym cyklu |
| Użytkownik jest poza zakresem | Obiekt użytkownika jest wyłączony lub usunięty. <br>Uwaga: to zachowanie jest zastępowane na potrzeby aprowizacji w programie [Workday](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions). |

### <a name="plan-security"></a>Planowanie zabezpieczeń

Jest to typowym sposobem, aby Przegląd zabezpieczeń był wymagany w ramach wdrożenia. Jeśli potrzebujesz przeglądu zabezpieczeń, zobacz wiele oficjalnych [dokumentów usługi Azure AD,](https://www.microsoft.com/download/details.aspx?id=36391) które zawierają omówienie tożsamości jako usługi.

### <a name="plan-rollback"></a>Planowanie wycofywania

Jeśli automatyczna implementacja inicjowania obsługi użytkowników nie będzie działać zgodnie z potrzebami w środowisku produkcyjnym, poniższe kroki wycofywania mogą pomóc w powracaniu do poprzedniego znanego dobrego stanu:

1. Przejrzyj [Raport z podsumowaniem aprowizacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) i [Inicjowanie obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) , aby określić, jakie nieprawidłowe operacje wystąpiły na odpowiednich użytkownikach i/lub w grupach.

1. Użyj dzienników inspekcji aprowizacji, aby określić ostatni znany dobry stan użytkowników i/lub grup, których dotyczy. Zapoznaj się również z systemami źródłowymi (Azure AD lub AD).

1. Aby zaktualizować użytkowników i/lub grupy dotknięte bezpośrednio w aplikacji przy użyciu ostatnich znanych wartości stanu, należy skontaktować się z właścicielem aplikacji.

## <a name="deploy-automatic-user-provisioning-service"></a>Wdróż automatyczną usługę aprowizacji użytkowników

Wybierz kroki, które należy dostosować do wymagań dotyczących rozwiązania.

### <a name="prepare-for-the-initial-cycle"></a>Przygotowanie do cyklu wstępnego

Gdy usługa Azure AD Provisioning jest uruchamiana po raz pierwszy, cykl początkowy względem systemu źródłowego i systemów docelowych tworzy migawkę wszystkich obiektów użytkownika dla każdego systemu docelowego.

Po włączeniu automatycznej aprowizacji aplikacji cykl początkowy może zająć od 20 minut do kilku godzin. Czas trwania zależy od rozmiaru katalogu usługi Azure AD oraz liczby użytkowników w zakresie aprowizacji. Zobacz [, jak zwiększyć wydajność aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish).

Usługa aprowizacji przechowuje stan obu systemów po początkowym cyklu, poprawiając wydajność kolejnych cykli przyrostowych.

### <a name="configure-automatic-user-provisioning"></a>Konfigurowanie automatycznej aprowizacji użytkowników

Użyj [Azure Portal](https://portal.azure.com/) , aby zarządzać automatyczną obsługą kont użytkowników i anulowaniem aprowizacji dla aplikacji, które go obsługują. Wykonaj kroki opisane w [Jak mogę skonfigurować automatyczne Inicjowanie obsługi aplikacji?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

Usługę aprowizacji użytkowników w usłudze Azure AD można także skonfigurować i zarządzać nią za pomocą [interfejsu API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Zarządzanie automatyczną obsługą użytkowników

Po wdrożeniu należy zarządzać rozwiązaniem.

### <a name="monitor-user-provisioning-operation-health"></a>Monitoruj kondycję operacji aprowizacji użytkowników

Po pomyślnym [cyklu początkowym](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)usługa Azure AD Provisioning będzie uruchamiać aktualizacje przyrostowe w odstępach czasu dla każdej aplikacji, dopóki nie wystąpi jedno z następujących zdarzeń:

* Usługa została zatrzymana ręcznie, a nowy cykl początkowy jest wyzwalany przy użyciu [Azure Portal](https://portal.azure.com/)lub przy użyciu odpowiedniego polecenia [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) .

* Nowy cykl początkowy jest wyzwalany przez zmianę mapowań atrybutów lub filtrów określania zakresu.

* Proces aprowizacji prowadzi do kwarantanny ze względu na wysoki współczynnik błędów i pozostaje w kwarantannie przez ponad cztery tygodnie, gdy zostanie on automatycznie wyłączony.

Aby przejrzeć te zdarzenia i wszystkie inne działania wykonywane przez usługę aprowizacji, zapoznaj się z [dziennikami aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/manage-apps/context/manage-apps-context)usługi Azure AD.

### <a name="gain-insights-from-reports"></a>Uzyskiwanie szczegółowych informacji z raportów

Usługa Azure AD może zapewnić [dodatkowe informacje](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) dotyczące użycia i kondycji operacyjnej w organizacji za pomocą dzienników inspekcji i raportów.

Administratorzy powinni sprawdzić Raport z podsumowaniem aprowizacji, aby monitorować kondycję operacyjną zadania aprowizacji. Wszystkie działania wykonywane przez usługę aprowizacji są rejestrowane w dziennikach inspekcji usługi Azure AD. Zobacz [Samouczek: Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

Zalecamy założenie, że własność i korzystanie z tych raportów w erze, który spełnia wymagania organizacji. Usługa Azure AD zachowuje większość danych inspekcji przez 30 dni.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Skorzystaj z poniższych linków, aby rozwiązać wszelkie problemy, które mogą zostać włączone podczas aprowizacji:

* [Wystąpił problem podczas konfigurowania aprowizacji użytkowników w aplikacji z galerii usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)

* [Synchronizowanie atrybutu z Active Directory lokalnego z usługą Azure AD w celu aprowizacji aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)

* [Inicjowanie obsługi administracyjnej użytkowników w aplikacji z galerii usługi Azure AD trwa kilka godzin](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)

* [Wystąpił problem podczas zapisywania poświadczeń administratora podczas konfigurowania aprowizacji użytkowników w aplikacji Galerii Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)

* [Nie zainicjowano obsługi użytkowników w aplikacji z galerii usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)

* [W aplikacji z galerii usługi Azure AD jest inicjowany niewłaściwy zestaw użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)

### <a name="helpful-documentation"></a>Przydatna dokumentacja

* [Pisanie wyrażeń do mapowania atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)

* [Omówienie interfejsu API synchronizacji usługi Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)

* [Azure AD Connect agenta aprowizacji: historia wersji](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)

#### <a name="resources"></a>Zasoby

* [Podaj opinię o produkcie](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Aktualne informacje o nowościach w usłudze Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Forum usługi Azure AD z przepełnieniem stosu](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie automatycznego aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)

* [Eksportowanie lub importowanie konfiguracji aprowizacji za pomocą interfejsu API Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration)

* [Pisanie wyrażeń dla mapowań atrybutów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
