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
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208308"
---
## <a name="roles-versus-hierarchical-entities"></a>Role i hierarchiczne jednostek

Za pomocą prostego jednostki przy użyciu ról należy użyć hierarchicznej jednostki lub wzorzec? 

To zależy. Wzorce wypowiedzi przykład są porównywalne, stanowią one wypowiedź użytkownika i specyficznych dla intencji.  

Jeśli wypowiedzi nie ma wyraźnego, korzystanie z jednostek hierarchicznej. 

|Hierarchiczna jednostek|Proste jednostki przy użyciu ról|
|--|--|
|Dostępne w przykładzie wypowiedzi firmy i wzorce|Dostępne tylko we wzorcach|
|Muszą mieć przykład wypowiedzi w intencji z jednostki podrzędne oznaczone|Role nie może być oznaczony jako w przykładzie wypowiedzi w intencji|
|Może być konieczne **więcej** przykład wypowiedzi w jasno określonym celu wyodrębnienia jednostki|Konieczne **mniej** przykład wypowiedzi w intencji|
