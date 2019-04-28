---
title: Infrastruktura nazwy FQDN dla zapory usługi Azure
description: Dowiedz się o infrastrukturę nazwy FQDN w zaporze na platformie Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066329"
---
# <a name="infrastructure-fqdns"></a>Infrastrukturalne nazwy FQDN

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. 

Następujące usługi są uwzględnione w kolekcji wbudowanych reguł:

- Obliczenia dostępu do magazynu repozytorium obrazów platformy (PIR)
- Dostęp do magazynu stanu dysków zarządzanych
- Narzędzie diagnostyczne systemu Azure i rejestrowania (MDS)
- Usługa Azure Active Directory

## <a name="overriding"></a>Zastępowanie 

Tej kolekcji reguł wbudowanych infrastruktury można zastąpić, tworząc odmowy wszystkich kolekcji reguł aplikacji, którą jest przetwarzane jako ostatnie. Zawsze będzie ona przetwarzana przed kolekcją reguł infrastruktury. Wszystko, co nie znajduje się w kolekcji reguł infrastruktury, zostanie odrzucone domyślnie.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [wdrażanie i konfigurowanie zapory usługi Azure](tutorial-firewall-deploy-portal.md).