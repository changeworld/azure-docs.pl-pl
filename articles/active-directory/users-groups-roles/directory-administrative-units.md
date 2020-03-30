---
title: Zarządzanie jednostkami administracyjnymi (wersja zapoznawcza) — azure ad | Dokumenty firmy Microsoft
description: Używanie jednostek administracyjnych do bardziej szczegółowego delegowania uprawnień w usłudze Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028408"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Zarządzanie jednostkami administracyjnymi w usłudze Azure Active Directory (wersja zapoznawcza)

W tym artykule opisano jednostki administracyjne w usłudze Azure Active Directory (Azure AD). Jednostka administracyjna to zasób usługi Azure AD, który może być kontenerem dla innych zasobów usługi Azure AD. W tej wersji zapoznawczej te zasoby mogą być tylko użytkownikami. Na przykład administrator konta użytkownika o zakresie jednostki administracyjnej może aktualizować informacje o profilu, resetować hasła i przypisywać licencje użytkownikom tylko w ich jednostce administracyjnej.

Jednostek administracyjnych można delegować uprawnienia administracyjne nad podzbiorami użytkowników i stosować zasady do podzbioru użytkowników. Jednostek administracyjnych można użyć do delegowania uprawnień do administratorów regionalnych lub do ustawiania zasad na poziomie szczegółowości.

## <a name="deployment-scenario"></a>Scenariusz wdrożenia

Jednostki administracyjne mogą być przydatne w organizacjach z niezależnymi działami. Rozważmy przykład dużej uczelni, która składa się z wielu autonomicznych szkół (School of Business, School of Engineering i tak dalej), że każdy ma własnych administratorów IT, którzy kontrolują dostęp, zarządzają użytkownikami i ustalają zasady dla swojej szkoły. Centralny administrator mógłby utworzyć jednostkę administracyjną dla Szkoły Biznesu i wypełnić ją tylko uczniami szkół biznesowych i pracownikami. Następnie administrator centralny może dodać pracowników IT szkół biznesowych do roli o określonym zakresie, która udziela uprawnień administracyjnych tylko użytkownikom usługi Azure AD w jednostce administracyjnej szkoły biznesowej.

## <a name="license-requirements"></a>Wymagania licencyjne

Aby używać jednostek administracyjnych wymaga licencji usługi Azure Active Directory Premium dla każdego administratora jednostki administracyjnej. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Zarządzanie jednostkami administracyjnymi

W tej wersji zapoznawczej jedynym sposobem tworzenia jednostek administracyjnych i zarządzania nimi jest użycie modułu cmdlet usługi Azure Active Directory dla programów Windows PowerShell zgodnie z opisem w [obszarze Praca z jednostkami administracyjnymi](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Aby uzyskać więcej informacji na temat wymagań dotyczących oprogramowania i instalowania modułu usługi Azure AD oraz informacje referencyjne dotyczące poleceń cmdlet modułu usługi Azure AD do zarządzania jednostkami administracyjnymi, w tym składni, opisów parametrów i przykładów, zobacz [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Następne kroki

[Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md)
