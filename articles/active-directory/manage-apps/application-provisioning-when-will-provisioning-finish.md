---
title: Przypisywanie użytkowników do aplikacji galerii usługi Azure AD jest całymi godzinami | Dokumentacja firmy Microsoft
description: Jak ustalić, dlaczego zainicjowania obsługi administracyjnej aplikacji może trwać dłużej niż oczekiwano
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 94859aa57ef6f565c1fe7ed4b199aff997661d0b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478087"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Przypisywanie użytkowników do aplikacji galerii usługi Azure AD jest całymi godzinami

Po pierwszym włączeniu automatycznej aprowizacji dla aplikacji początkowej synchronizacji może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie udostępniania. 

Kolejne synchronizacje po synchronizacji początkowej można szybciej, jak usługa aprowizowania magazyny znaki wodne, które reprezentują stanu obu systemów po początkowej synchronizacji, zwiększanie wydajności kolejne synchronizacje.

## <a name="how-to-improve-provisioning-performance"></a>Jak poprawić wydajność inicjowania obsługi administracyjnej

Jeśli synchronizacja początkowa trwa więcej niż kilka godzin, ma jedną z rzeczy, które można wykonać w celu zwiększenia wydajności:

-   **Filtrów określania zakresu użytkowników.** Filtrów określania zakresu umożliwiają dostosowanie dane usługi aprowizowania wyodrębnia z usługi Azure AD przez filtrowanie na podstawie wartości atrybutu określonych użytkowników. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz [aprowizacja aplikacji opartych na atrybutach z filtrami zakresu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Kolejne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)

