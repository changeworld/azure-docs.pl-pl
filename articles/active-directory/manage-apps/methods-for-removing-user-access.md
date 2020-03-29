---
title: Jak usunąć dostęp użytkownika do aplikacji | Dokumenty firmy Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65826098"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak usunąć dostęp użytkownika do aplikacji

Ten artykuł pomaga zrozumieć, jak usunąć dostęp użytkownika do aplikacji.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcę usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie użytkownika lub grupy do aplikacji, wykonaj kroki wymienione w [artykule Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć cały dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkowników do aplikacji, wykonaj kroki wymienione w [artykule Wyłącz logowanie użytkowników dla aplikacji dla przedsiębiorstwa w usłudze Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę całkowicie usunąć aplikację

Aby **usunąć aplikację,** postępuj zgodnie z poniższymi instrukcjami:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. Kliknij **polecenie Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz usunąć.

7. Po załadowaniu aplikacji kliknij ikonę **Usuń** z górnego okienka **Przegląd** aplikacji.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie przyszłe operacje zgody użytkownika do dowolnej aplikacji

Wyłączenie zgody użytkownika na cały katalog uniemożliwia użytkownikom końcowym wyrażenie zgody na dowolną aplikację. Administratorzy mogą nadal wyrażać zgodę w imieniu użytkownika. Aby uzyskać więcej informacji na temat zgody aplikacji i dlaczego możesz lub nie chcesz tego robić, przeczytaj [Opis zgody użytkownika i administratora](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Zobacz też uprawnienia [i zgoda](../develop/v2-permissions-and-consent.md).

Aby **wyłączyć wszystkie przyszłe operacje zgody użytkownika w całym katalogu, postępuj**zgodnie z poniższymi instrukcjami:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwieranie **rozszerzenia usługi Azure Active Directory** 

3.  W menu **nawigacyjnym kliknij polecenie Aplikacje przedsiębiorstwa.**

5.  Kliknij **pozycję Ustawienia użytkownika**.

6.  Ustaw **użytkownicy mogą zezwolić aplikacjom na dostęp do danych firmy w ich imieniu,** przełącz się na **Nie** i kliknij przycisk Zapisz.


## <a name="next-steps"></a>Następne kroki

[Zarządzanie dostępem do aplikacji](what-is-access-management.md)
