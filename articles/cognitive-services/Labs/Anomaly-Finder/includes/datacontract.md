---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348188"
---
Z [anomalii wyszukiwania API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), można przekazać danych serii godzina w formacie JSON do punktu końcowego interfejsu API, a następnie przeczytaj wynik z odpowiedzi interfejsu API. Możesz przekazać dane serii czasu, każdy punkt danych obejmuje:  
* Sygnatura czasowa - znacznik czasu dla punktu danych. Upewnij się, na przykład używa ciągu czasu UTC daty "2017-08-01T00:00:00Z"
* Wartość — pomiar tego punktu danych.

Wyniki obejmują:
* Okres — okresowości, który używa interfejsu API do wykrywania anomalii
* WarningText — możliwe ostrzeżenia
* ExpectedValue - wartością prognozowaną przez naukę na podstawie modelu
* IsAnomaly - wynik czy anomalii punktów danych lub nie w obu kierunkach (nagłego lub DIP)
* IsAnomaly_Neg - wyników na to, czy punkty danych są anomalii w kierunku ujemną (DIP)
* IsAnomaly_Pos - wyników na to, czy punkty danych są anomalii w kierunku dodatnią (nagłego)
* UpperMargin — Suma ExpectedValue i UpperMargin określa górną granicę punktu danych jest nadal traktować jako standardowa
* LowerMargin - (ExpectedValue - LowerMargin) określa dolnej granicy, że punktu danych jest nadal traktować jako standardowa

Szczegóły kontraktu danych można znaleźć [tutaj](../apiref.md).

