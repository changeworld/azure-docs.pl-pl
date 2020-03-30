---
title: Niestandardowe role administratora w usłudze Azure Active Directory | Dokumenty firmy Microsoft
description: Wyświetlanie podglądu niestandardowych ról usługi Azure AD do delegowannia zarządzania tożsamościami. Zarządzanie rolami platformy Azure w witrynie Azure portal, powershell lub graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5c7919dcc89e34831cb4cae7921b60b35eb4c69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024961"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Niestandardowe role administratora w usłudze Azure Active Directory (wersja zapoznawcza)

W tym artykule opisano sposób rozumienia ról niestandardowych usługi Azure AD w usłudze Azure Active Directory (Azure AD) z kontrolą dostępu opartymi na rolach i zakresami zasobów. Niestandardowe role usługi Azure AD powierzchni podstawowe uprawnienia [wbudowanych ról,](directory-assign-admin-roles.md)dzięki czemu można tworzyć i organizować własne role niestandardowe. Takie podejście umożliwia udzielenie dostępu w sposób bardziej szczegółowy niż wbudowane role, gdy są one potrzebne. Ta pierwsza wersja ról niestandardowych usługi Azure AD zawiera możliwość tworzenia roli przypisywania uprawnień do zarządzania rejestracjami aplikacji. Z biegiem czasu zostaną dodane dodatkowe uprawnienia do zasobów organizacji, takich jak aplikacje dla przedsiębiorstw, użytkownicy i urządzenia.  

Ponadto role niestandardowe usługi Azure AD obsługują przydziały dla zasobów, oprócz bardziej tradycyjnych przypisań dla całej organizacji. Takie podejście umożliwia udzielenie dostępu do zarządzania niektórymi zasobami (na przykład jedną rejestracją aplikacji) bez udzielania dostępu do wszystkich zasobów (wszystkie rejestracje aplikacji).

Kontrola dostępu oparta na rolach usługi Azure AD jest publiczną funkcją w wersji zapoznawczej usługi Azure AD i jest dostępna z dowolnym płatnym planem licencji usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Opis kontroli dostępu opartej na rolach usługi Azure AD

Udzielanie uprawnień przy użyciu niestandardowych ról usługi Azure AD jest procesem dwuetapowym, który polega na tworzeniu niestandardowej definicji roli, a następnie przypisywaniu jej przy użyciu przypisania roli. Niestandardowa definicja roli to zbiór uprawnień dodawanych z listy ustawień predefiniowanych. Te uprawnienia są te same uprawnienia używane w rolach wbudowanych.  

Po utworzeniu definicji roli można przypisać ją do użytkownika, tworząc przypisanie roli. Przypisanie roli udziela użytkownikowi uprawnień w definicji roli w określonym zakresie. Ten dwuetapowy proces umożliwia utworzenie definicji pojedynczej roli i przypisanie jej wiele razy w różnych zakresach. Zakres definiuje zestaw zasobów usługi Azure AD, do które ma dostęp członek roli. Najczęstszym zakresem jest zakres całej organizacji (w całej organizacji). Rola niestandardowa może być przypisana w zakresie całej organizacji, co oznacza, że element członkowski roli ma uprawnienia roli do wszystkich zasobów w organizacji. Rolę niestandardową można również przypisać w zakresie obiektu. Przykładem zakresu obiektu może być pojedyncza aplikacja. Tę samą rolę można przypisać do jednego użytkownika we wszystkich aplikacjach w organizacji, a następnie do innego użytkownika z zakresem tylko contoso raporty wydatków aplikacji.  

Wbudowane i niestandardowe role usługi Azure AD działają na pojęciach podobnych do [kontroli dostępu opartej na rolach platformy Azure.](../../role-based-access-control/overview.md) [Różnica między tymi dwoma systemami kontroli dostępu opartych na rolach](../../role-based-access-control/rbac-and-directory-admin-roles.md) polega na tym, że usługa Azure RBAC kontroluje dostęp do zasobów platformy Azure, takich jak maszyny wirtualne lub magazyn przy użyciu usługi Azure Resource Management, a role niestandardowe usługi Azure AD kontrolują dostęp do zasobów usługi Azure AD przy użyciu interfejsu API programu Graph. Oba systemy wykorzystują pojęcie definicji ról i przypisań ról.

### <a name="role-assignments"></a>Przypisania ról

Przypisanie roli jest obiektem, który dołącza definicję roli do użytkownika w określonym zakresie, aby udzielić dostępu do zasobów usługi Azure AD. Udzielenie dostępu polega na utworzeniu przypisania roli, a odwołanie dostępu — na usunięciu przypisania roli. W swej istocie przypisanie roli składa się z trzech elementów:

- Użytkownik (osoba posiadająca profil użytkownika w usłudze Azure Active Directory)
- Definicja roli
- Zakres zasobu

[Przypisania ról](roles-create-custom.md) można tworzyć przy użyciu witryny Azure Portal, Azure AD PowerShell lub Graph API. Można również [wyświetlić przydziały dla roli niestandardowej](roles-view-assignments.md#view-the-assignments-of-a-role).

Na poniższym diagramie przedstawiono przykład przypisania roli. W tym przykładzie Chrisowi Greenowi przypisano rolę niestandardową administratora rejestracji aplikacji w zakresie rejestracji aplikacji Konstruktora widżetów Contoso. Przypisanie przyznaje Chris uprawnienia roli administratora rejestracji aplikacji tylko dla tej rejestracji określonej aplikacji.

![Przypisanie roli to sposób wymuszania uprawnień i trzy części](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Podmiot zabezpieczeń

Podmiot zabezpieczeń reprezentuje użytkownika, który ma mieć przypisany dostęp do zasobów usługi Azure AD. *Użytkownik* to osoba, która ma profil użytkownika w usłudze Azure Active Directory.

### <a name="role"></a>Rola

Definicja roli lub rola jest kolekcją uprawnień. Definicja roli zawiera listę operacji, które można wykonać na zasobach usługi Azure AD, takich jak tworzenie, odczytywanie, aktualizowanie i usuwanie. Istnieją dwa typy ról w usłudze Azure AD:

- Wbudowane role utworzone przez firmę Microsoft, których nie można zmienić.
- Role niestandardowe utworzone i zarządzane przez organizację.

### <a name="scope"></a>Zakres

Zakres jest ograniczeniem dozwolonych akcji do określonego zasobu usługi Azure AD jako część przypisania roli. Po przypisaniu roli można określić zakres, który ogranicza dostęp administratora do określonego zasobu. Na przykład jeśli chcesz udzielić deweloperowi roli niestandardowej, ale tylko do zarządzania rejestracją określonej aplikacji, można dołączyć rejestrację określonej aplikacji jako zakres w przypisaniu roli.

  > [!Note]
  > Role niestandardowe można przypisać w zakresie katalogu i zakresie zasobów. Nie można ich jeszcze przypisać w zakresie jednostki administracyjnej.
  > Wbudowane role można przypisać w zakresie katalogu, a w niektórych przypadkach w zakresie jednostki administracyjnej. Nie można ich jeszcze przypisać do zakresu zasobów usługi Azure AD.

## <a name="required-license-plan"></a>Wymagany plan licencji

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Następne kroki

- Tworzenie niestandardowych przypisań ról przy użyciu [witryny Azure Portal, usługi Azure AD PowerShell i interfejsu API graph](roles-create-custom.md)
- [Wyświetlanie przydziałów dla roli niestandardowej](roles-view-assignments.md#view-assignments-of-single-application-scope)
