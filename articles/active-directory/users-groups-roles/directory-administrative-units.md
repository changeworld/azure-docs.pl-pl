---
title: Zarządzanie jednostkami administracyjnymi (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Korzystanie z jednostek administracyjnych na potrzeby bardziej szczegółowego delegowania uprawnień w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336921"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Zarządzanie jednostkami administracyjnymi w Azure Active Directory (wersja zapoznawcza)

W tym artykule opisano jednostki administracyjne w Azure Active Directory (Azure AD). Jednostka administracyjna to zasób usługi Azure AD, który może być kontenerem dla innych zasobów usługi Azure AD. W tej wersji zapoznawczej te zasoby mogą być tylko użytkownikami. Na przykład administrator konta użytkownika z zakresem jednostki administracyjnej może zaktualizować informacje o profilu, zresetować hasła i przypisać licencje dla użytkowników tylko w ich jednostce administracyjnej.

Za pomocą jednostek administracyjnych można delegować uprawnienia administracyjne w ramach podzestawów użytkowników i stosować zasady do podzbioru użytkowników. Jednostki administracyjne mogą służyć do delegowania uprawnień do administratorów regionalnych lub do ustawiania zasad na poziomie szczegółowym.

## <a name="deployment-scenario"></a>Scenariusz wdrażania

Jednostki administracyjne mogą być przydatne w organizacjach z niezależnymi oddziałami. Rozważmy przykład dużej uczelni, która składa się z wielu szkół autonomicznych (szkoły, szkoły inżynierów itp.), z których każdy ma własnych administratorów IT kontrolujących dostęp, zarządzanie użytkownikami i Ustawianie zasad dla swojej szkoły. Administrator centralny może utworzyć jednostkę administracyjną dla szkoły biznesowej i wypełnić ją tylko uczniami i personelem szkoły szkolnej. Następnie administrator centralny może dodać służbowy personel działu IT do roli o określonym zakresie, która przyznaje uprawnienia administracyjne wyłącznie użytkownikom usługi Azure AD w jednostce administracyjnej szkoły biznesowej.

## <a name="license-requirements"></a>Wymagania licencyjne

Aby korzystanie z jednostek administracyjnych wymagało Azure Active Directory — wersja Premium licencji dla każdego administratora jednostki administracyjnej. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Zarządzanie jednostkami administracyjnymi

W tej wersji zapoznawczej jedynym sposobem tworzenia i zarządzania jednostkami administracyjnymi jest użycie modułu Azure Active Directory dla poleceń cmdlet programu Windows PowerShell, zgodnie z opisem w temacie [Praca z jednostkami administracyjnymi](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) .

Aby uzyskać więcej informacji na temat wymagań programowych i instalowania modułu usługi Azure AD oraz informacje referencyjne dotyczące poleceń cmdlet modułu usługi Azure AD do zarządzania jednostkami administracyjnymi, w tym składnią, opisami parametrów i przykładami, zobacz [Azure Active Katalog programu PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Następne kroki

[Wersje Azure Active Directory](../fundamentals/active-directory-whatis.md)
