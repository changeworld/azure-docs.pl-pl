---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528841"
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
