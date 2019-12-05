---
title: 'Szybki Start: Rozpoznawanie mowy przechowywanej w usłudze BLOB Storage — usługa mowy'
titleSuffix: Azure Cognitive Services
description: do ustalenia
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828992"
---
W tym przewodniku szybki start użyjemy [interfejsu API REST transkrypcji usługi Batch](../../../batch-transcription.md) do rozpoznawania mowy przechowywanej w [obiekcie blob sygnatury dostępu współdzielonego](https://aka.ms/ignite2019/speech/placeholder). Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy przy użyciu interfejsu API REST trwa tylko kilka kroków:
> [!div class="checklist"]
> * Wyślij żądanie JSON do usługi mowy, aby rozpocząć jego przepisywania mowę.
> * Sprawdź stan transkrypcji.
> * Pobierz wyniki transkrypcji po zakończeniu.