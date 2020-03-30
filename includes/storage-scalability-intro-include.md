---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75477150"
---
To odwołanie szczegóły skalowalności i docelowych wydajności dla usługi Azure Storage. Cele skalowalności i wydajności wymienione w tym miejscu są wysokiej klasy obiekty docelowe, ale są osiągalne. We wszystkich przypadkach szybkość żądania i przepustowość osiągnięta przez konto magazynu zależy od rozmiaru przechowywanych obiektów, wzorców dostępu i typu obciążenia wykonywanego przez aplikację.

Upewnij się, aby przetestować usługę, aby ustalić, czy jej wydajność spełnia twoje wymagania. Jeśli to możliwe, należy unikać nagłych skoków natężenia ruchu i upewnij się, że ruch jest dobrze rozłożone na partycje.

Gdy aplikacja osiągnie limit, co partycja może obsłużyć dla obciążenia, usługa Azure Storage zaczyna zwracać kod błędu 503 (Serwer zajęty) lub kod błędu 500 (limit czasu operacji) odpowiedzi. Jeśli występują błędy 503, należy rozważyć zmodyfikowanie aplikacji, aby użyć wykładniczej zasady wycofywania dla ponownych prób. Wykładnicze wycofywanie umożliwia zmniejszenie obciążenia partycji i złagodzenie skoków w ruchu do tej partycji.
