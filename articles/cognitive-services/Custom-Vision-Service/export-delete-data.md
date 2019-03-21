---
title: Eksportowanie lub usunąć swoje dane — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak eksportować i usuwać dane w usłudze Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 4c395a062b1132710f888cc5a315529db082a805
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850030"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Eksportowanie lub usuwanie danych użytkownika w Custom Vision

Custom Vision zbiera dane użytkownika w celu oferowania usługi, ale klienci mają pełną kontrolę nad wyświetlania, eksportowanie i usuwanie danych przy użyciu przetwarzania niestandardowego [interfejsów API szkolenia](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby dowiedzieć się, jak eksportować i usuwać dane użytkownika Custom Vision, zobacz poniższej tabeli.

| Dane | Operacja eksportowania | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Usuń przy użyciu witryny Azure portal (subskrypcje platformy Azure). Lub za pomocą przycisku "Usunąć konta" na stronie ustawień CustomVision.ai (subskrypcje konta Microsoft) | 
| Szczegóły iteracji | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Szczegóły wydajności dla iteracji | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista iteracji | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty i szczegóły projektu | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) i [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Znaczniki obrazów | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) i [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrazy | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zawiera identyfikator uri do pobrania obrazu) i [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zawiera identyfikator uri do pobrania obrazu) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Wyeksportowane modeli | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Usunięty podczas usuwania konta |
