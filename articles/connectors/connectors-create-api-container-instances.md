---
title: Wdrażanie & zarządzanie wystąpieniami kontenerów platformy Azure
description: Automatyzuj zadania i przepływy pracy, które tworzą wdrożenia kontenerów i zarządzają nimi w wystąpieniach kontenerów platformy Azure przy użyciu aplikacji azure logic apps
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046294"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Wdrażanie wystąpień kontenerów platformy Azure i zarządzanie nimi przy użyciu aplikacji Azure Logic Apps

Za pomocą aplikacji Azure Logic Apps i łącznika wystąpienia kontenera platformy Azure można skonfigurować zautomatyzowane zadania i przepływy pracy wdrażane i zarządzające [grupami kontenerów.](../container-instances/container-instances-container-groups.md) Łącznik wystąpienia kontenera obsługuje następujące akcje:

* Tworzenie lub usuwanie grupy kontenerów
* Pobierz właściwości grupy kontenerów
* Wyświetlanie listy grup kontenerów
* Pobierz dzienniki wystąpienia kontenera

Użyj tych akcji w aplikacjach logiki dla zadań, takich jak uruchamianie obciążenia kontenera w odpowiedzi na wyzwalacz aplikacji logiki. Można również mieć inne akcje użyć danych wyjściowych z akcji wystąpienia kontenera. 

Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, należy użyć oddzielnego wyzwalacza, takiego jak wyzwalacz **cyklu,** aby uruchomić obciążenie kontenera zgodnie z regularnym harmonogramem. Lub może być konieczne wyzwolenie wdrożenia grupy kontenerów po zdarzeniu, takim jak nadejście wiadomości e-mail programu Outlook. 

Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) oraz [tworzenia wystąpień kontenerów i zarządzania nimi](../container-instances/container-instances-quickstart.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do wystąpień kontenera. Aby użyć akcji, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="add-a-container-instance-action"></a>Dodawanie akcji wystąpienia kontenera

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W obszarze ostatniego kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 

     — lub —

   * Między krokami, w których chcesz dodać akcję, przesuń wskaźnik myszy na strzałkę między krokami. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "instancję kontenera" jako filtr. W obszarze listy akcji wybierz odpowiednią akcję łącznika wystąpienia kontenera platformy Azure.

1. Podaj nazwę połączenia. 

1. Podaj szczegóły niezbędne do wybranej akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

  Na przykład wybierz **pozycję Utwórz grupę kontenerów** i wprowadź właściwości grupy kontenerów i co najmniej jednego wystąpienia kontenera w grupie, jak pokazano na poniższej ilustracji (szczegóły częściowe):

  ![Tworzenie grupy kontenerów](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/aci/) łącznika lub odwołanie do grupy kontenerów [YAML](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Następne kroki

* Zobacz [przykładową aplikację logiki,](https://github.com/Azure-Samples/aci-logicapps-integration) która uruchamia kontener w wystąpieniach kontenera platformy Azure, aby analizować tonację wiadomości e-mail lub tekstu Twittera

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)