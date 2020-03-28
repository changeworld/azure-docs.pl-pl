---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279668"
---
W sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **Zasoby platformy Azure** (menu po lewej stronie) skopiuj adres URL **przykładowej kwerendy,** a następnie wklej do nowej karty przeglądarki.

Adres URL punktu końcowego wygląda następująco, z własnym identyfikatorem aplikacji i kluczem końcowym zastępującym APP-ID i KEY-ID:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```