---
title: 'Szybki Start: Rozpoznawanie mowy przechowywanej w usłudze BLOB Storage — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503976"
---
W tym przewodniku szybki start użyjemy [interfejsu API REST transkrypcji usługi Batch](../../../batch-transcription.md) do rozpoznawania mowy przechowywanej w [obiekcie blob sygnatury dostępu współdzielonego](https://aka.ms/ignite2019/speech/placeholder). Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy przy użyciu interfejsu API REST trwa tylko kilka kroków:
> [!div class="checklist"]
> * Wyślij żądanie JSON do usługi mowy, aby rozpocząć jego przepisywania mowę.
> * Sprawdź stan transkrypcji.
> * Pobierz wyniki transkrypcji po zakończeniu.