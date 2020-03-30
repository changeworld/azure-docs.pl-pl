---
title: Autoryzowanie dostępu do konfiguracji aplikacji platformy Azure przy użyciu usługi Azure Active Directory
description: Włącz funkcję RBAC, aby autoryzować dostęp do wystąpienia konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472624"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autoryzowanie dostępu do konfiguracji aplikacji platformy Azure przy użyciu usługi Azure Active Directory
Usługa Azure App Configuration obsługuje używanie usługi Azure Active Directory (Azure AD) do autoryzowania żądań do wystąpień konfiguracji aplikacji.  Usługa Azure AD umożliwia korzystanie z kontroli dostępu opartej na rolach (RBAC) w celu przyznania uprawnień podmiotowi zabezpieczeń.  Podmiot zabezpieczeń może być użytkownikiem lub [podmiotem usługi aplikacji.](../active-directory/develop/app-objects-and-service-principals.md)  Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [Opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Żądania złożone przez podmiot zabezpieczeń (użytkownika lub aplikację) w celu uzyskania dostępu do zasobu konfiguracji aplikacji muszą być autoryzowane.  Dzięki usłudze Azure AD dostęp do zasobu jest procesem dwuetapowym.
1. Tożsamość podmiotu zabezpieczeń jest uwierzytelniona i zwracany jest token OAuth 2.0.  Nazwa zasobu, aby `https://login.microsoftonline.com/{tenantID}` zażądać tokenu, jest zgodna `{tenantID}` z identyfikatorem dzierżawy usługi Azure Active Directory, do którego należy podmiot usługi.
2. Token jest przekazywany jako część żądania do usługi konfiguracji aplikacji w celu autoryzacji dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawiera token dostępu OAuth 2.0 w czasie wykonywania.  Jeśli aplikacja jest uruchomiona w jednostce platformy Azure, takiej jak aplikacja usługi Azure Functions, aplikacja Azure Web App lub maszyna wirtualna platformy Azure, może użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobów.  Aby dowiedzieć się, jak uwierzytelniać żądania złożone przez tożsamość zarządzaną w konfiguracji aplikacji platformy Azure, zobacz [Uwierzytelnij dostęp do zasobów konfiguracji aplikacji platformy Azure za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure.](howto-integrate-azure-managed-service-identity.md)

Krok autoryzacji wymaga przypisania co najmniej jednej roli RBAC do podmiotu zabezpieczeń. Usługa Azure App Configuration udostępnia role RBAC, które obejmują zestawy uprawnień dla zasobów konfiguracji aplikacji. Role, które są przypisane do podmiotu zabezpieczeń określają uprawnienia dostarczone do podmiotu zabezpieczeń. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [Wbudowane role RBAC dla konfiguracji aplikacji platformy Azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC dla praw dostępu
Usługa Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC).](../role-based-access-control/overview.md)

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Program Access jest objęty zakresem zasobu Konfiguracja aplikacji. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem lub podmiotem usługi aplikacji lub [tożsamością zarządzaną dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Wbudowane role RBAC dla konfiguracji aplikacji platformy Azure
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych konfiguracji aplikacji przy użyciu usługi Azure AD i OAuth:

- Właściciel danych konfiguracji aplikacji platformy Azure: użyj tej roli, aby zapewnić dostęp do odczytu/zapisu do zasobów konfiguracji aplikacji.
- Czytnik danych konfiguracji aplikacji platformy Azure: użyj tej roli, aby zapewnić dostęp do odczytu zasobów konfiguracji aplikacji.
- Współautor: Użyj tej roli, aby dać administratorowi dostęp do usługi bez udzielania dostępu do danych przechowywanych w wystąpieniu konfiguracji aplikacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o administrowaniu usługą konfiguracji aplikacji przy użyciu [tożsamości zarządzanych.](howto-integrate-azure-managed-service-identity.md)