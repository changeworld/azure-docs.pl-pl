---
title: Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher — witryna Azure portal | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia, jak zarządzać funkcji przechwytywania pakietów usługi Network Watcher przy użyciu witryny Azure portal
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
ms.openlocfilehash: 18e5f8eda51f8834f6346eef3d7ad31bc556290a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090201"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher przy użyciu portalu

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów obserwatora sieci umożliwia tworzenie sesji przechwytywania, aby śledzić ruch do i z maszyny wirtualnej. Filtry są dostarczane dla sesji przechwytywania, aby upewnić się, że Przechwytywanie ruchu, który ma. Przechwytywanie pakietów ułatwia diagnozowanie anomalie sieci w sposób reaktywny i aktywnie. Inne zastosowania obejmują zbierania statystyk sieciowych, uzyskiwanie informacji na temat włamań sieci, debugowanie komunikacja klient serwer i wiele więcej. Dzięki możliwości zdalnie wyzwalać Przechwytywanie pakietów, ta funkcja ułatwia obciążeń działających przechwytywania pakietów, ręcznie i tylko na odpowiednią maszynę, co pozwoli zaoszczędzić cenny czas.

Ten artykuł przeprowadzi Cię przez zadania zarządzania różnych, które są aktualnie dostępne do przechwytywania pakietów.

- [**Rozpocząć przechwytywania pakietu**](#start-a-packet-capture)
- [**Zatrzymać przechwytywania pakietu**](#stop-a-packet-capture)
- [**Usuwanie przechwycenia pakietu**](#delete-a-packet-capture)
- [**Pobierz przechwytywania pakietów**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz następujące zasoby:

- Wystąpienie usługi Network Watcher w regionie, w której chcesz utworzyć przechwytywania pakietów
- Maszyna wirtualna o włączeniu rozszerzenia przechwytywania pakietów.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga rozszerzenia maszyny wirtualnej `AzureNetworkWatcherExtension`. Instalowanie rozszerzenia na maszynie Wirtualnej Windows można znaleźć [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla Windows](../virtual-machines/windows/extensions-nwa.md) i maszyny Wirtualnej systemu Linux można znaleźć pod adresem [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Rozszerzenie agenta przechwytywania pakietów przy użyciu portalu

Aby zainstalować agenta maszyny Wirtualnej przechwytywania pakietów, za pośrednictwem portalu, przejdź do maszyny wirtualnej, kliknij przycisk **rozszerzenia** > **Dodaj** i wyszukaj **sieci obserwatorów agenta dla Windows**

![Widok agenta][agent]

## <a name="packet-capture-overview"></a>Omówienie przechwytywania pakietów

Przejdź do [witryny Azure portal](https://portal.azure.com) i kliknij przycisk **sieć** > **usługi Network Watcher** > **przechwytywania pakietów**

Na stronie Przegląd pokazuje, że przechwytuje listę wszystkich pakietów, które istnieją niezależnie od tego stanu.

> [!NOTE]
> Przechwytywanie pakietów wymaga następujących łączność.
> * Łączność wychodząca z kontem magazynu za pośrednictwem portu 443.
> * Łączność ruchu przychodzącego i wychodzącego 169.254.169.254
> * Łączność ruchu przychodzącego i wychodzącego z adresu 168.63.129.16

![ekran Przegląd przechwytywania pakietów][1]

## <a name="start-a-packet-capture"></a>Rozpocząć przechwytywania pakietu

Kliknij przycisk **Dodaj** utworzyć przechwytywania pakietów.

Dostępne są następujące właściwości, które mogą być definiowane na przechwycenie pakietu:

**Ustawienia główne**

- **Subskrypcja** — ta wartość jest subskrypcję, która jest używana, wystąpienie usługi network watcher jest niezbędna w ramach każdej subskrypcji.
- **Grupa zasobów** — grupa zasobów maszyny wirtualnej, która jest celem.
- **Docelowa maszyna wirtualna** -maszyny wirtualnej, która jest uruchomiona przechwycenie pakietu
- **Nazwa przechwycenia pakietu** — ta wartość jest nazwa przechwycenia pakietu.

**Przechwyć konfigurację**

- **Ścieżka do pliku lokalnego** -ścieżkę lokalną na maszynie wirtualnej, w którym jest zapisany przechwytywania pakietów (prawidłowe tylko wtedy, gdy **[plik]** jest zaznaczona). Należy określić prawidłową ścieżkę. Jeśli używasz maszyny wirtualnej systemu Linux ścieżka musi zaczynać się / var / przechwytuje.
- **Konto magazynu** — Określa, jeśli przechwycenie pakietu zostanie zapisana na koncie magazynu.
- **Plik** -Określa, w przypadku przechwytywania pakietów jest zapisywany lokalnie na maszynie wirtualnej.
- **Konta magazynu** — wybrane konto magazynu do zapisywania przechwytywania pakietów w. Domyślna lokalizacja to identyfikator name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription konta https://{storage} /resourcegroups/ {Nazwa maszyny name}/providers/microsoft.compute/virtualmachines/{virtual grupy zasobów} / {YY} / {MM} / {DD} / {HH} packetcapture__{MM}_.cap {XXX} _ {SS}. (Tylko przy włączonej **magazynu** jest zaznaczone)
- **Ścieżka do pliku lokalnego** — ścieżka lokalna na maszynie wirtualnej, aby zapisać przechwytywania pakietów. (Tylko przy włączonej **pliku** jest zaznaczona). Należy podać prawidłową ścieżkę. Dla maszyny wirtualnej systemu Linux, ścieżka musi zaczynać się */var/przechwytuje*.
- **Maksymalna liczba bajtów na pakiet** — liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie wartości bajtowe są przechwytywane, jeśli pole pozostanie puste.
- **Maksymalna liczba bajtów na sesję** — łączna liczba bajtów, które są przechwytywane, gdy wartość zostanie osiągnięty zatrzymuje przechwytywania pakietów.
- **Limit czasu (w sekundach)** -ustawia limit czasu na przechwycenie pakietu zatrzymać. Domyślna to 18000 sekund.

> [!NOTE]
> Konta usługi Premium storage obecnie nie są obsługiwane dla przechowywania pakietów przechwytuje.

**Filtrowanie (opcjonalne)**

- **Protokół** -protokołu, aby filtrować pod kątem przechwytywania pakietów. Dostępne wartości to TCP, UDP i dowolny.
- **Lokalny adres IP** -tę wartość filtry Przechwytywanie pakietów do pakietów, jeśli lokalny adres IP odpowiada tej wartości filtru.
- **Port lokalny** -tę wartość filtry Przechwytywanie pakietów do pakietów, gdzie port lokalny zgodna z tą wartością filtru.
- **Zdalny adres IP** -tę wartość filtry Przechwytywanie pakietów do pakietów, jeśli zdalny adres IP odpowiada tej wartości filtru.
- **Port zdalny** -tę wartość filtry Przechwytywanie pakietów do pakietów, gdy port zdalny zgodna z tą wartością filtru.

> [!NOTE]
> Port i adres IP adres wartości mogą być pojedynczej wartości, zakres wartości lub zestaw. (to znaczy port 80 – 1024, aby uzyskać) Można zdefiniować dowolną liczbę filtrów, jak chcesz.

Po wypełnieniu wartości kliknij przycisk **OK** utworzyć przechwytywania pakietów.

![Utwórz przechwytywania pakietów][2]

Po upływie limitu czasu, ustaw na przechwycenie pakietu przechwycenie pakietu zostanie zatrzymane, a mogą być przeglądane. Można też ręcznie zatrzymać sesji przechwytywania pakietów.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwycenia pakietu

W widoku przechwytywania pakietów, kliknij przycisk **menu kontekstowe** (...) lub kliknij prawym przyciskiem myszy i kliknij przycisk **Usuń** można zatrzymać przechwytywania pakietu

![Usuwanie przechwycenia pakietu][3]

> [!NOTE]
> Usuwanie przechwycenia pakietu nie powoduje usunięcia plików na koncie magazynu.

Zostanie wyświetlona prośba o potwierdzenie, aby usunąć przechwycenia pakietu, kliknij przycisk **tak**

![Potwierdzenie][4]

## <a name="stop-a-packet-capture"></a>Zatrzymać przechwytywania pakietu

W widoku przechwytywania pakietów, kliknij przycisk **menu kontekstowe** (...) lub kliknij prawym przyciskiem myszy i kliknij przycisk **zatrzymać** można zatrzymać przechwytywania pakietu

## <a name="download-a-packet-capture"></a>Pobierz przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów pliku przechwytywania jest przekazywany do magazynu obiektów blob lub do pliku lokalnego na maszynie Wirtualnej. Lokalizacja magazynu przechwytywania pakietów jest zdefiniowany podczas tworzenia sesji. Wygodne narzędzie do nich dostęp pliki przechwytywania zapisane na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:  http://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Kolejne kroki

Informacje o automatyzowaniu przechwytywania pakietów przy użyciu alertów maszyny wirtualnej, wyświetlając [tworzenie przechwytywania pakietów wyzwolonych alertów](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, czy niektóre jest dozwolony ruch do lub z maszyny Wirtualnej, odwiedzając [weryfikowanie przepływu protokołu IP z Sprawdź](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













