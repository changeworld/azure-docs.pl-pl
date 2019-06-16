---
title: Jak usunąć dostęp użytkownika do aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć dostęp użytkownika do aplikacji
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65826098"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak usunąć dostęp użytkownika do aplikacji

Ten artykuł ułatwia zrozumienie, jak usunąć dostęp użytkownika do aplikacji.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Aby usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć użytkownika lub przypisania grupy do aplikacji, wykonaj czynności opisane w [usunąć przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artykułu.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkowników do aplikacji, wykonaj czynności opisane w [wyłączyć logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artykułu.

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę, aby całkowicie usunąć aplikację

Aby **usunąć aplikację**, wykonaj te instrukcje:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz usunąć.

7. Po załadowaniu aplikacji, kliknij przycisk **Usuń** ikonę z najważniejszych aplikacji **Przegląd** okienka.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie operacje zgody użytkownika przyszłych do dowolnej aplikacji

Wyłączanie zgody użytkownika dla całego katalogu uniemożliwić użytkownikom wyrażanie zgody na dowolnej aplikacji. Administratorzy nadal mogą wyrazić zgodę w imieniu użytkownika. Aby uzyskać więcej informacji na temat aplikacji zgody i dlatego może lub nie chcesz to zrobić, przeczytaj [opis użytkowników i zgody administratora](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Zobacz też [uprawnienia i zgody](../develop/v2-permissions-and-consent.md).

Aby **Wyłącz wszystkie operacje zgody użytkownika przyszłych całego katalogu**, wykonaj te instrukcje:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** 

3.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  Kliknij przycisk **ustawienia użytkownika**.

6.  Ustaw **użytkownicy mogą zezwalać aplikacjom na dostęp do danych firmy w ich imieniu na** Przełącz, aby **nie** i kliknij przycisk Zapisz.


## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie dostępem do aplikacji](what-is-access-management.md)
