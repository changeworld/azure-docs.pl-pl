---
title: Odzyskiwanie po awarii dla udziału danych platformy Azure
description: Odzyskiwanie po awarii dla udziału danych platformy Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483182"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Odzyskiwanie po awarii dla udziału danych platformy Azure

W tym artykule omówimy sposób konfigurowania środowiska odzyskiwania po awarii dla usługi Azure Data Share. Awarie centrum danych platformy Azure są rzadkie, ale mogą trwać od kilku minut do godzin. Awarie centrum danych mogą powodować zakłócenia w środowiskach, które są zależne od danych udostępnianych przez dostawcę danych. Wykonując kroki opisane w tym artykule, dostawcy danych mogą nadal udostępniać dane swoim odbiorcom danych w przypadku awarii centrum danych dla regionu podstawowego, który hostuje udział danych. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Osiągnięcie ciągłości biznesowej udostępniania danych platformy Azure

Aby przygotować się na awarię centrum danych, dostawca danych może mieć środowisko udostępniania danych aprowizowanego w regionie pomocniczym. Istnieją środki, które można podjąć, które zapewnią sprawne pracy awaryjnej w przypadku wystąpienia awarii centrum danych. 

Dostawcy danych mogą aprowizować pomocnicze zasoby udostępniania danych platformy Azure w dodatkowym regionie. Te zasoby udziału danych można skonfigurować tak, aby zawierały zestawy danych, które istnieją w środowisku udostępniania danych podstawowych. Dane, które konsumenci mogą być dodawane do udziału danych podczas konfigurowania środowiska odzyskiwania po awarii lub dodawane w późniejszym czasie (tj. w ramach ręcznych kroków pracy awaryjnej).

Jeśli konsumenci danych mają subskrypcję aktywnego udziału w środowisku pomocniczym aprowidywalnym dla celów odzyskiwania po awarii, mogą włączyć harmonogram migawki w ramach pracy awaryjnej. Jeśli konsumenci danych nie chcą subskrybować regionu pomocniczego do celów odzyskiwania po awarii, mogą zostać zaproszeni do udziału danych pomocniczych w późniejszym czasie. 

Konsumenci danych mogą mieć subskrypcję aktywnego udziału, która jest bezczynna do celów odzyskiwania po awarii, lub dostawcy danych mogą dodać je w późniejszym momencie w ramach procedur ręcznego pracy awaryjnej. 

## <a name="related-information"></a>Informacje pokrewne

- [Ciągłość działania i odzyskiwanie po awarii](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Wbudowywanie wysokiej dostępności w strategię zapewniania ciągłości biznesowej i odzyskiwania po awarii](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozpocząć udostępnianie danych, przejdź do [samouczka udostępniania danych.](share-your-data.md)




