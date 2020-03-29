---
title: Różnice między interfejsami API zarządzania a interfejsami API usług — usługa Azure Batch | Dokumenty firmy Microsoft
description: Interfejsy API działają na różnych warstwach usługi Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672756"
---
# <a name="service-level-and-management-level-apis"></a>Interfejsy API poziomu usług i poziomu zarządzania

Usługa Azure Batch ma dwa zestawy interfejsów API, jeden dla poziomu usługi i jeden dla poziomu zarządzania. Nazewnictwo jest często podobne, ale zwracają różne wyniki. Jeśli chcesz dzienniki aktywności, musisz użyć interfejsów API zarządzania. Interfejsy API poziomu usług pomijają warstwę zarządzania zasobami platformy Azure i nie są rejestrowane.


Zarządzanie wsadowe i usługa wsadowa mają interfejsy API dla puli, na przykład. 
- Ten interfejs API do usuwania puli jest kierowany bezpośrednio na konto wsadowe:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Ten interfejs API https://docs.microsoft.com/rest/api/batchmanagement/pool/delete do usuwania puli jest przeznaczony dla warstwy management.azure.com.

