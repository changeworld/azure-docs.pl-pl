---
title: Wprowadzenie do maszyn wirtualnych usługi Azure Stack
description: Informacje o maszynach wirtualnych Azure stosu
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 41e75a6806cc5ff13fad64fd415344376e0d6e88
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Wprowadzenie do maszyn wirtualnych usługi Azure Stack

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="overview"></a>Przegląd
Azure stosu maszyn wirtualnych (VM) jest jeden typ na żądanie, skalowalnych zasobów obliczeniowych udostępniającej stosu Azure. W większości przypadków maszynę wirtualną należy wybrać wtedy, gdy potrzebna jest większa kontrola nad środowiskiem obliczeniowym niż ta, jaką oferują inne opcje. Ten artykuł zawiera informacje o kwestiach, jakie należy rozważyć przed przystąpieniem do tworzenia maszyny wirtualnej. Opisano w nim także tworzenie maszyny wirtualnej i zarządzanie nią.

Maszyna wirtualna Azure stosu zapewnia elastyczność wirtualizacji bez konieczności zarządzania klastrami lub poszczególnych maszyn. Jednak nadal należy do obsługi maszyny Wirtualnej, wykonując takie zadania, takie jak konfigurowanie, poprawki i instalacji oprogramowania, które uruchamia na nim.

Maszyny wirtualne Azure stosu można użyć na różne sposoby. Na przykład:

* **Projektowania i testowania** — maszynach wirtualnych platformy Azure stosu oferują szybki i prosty sposób tworzenia komputerów o określonej konfiguracji wymagane do kodu i testowania aplikacji.

* **Aplikacje w chmurze** — ponieważ żądanie aplikacji mogą się zmieniać, może być uzasadnione gospodarczego go uruchomić na maszynie Wirtualnej Azure stosu. Jeśli zajdzie potrzeba użycia dodatkowych maszyn wirtualnych, wystarczy je opłacić. Gdy dodatkowe maszyny staną się zbędne, można je wyłączyć.

* **Rozszerzony datacenter** — maszyn wirtualnych w sieci wirtualnej platformy Azure stosu można łatwo można podłączyć do sieci danej organizacji lub na platformie Azure.

Maszyny wirtualne, które aplikacja używa skalowanie w górę lub skalować w poziomie do niezależnie od jest wymagany do własnych potrzeb.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co należy wziąć pod uwagę przed utworzeniem maszyny wirtualnej?

Brak zawsze wiele uwagi dotyczące projektowania, podczas tworzenia infrastruktury aplikacji w stosie Azure. Te aspekty maszyny wirtualnej są ważne przed rozpoczęciem tworzenia infrastruktury:

* Nazwy zasobów aplikacji.
* Rozmiar maszyny Wirtualnej.
* Maksymalna liczba maszyn wirtualnych, które mogą zostać utworzone.
* System operacyjny, który działa na Maszynie wirtualnej.
* Konfiguracja maszyny Wirtualnej po jej uruchomieniu.
* Powiązane zasoby, które wymaga maszyny Wirtualnej.

### <a name="naming"></a>Nazewnictwo

Maszyna wirtualna ma przypisane nazwę i ma nazwę komputera, skonfigurowany jako część systemu operacyjnego. Nazwa maszyny wirtualnej może zawierać maksymalnie 15 znaków.

Jeśli używasz stosu Azure można utworzyć dysku systemu operacyjnego, nazwę komputera i nazwy maszyny wirtualnej są takie same. Jeśli przekazywanie i użyć własnego obrazu, który zawiera wcześniej skonfigurowany system operacyjny i go użyć do utworzenia maszyny wirtualnej, nazwy mogą być różne. Podczas przekazywania pliku obrazu, wprowadź nazwę komputera w systemie operacyjnym i nazwy maszyny wirtualnej, takie same, najlepszym rozwiązaniem.

### <a name="vm-size"></a>Rozmiar maszyny wirtualnej

Rozmiar maszyny wirtualnej, którego używasz zależy od obciążenia, który chcesz uruchomić. Wybrany rozmiar ma więc wpływ na takie czynniki jak moc procesora, pamięć i przestrzeń dyskowa. Stos Azure dostępna jest szeroka gama rozmiarów do obsługi wielu różnych zastosowań.

### <a name="vm-limits"></a>Limity maszyny Wirtualnej

Subskrypcja ma domyślne limity przydziału w miejscu, które mogą mieć wpływ na wdrożenie wielu maszyn wirtualnych dla projektu. Aktualny limit dla każdej subskrypcji wynosi 20 maszyn wirtualnych na region.

### <a name="operating-system-disks-and-images"></a>Dyski i obrazy z systemem operacyjnym

Maszyny wirtualne wykorzystują wirtualne dyski twarde do przechowywania ich systemu operacyjnego oraz danych. Wirtualne dyski twarde są również używane do obsługi obrazów, spośród których można wybierać, chcąc zainstalować system operacyjny.
Stos Azure udostępnia marketplace do użycia z różnych wersji i typów systemów operacyjnych. Obrazy Marketplace są identyfikowane przez wydawcy obrazu, oferty, jednostki sku i wersji (zazwyczaj wersja jest określony jako r.)

W poniższej tabeli przedstawiono kilka sposobów, czy można znaleźć informacje dotyczące obrazu:

|Metoda|Opis|
|---------|---------|
|Portal Azure stosu|Wartości są podawane automatycznie po wybraniu obrazu, który ma zostać użyty.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|Interfejsy API REST     |[Wyświetl listę wydawców obrazów](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Wyświetl listę ofert obrazów](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Listy obrazów jednostki SKU](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Istnieje możliwość przekazywania i użycie własnego obrazu. Jeśli to zrobisz, nazwę wydawcy, oferty i jednostka sku nie są używane.

### <a name="extensions"></a>Rozszerzenia

Rozszerzenia maszyny Wirtualnej zapewniają dodatkowe możliwości maszyny Wirtualnej za pomocą konfiguracji wdrożenia post i automatycznych zadań.
Typowe zadania można realizować przy użyciu różnych rozszerzeń:

* Uruchamianie niestandardowych skryptów — niestandardowe rozszerzenie skryptu służy do konfigurowania obciążeń na maszynie Wirtualnej, uruchamiając skrypt po zainicjowaniu obsługi maszyny Wirtualnej.
* Wdrażanie i zarządzanie konfiguracjami — rozszerzenie programu PowerShell żądanego stanu konfiguracji (DSC) pomaga skonfigurować DSC na maszynie Wirtualnej, do zarządzania konfiguracjami i środowisk.
* Dane diagnostyczne zbieranie — rozszerzenie diagnostyki Azure ułatwia konfigurowanie maszyny Wirtualnej do zbierania danych diagnostycznych, który może służyć do monitorowania kondycji aplikacji.

### <a name="related-resources"></a>Powiązane zasoby

Zasoby w poniższej tabeli są używane przez maszynę Wirtualną i muszą istnieć lub można utworzyć podczas tworzenia maszyny Wirtualnej.


|Zasób|Wymagane|Opis|
|---------|---------|---------|
|Grupa zasobów|Yes|Maszyna wirtualna musi być zawarta w grupie zasobów.|
|Konto magazynu|Yes|Maszyna wirtualna wymaga konta magazynu do przechowywania wirtualnych dysków twardych.|
|Sieć wirtualna|Yes|Maszyna wirtualna musi należeć do sieci wirtualnej.|
|Publiczny adres IP|Nie|Maszyna wirtualna może mieć przypisany publiczny adres IP umożliwiający uzyskiwanie do niej dostępu zdalnego.|
|Interfejs sieciowy|Yes|Maszyna wirtualna wymaga interfejsu sieciowego do komunikacji w sieci.|
|Dyski z danymi|Nie|Maszyna wirtualna może zawierać dyski danych zwiększające jej pojemność.|

## <a name="how-do-i-create-my-first-vm"></a>Jak utworzyć maszynę wirtualną?

Można wybrać kilka opcji, aby utworzyć Maszynę wirtualną. Wybór zależy od środowiska.
Poniższa tabela zawiera informacje ułatwiające rozpoczęcie pracy tworzenia maszyny Wirtualnej.


|Metoda|Artykuł|
|---------|---------|
|Portal Azure stosu|Utwórz maszynę wirtualną z systemem Windows przy użyciu portalu Azure stosu<br>[Utwórz maszynę wirtualną systemu Linux przy użyciu portalu Azure stosu](azure-stack-quick-linux-portal.md)|
|Szablony|Szablony szybkiego startu stosu Azure znajdują się w folderze:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell w stosie Azure](azure-stack-quick-create-vm-windows-powershell.md)<br>[Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell w stosie Azure](azure-stack-quick-create-vm-linux-powershell.md)|
|Interfejs wiersza polecenia|[Utwórz maszynę wirtualną z systemem Windows przy użyciu interfejsu wiersza polecenia Azure stosu](azure-stack-quick-create-vm-windows-cli.md)<br>[Utwórz maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia Azure stosu](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Jak zarządzać maszyną wirtualną?

Maszyny wirtualne przy użyciu narzędzia wiersza polecenia z obsługą skryptów lub bezpośrednio za pośrednictwem interfejsów API portal oparty na przeglądarce, można zarządzać. Niektórych typowych zadań zarządzania, które można wykonać są:

* Pobieranie informacji na temat maszyny Wirtualnej
* Połączenie z maszyną wirtualną
* Zarządzanie dostępności
* Tworzenie kopii zapasowych

### <a name="get-information-about-a-vm"></a>Uzyskiwanie informacji o maszynie wirtualnej

W poniższej tabeli przedstawiono możesz niektóre sposoby, które można pobrać informacji o maszynie Wirtualnej.


|Metoda|Opis|
|---------|---------|
|Portal Azure stosu|W menu centralnym kliknij maszyn wirtualnych, a następnie wybierz maszynę Wirtualną z listy. Na stronie dla maszyny Wirtualnej masz dostęp do informacji, ustawienie wartości, monitorowanie i metryki.|
|Azure PowerShell|Zarządzanie maszyn wirtualnych jest podobny w stosie Azure i usługi Azure. Aby uzyskać więcej informacji o korzystaniu z programu PowerShell zobacz następujący temat Azure:<br>[Tworzenie i zarządzanie maszynami wirtualnymi systemu Windows z modułu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Zestawy SDK klienta|Zarządzanie za pomocą języka C# maszyn wirtualnych jest podobny w stosie Azure i usługi Azure. Aby uzyskać więcej informacji zobacz następujący temat Azure:<br>[Tworzenie i zarządzanie maszynami wirtualnymi systemu Windows na platformie Azure przy użyciu języka C#](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Można użyć **Connect** przycisk w portalu Azure stos, aby nawiązać połączenie z maszyną Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

* [Zagadnienia dotyczące maszyn wirtualnych Azure stosu](azure-stack-vm-considerations.md)
