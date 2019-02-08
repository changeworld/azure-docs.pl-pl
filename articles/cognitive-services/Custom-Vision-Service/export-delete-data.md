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
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: 7f27170a5e97d7860bcb059f16273b38757de747
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867039"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Eksportowanie lub usuwanie danych użytkownika w Custom Vision

Content Moderator zbiera dane użytkownika w celu oferowania usługi, ale klienci mają pełną kontrolę nad wyświetlania, eksportowanie i usuwanie danych przy użyciu usługi Custom Vision Service [interfejsu API szkolenia](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby uzyskać więcej informacji na temat sposobu eksportowania i usuwania danych użytkownika w Custom Vision Zobacz poniższej tabeli.

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
