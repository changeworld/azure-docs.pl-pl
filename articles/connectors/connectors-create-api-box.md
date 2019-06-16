---
title: Łączenie z polem — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie plikami za pomocą interfejsów API REST usługi Box i Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 971d38fa0fbd47f0deb815577033bbe684aac32f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312580"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Tworzenie i zarządzanie plikami w usłudze Box z usługą Azure Logic Apps

W tym artykule przedstawiono sposób tworzenia i zarządzania plikami w polu z wewnątrz aplikacji logiki z łącznikiem usługi Box. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływów pracy związanych z zarządzaniem plików, a inne czynności, na przykład:

* Tworzenie przepływu biznesowych na podstawie danych, który jest pobierany z usługi Box.

* Wyzwalanie automatycznych zadań i przepływ pracy, gdy plik jest tworzony lub aktualizowany.

* Wykonywanie operacji, które służy do kopiowania pliku, powoduje usunięcie pliku i nie tylko.

  Jeśli te akcje uzyskać odpowiedź, ich udostępnić dane wyjściowe dla innych akcji. 
  Na przykład gdy plik zostanie zmieniona w usłudze Box, możesz wysłać ten plik w wiadomości e-mail przy użyciu usługi Office 365.

## <a name="prerequisites"></a>Wymagania wstępne

* A [polu konta](https://www.box.com/home)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta w polu. Aby uruchomić aplikację logiki z wyzwalaczem pole, należy [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Podstawową wiedzę na temat o [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w łącznika interfejsu OpenAPI (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/box/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)