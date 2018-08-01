---
title: Jak usunąć dostęp użytkownika do aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć dostęp użytkownika do aplikacji
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0deb5215c1379ac552a492f4b9e90df83201aebf
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364485"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak usunąć dostęp użytkownika do aplikacji

Ten artykuł ułatwia zrozumienie, jak usunąć dostęp użytkownika do aplikacji.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Aby usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć użytkownika lub przypisania grupy do aplikacji, wykonaj czynności opisane w [usunąć przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artykułu.

. ## chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkowników do aplikacji, wykonaj czynności opisane w [wyłączyć logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artykułu.

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę, aby całkowicie usunąć aplikację

Aby **usunąć aplikację**, wykonaj te instrukcje:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, którą chcesz usunąć.

7.  Po załadowaniu aplikacji, kliknij przycisk **Usuń** ikonę z najważniejszych aplikacji **Przegląd** okienka.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie operacje zgody użytkownika przyszłych do dowolnej aplikacji

Wyłączanie zgody użytkownika dla całego katalogu uniemożliwić użytkownikom wyrażanie zgody na dowolnej aplikacji. Administratorzy nadal mogą wyrazić zgodę na behalves użytkownika. Aby dowiedzieć się więcej na temat zgody aplikacji i dlatego może lub nie chcesz to zrobić, przeczytaj [opis użytkowników i zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Aby **Wyłącz wszystkie operacje zgody użytkownika przyszłych całego katalogu**, wykonaj te instrukcje:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **ustawienia użytkownika**.

6.  Wyłącz wszystkie operacje zgody użytkownika w przyszłości, ustawiając **użytkownicy mogą zezwalać aplikacjom na dostęp do swoich danych na** Przełącz, aby **nie** i kliknij przycisk **Zapisz** przycisku.


# <a name="next-steps"></a>Kolejne kroki
[Zarządzanie dostępem do aplikacji](manage-apps/what-is-access-management.md)
