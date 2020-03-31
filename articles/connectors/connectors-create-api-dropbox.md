---
title: Łączenie się z Dropbox
description: Automatyzuj zadania i przepływy pracy, które przesyłają pliki w Dropbox i zarządzają nimi, korzystając z usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665755"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Przekazywanie plików w Dropbox i zarządzanie nimi przy użyciu usługi Azure Logic Apps

Dzięki łącznikowi Dropbox i usłudze Azure Logic Apps możesz tworzyć zautomatyzowane przepływy pracy, które przesyłają pliki na koncie Dropbox i zarządzają nimi. 

W tym artykule pokazano, jak połączyć się z Dropbox za pomocą aplikacji logiki, a następnie dodać Dropbox **Po utworzeniu pliku** wyzwalacza i dropbox pobierz zawartość pliku za pomocą akcji **ścieżka.**

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto Dropbox](https://www.dropbox.com/), które możesz zarejestrować za darmo. Poświadczenia konta są niezbędne do utworzenia połączenia między aplikacją logiki a kontem Dropbox.

* Podstawowa wiedza na temat [tworzenia aplikacji logicznych](../logic-apps/quickstart-create-first-logic-app-workflow.md). W tym przykładzie potrzebna jest pusta aplikacja logiki.

## <a name="add-trigger"></a>Dodawanie wyzwalacza

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wpisz "dropbox" jako filtr.
Z listy wyzwalaczy wybierz ten wyzwalacz: **Po utworzeniu pliku**

   ![Wybierz wyzwalacz Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Zaloguj się przy użyciu poświadczeń konta Dropbox i autoryzuj dostęp do danych Dropbox dla usługi Azure Logic Apps.

1. Podaj wymagane informacje dla wyzwalacza. 

   W tym przykładzie wybierz folder, w którym chcesz śledzić tworzenie plików. Aby przeglądać foldery, wybierz ikonę folderu obok pola **Folder.**

## <a name="add-action"></a>Dodawanie akcji

Teraz dodaj akcję, która pobiera zawartość z dowolnego nowego pliku.

1. W obszarze wyzwalacza wybierz pozycję **Następny krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wpisz "dropbox" jako filtr.
Z listy akcji wybierz tę akcję: **Pobierz zawartość pliku przy użyciu ścieżki**

1. Jeśli nie masz jeszcze autoryzowanego usługi Azure Logic Apps w celu uzyskania dostępu do Dropbox, autoryzuj dostęp teraz.

1. Aby przejść do ścieżki pliku, której chcesz użyć, obok pola **Ścieżka pliku** wybierz przycisk elipsy (**...**). 

   Można również kliknąć wewnątrz pola **Ścieżka pliku,** a z dynamicznej listy zawartości wybierz **pozycję Ścieżka pliku**, której wartość jest dostępna jako dane wyjściowe z wyzwalacza dodanego w poprzedniej sekcji.

1. Po zakończeniu zapisz aplikację logiki.

1. Aby wyzwolić aplikację logiki, utwórz nowy plik w Dropbox.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger [łącznika,](/connectors/dropbox/)zobacz stronę odwołania łącznika .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
