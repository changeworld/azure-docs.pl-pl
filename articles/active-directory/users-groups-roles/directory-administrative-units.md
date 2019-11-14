---
title: Zarządzanie jednostkami administracyjnymi (wersja zapoznawcza) — Azure AD | Microsoft Docs
description: Korzystanie z jednostek administracyjnych na potrzeby bardziej szczegółowego delegowania uprawnień w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028408"
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

Aby uzyskać więcej informacji na temat wymagań programowych i instalowania modułu usługi Azure AD oraz informacje referencyjne dotyczące poleceń cmdlet modułu usługi Azure AD do zarządzania jednostkami administracyjnymi, w tym składnią, opisami parametrów i przykładami, zobacz [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Następne kroki

[Wersje Azure Active Directory](../fundamentals/active-directory-whatis.md)
