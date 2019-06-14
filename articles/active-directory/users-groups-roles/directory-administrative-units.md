---
title: Zarządzanie jednostkami administracyjnymi (wersja zapoznawcza) — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Za pomocą jednostek administracyjnych dla bardziej szczegółowego delegowania uprawnień w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f1a6e5b1e8191c1497e437cc26e1caf1255ba7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472371"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Zarządzanie jednostkami administracyjnymi w usłudze Azure Active Directory (publiczna wersja zapoznawcza)

W tym artykule opisano jednostki administracyjne — nowy kontener usługi Azure Active Directory (Azure AD), zasobów, które mogą służyć do delegowania uprawnień administracyjnych za pośrednictwem podzbiór użytkowników i stosowanie zasad do podzbioru użytkowników. W usłudze Azure Active Directory jednostek administracyjnych, Włącz Administratorzy central, aby delegować uprawnienia do administratorów regionalnych oraz ustawiania zasad na poziomie szczegółowym.

Jest to przydatne w przypadku organizacji z niezależnych działy, na przykład university dużych, który składa się z wielu szkół autonomicznego (firmy, szkoły, służbowych inżynierii i tak dalej), niezależne od siebie nawzajem. Takie przegrody ma własnych administratorów IT, którzy kontroli dostępu, zarządzanie użytkownikami i ustawić zasady dla ich podział. Chcesz Administratorzy Central, aby można było udzielić tych oddziałów uprawnienia administratorów przez użytkowników w ich określonego działów. Dokładniej korzystając z tego przykładu, głównym administratorem można, na przykład tworzenie jednostki administracyjnej dla konkretnego szkoły (Business szkoły) i wypełnianie jej tylko użytkownicy biznesowi szkoły. Oznacza to, głównym administratorem można dodać służbowego firmy pracowników wsparcia informatycznego roli o określonym zakresie, następnie przyznać pracowników wsparcia informatycznego uprawnień administracyjnych school firm tylko za pośrednictwem jednostki administracyjne school biznesowej.

> [!IMPORTANT]
> Aby użyć jednostek administracyjnych wymaga od administratora o określonym zakresie jednostki administracyjnej mają licencję usługi Azure Active Directory Premium i licencje usługi Azure Active Directory w warstwie podstawowa dla wszystkich użytkowników w jednostce administracyjnej. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>


Z punktu widzenia administratora centralnego jednostki administracyjnej jest obiekt katalogu, który może zostać utworzony i wypełniony zasobów. **W tej wersji zapoznawczej te zasoby może być tylko użytkownicy.** Po utworzeniu i wypełnieniu jednostki administracyjnej może służyć jako zasięg ograniczyć uprawnienia udzielone tylko za pośrednictwem zasobów znajdujących się w jednostce administracyjnej.

## <a name="managing-administrative-units"></a>Zarządzanie jednostkami administracyjnymi
W tej wersji zapoznawczej możesz tworzyć i zarządzać jednostek administracyjnych przy użyciu poleceń cmdlet usługi Azure Active Directory modułu dla Windows PowerShell. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [Praca z jednostkami administracyjnymi](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Aby uzyskać więcej informacji na temat wymagań programowych i instalowanie modułu Azure AD i uzyskać informacji na temat polecenia cmdlet modułu usługi Azure AD, do zarządzania jednostkami administracyjnymi składni, opisy parametrów i przykłady, zobacz [usługi Azure Active PowerShell katalogu](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Kolejne kroki
[Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md)
