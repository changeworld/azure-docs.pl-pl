---
title: Grupy dynamiczne i współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak używać grup dynamicznych usługi Azure AD przy użyciu funkcji współpracy B2B usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17f701eb9ad3c083712ddf9e774e6ddbefbe91fd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65227945"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupy dynamiczne i współpracy B2B usługi Azure Active Directory

## <a name="what-are-dynamic-groups"></a>Co to są grupy dynamiczne?
Dynamiczna konfiguracja członkostwa w grupie zabezpieczeń usługi Azure Active Directory (Azure AD) jest dostępna w [witryny Azure portal](https://portal.azure.com). Administratorzy mogą ustawić zasady do wypełniania grup, które są tworzone w usłudze Azure AD na podstawie atrybutów użytkownika (na przykład userType, działu lub kraj/region). Automatyczne dodawanie członków i usunięty z grupy zabezpieczeń, na podstawie ich atrybutów. Te grupy mogą zapewnić dostęp do aplikacji lub zasobów w chmurze (witryny programu SharePoint, dokumentów) oraz do przypisywania licencji do elementów członkowskich. Przeczytaj więcej na temat grup dynamicznych w [grup w usłudze Azure Active Directory w wersji dedykowanej](../active-directory-accessmanagement-dedicated-groups.md).

Odpowiednie [licencjonowania usługi Azure AD Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/) jest wymagany do tworzenia i używania grup dynamicznych. Dowiedz się więcej w artykule [tworzenia reguł opartych na atrybutach dynamiczne członkostwo w grupie w usłudze Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Co to są wbudowane grupy dynamiczne?
**Wszyscy użytkownicy** grupy dynamicznej umożliwia administratorom dzierżawy do utworzenia grupy obejmującej wszystkich użytkowników w dzierżawie za pomocą jednego kliknięcia. Domyślnie **wszyscy użytkownicy** grupa zawiera wszystkich użytkowników w katalogu, w tym członkami i gości.
W nowym portalu administratora usługi Azure Active Directory, możesz włączyć **wszyscy użytkownicy** grupy w widoku ustawienia grupy.

![Pokazuje włączyć grupę Wszyscy użytkownicy, wartość tak](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Wzmacnianie ochrony wszystkich użytkowników grupy dynamicznej
Domyślnie **wszyscy użytkownicy** grupa zawiera również użytkowników (Gość) współpracy B2B. Można dodatkowo zabezpieczyć swoje **wszyscy użytkownicy** grupy przy użyciu reguły, aby usunąć użytkowników-gości. Poniższa ilustracja przedstawia **wszyscy użytkownicy** zmodyfikować grupy do wykluczenia gości.

![Reguła pokazuje, gdzie typ użytkownika nie jest równa gościa](media/use-dynamic-groups/exclude-guest-users.png)

Może również okazać przydatne utworzyć nową grupę dynamiczną, która zawiera tylko użytkowników-gości, tak aby mogły stosować zasady (na przykład zasady dostępu warunkowego usługi Azure AD) do nich.
Taka grupa jak może wyglądać:

![Reguły pokazuje, gdzie typ użytkownika jest równe gościa](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Kolejne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)
- [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)
- [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md)

