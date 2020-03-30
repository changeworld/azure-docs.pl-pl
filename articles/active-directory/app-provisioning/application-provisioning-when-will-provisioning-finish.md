---
title: Inicjowanie obsługi administracyjnej aplikacji usługi Azure AD Gallery trwa wiele godzin lub więcej
description: Jak dowiedzieć się, dlaczego inicjowanie obsługi administracyjnej aplikacji może trwać dłużej niż oczekiwano
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522649"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Inicjowanie obsługi administracyjnej aplikacji usługi Azure AD Gallery trwa wiele godzin lub więcej

Po pierwszym włączeniu automatycznego inicjowania obsługi administracyjnej dla aplikacji, początkowy cykl może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie inicjowania obsługi administracyjnej. 

Kolejne synchronizacje po początkowym cyklu być szybsze, jak usługa inicjowania obsługi administracyjnej przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym, zwiększając wydajność kolejnych synchronizacji.

## <a name="how-to-improve-provisioning-performance"></a>Jak poprawić wydajność inicjowania obsługi administracyjnej

Jeśli początkowy cykl trwa dłużej niż kilka godzin, jest jedna rzecz, którą możesz zrobić, aby poprawić wydajność:

-   **Filtry zakresu użytkownika.** Filtry zakresu umożliwiają precyzyjne dostrojenie danych wyodrębnianych przez usługę inicjowania obsługi administracyjnej z usługi Azure AD przez odfiltrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat filtrów zakresu, zobacz [Inicjowanie obsługi administracyjnej aplikacji oparte na atrybutach za pomocą filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)

