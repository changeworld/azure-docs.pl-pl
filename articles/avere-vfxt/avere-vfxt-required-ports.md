---
title: Port listy dozwolonych adresów dla vFXT Avere dla platformy Azure
description: Lista portów używanych przez Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634175"
---
# <a name="required-ports"></a>Wymagane porty

Portów wymienionych w tej sekcji służą do vFXT komunikacji dla ruchu przychodzącego i wychodzącego.

Nigdy nie ujawniają klastra vFXT lub wystąpienie kontrolera klastra bezpośrednio do publicznej sieci internet.

## <a name="api"></a>Interfejs API

| Przychodzące: | | |
| --- | ---- | --- |
| TCP | 22  | Protokół SSH  |
| TCP | 443 | HTTPS|



| Wychodzące: |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| Przychodzący i wychodzący  | | |
| --- | --- | ---|
| TCP/UDP | 111  | USŁUGI RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | STAN   |

