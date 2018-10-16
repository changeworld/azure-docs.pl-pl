---
title: Eksportowanie lub usunąć swoje dane — Content Moderator
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak eksportować i usuwać dane w pakiecie Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: f4da7cdffc2d2aad21c2ea4cfc67939d0dbba530
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339376"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Eksportowanie lub usuwanie danych użytkownika w pakiecie Content Moderator

Content Moderator zbiera dane użytkownika w celu oferowania usługi, ale klienci mają pełną kontrolę nad wyświetlania, eksportowanie i usuwanie danych przy użyciu [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) i [interfejsów API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby uzyskać więcej informacji na temat sposobu eksportowania i usuwania danych użytkownika w pakiecie Content Moderator Zobacz poniższej tabeli.

| Dane | Operacja eksportowania | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | ND | Usuwanie w witrynie Azure portal (subskrypcje platformy Azure). Lub użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |
| Obrazy niestandardowe dopasowanie | [Pobieranie identyfikatorów obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Obrazy są przechowywane w formacie jednokierunkowego skrótu własności, a nie istnieje sposób wyodrębnić rzeczywistych obrazów. | [Usuń wszystkie obrazy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Lub usuwanie zasobu usługi Content Moderator, przy użyciu witryny Azure portal. |
| Warunki niestandardowe dopasowanie | [Pobierz wszystkie terminy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Usuń wszystkie terminy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Lub usuwanie zasobu usługi Content Moderator, przy użyciu witryny Azure portal. |
| Tagi | ND | Użyj **Usuń** ikonę dostępne dla każdego tagu na stronie ustawień Przegląd interfejsu użytkownika tagu. Lub użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |
| Przeglądy | [Rozpoczynanie przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu.
| Użytkownicy | ND | Użyj **Usuń** ikonę dostępne dla każdego użytkownika w [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. Lub użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](http://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |

