---
title: Przewodnik po operacjach zarządzania tożsamościami i dostępami usługi Azure Active Directory
description: W tym przewodniku dotyczącym operacji opisano kontrole i akcje, które należy podjąć w celu zabezpieczenia operacji zarządzania tożsamościami i dostępem
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298618"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Przewodnik po operacjach zarządzania tożsamościami i dostępami usługi Azure Active Directory

W tej sekcji [przewodnika odnośności operacji usługi Azure AD](active-directory-ops-guide-intro.md) opisano kontrole i akcje, które należy wziąć pod uwagę w celu zabezpieczenia cyklu życia tożsamości i ich przydziałów oraz zarządzania nimi.

> [!NOTE]
> Zalecenia te są aktualne na dzień publikacji, ale mogą się zmieniać w czasie. Organizacje powinny stale oceniać swoje praktyki tożsamości w miarę rozwoju produktów i usług firmy Microsoft w miarę rozwoju w czasie.

## <a name="key-operational-processes"></a>Kluczowe procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie usługą Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu utrzymania środowiska. Do kluczowych zadań i ich zalecanych właścicieli należą:

| Zadanie | Właściciel |
| :- | :- |
| Definiowanie procesu tworzenia subskrypcji platformy Azure | Zależy od organizacji |
| Zdecyduj, kto otrzymuje licencje Enterprise Mobility + Security | Zespół operacyjny IAM |
| Decyduj, kto otrzymuje licencje na usługi Office 365 | Zespół produktywności |
| Zdecyduj, kto otrzymuje inne licencje, na przykład Dynamics, VSO | Właściciel aplikacji |
| Przypisywanie licencji | Zespół operacyjny IAM |
| Rozwiązywanie problemów z błędami przypisania licencji i korygowanie ich | Zespół operacyjny IAM |
| Udostępnianie tożsamości aplikacjom w usłudze Azure AD | Zespół operacyjny IAM |

Podczas przeglądania listy może okazać się konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosowanie własności do zadań z właścicielami, które nie są zgodne z powyższymi zaleceniami.

#### <a name="assigning-owners-recommended-reading"></a>Przypisywanie właścicielom zalecane czytanie

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Nadzór na platformie Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Lokalna synchronizacja tożsamości

### <a name="identify-and-resolve-synchronization-issues"></a>Identyfikowanie i rozwiązywanie problemów z synchronizacją

Firma Microsoft zaleca, aby mieć dobrą linię bazową i zrozumienie problemów w środowisku lokalnym, które mogą powodować problemy z synchronizacją z chmurą. Ponieważ zautomatyzowane narzędzia, takie jak [IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) i [Usługa Azure AD Connect Health,](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) mogą generować dużą liczbę fałszywych alarmów, zaleca się zidentyfikowanie błędów synchronizacji, które zostały pozostawione bez użycia przez ponad 100 dni, przez błędne wyczyszczone te obiekty. Długoterminowe nierozwiązane błędy synchronizacji mogą generować zdarzenia pomocy technicznej. [Rozwiązywanie problemów z błędami podczas synchronizacji](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) zawiera omówienie różnych typów błędów synchronizacji, niektóre z możliwych scenariuszy, które powodują te błędy i potencjalne sposoby naprawienia błędów.

### <a name="azure-ad-connect-sync-configuration"></a>Konfiguracja usługi Azure AD Connect Sync

Aby włączyć wszystkie środowiska hybrydowe, postawę zabezpieczeń opartą na urządzeniach i integrację z usługą Azure AD, wymagane jest zsynchronizowanie kont użytkowników używanych przez pracowników do logowania się do swoich pulpitów.

Jeśli użytkownik lasu nie zostanie zsynchronizowany, należy zmienić synchronizację, aby pochodzić z właściwego lasu.

#### <a name="synchronization-scope-and-object-filtering"></a>Zakres synchronizacji i filtrowanie obiektów

Usuwanie znanych zasobników obiektów, które nie muszą być synchronizowane, ma następujące korzyści operacyjne:

- Mniej źródeł błędów synchronizacji
- Szybsze cykle synchronizacji
- Mniej "śmieci" do przenoszenia z lokalnego, na przykład zanieczyszczenie globalnej listy adresów dla lokalnych kont usług, które nie są istotne w chmurze

> [!NOTE]
> Jeśli okaże się, że importujesz wiele obiektów, które nie są eksportowane do chmury, należy filtrować według jednostek organizacyjnych lub określonych atrybutów.

Przykładami obiektów do wykluczenia są:

- Konta usług, które nie są używane w aplikacjach w chmurze
- Grupy, które nie mają być używane w scenariuszach chmury, takich jak te używane do udzielania dostępu do zasobów
- Użytkownicy lub kontakty, które są tożsamościami zewnętrznymi, które mają być reprezentowane za pomocą usługi Azure AD B2B Collaboration
- Konta komputerowe, na których pracownicy nie mają dostępu do aplikacji w chmurze, na przykład z serwerów

> [!NOTE]
> Jeśli pojedyncza tożsamość ludzka ma wiele kont aprowizowanych z czegoś takiego jak migracja, fuzja lub przejęcie starszej domeny, należy synchronizować konto używane przez użytkownika tylko na co dzień, na przykład, czego używają do logowania się do komputera .

W idealnym przypadku należy osiągnąć równowagę między zmniejszeniem liczby obiektów do synchronizacji i złożoności reguł. Ogólnie rzecz biorąc, kombinacja [filtrowania](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) jednostek organizacyjnych/kontenerów oraz proste mapowanie atrybutów do atrybutu cloudFiltered jest skuteczną kombinacją filtrowania.

> [!IMPORTANT]
> Jeśli używasz filtrowania grup w produkcji, należy przejść do innego podejścia filtrowania.

#### <a name="sync-failover-or-disaster-recovery"></a>Synchronizacja trybu failover lub odzyskiwania po awarii

Usługa Azure AD Connect odgrywa kluczową rolę w procesie inicjowania obsługi administracyjnej. Jeśli serwer synchronizacji przejdzie w tryb offline z jakiegokolwiek powodu, zmiany w środowisku lokalnym nie mogą być aktualizowane w chmurze i może spowodować problemy z dostępem dla użytkowników. W związku z tym ważne jest zdefiniowanie strategii pracy awaryjnej, która umożliwia administratorom szybkie wznowienie synchronizacji po przejściu serwera synchronizacji w tryb offline. Takie strategie mogą należeć do następujących kategorii:

- **Wdrażanie serwerów Azure AD Connect w trybie przemieszczania** — umożliwia administratorowi "promowanie" serwera przejściowego do środowiska produkcyjnego za pomocą prostego przełącznika konfiguracji.
- **Użyj wirtualizacji** — jeśli połączenie usługi Azure AD jest wdrożony na maszynie wirtualnej (VM), administratorzy mogą korzystać z ich stosu wirtualizacji do migracji na żywo lub szybko ponownie wdrożyć maszynę wirtualną i w związku z tym wznowić synchronizację.

Jeśli w organizacji brakuje strategii odzyskiwania po awarii i pracy awaryjnej dla synchronizacji, nie należy wahać się wdrożyć usługę Azure AD Connect w trybie przemieszczania. Podobnie jeśli istnieje niezgodność między konfiguracją produkcyjną a przejściową, należy ponownie ujedniakować tryb przemieszczania usługi Azure AD Connect, aby dopasować konfigurację produkcyjną, w tym wersje oprogramowania i konfiguracje.

![Zrzut ekranu przedstawiający konfigurację trybu przemieszczania usługi Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Dbanie o aktualność

Firma Microsoft regularnie aktualizuje usługę Azure AD Connect. Bądź na bieżąco, aby skorzystać z ulepszeń wydajności, poprawek błędów i nowych funkcji, które zapewnia każda nowa wersja.

Jeśli twoja wersja usługi Azure AD Connect jest więcej niż sześć miesięcy w tyle, należy uaktualnić do najnowszej wersji.

#### <a name="source-anchor"></a>Kotwica źródła

Użycie **narzędzia ms-DS-consistencyguid** jako [kotwicy źródłowej](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) umożliwia łatwiejszą migrację obiektów między lasami i domenami, co jest powszechne w konsolidacji/oczyszczaniu domeny usługi AD, fuzjach, przejęciach i zbyciach.

Jeśli obecnie używasz **ObjectGuid** jako kotwicy źródłowej, zalecamy przełączenie się na korzystanie **z ms-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Reguły niestandardowe

Reguły niestandardowe usługi Azure AD Connect umożliwiają kontrolowanie przepływu atrybutów między obiektami lokalnymi a obiektami w chmurze. Jednak nadużywanie lub nadużywanie reguł niestandardowych może powodować następujące zagrożenia:

- Rozwiązywanie problemów ze złożonością
- Obniżenie wydajności podczas wykonywania złożonych operacji między obiektami
- Większe prawdopodobieństwo rozbieżności konfiguracji między serwerem produkcyjnym a serwerem przejściowym
- Dodatkowe obciążenie podczas uaktualniania usługi Azure AD Connect, jeśli reguły niestandardowe są tworzone w ramach pierwszeństwa większego niż 100 (używane przez wbudowane reguły)

Jeśli używasz zbyt skomplikowanych reguł, należy zbadać przyczyny złożoności i znaleźć możliwości uproszczenia. Podobnie, jeśli utworzono reguły niestandardowe o wartości pierwszeństwa powyżej 100, należy naprawić reguły, aby nie były zagrożone lub są w konflikcie z zestawem domyślnym.

Przykłady nadużywania reguł niestandardowych obejmują:

- **Kompensacja brudnych danych w katalogu** — w tym przypadku zaleca się pracę z właścicielami zespołu usługi AD i czyszczenie danych w katalogu jako zadanie korygowania i dostosowywanie procesów w celu uniknięcia ponownego wprowadzenia złych danych.
- **Jednorazowe korygowanie poszczególnych użytkowników** — często można znaleźć reguły, które odstają od szczególnych przypadków, zwykle z powodu problemu z określonym użytkownikiem.
- **Nadmiernie skomplikowane "CloudFiltering"** — przy jednoczesnym zmniejszeniu liczby obiektów jest dobrą praktyką, istnieje ryzyko tworzenia i nadmiernie skomplikowane zakres synchronizacji przy użyciu wielu reguł synchronizacji. Jeśli istnieje złożona logika do include/exclude obiektów poza filtrowania jednostki organizacyjnej, zaleca się do czynienia z tej logiki poza synchronizacji i etykiety obiektów z prostym "cloudFiltered" atrybut, który może przepływać za pomocą prostej reguły synchronizacji.

#### <a name="azure-ad-connect-configuration-documenter"></a>Dokumentator konfiguracji usługi Azure AD Connect

[Dokumentator konfiguracji usługi Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter) to narzędzie, którego można użyć do generowania dokumentacji instalacji usługi Azure AD Connect, aby umożliwić lepsze zrozumienie konfiguracji synchronizacji, budowanie zaufania do poprawiania sytuacji i wiedzieć, co zostało zmienione po zastosowaniu nowej kompilacji lub konfiguracji usługi Azure AD Connect lub dodanych lub zaktualizowanych reguł synchronizacji niestandardowej. Obecne możliwości narzędzia obejmują:

- Dokumentacja pełnej konfiguracji synchronizacji usługi Azure AD Connect.
- Dokumentacja wszelkich zmian w konfiguracji dwóch serwerów synchronizacji usługi Azure AD Connect lub zmian z danej linii bazowej konfiguracji.
- Generowanie skryptu wdrażania programu PowerShell w celu migracji różnic reguł synchronizacji lub dostosowań z jednego serwera na inny.

## <a name="assignment-to-apps-and-resources"></a>Przypisywanie do aplikacji i zasobów

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licencjonowanie oparte na grupach dla usług w chmurze firmy Microsoft

Usługa Azure Active Directory usprawnia zarządzanie licencjami za pomocą [licencjonowania opartego na grupach](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) usług w chmurze firmy Microsoft. W ten sposób IAM zapewnia infrastrukturę grupy i delegowane zarządzanie tymi grupami odpowiednim zespołom w organizacjach. Istnieje wiele sposobów konfigurowania członkostwa grup w usłudze Azure AD, w tym:

- **Zsynchronizowane z lokalnych** — grupy mogą pochodzić z katalogów lokalnych, co może być dobre dla organizacji, które utworzyły procesy zarządzania grupami, które można rozszerzyć, aby przypisać licencje w usłudze Office 365.

- **Oparte na atrybutach / dynamiczne** — grupy mogą być tworzone w chmurze na podstawie wyrażenia opartego na atrybutach użytkownika, na przykład Dział jest równy "sprzedaży". Usługa Azure AD utrzymuje członków grupy, zachowując ją zgodnie z zdefiniowanym wyrażeniem. Użycie tego rodzaju grupy do przypisywania licencji umożliwia przypisanie licencji oparte na atrybutach, co jest dobre dla organizacji, które mają wysoką jakość danych w katalogu.

- **Delegowane własności** — grupy mogą być tworzone w chmurze i mogą być wyznaczone właścicieli. W ten sposób można umożliwić właścicielom firm, na przykład zespołowi współpracy lub zespołowi analizy biznesowej, określenie, kto powinien mieć dostęp.

Jeśli obecnie używasz ręcznego procesu przypisywania licencji i składników użytkownikom, zalecamy wdrożenie licencjonowania opartego na grupach. Jeśli bieżący proces nie monitoruje błędów licencjonowania lub co jest przypisane w porównaniu z dostępne, należy zdefiniować ulepszenia procesu w celu wyeliminowania błędów licencjonowania i monitorowania przypisania licencjonowania.

Innym aspektem zarządzania licencjami jest definicja planów usług (składników licencji), które powinny być włączone na podstawie funkcji zadań w organizacji. Przyznanie dostępu do planów usług, które nie są konieczne, może spowodować, że użytkownicy zobaczą narzędzia w portalu pakietu Office, dla których nie zostali przeszkoleni lub nie powinni ich używać. Może zwiększyć ilość dodatkowych działów pomocy technicznej, niepotrzebne inicjowanie obsługi administracyjnej i narazić zgodność i zarządzanie na ryzyko, na przykład podczas inicjowania obsługi administracyjnej usługi OneDrive dla firm osobom, które mogą nie być dozwolone do udostępniania zawartości.

Aby zdefiniować plany usług dla użytkowników, należy użyć następujących wskazówek:

- Administratorzy powinni zdefiniować "pakiety" planów usług, które mają być oferowane użytkownikom na podstawie ich roli, na przykład pracownika umysłowego w porównaniu z pracownikiem podłogowym.
- Tworzenie grup według klastra i przypisywanie licencji z planem usług.
- Opcjonalnie można zdefiniować atrybut do przechowywania pakietów dla użytkowników.

> [!IMPORTANT]
> Licencjonowanie oparte na grupach w usłudze Azure AD wprowadza koncepcję użytkowników w stanie błędu licencjonowania. Jeśli zauważysz błędy licencjonowania, należy natychmiast [zidentyfikować i rozwiązać](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) wszelkie problemy z przypisaniem licencji.

![Zrzut ekranu ekranu komputera Opis generowany automatycznie](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Zarządzanie cyklem życia

Jeśli obecnie używasz narzędzia, takiego jak [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) lub system innej firmy, który opiera się na infrastrukturze lokalnej, zalecamy odciążenie przypisania z istniejącego narzędzia, wdrożenie licencjonowania opartego na grupach i zdefiniowanie zarządzania cyklem życia grupy na podstawie [grup](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups). Podobnie, jeśli istniejący proces nie uwzględnia nowych pracowników lub pracowników opuszczających organizację, należy wdrożyć licencjonowanie oparte na grupach na podstawie grup dynamicznych i zdefiniować cykl życia członkostwa w grupie. Na koniec, jeśli licencjonowanie oparte na grupach jest wdrażane w grupach lokalnych, które nie mają zarządzania cyklem życia, należy rozważyć użycie grup w chmurze, aby włączyć możliwości, takie jak delegowane własności lub oparte na atrybutach członkostwa dynamicznego.

### <a name="assignment-of-apps-with-all-users-group"></a>Przypisywanie aplikacji z grupą "Wszyscy użytkownicy"

Właściciele zasobów mogą uwierzyć, że grupa **Wszyscy użytkownicy** zawiera tylko **pracowników przedsiębiorstwa,** gdy mogą one rzeczywiście zawierać zarówno **pracowników przedsiębiorstwa, jak** i **gości.** W związku z tym należy zwrócić szczególną szczególną ostrożność podczas korzystania z grupy **Wszyscy użytkownicy** do przypisywania aplikacji i udzielania dostępu do zasobów, takich jak zawartość programu SharePoint lub aplikacje.

> [!IMPORTANT]
> Jeśli grupa **Wszyscy użytkownicy** jest włączona i używana dla zasad dostępu warunkowego, przypisania aplikacji lub zasobów, upewnij się, że [zabezpieczysz grupę,](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) jeśli nie chcesz, aby obejmowała ona użytkowników-gości. Ponadto należy naprawić przypisania licencji, tworząc i przypisując do grup, które zawierają tylko **pracowników przedsiębiorstwa.** Z drugiej strony, jeśli okaże się, że grupa **Wszyscy użytkownicy** jest włączona, ale nie jest używana do udzielania dostępu do zasobów, upewnij się, że wskazówki operacyjne organizacji są celowo używane do tej grupy (która obejmuje zarówno **pracowników korporacyjnych,** jak i **gości).**

### <a name="automated-user-provisioning-to-apps"></a>Automatyczne inicjowanie obsługi administracyjnej aplikacji

[Automatyczne inicjowanie obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) do aplikacji jest najlepszym sposobem, aby utworzyć spójne inicjowania obsługi administracyjnej, anulowania obsługi administracyjnej i cyklu życia tożsamości w wielu systemach.

Jeśli obecnie inicjujesz aprowizacji aplikacji w sposób ad hoc lub przy użyciu rzeczy, takich jak pliki CSV, JIT lub rozwiązanie lokalne, które nie dotyczy zarządzania cyklem życia, zaleca się [wdrożenie inicjowania obsługi administracyjnej aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) za pomocą usługi Azure AD dla obsługiwanych aplikacji i zdefiniować spójny wzorzec dla aplikacji, które nie są jeszcze obsługiwane przez usługę Azure AD.

![Usługa inicjowania obsługi administracyjnej usługi Azure AD](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Linia bazowa cyklu synchronizacji różnicowej usługi Azure AD Connect

Ważne jest, aby zrozumieć ilość zmian w organizacji i upewnij się, że nie trwa zbyt długo, aby mieć przewidywalny czas synchronizacji.

Domyślna częstotliwość [synchronizacji różnicowej](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) wynosi 30 minut. Jeśli synchronizacja delta trwa dłużej niż 30 minut konsekwentnie lub istnieją znaczne rozbieżności między wydajnością synchronizacji delta przemieszczania i produkcji, należy zbadać i przejrzeć [czynniki wpływające na wydajność usługi Azure AD Connect.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Zalecane rozwiązywanie problemów z usługą Azure AD Connect

- [Przygotowywanie atrybutów katalogu do synchronizacji z pakietem Office 365 przy użyciu narzędzia IdFix — Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Usługa Azure AD Connect: rozwiązywanie problemów z błędami podczas synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Podsumowanie

Istnieje pięć aspektów bezpiecznej infrastruktury tożsamości. Ta lista pomoże Ci szybko znaleźć i podjąć niezbędne działania w celu zabezpieczenia cyklu życia tożsamości i ich uprawnień w organizacji oraz zarządzania nimi.

- Przypisz właścicieli do kluczowych zadań.
- Znajdź i rozwiąż problemy z synchronizacją.
- Zdefiniuj strategię pracy awaryjnej dla odzyskiwania po awarii.
- Usprawnij zarządzanie licencjami i przydzielanie aplikacji.
- Automatyzacja inicjowania obsługi administracyjnej aplikacji przez użytkowników.

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do [sprawdzania i akcji zarządzania uwierzytelnianiem](active-directory-ops-guide-auth.md).
