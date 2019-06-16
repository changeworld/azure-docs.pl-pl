---
title: Pomiary rzeczywistego użytkownika w usłudze Azure Traffic Manager
description: Wprowadzenie do pomiarów rzeczywistego użytkownika w usłudze Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: cbde96f135aa52ada9bb10b47ca81687992e5833
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070981"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Omówienie pomiarów rzeczywistego użytkownika Menedżera ruchu

Po skonfigurowaniu profilu usługi Traffic Manager przy użyciu metody routingu wydajności usługi przegląda gdzie pochodzą z żądań zapytań DNS i sprawia, że decyzje w kwestii routingu do kierowania tych dostępu komputerom zgłaszającym żądania do regionu platformy Azure, co umożliwia im najmniejszego opóźnienia. Jest to realizowane przy użyciu analizy opóźnienia sieci, które usługi Traffic Manager obsługuje dla użytkowników końcowych w różnych sieciach.

Pomiary dotyczące prawdziwych użytkowników umożliwia pomiar pomiary opóźnienia sieci do regionów platformy Azure z aplikacji klienckich, których użytkownicy końcowi, i mają usługi Traffic Manager należy wziąć pod uwagę także te informacje podczas podejmowania decyzji dotyczących routingu. Wybierając używania pomiarów rzeczywistego użytkownika, można zwiększyć jego dokładność routingu w przypadku żądań pochodzących z tych sieci, w którym znajdują się użytkownicy końcowi. 

## <a name="how-real-user-measurements-work"></a>Jak działają pomiarów rzeczywistego użytkownika

Pomiary dotyczące prawdziwych użytkowników działają przez opóźnienie miary aplikacji klienta do regionów platformy Azure widziany przez sieci przez użytkownika końcowego, w których są używane. Na przykład, jeśli strony sieci web, która jest dostępna dla użytkowników w różnych lokalizacjach (na przykład w regionach Ameryki Północnej), umożliwia pomiarów rzeczywistego użytkownika za pomocą metody routingu opartego na wydajności przygotowania ich do najlepszych regionu platformy Azure, w którym serwer Aplikacja jest hostowana.

Rozpoczyna się od osadzania Azure podanego kodu JavaScript (z unikatowym kluczem w nim) na stronach sieci web. Gdy zostanie to zrobione, zawsze wtedy, gdy użytkownik odwiedzi tej strony sieci Web, kod JavaScript wysyła zapytanie do usługi Traffic Manager, aby uzyskać informacje o regionach platformy Azure, czy powinien on miar. Usługa zwraca zestaw punktów końcowych do skryptu, a następnie miary w tych regionach po kolei pobierając obrazem piksela hostowaną w tych regionach platformy Azure i zapisując opóźnienia między czasem żądanie zostało wysłane i godzinę po odebrania pierwszego bajtu . Te są następnie odsyłane usługi Traffic Manager.

Wraz z upływem czasu dzieje się tak wiele razy, a w wielu sieciach prowadzące do usługi Traffic Manager wprowadzenie dokładniejszych informacji na temat opóźnienia sieci, w którym znajdują się użytkownicy końcowi. Te informacje uruchamia, wprowadzenie do uwzględnienia w decyzje w kwestii routingu podjęte przez Traffic Manager. W rezultacie prowadzi do zwiększenia dokładności w te decyzje oparte na pomiarów rzeczywistego użytkownika wysyłane.

Gdy używasz pomiarów rzeczywistego użytkownika, są rozliczane na podstawie liczby pomiarów wysyłane do usługi Traffic Manager. Aby uzyskać więcej informacji na temat cen, odwiedź stronę [usługi Traffic Manager stronę z cennikiem](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak używać [pomiarów rzeczywistego użytkownika ze stronami sieci web](traffic-manager-create-rum-web-pages.md)
- Dowiedz się, [jak działa usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez usługę Traffic Manager
- Dowiedz się, jak [Tworzenie profilu usługi Traffic Manager](traffic-manager-create-profile.md)

