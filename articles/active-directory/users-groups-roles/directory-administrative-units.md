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
ms.openlocfilehash: 7b4bdced50f806367a53881d5ef0abd0a3710496
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736786"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Zarządzanie jednostkami administracyjnymi w Azure Active Directory (publiczna wersja zapoznawcza)

W tym artykule opisano jednostki administracyjne w Azure Active Directory (Azure AD), które są kontenerami zasobów, które mogą służyć do delegowania uprawnień administracyjnych w ramach podzestawów użytkowników i stosowania zasad do podzbioru użytkowników. W usłudze Azure AD jednostki administracyjne umożliwiają administratorom centralnym delegowanie uprawnień do administratorów regionalnych lub Ustawianie zasad na poziomie szczegółowości.

Jest to przydatne w organizacjach z niezależnymi oddziałami, na przykład z dużą uczelnią składającą się z wielu szkół autonomicznych (szkoły roboczej, szkoły inżynieryjnej itd.), które są niezależne od siebie nawzajem. Takie działy mają własnych administratorów IT kontrolujących dostęp, zarządzanie użytkownikami i Ustawianie zasad przeznaczonych dla ich działu. Administratorzy centralni chcą udzielić tych uprawnień administratorom, którzy korzystają z ich określonych podziałów. W szczególności przy użyciu tego przykładu administrator centralny może na przykład utworzyć jednostkę administracyjną dla określonej szkoły (szkoły służbowej) i wypełnić ją tylko użytkownikami szkoły. Następnie administrator centralny może dodać służbowy personel działu IT do roli o określonym zakresie, innymi słowy, przyznać personelowi IT uprawnień administracyjnych tylko za pośrednictwem jednostki administracyjnej szkoły roboczej.

> [!IMPORTANT]
> Aby można było korzystać z jednostek administracyjnych, administrator jednostki administracyjnej musi mieć licencję Azure Active Directory — wersja Premiumową. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md).
>

Z punktu widzenia administratora centralnego jednostka administracyjna to obiekt katalogu, który można utworzyć i wypełnić zasobami. **W tej wersji zapoznawczej te zasoby mogą być tylko użytkownikami.** Po utworzeniu i wypełnieniu jednostka administracyjna może służyć jako zakres ograniczania przyznanych uprawnień tylko do zasobów znajdujących się w jednostce administracyjnej.

## <a name="managing-administrative-units"></a>Zarządzanie jednostkami administracyjnymi

W tej wersji zapoznawczej można tworzyć jednostki administracyjne i zarządzać nimi przy użyciu modułu Azure Active Directory dla poleceń cmdlet programu Windows PowerShell. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [Praca z jednostkami administracyjnymi](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Aby uzyskać więcej informacji na temat wymagań programowych i instalowania modułu usługi Azure AD oraz informacje o poleceniach cmdlet modułu usługi Azure AD do zarządzania jednostkami administracyjnymi, w tym składnią, opisami parametrów i przykładami, zobacz [Azure Active Directory Program PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Następne kroki

[Wersje Azure Active Directory](../fundamentals/active-directory-whatis.md)
