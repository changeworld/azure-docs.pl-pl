---
title: Obciążenia IBM na platformie Azure | Dokumenty firmy Microsoft
description: Użyj emulatora mainframe i innych usług od partnerów firmy Microsoft, aby ponownie hostować obciążenia IBM z/OS przy użyciu platformy Microsoft Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834587"
---
# <a name="ibm-workloads-on-azure"></a>Obciążenia IBM na platformie Azure

Wiele obciążeń ibm mainframe opartych na z/OS mogą być replikowane na platformie Azure bez utraty funkcjonalności i bez użytkowników nawet zauważyć zmiany w swoich systemach bazowych. Ponowne hostowanie aplikacji na platformie Azure zapewnia funkcje podobne do modelu mainframe, których potrzebujesz, a także elastyczność, dostępność i potencjalne oszczędności kosztów w chmurze.

Platforma Azure obsługuje integrację z istniejącymi środowiskami ibm mainframe, umożliwiając migrację aplikatorów, które mają sens, uruchamianie rozwiązań hybrydowych w razie potrzeby i migrację w czasie. Chociaż można całkowicie przepisać istniejące programy oparte na platformie Azure oparte na komputerach mainframe, jest bardziej powszechne, aby ponownie je hostować. Przepisywanie zwiększa koszt, złożoność i czas do projektów migracji. Dzięki ponownej hostowaniu możesz:

- Przenoszenie aplikacji do emulatora opartego na chmurze.

- Migrowanie bazy danych do bazy danych w chmurze.

- Wymień moduły i kod za pomocą aparatów przekształcania kodu.

Ponadto oprogramowanie IBM, w tym WebSphere i MQ, znajduje się teraz w portalu Azure Marketplace. Dzięki licencji na oprogramowanie IBM możesz korzystać ze skalowania infrastruktury na żądanie udostępnianego przez platformę Azure, aby szybko uruchomić maszynę wirtualną.

Dostępny jest rozbudowany ekosystem partnerów ułatwiacy migrację systemów MAINFRAME IBM na platformę Azure. Większość z nich stosuje pragmatyczne podejście do ponownego użycia w miarę możliwości przed rozpoczęciem stopniowego wdrażania przepisywania lub zastępowania aplikacji. Uzyskaj więcej wskazówek i pomocy od partnerów w [centrum migracji usługi Azure Mainframe](https://azure.microsoft.com/migration/mainframe/).

**Następne kroki**

- [Migracja mainframe: mity i fakty](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Instalowanie środowiska deweloperskiego/testowego IBM zD&T na platformie Azure](./install-ibm-z-environment.md)
- [Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD&T v1](./demo.md)
- [IBM DB2 pureScale na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
