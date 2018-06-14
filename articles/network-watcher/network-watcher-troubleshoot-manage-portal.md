---
title: Rozwiązywanie problemów z bramy sieci wirtualnej platformy Azure i połączenia — Portal | Dokumentacja firmy Microsoft
description: Ta strona wyjaśniono, jak użyć obserwatora sieciowego Azure Rozwiązywanie problemów z portalu
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: becc4f799e34aabba5c703f09e51a4b7d497c45a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409112"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-network-watcher-from-azure-portal"></a>Rozwiązywanie problemów z Brama sieci wirtualnej i połączeń za pomocą Monitora sieci z portalu Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Obserwatora sieciowego zawiera wiele funkcji w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jeden z tych funkcji jest zasobem rozwiązywania problemów. Rozwiązywanie problemów z zasobów można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu, obserwatora sieciowego sprawdza kondycję Brama sieci wirtualnej lub połączenie i zwraca jego wyniki.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

Lista odwiedziny typy obsługiwanych bramy [bramy obsługiwane typy](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Przegląd

Rozwiązywanie problemów z zasobów umożliwia rozwiązywanie problemów, które wynikają z bramy sieci wirtualnej i połączenia. Po wysłaniu żądania do zasobów dotyczących rozwiązywania problemów, dzienniki są proszeni i inspekcji. Po zakończeniu kontroli wyniki są zwracane. Żądania dotyczące rozwiązywania problemów są długotrwałe żądania zasobów, może to potrwać kilka minut do zwrócenia wyniku. Dzienniki na rozwiązywanie problemów z są przechowywane w kontenerze na koncie magazynu, który jest określony.

## <a name="troubleshoot-a-gateway-or-connection"></a>Rozwiązywanie problemów z bramy i połączenie

1. Przejdź do [portalu Azure](https://portal.azure.com) i kliknij przycisk **sieci** > **obserwatora sieciowego** > **diagnostyki sieci VPN**
2. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**.
3. Rozwiązywanie problemów z zasobów zwraca dane dotyczące kondycji zasobu. Zapisuje również odpowiedniego dzienniki na konto magazynu, należy sprawdzić. Kliknij przycisk **konta magazynu** wybierz konto magazynu.
4. Na **kont magazynu** bloku, wybrać istniejące konto magazynu, lub kliknij przycisk **+ konto magazynu** Aby utworzyć nowy.
5. Na **kontenery** bloku, wybierz istniejącego kontenera, lub kliknij przycisk **+ kontener** do utworzenia nowego kontenera. Po zakończeniu kliknij przycisk **wybierz**
6. Wybierz zasoby bramy i połączenia, rozwiązywanie problemów i kliknij przycisk **Uruchom Rozwiązywanie problemów**

Jeśli wybrano wiele zasobów, rozwiązywania problemów jest uruchamiane jednocześnie zasobów bramy. Nie można uruchomić na połączenie i związany z nim bramy w tym samym czasie. Zalecane jest rozwiązywanie problemów z bram najpierw Rozwiązywanie problemów z połączenia jest procesem dłużej. Podczas diagnostyki sieci VPN w zasobie **rozwiązywania problemów stanu** będzie stan kolumny **uruchomiona**

Po zakończeniu w kolumnie Stan zmienia się na **dobra kondycja** lub **niezdrowego**.

![Rozwiązywanie problemów ukończone][2]

## <a name="understanding-the-results"></a>Opis wyników

W **szczegóły** okna, **stan** karta przedstawia stan ostatniego rozwiązywania problemów uruchamiania wybranego zasobu. Wyniki diagnostyki najnowsze są wyświetlane xx minut od ostatniego uruchomienia.

|Właściwość  |Opis  |
|---------|---------|
|Zasób     | Łącze do zasobu.        |
|Ścieżki do magazynu     |  Ścieżka do konto magazynu i kontener, które zawierają dzienniki (Jeśli żadnego zostały utworzone podczas uruchamiania). To ustawienie nie jest trwały, po zakończeniu działania portalu.        |
|Podsumowanie     | Podsumowanie kondycji zasobów.        |
|Szczegóły     | Szczegółowe informacje o kondycji zasobów.        |
|Ostatnie uruchomienie     | Czas ostatniego czasu rozwiązywania zostało uruchomione.        |


**Akcji** karta zawiera ogólne wskazówki na temat sposobu rozwiązania problemu. Jeśli dla problemu można podjąć akcję, link jest dostarczany z dodatkowych wskazówek. W przypadku których nie ma dodatkowych wskazówek, odpowiedź zawiera adres url, aby otworzyć sprawę pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i dostępnych odwiedź [Rozwiązywanie problemów z obserwatora sieciowego — omówienie](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>Kolejne kroki

Jeśli ustawienia zostały zmienione które zatrzymania połączenia sieci VPN, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) śledzić reguły zabezpieczeń sieciowych grupy i zabezpieczeń, które mogą być zagrożona.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
