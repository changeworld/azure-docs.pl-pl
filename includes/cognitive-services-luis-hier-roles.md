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
ms.openlocfilehash: 1d7723f356274bbd18b1ea08e34046da82a1057c
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646357"
---
**Pytanie**: należy użyć hierarchicznej jednostki lub wzorca za pomocą prostego jednostki przy użyciu ról? 

**Odpowiedź**: to zależy. Wzorce wypowiedzi przykład są porównywalne, stanowią one wypowiedź użytkownika i specyficznych dla intencji.  

Jeśli wypowiedzi nie ma wyraźnego, korzystanie z jednostek hierarchicznej. 

|Hierarchiczna jednostek|Proste jednostki przy użyciu ról|
|--|--|
|musi mieć wypowiedzi przykład za pomocą jednostki podrzędne oznaczony etykietą w intencji|musi mieć wypowiedzi przykład **role nie może być oznaczony jako w intencji**|
|można używać we wzorcach|**należy** korzystać we wzorcach|
|może być konieczne **więcej** przykład wypowiedzi w intencji|może być konieczne **mniej** przykład wypowiedzi w intencji|
