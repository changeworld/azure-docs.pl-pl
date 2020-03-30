---
title: Udzielanie zgody administratora dla całej dzierżawy na aplikację — usługa Azure AD
description: Dowiedz się, jak udzielić aplikacji zgody całej dzierżawy, aby użytkownicy końcowi nie monitowani o zgodę podczas logowania się do aplikacji.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480920"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Udzielanie zgody administratora dla całej dzierżawy na aplikację

Dowiedz się, jak uprościć środowisko użytkownika, udzielając aplikacji zgody administratora w całej dzierżawie. Ten artykuł zawiera różne sposoby osiągnięcia tego celu. Metody dotyczą wszystkich użytkowników końcowych w dzierżawie usługi Azure Active Directory (Azure AD).

Aby uzyskać więcej informacji na temat zgody na aplikacje, zobacz [Framework zgody usługi Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Wymagania wstępne

Udzielanie zgody administratora w całej dzierżawie wymaga zalogowania się jako [administrator globalny,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)lub administrator aplikacji [w chmurze.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)

> [!IMPORTANT]
> Gdy aplikacja została udzielona w całej dzierżawie zgody administratora, wszyscy użytkownicy będą mogli zalogować się do aplikacji, chyba że został skonfigurowany do żądania przypisania użytkownika. Aby ograniczyć, którzy użytkownicy mogą logować się do aplikacji, wymagają przypisania użytkownika, a następnie przypisują użytkowników lub grupy do aplikacji. Aby uzyskać więcej informacji, zobacz [Metody przypisywania użytkowników i grup](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> Udzielenie zgody administratora w całej dzierżawie aplikacji zapewni aplikacji i wydawcy aplikacji dostęp do danych organizacji. Dokładnie przejrzyj uprawnienia, o które żąda aplikacja przed udzieleniem zgody.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Udzielanie zgody administratora z witryny Azure portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Udzielanie zgody administratora w aplikacjach enterprise

Można udzielić zgody administratora całej dzierżawy za pośrednictwem *aplikacji Enterprise,* jeśli aplikacja została już udostępniona w dzierżawie. Na przykład aplikacja może być aprowizowana w dzierżawie, jeśli co najmniej jeden użytkownik wyraził już zgodę na aplikację. Aby uzyskać więcej informacji, zobacz [Jak i dlaczego aplikacje są dodawane do usługi Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Aby udzielić zgody administratora w całej dzierżawie na aplikację wymienioną w **aplikacjach dla przedsiębiorstw:**

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako [administrator globalny,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)lub administrator aplikacji [w chmurze.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Wybierz **pozycję Azure Active Directory,** a następnie **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz udzielić zgody administratora w całej dzierżawie.
4. Wybierz **pozycję Uprawnienia,** a następnie kliknij pozycję **Udziel zgody administratora**.
5. Dokładnie przejrzyj uprawnienia wymagane przez aplikację.
6. Jeśli zgadzasz się z uprawnieniami, których wymaga aplikacja, udziel zgody. Jeśli nie, kliknij przycisk **Anuluj** lub zamknij okno.

### <a name="grant-admin-consent-in-app-registrations"></a>Udzielanie zgody administratora w rejestracjach aplikacji

W przypadku aplikacji opracowanych przez organizację lub zarejestrowanych bezpośrednio w dzierżawie usługi Azure AD można również udzielić zgody administratora w całej **dzierżawie** z rejestracji aplikacji w witrynie Azure portal.

Aby udzielić zgody administratora w całej dzierżawie z **rejestracji aplikacji:**

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako [administrator globalny,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)lub administrator aplikacji [w chmurze.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Wybierz **pozycję Azure Active Directory,** a następnie **rejestracje aplikacji**.
3. Wybierz aplikację, do której chcesz udzielić zgody administratora w całej dzierżawie.
4. Wybierz **pozycję Uprawnienia interfejsu API,** a następnie kliknij pozycję **Udzielaj zgody administratora**.
5. Dokładnie przejrzyj uprawnienia wymagane przez aplikację.
6. Jeśli zgadzasz się z uprawnieniami, których wymaga aplikacja, udziel zgody. Jeśli nie, kliknij przycisk **Anuluj** lub zamknij okno.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Tworzenie adresu URL w celu udzielenia zgody administratora dla całej dzierżawy

Podczas udzielania zgody administratora całej dzierżawy przy użyciu jednej z metod opisanych powyżej, otwiera się okno z witryny Azure Portal, aby monitować o zgodę administratora dla całej dzierżawy. Jeśli znasz identyfikator klienta (znany również jako identyfikator aplikacji) aplikacji, możesz utworzyć ten sam adres URL, aby udzielić zgody administratora w całej dzierżawie.

Adres URL zgody administratora w całej dzierżawie jest zgodny z następującym formatem:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

gdzie:

* `{client-id}`jest identyfikatorem klienta aplikacji (znanym również jako identyfikator aplikacji).
* `{tenant-id}`to identyfikator dzierżawy organizacji lub dowolna zweryfikowana nazwa domeny.

Jak zawsze, dokładnie przejrzyj uprawnienia żądane przez aplikację przed udzieleniem zgody.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie sposobu wyrażania zgody użytkowników końcowych na aplikacje](configure-user-consent.md)

[Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

[Usługa Azure AD w przepływie stosuPrzepełnienie](https://stackoverflow.com/questions/tagged/azure-active-directory)
