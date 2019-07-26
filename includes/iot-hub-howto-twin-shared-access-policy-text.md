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
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403961"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

W tym artykule opisano tworzenie usługi zaplecza, która dodaje odpowiednie właściwości do sznurka urządzenia, a następnie wysyła zapytanie do rejestru tożsamości w celu znalezienia wszystkich urządzeń z zgłoszonymi właściwościami, które zostały odpowiednio zaktualizowane. Twoja usługa wymaga uprawnień do **nawiązania połączenia z usługą** w celu zmodyfikowania odpowiednich właściwości sznurka urządzenia i wymaga uprawnień do **odczytu rejestru** w celu zbadania rejestru tożsamości. Nie ma domyślnych zasad dostępu współdzielonego, które zawierają tylko te dwa uprawnienia, więc należy ją utworzyć.
