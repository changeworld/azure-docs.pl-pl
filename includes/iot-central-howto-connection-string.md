---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747503"
---
1. Użyj `dps-keygen` narzędzie wiersza polecenia, aby wygenerować parametry połączenia:

    Aby zainstalować [klucza narzędzie generatora](https://github.com/Azure/dps-keygen), uruchom następujące polecenie:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Aby wygenerować parametry połączenia, uruchom następujące polecenie, używając szczegóły połączenia zapisanymi wcześniej:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Skopiuj parametry połączenia z `dps-keygen` dane wyjściowe do użycia w kodzie urządzenia.