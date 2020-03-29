---
title: Eksportowanie lub usuwanie danych użytkownika — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Masz pełną kontrolę nad swoimi danymi. Dowiedz się, jak wyświetlać, eksportować lub usuwać dane w aplikacji Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744792"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Eksportowanie lub usuwanie danych użytkownika w moderatorze zawartości

Content Moderator zbiera dane użytkownika do obsługi usługi, ale klienci mają pełną kontrolę, aby wyświetlić, wyeksportować i usunąć swoje dane za pomocą [narzędzia Przeglądaj](https://contentmoderator.cognitive.microsoft.com/) i [interfejsów API moderowania i przeglądu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby uzyskać więcej informacji na temat eksportowania i usuwania danych użytkownika w moderatorze zawartości, zobacz poniższą tabelę.

| Dane | Operacja eksportu | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | Nie dotyczy | Usuń przy użyciu witryny Azure portal (subskrypcje platformy Azure). Możesz też użyć przycisku **Usuń zespół** na stronie Ustawienia zespołu [interfejsu użytkownika.](https://contentmoderator.cognitive.microsoft.com/) |
| Obrazy do dopasowywania niestandardowego | Wywołanie [interfejsu API identyfikatorów obrazów .](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676) Obrazy są przechowywane w jednokierunkowym zastrzeżonym formacie skrótu i nie ma możliwości wyodrębnienia rzeczywistych obrazów. | Wywołanie [interfejsu API Usuń wszystkie obrazy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Możesz też usunąć zasób moderatora zawartości za pomocą witryny Azure portal. |
| Terminy dopasowywania niestandardowego | Cal [pobierz wszystkie terminy INTERFEJSU API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Wywołanie [Usuń wszystkie terminy INTERFEJSU API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Możesz też usunąć zasób moderatora zawartości za pomocą witryny Azure portal. |
| Tagi | Nie dotyczy | Użyj ikony **Usuń** dostępnej dla każdego tagu na stronie Ustawienia znacznika interfejsu użytkownika. Możesz też użyć przycisku **Usuń zespół** na stronie Ustawienia zespołu [interfejsu użytkownika.](https://contentmoderator.cognitive.microsoft.com/) |
| Przeglądy | Wywoływanie [interfejsu API recenzji pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Użyj przycisku **Usuń zespół** na stronie Przeglądanie ustawień zespołu [interfejsu użytkownika.](https://contentmoderator.cognitive.microsoft.com/)
| Użytkownicy | Nie dotyczy | Użyj ikony **Usuń** dostępnej dla każdego użytkownika na stronie Ustawienia zespołu [interfejsu użytkownika.](https://contentmoderator.cognitive.microsoft.com/) Możesz też użyć przycisku **Usuń zespół** na stronie Ustawienia zespołu [interfejsu użytkownika.](https://contentmoderator.cognitive.microsoft.com/) |

