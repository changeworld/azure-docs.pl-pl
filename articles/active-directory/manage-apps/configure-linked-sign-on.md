---
title: Połączone logowanie dla aplikacji usługi Azure AD — platforma tożsamości firmy Microsoft
description: Konfigurowanie połączonego logowania jednokrotnego (Logowania jednokrotnego) do aplikacji dla przedsiębiorstw usługi Azure AD na platformie tożsamości firmy Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063547"
---
# <a name="configure-linked-sign-on"></a>Konfigurowanie połączonego logowania

Po dodaniu aplikacji sieci web galerii lub spoza galerii jedną z dostępnych opcji logowania jednokrotnego jest [połączone logowanie](what-is-single-sign-on.md). Wybierz tę opcję, aby dodać łącze do aplikacji w panelu dostępu usługi Azure AD w organizacji lub w portalu usługi Office 365. Za pomocą tej metody można dodać łącza do niestandardowych aplikacji sieci Web, które obecnie używają usług federacyjnych Active Directory (lub innej usługi federacyjnej) zamiast usługi Azure AD do uwierzytelniania. Można też dodać głębokie łącza do określonych stron programu SharePoint lub innych stron sieci Web, które mają być wyświetlane w panelach dostępu użytkownika.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Dodawanie aplikacji galerii](add-gallery-app.md) lub [Dodawanie aplikacji spoza galerii.](add-non-gallery-app.md)

### <a name="open-the-app-and-select-linked-sign-on"></a>Otwórz aplikację i wybierz połączone logowanie

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

1. Przejdź do **aplikacji Azure Active Directory** > **Enterprise**. Pojawi się losowa próbka aplikacji w dzierżawie usługi Azure AD. 

1. W menu **Typ aplikacji** wybierz polecenie **Wszystkie aplikacje**, a następnie wybierz polecenie **Zastosuj**.

1. Wprowadź nazwę aplikacji w polu wyszukiwania, a następnie wybierz aplikację z wyników.

1. W sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**. 

1. Wybierz **pozycję Połączone**.

1. Wprowadź adres URL aplikacji, do z linkiem. Wpisz adres URL i wybierz pozycję **Zapisz**. 
 
1. Można przypisać użytkowników i grupy do aplikacji, co powoduje, że aplikacja ma być wyświetlana w [programie uruchamiania aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panelu dostępu usługi Azure AD](end-user-experiences.md) dla tych użytkowników.

1. Wybierz **pozycję Zapisz**.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md)
- [Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
