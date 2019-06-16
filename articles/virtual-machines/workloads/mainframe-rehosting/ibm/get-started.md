---
title: IBM obciążeń na platformie Azure | Dokumentacja firmy Microsoft
description: Umożliwia ponowne hostowanie obciążeń IBM z/OS za pomocą programu Microsoft Azure w emulatora mainframe i innych usług od partnerów firmy Microsoft.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: ccecfd6abe0e9418e217ad7f200ce980d73c7b45
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61468333"
---
# <a name="ibm-workloads-on-azure"></a>IBM obciążeń na platformie Azure

Wiele obciążeń mainframe firmy IBM z/OS w oparciu mogą być replikowane na platformie Azure bez utraty funkcjonalności i bez użytkowników, nawet Obserwowanie zmian w ich systemach bazowego. Rehostowanie aplikacji na platformie Azure zapewnia funkcje podobne do komputera mainframe, potrzebnych oraz elastyczność, dostępności, i potencjalne oszczędności chmury.

Platforma Azure obsługuje integrację z istniejącymi środowiskami mainframe firmy IBM, dzięki któremu można migrować applicates, które mają sens, uruchamianie rozwiązań hybrydowych w przypadku, gdy potrzebne, a następnie przeprowadzić migrację wraz z upływem czasu. Chociaż można całkowicie napisać ponownie istniejących programów typu mainframe na platformie Azure, jest bardziej powszechne, aby je rehost. Ponowne napisanie dodaje koszty, złożoność i czas do projektów migracji. Za pomocą ponowny hosting, możesz wykonywać następujące czynności:

- Przenieś aplikacje na emulator oparty na chmurze.

- Migrowanie bazy danych do bazy danych opartej na chmurze.

- Zastąp modułów i kodu za pomocą aparatów przekształcania kodu.

Ponadto oprogramowanie firmy IBM, w tym produkty WebSphere i MQ, jest teraz w witrynie Azure Marketplace. Posiadając licencję na oprogramowanie firmy IBM, możesz wykorzystać możliwości skalowania infrastruktury na żądanie, które platforma Azure udostępnia, aby umożliwić szybkie uruchomienie maszyny wirtualnej.

Ekosystem partnerów rozległe są dostępne dla pomagająca w migracji systemach mainframe firmy IBM na platformie Azure. Większość wykonaj pragmatyczne podejście ponowne używanie wszędzie tam, gdzie to możliwe przed wdrożeniem wdrożenia etapowego ponownego zapisywania adresów lub zastąpienie aplikacji. Uzyskaj więcej wskazówek i pomocy partnerów [Centrum migracji komputera Mainframe Azure](https://azure.microsoft.com/migration/mainframe/).

**Następne kroki**

- [Migracja komputera mainframe: mitów i faktów](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Instalowanie IBM zD & T, środowisko programistyczne/testowe na platformie Azure](./install-ibm-z-environment.md)
- [Ustaw się aplikacja deweloperów kontrolowane dystrybucji (ADCD) w IBM zD & T w wersji 1](./demo.md)
- [IBM DB2 pureScale na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
