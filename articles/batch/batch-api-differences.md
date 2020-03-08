---
title: Różnice między interfejsami API zarządzania i interfejsami API usługi — Azure Batch | Microsoft Docs
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672756"
---
# <a name="service-level-and-management-level-apis"></a>Interfejsy API poziomu usług i zarządzania

Azure Batch ma dwa zestawy interfejsów API, jeden dla poziomu usługi i jeden dla poziomu zarządzania. Nazewnictwo jest często podobne, ale zwracają różne wyniki. Jeśli potrzebujesz dzienników aktywności, musisz użyć interfejsów API zarządzania. Interfejsy API poziomu usługi pomijają warstwę zarządzania zasobami platformy Azure i nie są rejestrowane.


Zarządzanie partiami i usługa Batch mają interfejsy API dla puli, na przykład. 
- Ten interfejs API służący do usuwania puli jest przeznaczony bezpośrednio na koncie usługi Batch: https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Ten interfejs API służący do usuwania puli https://docs.microsoft.com/rest/api/batchmanagement/pool/delete jest przeznaczony dla warstwy management.azure.com.

