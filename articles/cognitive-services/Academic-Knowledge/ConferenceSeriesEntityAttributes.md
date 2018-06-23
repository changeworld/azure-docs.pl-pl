---
title: Atrybuty obiektu serii konferencji w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Więcej informacji na temat atrybutów używanych z jednostką serii konferencji w usługach kognitywnych.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346860"
---
# <a name="conference-series-entity"></a>Konferencja serii jednostki

<sub> * Następujące atrybuty są specyficzne dla konferencji serii jednostki. (Ty = "3") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
NAZWA POSPOLITA      |Znormalizowaną nazwę serii konferencji      |Ciąg     |Równa się
DCN     |Nazwa wyświetlana konferencji serii         |Ciąg     |brak
DW      |Seria konferencji całkowita liczba cytatu         |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany serii konferencji   |Int32      |brak
F.FId   |Pole identyfikatora jednostki badania skojarzona z serią konferencji |Int64  | Równa się
F.FN    |Pole o nazwie badania skojarzona z serią konferencji  | Równy,<br/>StartsWith