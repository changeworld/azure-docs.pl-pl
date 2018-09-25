---
title: Infrastruktura nazwy FQDN dla zapory usługi Azure
description: Dowiedz się o infrastrukturę nazwy FQDN w zaporze na platformie Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 1369a82829b2c80768d746ba92daf2482c1fd7f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994143"
---
# <a name="infrastructure-fqdns"></a>Nazwy FQDN infrastruktury

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