---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050477"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

W tym artykule utworzysz usługę zaplecza, która dodaje żądane właściwości do bliźniaczej reprezentacji urządzenia, a następnie wysyła kwerendy do rejestru tożsamości, aby znaleźć wszystkie urządzenia z zgłoszonymi właściwościami, które zostały odpowiednio zaktualizowane. Usługa potrzebuje uprawnienia **do łączenia usługi,** aby zmodyfikować żądane właściwości bliźniaczej reprezentacji urządzenia i wymaga uprawnienia do **odczytu rejestru,** aby zbadać rejestr tożsamości. Nie ma domyślnej zasady dostępu współdzielonego, która zawiera tylko te dwa uprawnienia, więc należy je utworzyć.
