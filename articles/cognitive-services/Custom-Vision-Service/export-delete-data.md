---
title: Eksportowanie lub usuwanie danych - Usługa Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Użytkownik zachowuje pełną kontrolę nad swoimi danymi. Z tego artykułu dowiesz się, jak wyświetlać, eksportować lub usuwać dane w usłudze Niestandardowej wizji.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718970"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Eksportowanie lub usuwanie danych użytkownika w uzywce niestandardowej

Usługa Custom Vision zbiera dane użytkowników do obsługi usługi, ale klienci mają pełną kontrolę nad wyświetlaniem, eksportowaniem i usuwaniem danych przy użyciu [interfejsów API szkolenia niestandardowego.](https://go.microsoft.com/fwlink/?linkid=865446)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby dowiedzieć się, jak eksportować i usuwać dane użytkowników w uzywce Niestandardowej wizji, zobacz poniższą tabelę.

| Dane | Operacja eksportu | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | [GetAccountInfo ( GetAccountInfo )](https://go.microsoft.com/fwlink/?linkid=865446) | Usuń przy użyciu witryny Azure portal (subskrypcje platformy Azure). Lub za pomocą przycisku "Usuń konto" na stronie ustawień CustomVision.ai (subskrypcje konta Microsoft) | 
| Szczegóły iteracji | [GetIteration ( GetIteration )](https://go.microsoft.com/fwlink/?linkid=865446) | [UsuwanieNierytration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Szczegóły wydajności iteracji | [GetIterationPerformance (Wyniki)](https://go.microsoft.com/fwlink/?linkid=865446) | [UsuwanieNierytration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista iteracji | [Getiteracje](https://go.microsoft.com/fwlink/?linkid=865446) | [UsuwanieNierytration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty i szczegóły projektu | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) i [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [Usuńprojekt](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Znaczniki obrazów | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) i [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [UsuńTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrazy | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zapewnia uri do pobrania obrazu) i [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zapewnia uri do pobrania obrazu) | [Usuńimages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Eksportowane modele | [GetExports (GetExports)](https://go.microsoft.com/fwlink/?linkid=865446) | Usunięte po usunięciu konta |
