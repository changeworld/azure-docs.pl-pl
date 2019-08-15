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
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912447"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

W tym artykule opisano tworzenie usługi zaplecza, która dodaje odpowiednie właściwości do sznurka urządzenia, a następnie wysyła zapytanie do rejestru tożsamości w celu znalezienia wszystkich urządzeń z zgłoszonymi właściwościami, które zostały odpowiednio zaktualizowane. Twoja usługa wymaga uprawnień do **nawiązania połączenia z usługą** w celu zmodyfikowania odpowiednich właściwości sznurka urządzenia i wymaga uprawnień do **odczytu rejestru** w celu zbadania rejestru tożsamości. Nie ma domyślnych zasad dostępu współdzielonego, które zawierają tylko te dwa uprawnienia, więc należy ją utworzyć.
