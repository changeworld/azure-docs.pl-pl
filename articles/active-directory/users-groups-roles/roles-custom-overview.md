---
title: Podgląd ról administratora platformy Azure z dostosowywalnymi uprawnieniami — Azure Active Directory | Microsoft Docs
description: Wyświetl podgląd niestandardowych ról usługi Azure AD na potrzeby delegowania zarządzania tożsamościami. Zarządzanie rolami platformy Azure w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 546abdae5d7c03bb0d4b49f9485fe06b521fdc1d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722214"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)

W tym artykule opisano sposób zrozumienia nowej niestandardowej kontroli RBAC (opartej na rolach) i zakresów zasobów w Azure Active Directory (Azure AD). Niestandardowe role RBAC to podstawowe uprawnienia [wbudowanych ról](directory-assign-admin-roles.md) , dzięki czemu można tworzyć i organizować własne role niestandardowe. Zakresy zasobów umożliwiają przypisanie roli niestandardowej do zarządzania częścią zasobów (np. jednej aplikacji) bez udzielania dostępu do wszystkich zasobów (wszystkie aplikacje).

Przyznawanie uprawnień przy użyciu niestandardowych ról RBAC jest procesem dwuetapowym. Najpierw należy utworzyć niestandardową definicję roli i dodać do niej uprawnienia z listy ustawień wstępnych. Są to te same uprawnienia, które są używane w rolach wbudowanych. Po utworzeniu roli przypiszesz ją do kogoś, tworząc przypisanie roli. Ten dwuetapowy proces umożliwia utworzenie jednej roli i przypisanie jej wiele razy w różnych zakresach. Rola niestandardowa może być przypisana w zakresie katalogu lub może być przypisana w zakresie obiektu. Przykładem zakresu obiektu jest pojedyncza aplikacja. W ten sposób można przypisać tę samą rolę do Sally przez wszystkie aplikacje w katalogu, a następnie Naveen ją tylko przez aplikację raporty wydatków firmy Contoso.

Ta pierwsza wersja niestandardowych ról RBAC obejmuje możliwość tworzenia roli w celu przypisywania uprawnień do zarządzania rejestracjami aplikacji. Z upływem czasu zostaną dodane dodatkowe uprawnienia do zasobów organizacji, takich jak aplikacje przedsiębiorstwa, użytkownicy i urządzenia.

Funkcje w wersji zapoznawczej:

- Aktualizacje interfejsu użytkownika portalu do tworzenia ról niestandardowych i zarządzania nimi oraz przypisywania ich do użytkowników w zakresie całej organizacji
- Moduł programu PowerShell w wersji zapoznawczej z nowymi poleceniami cmdlet:
  - Tworzenie ról niestandardowych i zarządzanie nimi
  - Przypisywanie ról niestandardowych z zakresem rejestracji dla całej organizacji lub dla aplikacji
  - Przypisywanie wbudowanych ról w zakresie całej organizacji (z parzystością w przypadku poleceń cmdlet o wysokiej dostępności)
  - Obsługa interfejs API programu Graph usługi Azure AD

Kontrola dostępu oparta na rolach usługi Azure AD jest publiczną funkcją w wersji zapoznawczej usługi Azure AD i jest dostępna z dowolnym płatnym planem licencjonowania usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Omówienie kontroli dostępu opartej na rolach w usłudze Azure AD

Kontrola dostępu oparta na rolach w usłudze Azure AD umożliwia przypisywanie ról, które są dostosowane do zezwalania na dozwolone akcje tylko dla jednego typu zasobu usługi Azure AD. Dostęp oparty na rolach usługi Azure AD działa w oparciu o koncepcje podobne do kontroli dostępu opartej na rolach (Azure[RBAC](../../role-based-access-control/overview.md) na potrzeby dostępu do zasobów platformy Azure), ale kontrola dostępu oparta na rolach usługi Azure AD opiera się na Microsoft Graph, a usługa Azure RBAC jest oparta na Azure Resource Manager. Jednak obydwa systemy opierają się na przypisaniach ról.

### <a name="role-assignments"></a>Przypisania ról

Sposób kontrolowania dostępu przy użyciu funkcji kontroli dostępu opartej na rolach usługi Azure AD polega na tworzeniu przypisań **ról**, które są używane do wymuszania uprawnień. Przypisanie roli składa się z trzech elementów:

- Podmiot zabezpieczeń
- Definicja roli
- Zakres zasobów

Udzielenie dostępu polega na utworzeniu przypisania roli, a odwołanie dostępu — na usunięciu przypisania roli. [Przypisania ról można tworzyć](roles-create-custom.md) przy użyciu Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph. Można oddzielnie [przeglądać przypisania dla roli niestandardowej](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Na poniższym diagramie przedstawiono przykład przypisania roli. W tym przykładzie Krzysztof zielony ma przypisaną rolę [administrator aplikacji](directory-assign-admin-roles.md#application-administrator) w zakresie aplikacji usługi Salesforce. Krzysztof nie ma dostępu do zarządzania żadną inną aplikacją, chyba że są one częścią innego przypisania roli.

![Przypisanie roli jest wymuszane i ma trzy części](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Podmiot zabezpieczeń

Podmiot zabezpieczeń reprezentuje nazwę główną użytkownika lub usługi, do której ma zostać przypisany dostęp do zasobów usługi Azure AD. *Użytkownik* to osoba, która ma profil użytkownika w Azure Active Directory. Nazwa *główna usługi* jest tożsamością zabezpieczeń używaną przez aplikacje lub usługi do uzyskiwania dostępu do określonych zasobów usługi Azure AD.

Podmiot zabezpieczeń przypomina tożsamość użytkownika w tym, że reprezentuje nazwę użytkownika i hasło lub certyfikat, ale dla aplikacji lub usługi, a nie użytkownika.

### <a name="role"></a>Role

Definicja roli lub rola jest kolekcją uprawnień. Definicja roli zawiera listę operacji, które mogą być wykonywane w zasobach usługi Azure AD, takich jak tworzenie, odczytywanie, aktualizowanie i usuwanie. Istnieją dwa typy ról w usłudze Azure AD:

- Nie można zmienić wbudowanych ról utworzonych przez firmę Microsoft. Wbudowana rola administratora globalnego ma wszystkie uprawnienia do wszystkich zasobów usługi Azure AD.
- Role niestandardowe utworzone i zarządzane przez organizację.

### <a name="scope"></a>Scope

Zakresem jest ograniczenie dozwolonych akcji w określonym zasobie usługi Azure AD. Podczas przypisywania roli można dostosować rolę w celu ograniczenia dozwolonych akcji administratora przez zdefiniowanie zakresu akcji. Na przykład, jeśli deweloperzy nie muszą w pełni zarządzać wszystkimi aplikacjami, możesz użyć niestandardowych ról usługi Azure AD, aby umożliwić im zarządzanie tylko rejestracjami aplikacji.

## <a name="required-license-plan"></a>Wymagany plan licencji

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie niestandardowych przypisań ról przy użyciu [Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](roles-create-custom.md)
- [Wyświetlanie przypisań roli niestandardowej](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
