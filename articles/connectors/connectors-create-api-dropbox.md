---
title: Łączenie z usługą Dropbox — Azure Logic Apps
description: Przekazywanie plików i zarządzanie nimi za pomocą interfejsów API REST usługi Dropbox i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312562"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Przekazywanie plików i zarządzanie nimi w usłudze Dropbox za pomocą usługi Azure Logic Apps

Łącznik usługi Dropbox i Azure Logic Apps umożliwia tworzenie zautomatyzowanych przepływów pracy i zarządzanie plikami na Twoim koncie Dropbox. 

W tym artykule pokazano, jak nawiązać połączenie usługi Dropbox z aplikacji logiki, a następnie dodaj usługi Dropbox **po utworzeniu pliku** wyzwalacza i Dropbox **Pobierz zawartość pliku przy użyciu ścieżki** akcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* A [konta usługi Dropbox](https://www.dropbox.com/), której można zarejestrować za darmo. Poświadczenia konta są niezbędne do utworzenia połączenia między aplikacją logiki i kontem usługi Dropbox.

* Podstawową wiedzę na temat o [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). W tym przykładzie należy pustej aplikacji logiki.

## <a name="add-trigger"></a>Dodaj wyzwalacz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wprowadź "dropbox" jako filtr.
Z listy wyzwalaczy wybierz następujący wyzwalacz: **Po utworzeniu pliku**

   ![Wybieranie wyzwalacza usługi Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Zaloguj się przy użyciu poświadczeń konta usługi Dropbox i autoryzować dostęp do danych usługi Dropbox do usługi Azure Logic Apps.

1. Podaj wymagane informacje dotyczące wyzwalacza. 

   W tym przykładzie wybierz folder, w którym chcesz ją śledzić tworzenia pliku. Aby przeglądać folderów, wybierz ikonę folderu **folderu** pole.

## <a name="add-action"></a>Dodaj akcję

Teraz Dodaj akcję, która pobiera zawartość z dowolnego nowego pliku.

1. W obszarze wyzwalacza wybierz **następny krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wprowadź "dropbox" jako filtr.
Z listy akcji wybierz następującą akcję: **Pobierz zawartość pliku przy użyciu ścieżki**

1. Jeśli już nie został autoryzowany Azure Logic Apps, aby dostęp do usługi Dropbox, teraz Autoryzowanie dostępu.

1. Aby przejść do ścieżki pliku, którego chcesz użyć obok **ścieżka pliku** , wybierz wielokropek (**...** ) przycisku. 

   Możesz również kliknąć wewnątrz **ścieżka pliku** , a z listy zawartości dynamicznej wybierz **ścieżka pliku**, którego wartość jest dostępna jako dane wyjściowe z wyzwalacza dodany w poprzedniej sekcji.

1. Gdy skończysz, Zapisz aplikację logiki.

1. Aby wyzwolić aplikację logiki, Utwórz nowy plik w usłudze Dropbox.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w łącznika interfejsu OpenAPI (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/dropbox/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
