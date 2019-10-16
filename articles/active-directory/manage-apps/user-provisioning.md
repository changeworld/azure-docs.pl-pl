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
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ff6d9e33e15aa04adfa03705172166492f87e30
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330025"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS przy użyciu Azure Active Directory

Azure Active Directory (Azure AD) umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), takich jak Dropbox, Salesforce, usługi ServiceNow i inne. Jest to znane jako zautomatyzowane Inicjowanie obsługi użytkowników dla aplikacji SaaS.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

Ta funkcja umożliwia:

- Automatycznie twórz nowe konta w odpowiednich systemach dla nowych osób po dołączeniu do zespołu lub organizacji.
- Automatycznie Dezaktywuj konta w odpowiednich systemach, gdy ludzie opuszczają zespół lub organizację.
- Upewnij się, że tożsamości w aplikacjach i systemach są aktualne na podstawie zmian w katalogu lub w systemie kadr.
- Udostępnianie obiektów niebędących użytkownikami, takich jak grupy, do aplikacji, które je obsługują.

Automatyczne Inicjowanie obsługi użytkowników obejmuje również następujące funkcje:

- Możliwość dopasowania istniejących tożsamości między systemami źródłowymi i docelowymi.
- Dostosowywalne mapowania atrybutów, które definiują, jakie dane użytkownika powinny być przepływać z systemu źródłowego do systemu docelowego.
- Opcjonalne alerty e-mail dotyczące błędów aprowizacji.
- Dzienniki raportowania i aktywności ułatwiające monitorowanie i rozwiązywanie problemów.

## <a name="why-use-automated-provisioning"></a>Dlaczego warto używać automatycznej aprowizacji?

Niektóre typowe motywacje dotyczące korzystania z tej funkcji obejmują:

- Uniknięcie kosztów, nieefektywności i błędu ludzkiego związanego z ręcznymi procesami aprowizacji.
- Uniknięcie kosztów związanych z hostingiem i utrzymywaniem niestandardowo opracowanych rozwiązań i skryptów aprowizacji.
- Zabezpieczanie organizacji przez natychmiastowe usuwanie tożsamości użytkowników z aplikacji Key SaaS, gdy opuszczają one organizację.
- Łatwo Importuj dużą liczbę użytkowników do określonej aplikacji lub systemu SaaS.
- Posiadanie jednego zestawu zasad, aby określić, kto jest zainicjowany i kto może logować się do aplikacji.

## <a name="how-does-automatic-provisioning-work"></a>Jak działa Automatyczna obsługa administracyjna?

**Usługa Azure AD Provisioning** udostępnia użytkownikom SaaS aplikacje i inne systemy, łącząc się z punktami końcowymi interfejsu API zarządzania użytkownikami udostępnionymi przez poszczególnych dostawców aplikacji. Te punkty końcowe interfejsu API zarządzania użytkownikami umożliwiają usłudze Azure AD Programistyczne tworzenie, aktualizowanie i usuwanie użytkowników. W przypadku wybranych aplikacji usługa aprowizacji może również tworzyć, aktualizować i usuwać dodatkowe obiekty powiązane z tożsamościami, takie jak grupy i role.

@no__t — 0Azure usługi AD Provisioning @ no__t-1*rysunek 1: usługa Azure AD Provisioning*

@no__t — przepływ pracy aprowizacji użytkowników 0Outbound @ no__t-1*rysunek 2: "wychodzące" przepływ pracy aprowizacji użytkowników z usługi Azure AD do popularnych aplikacji SaaS*

@no__t — przepływ pracy aprowizacji użytkowników 0Inbound @ no__t-1*rysunek 3: "przychodzące" przepływ pracy aprowizacji użytkowników z popularnych aplikacji do zarządzania stolicą Kadr (HCM) do Azure Active Directory i systemu Windows Server Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jakie aplikacje i systemy mogą być używane przez automatyczne Inicjowanie obsługi użytkowników w usłudze Azure AD?

Funkcje usługi Azure AD są wstępnie zintegrowane z obsługą wielu popularnych aplikacji SaaS i systemów kadr oraz ogólnego wsparcia dla aplikacji, które implementują określone części [standardu standard scim 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

### <a name="pre-integrated-applications"></a>Wstępnie zintegrowane aplikacje

Aby uzyskać listę wszystkich aplikacji, dla których usługa Azure AD obsługuje wstępnie zintegrowany łącznik aprowizacji, zapoznaj się z [listą samouczków aplikacji dotyczących aprowizacji użytkowników](../saas-apps/tutorial-list.md).

Aby skontaktować się z zespołem inżynierów usługi Azure AD, aby zażądać pomocy technicznej dotyczącej dodatkowych aplikacji, Prześlij komunikat za pomocą [forum opinii Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Aby aplikacja obsługiwała automatyczne Inicjowanie obsługi użytkowników, musi najpierw udostępnić niezbędne interfejsy API zarządzania użytkownikami, które umożliwiają programom zewnętrznym automatyzację tworzenia, obsługi i usuwania użytkowników. W związku z tym nie wszystkie aplikacje SaaS są zgodne z tą funkcją. W przypadku aplikacji, które obsługują interfejsy API zarządzania użytkownikami, zespół inżynierów usługi Azure AD może następnie utworzyć łącznik aprowizacji dla tych aplikacji, a ta ilość pracy jest określana przez potrzeby bieżących i potencjalnych klientów.

### <a name="connecting-applications-that-support-scim-20"></a>Łączenie aplikacji obsługujących standard scim 2,0

Aby uzyskać informacje na temat ogólnego łączenia aplikacji, które implementują interfejsy API zarządzania użytkownikami opartymi na systemie Standard scim 2,0, zobacz [Używanie Standard scim do automatycznego aprowizacji użytkowników i grup z Azure Active Directory do aplikacji](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak mogę skonfigurować automatyczne Inicjowanie obsługi administracyjnej aplikacji?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Użyj portalu Azure Active Directory, aby skonfigurować usługę Azure AD Provisioning dla wybranej aplikacji.

1. Otwórz **[portal Azure Active Directory](https://aad.portal.azure.com)** .
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji.
1. Wybierz pozycję **+ Nowa aplikacja** , aby dodać aplikację. Dodaj jedną z następujących czynności w zależności od scenariusza:

   - Opcja **Dodaj własną aplikację** obsługuje integracje Standard scim opracowane przez niestandardowo.
   - Wszystkie aplikacje w **dodatku z galerii** > **Polecane aplikacje** obsługują automatyczną obsługę administracyjną. Zapoznaj się z [listą samouczków aplikacji dotyczących aprowizacji użytkowników](../saas-apps/tutorial-list.md) , aby uzyskać dodatkowe.

1. Podaj wszystkie szczegóły i wybierz pozycję **Dodaj**. Nowa aplikacja zostanie dodana do listy aplikacji dla przedsiębiorstw i zostanie otwarta na swoim ekranie zarządzania aplikacjami.
1. Wybierz opcję **aprowizacji** , aby zarządzać ustawieniami aprowizacji konta użytkownika dla aplikacji.

   ![Pokazuje ekran ustawień aprowizacji](./media/user-provisioning/provisioning_settings0.PNG)

1. Wybierz opcję Automatyczna dla **trybu aprowizacji** , aby określić ustawienia poświadczeń administratora, mapowania, uruchamiania i zatrzymywania oraz synchronizację.

   - Rozwiń węzeł **poświadczenia administratora** , aby wprowadzić poświadczenia wymagane przez usługę Azure AD do łączenia się z interfejsem API zarządzania użytkownikami aplikacji. Ta sekcja umożliwia również włączenie powiadomień e-mail w przypadku niepowodzenia poświadczeń lub zadania aprowizacji do [kwarantanny](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).
   - Rozwiń węzeł **mapowania** , aby wyświetlić i edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową, gdy konta użytkowników są inicjowane lub aktualizowane. Jeśli aplikacja docelowa obsługuje tę funkcję, w tej sekcji można opcjonalnie skonfigurować obsługę administracyjną grup i kont użytkowników. Wybierz mapowanie w tabeli, aby otworzyć Edytor mapowania po prawej stronie, w którym można wyświetlać i dostosowywać atrybuty użytkownika.

     **Filtry określania zakresu** informują usługę aprowizacji, którą użytkownicy i grupy w systemie źródłowym powinny być obsługiwane lub cofać administracyjnie do systemu docelowego. W okienku **Mapowanie atrybutu** wybierz pozycję **Zakres obiektu źródłowego** , aby filtrować według określonych wartości atrybutów. Można na przykład określić, że w zakres aprowizacji mają wchodzić tylko użytkownicy, u których atrybut „Dział” ma wartość „Sprzedaż”. Aby uzyskać więcej informacji, zobacz [Używanie filtrów zakresu](define-conditional-rules-for-provisioning-user-accounts.md).

     Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów](customize-application-attributes.md).

   - **Ustawienia** kontrolują działanie usługi aprowizacji dla aplikacji, w tym to, czy jest ona obecnie uruchomiona. Menu **zakres** pozwala określić, czy tylko przypisani Użytkownicy i grupy powinny znajdować się w zakresie aprowizacji, czy też należy zainicjować obsługę administracyjną wszystkich użytkowników w katalogu usługi Azure AD. Aby uzyskać informacje na temat „przypisywania” użytkowników i grup, zobacz [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](assign-user-or-group-access-portal.md).

Na ekranie Zarządzanie aplikacjami wybierz pozycję **dzienniki aprowizacji (wersja zapoznawcza)** , aby wyświetlić rekordy wszystkich operacji wykonywanych przez usługę Azure AD Provisioning. Aby uzyskać więcej informacji, zobacz [Przewodnik po raportowaniu obsługi administracyjnej](check-status-user-account-provisioning.md).

![Przykład — ekran dzienniki aprowizacji dla aplikacji](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Usługę aprowizacji użytkowników w usłudze Azure AD można także skonfigurować i zarządzać nią za pomocą [interfejsu API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

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

Przykładowy plan wdrożenia krok po kroku dotyczący wychodzącej aprowizacji użytkowników do aplikacji można znaleźć w [Podręczniku wdrażania tożsamości dla aprowizacji użytkowników](https://aka.ms/userprovisioningdeploymentplan).

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

## <a name="related-articles"></a>Pokrewne artykuły:

- [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
- [Dostosowywanie mapowań atrybutów na potrzeby aprowizacji użytkowników](customize-application-attributes.md)
- [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
- [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
- [Korzystanie z standard scim w celu włączenia automatycznej aprowizacji użytkowników i grup z usługi Azure AD do aplikacji](use-scim-to-provision-users-and-groups.md)
- [Omówienie interfejsu API synchronizacji usługi Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
