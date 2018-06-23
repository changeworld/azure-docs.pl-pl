---
title: Jak usunąć dostępu użytkownika do aplikacji | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu usuwania dostępu użytkownika do aplikacji
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: e227f46db05c108c27322e8dd76177cf9849c937
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330336"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak usunąć dostępu użytkownika do aplikacji

Ten artykuł pomaga zrozumieć sposób usuwania dostępu użytkownika do aplikacji.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie grupy do aplikacji lub użytkownika, wykonaj czynności opisane w [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artykułu.

. ## chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkownika do aplikacji, wykonaj czynności opisane w [wyłączyć logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artykułu.

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę, aby całkowicie usunąć aplikację

Aby **usunąć aplikację**, wykonaj następujące instrukcje:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, którą chcesz usunąć.

7.  Po załadowaniu aplikacji, kliknij przycisk **usunąć** ikony z najwyższym aplikacji **omówienie** okienka.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie operacje zgody przyszłych użytkownika do aplikacji

Wyłączanie zgody użytkownika dla całego katalogu uniemożliwić użytkownikom końcowym zgodę dowolnej aplikacji. Administratorzy mogą nadal oznacza zgodę na behalves użytkownika. Dowiedz się więcej o zgodę aplikacji i dlaczego może lub nie chcesz to zrobić, przeczytaj [zgody administratora i użytkownika opis](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Aby **Wyłącz wszystkie operacje zgody użytkownika w przyszłości w katalogu cały**, wykonaj następujące instrukcje:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **ustawienia użytkownika**.

6.  Wyłącz wszystkie operacje zgody użytkownika w przyszłości przez ustawienie **użytkownicy mogą zezwolić aplikacjom na dostęp do danych** Przełącz, aby **nr** i kliknij przycisk **zapisać** przycisku.


# <a name="next-steps"></a>Kolejne kroki
[Zarządzanie dostępem do aplikacji](manage-apps/what-is-access-management.md)
