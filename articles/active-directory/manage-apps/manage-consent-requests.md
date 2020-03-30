---
title: Zarządzanie zgodą aplikacji i ocena żądań zgody — Usługa Azure AD
description: Dowiedz się, jak zarządzać żądaniami zgody, gdy zgoda użytkownika jest wyłączona lub ograniczona, oraz jak oceniać żądanie zgody administratora dla całej dzierżawy na aplikację.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367854"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Zarządzanie zgodą aplikacji i ocena wniosków o zgodę

Firma Microsoft [zaleca](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) wyłączenie zgody użytkownika końcowego na aplikacje. Spowoduje to scentralizowanie procesu decyzyjnego za pomocą zespołu administratora zabezpieczeń i tożsamości w organizacji.

Po wyłączeniu lub ograniczeniu zgody użytkownika końcowego istnieje kilka ważnych kwestii, aby zapewnić bezpieczeństwo organizacji, a jednocześnie umożliwić korzystanie z aplikacji o krytycznym znaczeniu dla firmy. Te kroki mają kluczowe znaczenie dla zminimalizowania wpływu na zespół pomocy technicznej organizacji i administratorów IT, jednocześnie zapobiegając używaniu niezarządzanych kont w aplikacjach innych firm.

## <a name="process-changes-and-education"></a>Zmiany procesów i edukacja

 1. Rozważ włączenie [przepływu pracy zgody administratora (podgląd),](configure-admin-consent-workflow.md) aby umożliwić użytkownikom żądanie zatwierdzenia administratora bezpośrednio z ekranu zgody.

 2. Upewnij się, że wszyscy administratorzy rozumieją [ramy uprawnień i zgody,](../develop/consent-framework.md)jak działa [monit o zgodę](../develop/application-consent-experience.md) i jak [ocenić żądanie zgody administratora dla całej dzierżawy.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Przejrzyj istniejące procesy organizacji, aby użytkownicy zażądali zatwierdzenia aplikacji przez administratora i w razie potrzeby dokonali aktualizacji. W przypadku zmiany procesów:
    * Zaktualizuj odpowiednią dokumentację, monitorowanie, automatyzację i tak dalej.
    * Komunikuj zmiany procesów wszystkim użytkownikom, deweloperom, zespołom pomocy technicznej i administratorom IT.

## <a name="auditing-and-monitoring"></a>Inspekcja i monitorowanie

1. [Inspekcja aplikacji i uprawnienia w](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) organizacji, aby upewnić się, że żadne nieuzasadnione lub podejrzane aplikacje nie uzyskały wcześniej dostępu do danych.

2. Przejrzyj [wykrywanie i korygowanie dotacji nielegalnej zgody w usłudze Office 365, aby](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) uzyskać dodatkowe najlepsze rozwiązania i zabezpieczenia przed podejrzanymi aplikacjami żądającymi zgody firmy OAuth.

3. Jeśli twoja organizacja ma odpowiednią licencję:

    * Użyj dodatkowych [funkcji inspekcji aplikacji OAuth w programie Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth).
    * Użyj [skoroszytów usługi Azure Monitor do monitorowania uprawnień i](../reports-monitoring/howto-use-azure-monitor-workbooks.md) działań związanych z zgodą. Skoroszyt *Usługi Consent Insights* udostępnia widok aplikacji według liczby żądań zgody, które nie powiodły się. Może to być przydatne w celu nadania priorytetu aplikacjom, aby administratorzy mogli je przejrzeć i zdecydować, czy udzielić im zgody administratora.

### <a name="additional-considerations-for-reducing-friction"></a>Dodatkowe kwestie związane z redukcją tarcia

Aby zminimalizować wpływ na zaufane aplikacje o znaczeniu krytycznym dla firmy, które są już używane, należy rozważyć proaktywne udzielanie zgody administratora aplikacjom, które mają dużą liczbę dotacji zgody użytkownika:

1. Weź udział w spisie aplikacji już dodanych do organizacji z wysokim użyciem na podstawie dzienników logowania lub działań związanych z udzielaniem zgody. Skrypt programu [PowerShell](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) może służyć do szybkiego i łatwego odnajdywanie aplikacji z dużą liczbą dotacji zgody użytkownika.

2. Oceń najlepsze aplikacje, które nie uzyskały jeszcze zgody administratora.

   > [!IMPORTANT]
   > Dokładnie ocenić aplikację przed udzieleniem zgody administratora całej dzierżawy, nawet jeśli wielu użytkowników w organizacji już wyraziłzgodę dla siebie.

3. Dla każdej aplikacji, która jest zatwierdzona, udzielić zgody administratora całej dzierżawy przy użyciu jednej z metod udokumentowanych poniżej.

4. Dla każdej zatwierdzonej aplikacji należy rozważyć [ograniczenie dostępu użytkownika](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Ocena żądania zgody administratora dla całej dzierżawy

Udzielanie zgody administratora w całej dzierżawie jest operacją wrażliwą.  Uprawnienia będą przyznawane w imieniu całej organizacji i mogą obejmować uprawnienia do podejmowania operacji o wysokim stopniu uprzywilejowania. Na przykład zarządzanie rolami, pełny dostęp do wszystkich skrzynek pocztowych lub wszystkich witryn oraz pełna personifikacja użytkowników.

Przed udzieleniem zgody administratora w całej dzierżawie, należy upewnić się, że ufasz aplikacji i wydawcy aplikacji, na poziomie dostępu, który udzielasz. Jeśli nie masz pewności, że rozumiesz, kto kontroluje aplikację i dlaczego aplikacja żąda uprawnień, *nie udzielaj zgody.*

Poniższa lista zawiera kilka zaleceń, które należy wziąć pod uwagę podczas oceny żądania udzielenia zgody administratora.

* **Poznaj [uprawnienia i strukturę zgody](../develop/consent-framework.md) na platformie tożsamości firmy Microsoft.**

* **Poznaj różnicę między [uprawnieniami delegowanymi a uprawnieniami aplikacji](../develop/v2-permissions-and-consent.md#permission-types).**

   Uprawnienia aplikacji umożliwiają aplikacji dostęp do danych dla całej organizacji, bez interakcji z użytkownikiem. Delegowane uprawnienia umożliwiają aplikacji działanie w imieniu użytkownika, który w pewnym momencie został zalogowany do aplikacji.

* **Poznaj wymagane uprawnienia.**

   Uprawnienia wymagane przez aplikację są wymienione w [wierszu zgody](../develop/application-consent-experience.md). Rozwinięcie tytułu uprawnienia spowoduje wyświetlenie opisu uprawnienia. Opis uprawnień aplikacji zazwyczaj kończy się na "bez zalogowanego użytkownika". Opis uprawnień delegowanych zazwyczaj kończy się na "w imieniu zalogowanego użytkownika". Uprawnienia do interfejsu API programu Microsoft Graph są opisane w [Microsoft Graph Permissions References]- zapoznaj się z dokumentacją innych interfejsów API, aby zrozumieć uprawnienia, które ujawniają.

   Jeśli nie rozumiesz żądanej zgody, *nie udzielaj zgody.*

* **Dowiedz się, która aplikacja żąda uprawnień i kto opublikował aplikację.**

   Uważaj na złośliwe aplikacje próbujące wyglądać jak inne aplikacje.

   Jeśli masz wątpliwości co do zasadności wniosku lub jego wydawcy, *nie udzielaj zgody.* Zamiast tego należy szukać dodatkowego potwierdzenia (na przykład bezpośrednio od wydawcy aplikacji).

* **Upewnij się, że żądane uprawnienia są wyrównane z funkcjami, których oczekujesz od aplikacji.**

   Na przykład aplikacja oferująca zarządzanie witryną programu SharePoint może wymagać delegowanego dostępu do odczytu wszystkich zbiorów witryn, ale niekoniecznie musi wymagać pełnego dostępu do wszystkich skrzynek pocztowych lub pełnych uprawnień personifikacji w katalogu.

   Jeśli podejrzewasz, że aplikacja żąda więcej uprawnień niż potrzebuje, *nie udzielaj zgody.* Skontaktuj się z wydawcą aplikacji, aby uzyskać więcej informacji.

## <a name="granting-consent-as-an-administrator"></a>Udzielanie zgody jako administrator

### <a name="granting-tenant-wide-admin-consent"></a>Udzielanie zgody administratora dla całej dzierżawy

Zobacz [Udzielanie zgody administratora dla całej dzierżawy](grant-admin-consent.md) do aplikacji, aby uzyskać instrukcje krok po kroku dotyczące udzielania zgody administratora dla całej dzierżawy z witryny Azure portal, przy użyciu usługi Azure AD PowerShell lub z samego monitu o zgodę.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Udzielanie zgody w imieniu określonego użytkownika

Zamiast udzielać zgody dla całej organizacji, administrator może również użyć [interfejsu API wykresu microsft,](https://docs.microsoft.com/graph/use-the-api) aby udzielić zgody na delegowane uprawnienia w imieniu jednego użytkownika. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu w imieniu użytkownika](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Ograniczanie dostępu użytkowników do aplikacji

Dostęp użytkowników do aplikacji może być nadal ograniczony nawet wtedy, gdy udzielono zgody administratora w całej dzierżawie. Aby uzyskać więcej informacji na temat wymagania przypisania przez użytkownika do aplikacji, zobacz [metody przypisywania użytkowników i grup](methods-for-assigning-users-and-groups.md).

Aby uzyskać bardziej szersze omówienie, w tym sposób obsługi dodatkowych złożonych scenariuszy, zobacz [korzystanie z usługi Azure AD do zarządzania dostępem do aplikacji.](what-is-access-management.md)

## <a name="next-steps"></a>Następne kroki

[Pięć kroków do zabezpieczenia infrastruktury tożsamości](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)

[Konfigurowanie sposobu wyrażania zgody użytkowników końcowych na aplikacje](configure-user-consent.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

[Usługa Azure AD w przepływie stosuPrzepełnienie](https://stackoverflow.com/questions/tagged/azure-active-directory)