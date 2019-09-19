---
title: Nazwa FQDN infrastruktury dla zapory platformy Azure
description: Dowiedz się więcej o nazwach FQDN infrastruktury w zaporze platformy Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130203"
---
# <a name="infrastructure-fqdns"></a>Infrastrukturalne nazwy FQDN

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. 

W wbudowanej kolekcji reguł znajdują się następujące usługi:

- Dostęp obliczeniowy do repozytorium obrazów platformy magazynu (PIR)
- Dostęp do magazynu stanu dysków zarządzanych
- Diagnostyka Azure i rejestrowanie (MDS)

## <a name="overriding"></a>Zastępuje 

Tę wbudowaną kolekcję reguł infrastruktury można zastąpić, tworząc ostatnią kolekcję reguł aplikacji, która jest przetwarzana jako Ostatnia. Zawsze będzie ona przetwarzana przed kolekcją reguł infrastruktury. Wszystko, co nie znajduje się w kolekcji reguł infrastruktury, zostanie odrzucone domyślnie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).