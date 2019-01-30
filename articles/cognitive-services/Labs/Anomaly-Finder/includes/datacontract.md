---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 3cc0e521e43f6855397a19fe34fce99da3e20494
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228861"
---
Za pomocą [interfejs API wyszukiwania anomalii](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), przekazywanie danych szeregów czasowych w formacie JSON do punktu końcowego interfejsu API, a następnie zapoznaj się z wynikiem z odpowiedzi interfejsu API. Możesz przekazać dane szeregów czasowych, każdy punkt danych obejmuje:  
* Sygnatura czasowa — sygnatura czasowa dla punktu danych. Upewnij się, używa ciągu czas daty UTC, na przykład "2017-08-01T00:00:00Z"
* Wartość — pomiar tego punktu danych.

Wyniki obejmują:
* Okres - okresowości, która korzysta z interfejsu API wykrywania anomalii
* WarningText — możliwe ostrzeżenia
* ExpectedValue — wartością prognozowaną ucząc model oparty na protokole
* IsAnomaly - wyniku tego, czy punkty danych są anomalii lub nie w obu kierunkach (wzrostów lub spadku)
* IsAnomaly_Neg — wynik dla tego, czy punkty danych są anomalie w kierunku ujemna (spadku)
* IsAnomaly_Pos — wynik dla tego, czy punkty danych są anomalie w kierunku dodatnią (wzrostów)
* UpperMargin — Suma ExpectedValue i UpperMargin okaże się, że górna granica, która punktu danych jest nadal traktować w zwykły sposób
* LowerMargin - (ExpectedValue - LowerMargin) określa dolna granica, że punkt danych nadal jest uznawane za w zwykły sposób

Szczegóły kontraktu danych można znaleźć [tutaj](../apiref.md).

