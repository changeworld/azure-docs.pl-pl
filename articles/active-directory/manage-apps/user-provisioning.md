---
title: Automatyczne Inicjowanie obsługi użytkowników aplikacji SaaS w usłudze Azure AD | Microsoft Docs
description: Wprowadzenie do korzystania z usługi Azure AD w celu automatycznego udostępniania, cofania aprowizacji i ciągłego aktualizowania kont użytkowników w wielu aplikacjach SaaS innych firm.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144551"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji przy użyciu Azure Active Directory

W Azure Active Directory (Azure AD) termin **aprowizacji aplikacji** dotyczy automatycznego tworzenia tożsamości i ról użytkowników w aplikacjach w chmurze ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne Inicjowanie obsługi obejmuje konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról. Typowe scenariusze obejmują Inicjowanie obsługi użytkownika usługi Azure AD w aplikacjach takich jak [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [usługi ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)i inne.

![Diagram omówienia aprowizacji](media/user-provisioning/provisioning-overview.png)

Ta funkcja umożliwia:

- **Automatyzowanie aprowizacji**: automatyczne tworzenie nowych kont w odpowiednich systemach dla nowych osób po dołączeniu do zespołu lub organizacji.
- **Automatyzowanie anulowania aprowizacji:** Automatycznie Dezaktywuj konta w odpowiednich systemach, gdy ludzie opuszczają zespół lub organizację.
- **Synchronizuj dane między systemami:** Upewnij się, że tożsamości w Twoich aplikacjach i systemach są aktualne na podstawie zmian w katalogu lub w systemie zasobów ludzkich.
- **Grupy udostępniania:** Zainicjuj obsługę grup dla aplikacji, które je obsługują.
- **Zarządzanie dostępem:** Monitoruj i przeprowadzaj inspekcję, która została zainicjowana w aplikacjach.
- **Bezproblemowo Wdrażaj w scenariuszach brązowych pól:** Dopasowuje istniejące tożsamości między systemami i pozwala na łatwą integrację, nawet jeśli użytkownicy znajdują się już w systemie docelowym.
- **Użyj zaawansowanego dostosowywania:** Korzystaj z dostosowywalnych mapowań atrybutów, które definiują, jakie dane użytkownika powinny być przepływać z systemu źródłowego do systemu docelowego.
- **Otrzymywanie alertów dotyczących zdarzeń krytycznych:** Usługa aprowizacji udostępnia alerty dla zdarzeń krytycznych i umożliwia integrację Log Analytics, w której można zdefiniować niestandardowe alerty w celu określenia potrzeb firmy.

## <a name="benefits-of-automatic-provisioning"></a>Zalety automatycznej aprowizacji

Wraz ze wzrostem liczby aplikacji używanych w nowoczesnych organizacjach Administratorzy IT są zależne od zarządzania dostępem w odpowiedniej skali. Standardy, takie jak Security Assertions Markup Language (SAML) lub Open ID Connect (OIDC), umożliwiają administratorom szybkie konfigurowanie logowania jednokrotnego (SSO), ale dostęp wymaga również, aby użytkownicy mieli do nich możliwość aprowizacji. W przypadku wielu administratorów Inicjowanie obsługi administracyjnej oznacza ręczne tworzenie każdego konta użytkownika lub przekazywanie plików CSV w każdym tygodniu, ale te procesy są czasochłonne, kosztowne i podatne na błędy. Rozwiązania takie jak "JIT" (just-in-Time) zostały wdrożone w celu zautomatyzowania aprowizacji, ale przedsiębiorstwa również wymagają rozwiązania, aby anulować obsługę administracyjną użytkowników, którzy opuszczają organizację lub nie potrzebują już dostępu do niektórych aplikacji na podstawie zmiany roli.

Niektóre typowe motywacje dotyczące korzystania z automatycznej aprowizacji obejmują:

- Maksymalizacja wydajności i dokładności procesów aprowizacji.
- Oszczędności związane z hostingiem i utrzymywaniem niestandardowo opracowanych rozwiązań i skryptów aprowizacji.
- Zabezpieczanie organizacji przez natychmiastowe usuwanie tożsamości użytkowników z aplikacji Key SaaS, gdy opuszczają one organizację.
- Łatwo Importuj dużą liczbę użytkowników do określonej aplikacji lub systemu SaaS.
- Posiadanie jednego zestawu zasad, aby określić, kto jest zainicjowany i kto może logować się do aplikacji.

Inicjowanie obsługi użytkowników w usłudze Azure AD może pomóc rozwiązać te wyzwania. Aby dowiedzieć się więcej o tym, jak klienci korzystają z aprowizacji użytkowników usługi Azure AD, możesz przeczytać [analizę przypadku ASOs](https://aka.ms/asoscasestudy). Poniższe wideo zawiera omówienie aprowizacji użytkowników w usłudze Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jakie aplikacje i systemy mogą być używane przez automatyczne Inicjowanie obsługi użytkowników w usłudze Azure AD?

Funkcje usługi Azure AD są wstępnie zintegrowane z obsługą wielu popularnych aplikacji SaaS i systemów kadr oraz ogólnego wsparcia dla aplikacji, które implementują określone części [standardu standard scim 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Wstępnie zintegrowane aplikacje (aplikacje w galerii SaaS)** . Możesz znaleźć wszystkie aplikacje, dla których usługa Azure AD obsługuje wstępnie zintegrowany łącznik aprowizacji na [liście samouczków aplikacji na potrzeby aprowizacji użytkowników](../saas-apps/tutorial-list.md). Wstępnie zintegrowane aplikacje wymienione w galerii zwykle używają interfejsów API zarządzania użytkownikami opartymi na Standard scim 2,0 na potrzeby aprowizacji. 

   ![Logo usługi Salesforce](media/user-provisioning/gallery-app-logos.png)

   Jeśli chcesz zażądać nowej aplikacji do aprowizacji, możesz [poprosić o integrację aplikacji z naszą galerią aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing). W przypadku żądania aprowizacji użytkowników wymagamy, aby aplikacja miała punkt końcowy zgodny z standard scim. Zażądaj, aby Dostawca aplikacji był zgodny ze standardem Standard scim, dzięki czemu możemy szybko dodać aplikację do naszej platformy.

* **Aplikacje obsługujące standard scim 2,0**. Aby uzyskać informacje na temat ogólnego łączenia aplikacji, które implementują interfejsy API zarządzania użytkownikami opartymi na systemie Standard scim 2,0, zobacz [Używanie Standard scim do automatycznego aprowizacji użytkowników i grup z Azure Active Directory do aplikacji](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>Co to jest standard scim?

Aby ułatwić automatyzację aprowizacji i anulowania obsługi, aplikacje uwidaczniają zastrzeżone interfejsy API użytkowników i grup. Jednak każda osoba, która podjęła próbę zarządzania użytkownikami w więcej niż jednej aplikacji, powiedzie, że każda aplikacja próbuje wykonać te same proste akcje, takie jak tworzenie lub aktualizowanie użytkowników, Dodawanie użytkowników do grup lub cofanie aprowizacji użytkowników. Jednak wszystkie te proste akcje są implementowane tylko nieco inaczej, przy użyciu różnych ścieżek punktów końcowych, różnych metod, aby określić informacje o użytkownikach i innych schematów do reprezentowania poszczególnych elementów informacji.

Aby rozwiązać te problemy, Specyfikacja Standard scim zapewnia wspólny schemat użytkownika, aby ułatwić użytkownikom przechodzenie do aplikacji, a także z nich. Standard scim staje się de facto standardem do aprowizacji i, w połączeniu z standardami Federacji, takimi jak SAML lub OpenID Connect Connect, zapewnia administratorom kompleksowe rozwiązanie do zarządzania dostępem oparte na standardach.

Aby uzyskać szczegółowe wskazówki dotyczące korzystania z programu Standard scim do automatyzowania aprowizacji i cofania obsługi administracyjnej użytkowników i grup w aplikacji, zobacz [Standard scim Inicjowanie obsługi administracyjnej użytkowników przy użyciu Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="how-does-automatic-provisioning-work"></a>Jak działa Automatyczna obsługa administracyjna?

**Usługa Azure AD Provisioning** udostępnia użytkownikom SaaS aplikacje i inne systemy, łącząc się z punktami końcowymi interfejsu API zarządzania użytkownikami udostępnionymi przez poszczególnych dostawców aplikacji. Te punkty końcowe interfejsu API zarządzania użytkownikami umożliwiają usłudze Azure AD Programistyczne tworzenie, aktualizowanie i usuwanie użytkowników. W przypadku wybranych aplikacji usługa aprowizacji może również tworzyć, aktualizować i usuwać dodatkowe obiekty powiązane z tożsamościami, takie jak grupy i role.

![usługi Azure AD Provisioning](./media/user-provisioning/provisioning0.PNG)
*rysunek 1: usługa Azure AD Provisioning*

![wychodzący przepływ pracy aprowizacji użytkowników](./media/user-provisioning/provisioning1.PNG)
*rysunek 2: "wychodzące" przepływ pracy aprowizacji użytkowników z usługi Azure AD do popularnych aplikacji SaaS*

![przepływ pracy inicjowania obsługi użytkowników przychodzących](./media/user-provisioning/provisioning2.PNG)
*rysunek 3: "przychodzący" przepływ pracy aprowizacji użytkowników z popularnych aplikacji do zarządzania stolicą Kadr (HCM) do Azure Active Directory i systemu Windows Server Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak mogę skonfigurować automatyczne Inicjowanie obsługi administracyjnej aplikacji?

W przypadku wstępnie zintegrowanych aplikacji wymienionych w galerii wskazówki krok po kroku są dostępne w celu skonfigurowania automatycznej aprowizacji. Zapoznaj się z [listą samouczków dotyczących zintegrowanych aplikacji galerii](https://docs.microsoft.com/azure/active-directory/saas-apps/). Poniższy film wideo pokazuje, jak skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w usłudze SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

W przypadku innych aplikacji, które obsługują standard scim 2,0, wykonaj kroki opisane w artykule [Standard scim aprowizacji użytkowników w Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="what-happens-during-provisioning"></a>Co się stanie w trakcie aprowizacji?

Gdy usługa Azure AD jest systemem źródłowym, do monitorowania użytkowników i grup za pomocą [funkcji tworzenia zapytań różnicowych usługi Azure ad interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) . Usługa aprowizacji uruchamia cykl początkowy względem systemu źródłowego i systemu docelowego, a następnie okresowe cykle przyrostowe.

### <a name="initial-cycle"></a>Cykl początkowy

Po uruchomieniu usługi aprowizacji pierwsza uruchomiona synchronizacja będzie:

1. Wykonaj zapytania dla wszystkich użytkowników i grup z systemu źródłowego, pobierając wszystkie atrybuty zdefiniowane w [mapowaniu atrybutów](customize-application-attributes.md).
1. Filtrowanie zwracanych użytkowników i grup przy użyciu dowolnych skonfigurowanych [przypisań](assign-user-or-group-access-portal.md) lub [filtrów określania zakresów opartych na atrybutach](define-conditional-rules-for-provisioning-user-accounts.md).
1. Gdy użytkownik jest przypisany lub w zakresie aprowizacji, usługa wysyła zapytanie do systemu docelowego dla pasującego użytkownika przy użyciu określonych [pasujących atrybutów](customize-application-attributes.md#understanding-attribute-mapping-properties). Przykład: Jeśli nazwa userPrincipal w systemie źródłowym ma pasujący atrybut i jest mapowana na nazwę użytkownika w systemie docelowym, usługa aprowizacji wysyła zapytanie do systemu docelowego o nazwy użytkowników, które pasują do wartości nazw userPrincipal w systemie źródłowym.
1. Jeśli w systemie docelowym nie znaleziono pasującego użytkownika, jest on tworzony przy użyciu atrybutów zwróconych z systemu źródłowego. Po utworzeniu konta użytkownika usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika, który jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.
1. Jeśli zostanie znaleziony pasujący użytkownik, jest on aktualizowany przy użyciu atrybutów dostarczonych przez system źródłowy. Po dopasowaniu konta użytkownika usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika, który jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.
1. Jeśli mapowania atrybutu zawierają atrybuty "Reference", usługa wykonuje dodatkowe aktualizacje w systemie docelowym w celu utworzenia i połączenia obiektów, do których się odwołuje. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony z innym użytkownikiem utworzonym w systemie docelowym.
1. Utrwalanie znaku wodnego na końcu początkowego cyklu, który zapewnia punkt początkowy dla późniejszych cykli przyrostowych.

Niektóre aplikacje, takie jak usługi ServiceNow, G Suite i Box, obsługują nie tylko użytkowników aprowizacji, ale również aprowizacji grup i ich członków. W takich przypadkach, jeśli w [mapowaniu](customize-application-attributes.md)włączono obsługę grupy, usługa aprowizacji zsynchronizuje użytkowników i grupy, a następnie zsynchronizuje członkostwo w grupie.

### <a name="incremental-cycles"></a>Cykle przyrostowe

Po początkowym cyklu wszystkie pozostałe cykle będą:

1. Zbadaj system źródłowy dla wszystkich użytkowników i grup, które zostały zaktualizowane od momentu zapisania ostatniego znaku wodnego.
1. Filtrowanie zwracanych użytkowników i grup przy użyciu dowolnych skonfigurowanych [przypisań](assign-user-or-group-access-portal.md) lub [filtrów określania zakresów opartych na atrybutach](define-conditional-rules-for-provisioning-user-accounts.md).
1. Gdy użytkownik jest przypisany lub w zakresie aprowizacji, usługa wysyła zapytanie do systemu docelowego dla pasującego użytkownika przy użyciu określonych [pasujących atrybutów](customize-application-attributes.md#understanding-attribute-mapping-properties).
1. Jeśli w systemie docelowym nie znaleziono pasującego użytkownika, jest on tworzony przy użyciu atrybutów zwróconych z systemu źródłowego. Po utworzeniu konta użytkownika usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika, który jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.
1. Jeśli zostanie znaleziony pasujący użytkownik, jest on aktualizowany przy użyciu atrybutów dostarczonych przez system źródłowy. Jeśli jest to nowo przypisane konto, które jest dopasowane, usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika, który jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.
1. Jeśli mapowania atrybutu zawierają atrybuty "Reference", usługa wykonuje dodatkowe aktualizacje w systemie docelowym w celu utworzenia i połączenia obiektów, do których się odwołuje. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony z innym użytkownikiem utworzonym w systemie docelowym.
1. Jeśli użytkownik, który był wcześniej objęty zakresem aprowizacji, zostanie usunięty z zakresu (w tym nieprzypisane), usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.
1. Jeśli użytkownik, który był wcześniej objęty zakresem aprowizacji, jest wyłączony lub nietrwały usunięty w systemie źródłowym, usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.
1. Jeśli użytkownik, który był wcześniej objęty zakresem aprowizacji, został trwale usunięty w systemie źródłowym, usługa usunie użytkownika w systemie docelowym. W usłudze Azure AD użytkownicy są trwale usunięci przez 30 dni po ich usunięciu nietrwałego.
1. Utrwalaj nowy znak wodny na końcu przyrostowego cyklu, który zapewnia punkt początkowy dla późniejszych cykli przyrostowych.

> [!NOTE]
> Opcjonalnie można wyłączyć operacje **tworzenia**, **aktualizowania**lub **usuwania** przy użyciu pól wyboru **Akcje obiektu docelowego** w sekcji [mapowania](customize-application-attributes.md) . Logika, w której należy wyłączyć użytkownika podczas aktualizacji, jest również kontrolowana za pośrednictwem mapowania atrybutów z pola, takiego jak "accountEnabled".

Usługa aprowizacji nadal uruchamia cykliczne cykle przyrostowe, w odstępach czasu zdefiniowanych w [samouczku dla każdej aplikacji](../saas-apps/tutorial-list.md), dopóki nie wystąpi jedno z następujących zdarzeń:

- Usługa została zatrzymana ręcznie przy użyciu Azure Portal lub przy użyciu odpowiedniego polecenia interfejs API programu Graph 
- Nowy cykl początkowy jest wyzwalany przy użyciu opcji **Wyczyść stan i uruchom ponownie** w Azure Portal lub przy użyciu odpowiedniego polecenia interfejs API programu Graph. Ta akcja Czyści wszystkie przechowywane znaki wodne i powoduje ponowną ocenę wszystkich obiektów źródłowych.
- Nowy początkowy cykl jest wyzwalany ze względu na zmianę mapowań atrybutów lub filtrów określania zakresu. Ta akcja Czyści również wszystkie przechowywane znaki wodne i powoduje ponowne obliczenie wszystkich obiektów źródłowych.
- Proces aprowizacji jest kierowany do kwarantanny (patrz poniżej) z powodu wysokiego współczynnika błędów i pozostaje w kwarantannie przez ponad cztery tygodnie. W tym zdarzeniu usługa zostanie automatycznie wyłączona.

### <a name="errors-and-retries"></a>Błędy i ponowne próby

Jeśli nie można dodać, zaktualizować lub usunąć pojedynczego użytkownika w systemie docelowym z powodu błędu w systemie docelowym, operacja zostanie ponowiona w następnym cyklu synchronizacji. Jeśli użytkownik będzie nadal kończyć się niepowodzeniem, ponowne próby rozpoczną się z ograniczoną częstotliwością, stopniowo skalowanie z powrotem do zaledwie jednej próby dziennie. Aby rozwiązać ten problem, Administratorzy muszą sprawdzić [dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) , aby określić główną przyczynę i podjąć odpowiednie działania. Typowe błędy mogą obejmować:

- Użytkownicy nie mający atrybutu wypełnionego w systemie źródłowym, który jest wymagany w systemie docelowym
- Użytkownicy mający wartość atrybutu w systemie źródłowym, dla którego istnieje ograniczenie UNIQUE w systemie docelowym, a ta sama wartość jest obecna w innym rekordzie użytkownika.

Te błędy można rozwiązać przez dostosowanie wartości atrybutów dla danego użytkownika w systemie źródłowym lub przez dostosowanie mapowań atrybutów, aby nie powodowały konfliktów.

### <a name="quarantine"></a>Magazynu

Jeśli większość lub wszystkie wywołania związane z systemem docelowym są spójne niepowodzeniem z powodu błędu (na przykład nieprawidłowe poświadczenia administratora), zadanie aprowizacji przejdzie do stanu "Kwarantanna". Ten stan jest wskazany w raporcie dotyczącym [podsumowania aprowizacji](check-status-user-account-provisioning.md) i za pośrednictwem poczty e-mail, jeśli powiadomienia e-mail zostały skonfigurowane w Azure Portal.

W przypadku kwarantanny Częstotliwość cykli przyrostowych jest stopniowo zmniejszana do raz dziennie.

Zadanie aprowizacji zostanie usunięte z kwarantanny po naprawieniu wszystkich błędów powodujących problemy, gdy zostanie uruchomiony następny cykl synchronizacji. Jeśli zadanie aprowizacji pozostaje w kwarantannie przez ponad cztery tygodnie, zadanie aprowizacji jest wyłączone. [Tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)znajdziesz więcej informacji na temat stanu kwarantanny.

## <a name="how-long-will-it-take-to-provision-users"></a>Jak długo trwa inicjowanie obsługi administracyjnej użytkowników?

Wydajność zależy od tego, czy zadanie aprowizacji ma uruchomiony początkowy cykl aprowizacji czy cykl przyrostowy. Aby uzyskać szczegółowe informacje o tym, jak długo trwa inicjowanie obsługi i jak monitorować stan usługi aprowizacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Jak mogę sprawdzić, czy użytkownicy są prawidłowo administracyjni?

Wszystkie operacje wykonywane przez usługę aprowizacji użytkowników są rejestrowane w dziennikach aprowizacji usługi Azure AD [(wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Obejmuje to wszystkie operacje odczytu i zapisu dokonane w systemach źródłowych i docelowych oraz dane użytkownika, które zostały odczytane lub zapisane podczas każdej operacji.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji w Azure Portal, zobacz [Przewodnik po raportowaniu obsługi administracyjnej](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Jak mogę rozwiązywania problemów z obsługą administracyjną użytkowników?

Aby zapoznać się ze wskazówkami dotyczącymi sposobu rozwiązywania problemów dotyczących automatycznego aprowizacji użytkowników, zobacz [problemy dotyczące konfigurowania i aprowizacji użytkowników w aplikacji](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Jakie są najlepsze rozwiązania w zakresie wdrażania automatycznej aprowizacji użytkowników?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Przykładowy plan wdrożenia krok po kroku dotyczący wychodzącej aprowizacji użytkowników do aplikacji można znaleźć w [Podręczniku wdrażania tożsamości dla aprowizacji użytkowników](https://aka.ms/deploymentplans/userprovisioning).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Czy automatyczne Inicjowanie obsługi użytkowników w aplikacjach SaaS współpracuje z użytkownikami B2B w usłudze Azure AD?

Tak, można używać usługi aprowizacji użytkowników w usłudze Azure AD do udostępniania użytkownikom B2B (lub gościa) w usłudze Azure AD w celu SaaS aplikacji.

Jednak aby użytkownicy B2B mogli logować się do aplikacji SaaS przy użyciu usługi Azure AD, aplikacja SaaS musi mieć skonfigurowaną funkcję logowania jednokrotnego opartego na protokole SAML. Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji SaaS do obsługi logowań użytkowników B2B, zobacz [Konfigurowanie aplikacji SaaS na potrzeby współpracy B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Czy automatyczne Inicjowanie obsługi użytkowników w aplikacjach SaaS współpracuje z grupami dynamicznymi w usłudze Azure AD?

Tak. W przypadku skonfigurowania "Synchronizuj tylko przypisanych użytkowników i grup" usługa aprowizacji użytkowników w usłudze Azure AD może inicjować lub cofać obsługę administracyjną użytkowników w aplikacji SaaS na podstawie tego, czy są członkami [grupy dynamicznej](../users-groups-roles/groups-create-rule.md). Grupy dynamiczne działają również z opcją "Synchronizuj wszystkich użytkowników i grupy".

Jednak użycie grup dynamicznych może mieć wpływ na ogólną wydajność inicjowania obsługi administracyjnej użytkowników z usługi Azure AD do aplikacji SaaS. W przypadku korzystania z grup dynamicznych należy zachować te zastrzeżenia i zalecenia:

- Jak Fast użytkownicy w grupie dynamicznej są obsługiwani lub cofają obsługę administracyjną w aplikacji SaaS, zależy od tego, jak szybko Grupa dynamiczna może oszacować zmiany członkostwa. Aby uzyskać informacje na temat sprawdzania stanu przetwarzania grupy dynamicznej, zobacz [Sprawdzanie stanu przetwarzania dla reguły członkostwa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- W przypadku korzystania z grup dynamicznych należy uważnie rozważyć zainicjowanie obsługi administracyjnej użytkowników i odinicjowanie obsługi administracyjnej, ponieważ utrata członkostwa w zdarzeniu powodującym anulowanie aprowizacji.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Czy automatyczne Inicjowanie obsługi użytkowników w aplikacjach SaaS współpracuje z zagnieżdżonymi grupami w usłudze Azure AD?

Nie. W przypadku skonfigurowania "Synchronizuj tylko przypisanych użytkowników i grup" usługa aprowizacji użytkowników w usłudze Azure AD nie może odczytywać ani udostępniać użytkowników znajdujących się w grupach zagnieżdżonych. Tylko użytkownicy mogą odczytywać i inicjować dostęp do użytkowników, którzy są bezpośrednimi członkami jawnie przypisanej grupy.

Jest to ograniczenie "przypisań opartych na grupach do aplikacji", co również ma wpływ na logowanie jednokrotne i zostało opisane w temacie [używanie grupy do zarządzania dostępem do aplikacji SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

W celu obejścia tego problemu należy jawnie przypisać (lub inaczej określić [zakres](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) grup, które zawierają użytkowników, którzy muszą być obsługiwani.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Czy inicjowanie obsługi administracyjnej między usługą Azure AD a aplikacją docelową odbywa się przy użyciu szyfrowanego kanału?

Tak. Używamy szyfrowania protokołu HTTPS SSL dla serwera docelowego.

## <a name="related-articles"></a>Pokrewne artykuły

- [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
- [Dostosowywanie mapowań atrybutów na potrzeby aprowizacji użytkowników](customize-application-attributes.md)
- [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
- [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
- [Korzystanie z standard scim w celu włączenia automatycznej aprowizacji użytkowników i grup z usługi Azure AD do aplikacji](use-scim-to-provision-users-and-groups.md)
- [Omówienie interfejsu API synchronizacji usługi Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
