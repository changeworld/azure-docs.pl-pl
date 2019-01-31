---
title: Co to jest interfejs API usługi Entity Linking Intelligence Service?
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API usługi Entity Linking Intelligence Service do obsługi zadań łączenia jednostek.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: overview
ms.date: 03/28/2016
ms.author: davl
ms.openlocfilehash: fa2e480cd2c46e6092f4b0421adca63542e54d1b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224636"
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


