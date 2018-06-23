---
title: Eksportowanie lub usuwać dane w niestandardowych wizji, kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyeksportować lub usuwać dane w wizji niestandardowe.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349537"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Eksportowanie lub usuwać dane użytkownika w wizji niestandardowe

Zawartości moderatora zbiera dane użytkownika w celu oferowania usługi, ale klienci mają pełną kontrolę nad wyświetlanie, eksportowanie i usuwanie ich danych przy użyciu usługi wizji niestandardowe [interfejsu API uczenia](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby uzyskać więcej informacji na temat eksportowania i usuwać dane użytkownika w wizji niestandardowych Zobacz poniższej tabeli.

| Dane | Operacja eksportowania | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Usuwanie przy użyciu portalu Azure (subskrypcji platformy Azure). Lub przy użyciu przycisku "Usuń Twoje konto" na stronie ustawień CustomVision.ai (subskrypcje konta Microsoft) |
| Szczegóły iteracji | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Szczegóły wydajności iteracji | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Lista iteracji | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty i szczegółów projektu | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) i [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Tagi obrazu | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) i [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Obrazy | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zawiera identyfikator uri do pobrania obrazu) i [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zawiera identyfikator uri do pobrania obrazu) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Wyeksportowane modeli | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Usunięte podczas usuwania konta |
