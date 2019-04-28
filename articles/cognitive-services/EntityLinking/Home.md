---
title: Co to jest interfejs API usługi Entity Linking Intelligence Service?
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API usługi Entity Linking Intelligence Service do obsługi zadań łączenia jednostek.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: overview
ms.date: 03/28/2016
ms.author: davl
ms.openlocfilehash: 4faeccb30e936177dfa993e912f597c44f8fac60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816422"
---
# <a name="what-is-the-entity-linking-intelligence-service-api"></a>Co to jest interfejs API usługi Entity Linking Intelligence Service?

Zapraszamy do zapoznania się z usługą Entity Linking Intelligence Service — usługą internetową opracowaną po to, aby ułatwić deweloperom obsługę zadań związanych z łączeniem jednostek.

## <a name="entity-linking"></a>Łączenie jednostek

Czasami w różnych kontekstach wyraz może zostać użyty jako nazwana jednostka (podmiot), czasownik lub inna część mowy w obrębie danego zdania. Na przykład w przypadku, gdy wyraz „patrol” jest nazwaną jednostką (podmiotem), wciąż może odnosić się on do dwóch oddzielnie rozróżnialnych jednostek, takich jak „patrol drogowy” i „Psi Patrol”. Biorąc pod uwagę określony akapit w dokumencie, usługa Entity Linking Intelligence Service rozpozna i określi każdą osobną jednostkę na podstawie kontekstu.  

Na poniższej ilustracji przedstawiono przykład łączenia jednostek. Mówiąc ściślej, kiedy korzystamy z Wikipedii, usługa Entity Linking Intelligence Service wykrywa wszystkie jednostki wymienione w tekście wejściowym i łączy je z odpowiednimi punktami odniesienia zgodnie z identyfikatorem strony.

 ![Przykład łączenia jednostek dla wyrazu „Mars”](./Images/EntityLinkingSample1.png)
 
## <a name="get-started"></a>Rozpoczęcie pracy
 
Aby szybko zapoznać się z podstawowymi funkcjami usługi Entity Linking oraz z procesem subskrypcji, przejdź do samouczka wprowadzającego.
[Wprowadzenie do interfejsu API usługi Entity Linking w języku C#](GettingStarted.md)


