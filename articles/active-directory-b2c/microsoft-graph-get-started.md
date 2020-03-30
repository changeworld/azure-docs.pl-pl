---
title: Zarządzanie zasobami za pomocą programu Microsoft Graph
titleSuffix: Azure AD B2C
description: Przygotuj się do zarządzania zasobami usługi Azure AD B2C za pomocą programu Microsoft Graph, rejestrując aplikację, która ma wymagane uprawnienia interfejsu API programu Graph.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184359"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Zarządzanie usługą Azure AD B2C za pomocą programu Microsoft Graph

[Program Microsoft Graph][ms-graph] umożliwia zarządzanie wieloma zasobami w dzierżawie usługi Azure AD B2C, w tym kontami użytkowników klientów i zasadami niestandardowymi. Pisząc skrypty lub aplikacje wywołujące [interfejs API programu Microsoft Graph,][ms-graph-api]można zautomatyzować zadania zarządzania dzierżawą, takie jak:

* Migrowanie istniejącego magazynu użytkowników do dzierżawy usługi Azure AD B2C
* Wdrażanie zasad niestandardowych za pomocą potoku platformy Azure w usłudze Azure DevOps i zarządzanie niestandardowymi kluczami zasad
* Hostowanie rejestracji użytkowników na własnej stronie i tworzenie kont użytkowników w katalogu usługi Azure AD B2C za kulisami
* Automatyzacja rejestracji aplikacji
* Uzyskiwanie dzienników inspekcji

Poniższe sekcje ułatwiają przygotowanie do korzystania z interfejsu API programu Microsoft Graph w celu zautomatyzowania zarządzania zasobami w katalogu usługi Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Tryby interakcji interfejsu API programu Microsoft Graph

Istnieją dwa tryby komunikacji, których można użyć podczas pracy z interfejsem API programu Microsoft Graph do zarządzania zasobami w dzierżawie usługi Azure AD B2C:

* **Interaktywne** — odpowiednie do uruchamiania po zakończeniu zadań, można użyć konta administratora w dzierżawie B2C do wykonywania zadań zarządzania. Ten tryb wymaga, aby administrator zalogował się przy użyciu swoich poświadczeń przed wywołaniem interfejsu API programu Microsoft Graph.

* **Automatyczne** — w przypadku zaplanowanych lub stale uruchamianych zadań ta metoda używa konta usługi skonfigurowanego z uprawnieniami wymaganymi do wykonywania zadań zarządzania. "Konto usługi" w usłudze Azure AD B2C można utworzyć, rejestrując aplikację używaną przez aplikacje i skrypty do uwierzytelniania przy użyciu identyfikatora *aplikacji (klienta)* i przyznania poświadczeń klienta OAuth 2.0. W takim przypadku aplikacja działa jako sama do wywołania interfejsu API programu Microsoft Graph, a nie użytkownika administratora, jak w opisanej wcześniej metodzie interaktywnej.

Włącz **scenariusz automatycznej** interakcji, tworząc rejestrację aplikacji pokazaną w poniższych sekcjach.

## <a name="register-management-application"></a>Zarejestruj aplikację do zarządzania

Zanim skrypty i aplikacje będą mogły wchodzić w interakcje z [interfejsem API programu Microsoft Graph][ms-graph-api] w celu zarządzania zasobami usługi Azure AD B2C, należy utworzyć rejestrację aplikacji w dzierżawie usługi Azure AD B2C, która udziela wymaganych uprawnień interfejsu API.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Udzielanie dostępu do interfejsu API

Następnie przyznaj uprawnienia zarejestrowanej aplikacji do manipulowania zasobami dzierżawy za pośrednictwem wywołań interfejsu API programu Microsoft Graph.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Tworzenie klucza tajnego klienta

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Masz teraz aplikację, która ma uprawnienia do *tworzenia,* *odczytu,* *aktualizacji*i *usuwania* użytkowników w dzierżawie usługi Azure AD B2C. Przejdź do następnej sekcji, aby dodać uprawnienia do *aktualizacji hasła.*

## <a name="enable-user-delete-and-password-update"></a>Włączanie usuwania użytkowników i aktualizacji hasła

Uprawnienie *Do odczytu i zapisu danych katalogu* **NIE** obejmuje możliwości usuwania użytkowników lub aktualizowania haseł kont użytkowników.

Jeśli aplikacja lub skrypt musi usunąć użytkowników lub zaktualizować ich hasła, przypisz rolę *administratora użytkownika* do aplikacji:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i użyj filtru **Katalog + Subskrypcja,** aby przełączyć się do dzierżawy usługi Azure AD B2C.
1. Wyszukaj i wybierz **pozycję Azure AD B2C**.
1. W obszarze **Zarządzanie**wybierz pozycję **Role i administratorzy**.
1. Wybierz rolę **Administratora użytkownika.**
1. Wybierz **pozycję Dodaj przydziały**.
1. W polu tekstowym **Zaznacz** wprowadź nazwę aplikacji zarejestrowanej wcześniej, na przykład *managementapp1*. Wybierz aplikację, gdy pojawi się w wynikach wyszukiwania.
1. Wybierz pozycję **Dodaj**. Może upłynąć kilka minut, aby uprawnienia do pełnego propagacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zarejestrowałeś aplikację do zarządzania i przyznano jej wymagane uprawnienia, aplikacje i usługi (na przykład usługi Azure Pipelines) mogą używać jego poświadczeń i uprawnień do interakcji z interfejsem API programu Microsoft Graph.

* [Operacje B2C obsługiwane przez program Microsoft Graph](microsoft-graph-operations.md)
* [Zarządzanie kontami użytkowników usługi Azure AD B2C za pomocą programu Microsoft Graph](manage-user-accounts-graph-api.md)
* [Pobierz dzienniki inspekcji za pomocą interfejsu API raportowania usługi Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
