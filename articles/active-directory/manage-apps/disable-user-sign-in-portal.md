---
title: Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak wyłączyć aplikacji dla przedsiębiorstw, dzięki czemu użytkownicy mogą zalogować się w niej w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168c3415996ff97137c4e312348f74a48e9531b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564996"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste, można wyłączyć aplikacji dla przedsiębiorstw, dzięki czemu użytkownicy mogą zalogować się w niej w usłudze Azure Active Directory (Azure AD). Konieczne jest odpowiednie uprawnienia do zarządzania aplikacji przedsiębiorstwa. Ponadto musisz być administratorem globalnym katalogu.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak wyłączyć logowania użytkowników?
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz **wszystkich usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz pozycję **Enter**.
1. Na **usługi Azure Active Directory** -  ***directoryname*** okienku (oznacza to, że usługa Azure AD dla katalogu zarządzasz), wybierz **aplikacje dla przedsiębiorstw**.
1. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** okienku można wyświetlić listę aplikacje, którymi można zarządzać. Wybierz aplikację.
1. Na ***appname*** okienku (czyli nazwę wybranej aplikacji w tytule), wybierz **właściwości**.
1. Na ***appname*** - **właściwości** okienku wybierz **nie** dla **włączono dla użytkowników do logowania?**.
1. Wybierz **Zapisz** polecenia.

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
