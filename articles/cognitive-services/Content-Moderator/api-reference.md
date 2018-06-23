---
title: Odwołanie API moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zawartości łagodzenia i przejrzyj interfejsów API moderatora zawartości.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 94de9b91cc242e8b7e5479cacab8380adac551f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346993"
---
# <a name="content-moderator-api-reference"></a>Dokumentacja interfejsu API usługi Content Moderator

Rozpoczynanie pracy z interfejsami API Azure zawartości moderatora, w następujący sposób: (Zobacz też [Zarządzanie poświadczeniami](review-tool-user-guide/credentials.md).)

- W portalu Azure [subskrybować zawartości interfejsów API moderatora](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Zaloguj się do [narzędzie przeglądu moderatora zawartości](http://contentmoderator.cognitive.microsoft.com/). Zobacz [szybkiego startu](quick-start.md).

## <a name="moderation-apis"></a>Interfejsy API moderowania

Następujące interfejsy API zawartości moderatora służy do konfigurowania przepływów pracy po łagodzenia.

| Opis | Informacje ogólne |
| -------------------- |-------------|
| **Obraz łagodzenia interfejsu API**<br /><br />Skanowanie obrazów i wykrywa potencjalne zawartość dla dorosłych i luksusowych przy użyciu tagów, wyniki zaufania i inne informacje wyodrębnione. <br /><br />Te informacje służą do publikowania, odrzuć lub przejrzyj zawartość w przepływie pracy po łagodzenia. <br /><br />| [Dokumentacja interfejsu API łagodzenia obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "dokumentacja interfejsu API łagodzenia obrazu")   |
| **Tekst łagodzenia interfejsu API**<br /><br />Skanowanie zawartości tekstowej. Zwracane są warunki niestosownych wyrażeń i informacje umożliwiające identyfikację (dane osobowe). <br /><br />Te informacje służą do publikowania, odrzuć lub przejrzyj zawartość w przepływie pracy po łagodzenia.<br /><br /> | [Dokumentacja interfejsu API łagodzenia tekst](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "dokumentacja interfejsu API łagodzenia tekstu")   |
| **Łagodzenie wideo interfejsu API**<br /><br />Skanuj pliki wideo i wykrywa potencjalne zawartość dla dorosłych i luksusowych. <br /><br />Te informacje służą do publikowania, odrzuć lub przejrzyj zawartość w przepływie pracy po łagodzenia.<br /><br /> | [Film poglądowy dotyczący interfejsu API łagodzenia](video-moderation-api.md "omówienie API łagodzenia wideo")   |
| **Interfejs API zarządzania listy**<br /><br />Utwórz i Zarządzaj listy wykluczeń lub dołączeń obrazy i tekst. U możliwia **obrazu — odpowiada** i **tekst — ekranu** czy operacji dopasowywania rozmytego przesłanej zawartości przed niestandardowych list. <br /><br />W celu zwiększenia wydajności można pominąć krok na podstawie learning łagodzenia maszyny.<br /><br /> | [Dokumentacja interfejsu API zarządzania listy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "interfejs API zarządzania listy odwołania")   |

## <a name="review-api"></a>Przegląd interfejsu API

Przegląd interfejsu API zawiera następujące składniki:

| Opis | Informacje ogólne |
| -------------------- |-------------|
| **Zadania**<br /><br /> Zainicjuj łagodzenia skanowania i Przejrzyj przepływy pracy, dla zawartości zarówno obraz i tekst. Zadanie łagodzenia skanowanie zawartości przy użyciu interfejsu API łagodzenia obrazu i interfejsu API łagodzenia tekstu. Łagodzenia zadania przy użyciu zdefiniowanych a domyślna przepływy pracy, aby wygenerować recenzje. <br /><br />Po człowieka moderatora ma przejrzeć dane prognozowania i automatycznie przypisane tagi i przesłane decyzji łagodzenia zawartości, interfejsu API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.<br /><br /> | [Odwołanie zadania](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "odwołanie zadania")   |
| **Przeglądy**<br /><br />Narzędzie przeglądu bezpośrednio utworzyć dla człowieka moderatorów przeglądami image lub text.<br /><br /> Po człowieka moderatora ma przejrzeć dane prognozowania i automatycznie przypisane tagi i przesłane decyzji łagodzenia zawartości, interfejsu API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.<br /><br /> | [Przejrzyj odwołanie](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Przejrzyj odwołania")   |
| **Przepływy pracy**<br /><br />Tworzenie, aktualizowanie i uzyskać szczegółowe informacje o niestandardowych przepływów pracy tworzonych przez zespół. Należy zdefiniować przepływy pracy przy użyciu narzędzia do przeglądu. <br /> <br />Przepływy pracy zwykle używać moderatora zawartości, ale można również użyć niektórych innych interfejsów API, które są dostępne jako łączniki w narzędziu przeglądu.<br /><br /> | [Odwołanie do przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "odwołanie do przepływu pracy")   |


