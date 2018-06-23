---
title: Eksportowanie lub usuwać dane w zawartości moderatora - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyeksportować lub usuwać dane w moderatora zawartości.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 1dbb645a033c6db5ffa9003a53f30fd927131298
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349533"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Eksportowanie lub usuwać dane użytkownika w moderatora zawartości

Zawartości moderatora zbiera dane użytkownika w celu oferowania usługi, ale klienci mają pełną kontrolę nad wyświetlanie, eksportowanie i usuwanie ich danych przy użyciu [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) i [interfejsów API](https://docs.microsoft.com/en-us/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby uzyskać więcej informacji na temat eksportowania i usuwać dane użytkownika w moderatora zawartości Zobacz poniższej tabeli.

| Dane | Operacja eksportowania | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | ND | Usuń przy użyciu portalu Azure (subskrypcji platformy Azure). Lub użyj **usunąć zespołu** przycisk [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |
| Obrazy niestandardowe dopasowania | [Pobierz identyfikatory obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Obrazy są przechowywane w formacie jednokierunkowego skrótu zastrzeżonych, a nie istnieje sposób wyodrębnić rzeczywistych obrazów. | [Usuń wszystkie obrazy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Lub usuń zasób moderatora zawartości przy użyciu portalu Azure. |
| Warunki do dopasowania niestandardowych | [Pobierz wszystkie warunki](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Usuń wszystkie warunki](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Lub usuń zasób moderatora zawartości przy użyciu portalu Azure. |
| Tagi | ND | Użyj **usunąć** ikona dostępne dla każdego znacznika na stronie ustawień przeglądu Tag interfejsu użytkownika. Lub użyj **usunąć zespołu** przycisk [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |
| Przeglądy | [Pobierz przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Użyj **usunąć zespołu** przycisk [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu.
| Użytkownicy | ND | Użyj **usunąć** ikona dostępne dla każdego użytkownika w [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. Lub użyj **usunąć zespołu** przycisk [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |

