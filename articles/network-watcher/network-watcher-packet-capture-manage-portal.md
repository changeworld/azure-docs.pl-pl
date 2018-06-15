---
title: Zarządzanie przechwytywania pakietów z obserwatora sieciowego Azure - Azure portal | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia sposób zarządzania funkcja przechwytywania pakietów obserwatora sieciowego przy użyciu portalu Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 508b9e7eef757277d4bc0e93a26f3a63045f31e4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187535"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Zarządzanie przechwytywania pakietów za pomocą Monitora sieci Azure przy użyciu portalu

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API Azure REST](network-watcher-packet-capture-manage-rest.md)

Przechwytywania pakietów obserwatora sieciowego umożliwia tworzenie sesji przechwytywania, aby śledzić ruch do i z maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytywać ruch, który ma. Przechwytywania pakietów ułatwia diagnozowanie anomalii sieci rozbudowy i aktywne. Innych celów obejmują zbieranie statystyk sieciowych, uzyskanie informacji na temat wtargnięcia sieci, aby debugować komunikacja klient serwer i o wiele więcej. Dzięki możliwości zdalnie wywołać przechwytywania pakietów, ta funkcja ułatwia obciążeń uruchomionych przechwytywania pakietów, ręcznie i tylko na odpowiednią maszynę, która zapisuje cenny czas.

Ten artykuł przedstawia za pomocą zadań zarządzania różnych, które są obecnie dostępne dla przechwytywania pakietów.

- [**Uruchom przechwytywania pakietów**](#start-a-packet-capture)
- [**Zatrzymaj przechwytywania pakietów**](#stop-a-packet-capture)
- [**Usuń przechwytywania pakietów**](#delete-a-packet-capture)
- [**Pobierz przechwytywania pakietów**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

- Wystąpienia obserwatora sieciowego w regionie, w którym chcesz utworzyć przechwytywania pakietów
- Maszyna wirtualna z rozszerzeniem przechwytywania pakietów włączone.

> [!IMPORTANT]
> Rozszerzenie maszyny wirtualnej wymaga przechwytywania pakietów `AzureNetworkWatcherExtension`. Instalowanie rozszerzenia na maszynie Wirtualnej systemu Windows można znaleźć [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Windows](../virtual-machines/windows/extensions-nwa.md) i dla maszyny Wirtualnej systemu Linux, odwiedź [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Rozszerzenie agenta przechwytywania pakietów, za pośrednictwem portalu

Aby zainstalować agenta pakietu Przechwytywanie maszyny Wirtualnej za pośrednictwem portalu, przejdź do maszyny wirtualnej, kliknij przycisk **rozszerzenia** > **Dodaj** i wyszukaj **sieci obserwatorów agenta dla systemu Windows**

![Widok agenta][agent]

## <a name="packet-capture-overview"></a>Omówienie przechwytywania pakietów

Przejdź do [portalu Azure](https://portal.azure.com) i kliknij przycisk **sieci** > **obserwatora sieciowego** > **przechwytywania pakietów**

Strony Przegląd przedstawia listę wszystkich pakietów przechwytuje, który istnieje, bez względu na stan.

> [!NOTE]
> Przechwytywania pakietów wymaga następujących łączność.
> * Łączność wychodząca z kontem magazynu za pośrednictwem portu 443.
> * Przychodzący i wychodzący łączności 169.254.169.254
> * Przychodzący i wychodzący łączności 168.63.129.16

![ekran Przegląd przechwytywania pakietów][1]

## <a name="start-a-packet-capture"></a>Uruchom przechwytywania pakietów

Kliknij przycisk **Dodaj** utworzyć przechwytywania pakietów.

Właściwości, które mogą być definiowane w przechwytywania pakietów są:

**Ustawienia głównego**

- **Subskrypcja** — ta wartość jest subskrypcji, która jest używana, wystąpienia obserwatora sieciowego jest niezbędna w każdej subskrypcji.
- **Grupa zasobów** -grupa zasobów do docelowej maszyny wirtualnej.
- **Docelowa maszyna wirtualna** -maszynę wirtualną, która działa przechwytywania pakietów
- **Nazwa przechwytywania pakietów** — ta wartość jest nazwą przechwytywania pakietów.

**Przechwytywanie konfiguracji**

- **Ścieżka do pliku lokalnego** — ścieżka lokalna na maszynie wirtualnej, w której jest zapisywany przechwytywania pakietów (prawidłowe tylko w przypadku **[plik]** jest zaznaczona). Należy określić prawidłową ścieżkę. Jeśli korzystasz z maszyny wirtualnej systemu Linux, ścieżka musi rozpoczynać się od / var / przechwytuje.
- **Konto magazynu** — Określa, czy przechwytywania pakietów jest zapisywane na koncie magazynu.
- **Plik** — Określa, czy przechwytywania pakietów jest zapisywane lokalnie na maszynie wirtualnej.
- **Konta magazynu** — wybrać konto magazynu, aby zapisać pakiet w. Domyślna lokalizacja to identyfikator name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription konta https://{storage} /resourcegroups/ {Nazwa maszyny name}/providers/microsoft.compute/virtualmachines/{virtual grupy zasobów} / {YY} / {MM} / {DD} / {HH} packetcapture__{MM}_ CAP _ {XXX} {SS}. (Włączone, tylko jeśli **magazynu** jest zaznaczone)
- **Ścieżka do pliku lokalnego** — ścieżka lokalna na maszynie wirtualnej, aby zapisać przechwytywania pakietów. (Włączone, tylko jeśli **pliku** jest zaznaczona). Należy podać prawidłową ścieżkę. Dla maszyny wirtualnej systemu Linux, ścieżka musi rozpoczynać się od */var/przechwytuje*.
- **Maksymalna liczba bajtów na pakiet** — liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie bajty są przechwytywane, jeśli pole pozostanie puste.
- **Maksymalna liczba bajtów na sesji** — jest to całkowita liczba bajtów, które są przechwytywane, gdy wartość osiągnięciu zatrzymuje przechwytywania pakietów.
- **Limit czasu (w sekundach)** -ustawia limit czasu dla przechwytywania pakietów zatrzymać. Domyślna to 18000 sekund.

> [!NOTE]
> Konta Premium magazynu nie są obecnie obsługiwane dla przechwytuje przechowywania pakietów.

**Filtrowanie (opcjonalnie)**

- **Protokół** — protokół, aby filtrować pod kątem przechwytywania pakietów. Dostępne wartości to TCP, UDP i dowolne.
- **Lokalny adres IP** — ta wartość filtry przechwytywania pakietów do pakietów Jeśli lokalny adres IP odpowiada to wartości filtru.
- **Port lokalny** — ta wartość filtry przechwytywania pakietów do pakietów gdzie portu lokalnego zgodna z tą wartością filtru.
- **Zdalny adres IP** — ta wartość filtry przechwytywania pakietów do pakietów Jeśli zdalny adres IP odpowiada to wartości filtru.
- **Port zdalny** — ta wartość filtry przechwytywania pakietów do pakietów Jeśli port zdalny odpowiada to wartości filtru.

> [!NOTE]
> Wartości adresów IP i port może być pojedynczą wartością, zakres wartości lub zestawu. (to znaczy port 80 – 1024 dla) Można określić dowolną liczbę filtry.

Po wartości są wypełnione, kliknij przycisk **OK** utworzyć przechwytywania pakietów.

![Utwórz przechwytywania pakietów][2]

Po upływie terminu na przechwytywania pakietów przechwytywania pakietów spowoduje zatrzymanie i mogą być przeglądane. Można też ręcznie zatrzymać sesji przechwytywania pakietów.

## <a name="delete-a-packet-capture"></a>Usuń przechwytywania pakietów

W widoku przechwytywania pakietów, kliknij przycisk **menu kontekstowe** (...) lub kliknij prawym przyciskiem myszy i kliknij przycisk **usunąć** przestanie przechwytywania pakietów

![Usuń przechwytywania pakietów][3]

> [!NOTE]
> Usunięcie przechwytywania pakietów nie powoduje usunięcia plików na koncie magazynu.

Zostanie wyświetlona prośba o potwierdzenie usunięcia przechwytywania pakietów, kliknij przycisk **tak**

![Potwierdzenie][4]

## <a name="stop-a-packet-capture"></a>Zatrzymaj przechwytywania pakietów

W widoku przechwytywania pakietów, kliknij przycisk **menu kontekstowe** (...) lub kliknij prawym przyciskiem myszy i kliknij przycisk **zatrzymać** przestanie przechwytywania pakietów

## <a name="download-a-packet-capture"></a>Pobierz przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów, plik przechwytywania jest przekazywany do magazynu obiektów blob lub do pliku lokalnego na maszynie Wirtualnej. Lokalizacja magazynu przechwytywania pakietów jest definiowany podczas tworzenia sesji. Wygodne narzędzie one dostęp do plików przechwytywania na koncie magazynu jest Microsoft Azure Eksploratora usługi Storage, który można pobrać tutaj:  http://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak można zautomatyzować przechwytywania pakietów z alertami maszyny wirtualnej, wyświetlając [utworzyć przechwytywania alertów wyzwalanych pakietów](network-watcher-alert-triggered-packet-capture.md)

Znajdź, jeśli niektórych ruch jest dozwolony w lub z maszyny Wirtualnej, odwiedzając [Sprawdź przepływ Sprawdź IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













