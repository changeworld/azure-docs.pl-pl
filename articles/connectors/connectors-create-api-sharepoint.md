---
title: Łączenie się z programem SharePoint za pomocą aplikacji logiki platformy Azure
description: Automatyzuj zadania i przepływy pracy monitoruj zasoby w usłudze SharePoint Online lub SharePoint Server lokalnie i zarządzają nimi przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb82ef2d6fb83c2e1b0fa81aa9504c9bb7d8234b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789260"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorowanie zasobów programu SharePoint i zarządzanie nimi za pomocą aplikacji Azure Logic Apps

Za pomocą aplikacji Azure Logic Apps i łącznika programu SharePoint można tworzyć zautomatyzowane zadania i przepływy pracy monitorujące zasoby, takie jak pliki, foldery, listy, elementy, osoby itd., w usłudze SharePoint Online lub w programie SharePoint Server lokalnie, na przykład:

* Monitoruj, kiedy pliki lub elementy są tworzone, zmieniane lub usuwane.
* Tworzenie, pobierz, aktualizuj lub usuń elementy.
* Dodawanie, uładanie lub usuwanie załączników. Pobierz zawartość z załączników.
* Tworzenie, kopiowanie, aktualizowanie lub usuwanie plików. 
* Aktualizowanie właściwości pliku. Pobierz zawartość, metadane lub właściwości pliku.
* Lista lub wyodrębnianie folderów.
* Pobierz listy lub widoki listy.
* Ustaw stan zatwierdzania zawartości.
* Rozwiązuj osoby.
* Wyślij żądania HTTP do programu SharePoint.
* Pobierz wartości encji.

Można użyć wyzwalaczy, które otrzymują odpowiedzi z programu SharePoint i udostępniają dane wyjściowe innym działaniom. Akcje w aplikacjach logiki umożliwiają wykonywanie zadań w programie SharePoint. Można również użyć innych akcji przy użyciu danych wyjściowych z akcji programu SharePoint. Na przykład jeśli regularnie pobierasz pliki z programu SharePoint, możesz wysyłać wiadomości do zespołu przy użyciu łącznika Slack.
Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Adres witryny programu SharePoint i poświadczenia użytkownika

  Poświadczenia autoryzują aplikację logiki do tworzenia połączenia i uzyskiwania dostępu do konta programu SharePoint. 

* Przed połączeniem aplikacji logiki z systemami lokalnymi, takimi jak SharePoint Server, należy [zainstalować i skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). W ten sposób można określić, aby używać instalacji bramy podczas tworzenia połączenia programu SharePoint Server dla aplikacji logiki.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta programu SharePoint. Aby rozpocząć od wyzwalacza programu SharePoint, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji programu SharePoint, uruchom aplikację logiki za pomocą wyzwalacza, takiego jak wyzwalacz Salesforce, jeśli masz konto Salesforce.

  Na przykład można uruchomić aplikację logiki z **when rekord jest tworzony** Salesforce wyzwalacza. 
  Ten wyzwalacz jest uruchamiany za każdym razem, gdy w salesforce tworzony jest nowy rekord, taki jak potencjalny klient. 
  Następnie można wykonać ten wyzwalacz za pomocą akcji **Utwórz plik** programu SharePoint. W ten sposób po utworzeniu nowego rekordu aplikacja logiki tworzy plik w programie SharePoint z informacjami o tym nowym rekordzie.

## <a name="connect-to-sharepoint"></a>Łączenie się z programem SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź "sharepoint" jako filtr. W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję programu SharePoint, wybierz pozycję **Nowy krok**. 
   W polu wyszukiwania wpisz "sharepoint" jako filtr. 
   W obszarze listy akcje wybierz odpowiednią akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Po wyświetleniu monitu o zalogowanie się podaj niezbędne informacje o połączeniu. Jeśli korzystasz z programu SharePoint Server, upewnij się, że wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/sharepoint/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
