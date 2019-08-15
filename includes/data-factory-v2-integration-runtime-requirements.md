---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966357"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Jeśli magazyn danych jest skonfigurowany w jeden z następujących sposobów, należy skonfigurować samoobsługowe [Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) w celu nawiązania połączenia z tym magazynem danych:

- Magazyn danych znajduje się w sieci lokalnej, w ramach platformy Azure Virtual Network lub w ramach wirtualnej chmury prywatnej Amazon.
- Magazyn danych to zarządzana usługa danych w chmurze, do której dostęp jest ograniczony do adresów IP listy dozwolonych w regułach zapory.
