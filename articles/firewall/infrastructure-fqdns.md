---
title: Sieci FQDN infrastruktury dla zapory platformy Azure
description: Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168979"
---
# <a name="infrastructure-fqdns"></a>Infrastrukturalne nazwy FQDN

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. 

Następujące usługi są zawarte w wbudowanej kolekcji reguł:

- Dostęp obliczeniowy do repozytorium obrazów platformy pamięci masowej (PIR)
- Dostęp do magazynu stanu dysków zarządzanych
- Diagnostyka i rejestrowanie platformy Azure (MDS)

## <a name="overriding"></a>Zastępowanie 

Można zastąpić tę kolekcję reguł wbudowanej infrastruktury, tworząc kolekcję reguł odmowy wszystkich aplikacji, która jest przetwarzana jako ostatnia. Zawsze będzie ona przetwarzana przed kolekcją reguł infrastruktury. Wszystko, co nie znajduje się w kolekcji reguł infrastruktury, zostanie odrzucone domyślnie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć i skonfigurować Zaporę platformy Azure](tutorial-firewall-deploy-portal.md).