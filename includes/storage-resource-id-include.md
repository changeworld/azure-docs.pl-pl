---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249909"
---
Identyfikator zasobu usługi Azure AD wskazuje odbiorców, dla których token, który jest wystawiany, może służyć do zapewnienia dostępu do zasobu platformy Azure. W przypadku usługi Azure Storage identyfikator zasobu może być specyficzny dla jednego konta magazynu lub może mieć zastosowanie do dowolnego konta magazynu. W poniższej tabeli opisano wartości, które można podać dla identyfikatora zasobu:

|Identyfikator zasobu  |Opis  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Punkt końcowy usługi dla danego konta magazynu. Ta wartość służy do uzyskiwania tokenu do autoryzowania żądań tylko do określonego konta i usługi usługi Azure Storage. Zastąp wartość w nawiasach nazwą konta magazynu.      |
|`https://storage.azure.com/`     | Służy do uzyskiwania tokenu do autoryzowania żądań do dowolnego konta usługi Azure Storage.        |
