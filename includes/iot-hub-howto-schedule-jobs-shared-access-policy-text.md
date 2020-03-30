---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70049071"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

W tym artykule utworzysz usługę zaplecza, która planuje zadanie do wywołania metody bezpośredniej na urządzeniu, planuje zadanie, aby zaktualizować bliźniaczej reprezentacji urządzenia i monitoruje postęp każdego zadania. Aby wykonać te operacje, usługa wymaga uprawnień do **odczytu** i **zapisu rejestru rejestru.** Domyślnie każdy centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **registryReadWrite,** który udziela tych uprawnień.
