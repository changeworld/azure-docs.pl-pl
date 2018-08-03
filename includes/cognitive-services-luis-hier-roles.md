---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480369"
---
**Pytanie**: należy użyć hierarchicznej jednostki lub wzorca za pomocą prostego jednostki przy użyciu ról? 

**Odpowiedź**: to zależy. Wzorce wypowiedzi przykład są porównywalne, stanowią one wypowiedź użytkownika i specyficznych dla intencji.  

Jeśli wypowiedzi nie ma wyraźnego, korzystanie z jednostek hierarchicznej. 

|Hierarchiczna jednostek|Proste jednostki przy użyciu ról|
|--|--|
|musi mieć wypowiedzi przykład za pomocą jednostki podrzędne oznaczony etykietą w intencji|musi mieć wypowiedzi przykład **role nie może być oznaczony jako w intencji**|
|można używać we wzorcach|**należy** korzystać we wzorcach|
|może być konieczne **więcej** przykład wypowiedzi w intencji|może być konieczne ** mniej wypowiedzi przykładu w intencji|