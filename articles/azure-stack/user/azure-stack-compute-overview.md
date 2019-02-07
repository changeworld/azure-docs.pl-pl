---
title: Wprowadzenie do maszyn wirtualnych usługi Azure Stack
description: Dowiedz się więcej o maszynach wirtualnych usługi Azure Stack
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: cd42a347de236de2e3374d7ac854779f4c222a00
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768150"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Wprowadzenie do maszyn wirtualnych usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack oferuje maszyn wirtualnych (VM) jako jednego typu na żądanie, skalowalnych zasobów obliczeniowych. Można wybrać Maszynę wirtualną, gdy potrzebujesz większej kontroli nad środowiskiem obliczeniowym. Ten artykuł zawiera szczegółowe informacje, przed utworzeniem pierwszej maszyny Wirtualnej.

Maszynę wirtualną platformy Azure Stack umożliwia swobodne korzystanie z wirtualizacji bez konieczności zarządzania klastrami lub poszczególnych maszyn. Jednak nadal trzeba utrzymywać w maszynie Wirtualnej, wykonując zadania, takie jak konfigurowania, poprawiania i instalacji oprogramowania, które uruchamia na ich temat.

Za pomocą usługi Azure Stack maszyny wirtualne na kilka sposobów. Na przykład:

- **Tworzenie i testowanie**  
    Maszyny wirtualne platformy Azure Stack umożliwia utworzenie komputera o określonej konfiguracji wymaganej do kodowania i testowania aplikacji.

- **Aplikacje w chmurze**  
    Ponieważ żądanie aplikacji mogą się zmieniać, może mieć ze względów ekonomicznych warto ją uruchomić na maszynie Wirtualnej w usłudze Azure Stack. Płaci się za dodatkowe maszyny wirtualne, gdy potrzeby i wyłączać je po użytkownik nie.

- **Rozszerzone centrum danych**  
    Maszyny wirtualne w sieci wirtualnej usługi Azure Stack może być połączona z siecią organizacji lub na platformie Azure.

Maszyny wirtualne, które używane przez aplikację można skalować w górę lub skalowania w poziomie, aby niezależnie od rodzaju jest wymagany do własnych potrzeb.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co należy wziąć pod uwagę przed utworzeniem maszyny Wirtualnej

Brak zawsze zagadnienia dotyczące projektowania, podczas tworzenia infrastruktury aplikacji w usłudze Azure Stack. Te aspekty maszyny Wirtualnej są ważne wziąć pod uwagę przed rozpoczęciem tworzenia infrastruktury:

- Nazwy zasobów aplikacji.
- Rozmiar maszyny Wirtualnej.
- Maksymalna liczba maszyn wirtualnych, które mogą być tworzone.
- System operacyjny uruchomiony na maszynie Wirtualnej.
- Konfiguracja maszyny Wirtualnej po jej uruchomieniu.
- Powiązane zasoby, których potrzebuje maszyny Wirtualnej.

### <a name="naming"></a>Nazewnictwo

Maszyna wirtualna ma nazwę do niej przypisany, i ma nazwę komputera, skonfigurowany jako część systemu operacyjnego. Nazwa maszyny wirtualnej może zawierać maksymalnie 15 znaków.

Jeśli używasz usługi Azure Stack można utworzyć dysku systemu operacyjnego, nazwę komputera i nazwę maszyny wirtualnej są takie same. Jeśli przekazywanie i użycia własnego obrazu, który zawiera skonfigurowany wcześniej system operacyjny i używać go do utworzenia maszyny wirtualnej, nazwy mogą być różne. Przesłany własny plik obrazu, najlepszym rozwiązaniem, upewnij się, nazwa komputera systemu operacyjnego i nazwę maszyny wirtualnej są takie same.

### <a name="vm-size"></a>Rozmiar maszyny wirtualnej

Rozmiar maszyny Wirtualnej, którego używasz, zależy od obciążenia, które chcesz uruchomić. Wybrany rozmiar ma więc wpływ na takie czynniki jak moc procesora, pamięć i przestrzeń dyskowa. Usługa Azure Stack oferuje wiele różnych rozmiarów do obsługi wielu różnych zastosowań.

### <a name="vm-limits"></a>Limity maszyn wirtualnych

Twoja subskrypcja ma domyślnie ustawiony limit przydziałów, w miejscu, które mogą mieć wpływ na wdrożenie maszyn wirtualnych w projekcie. Aktualny limit dla każdej subskrypcji wynosi 20 maszyn wirtualnych na region.

### <a name="operating-system-disks-and-images"></a>Dyski i obrazy z systemem operacyjnym

Maszyny wirtualne wykorzystują wirtualne dyski twarde do przechowywania ich systemu operacyjnego oraz danych. Wirtualne dyski twarde są również używane do obsługi obrazów, spośród których można wybierać, chcąc zainstalować system operacyjny. Usługa Azure Stack zapewnia marketplace do użycia z różnymi wersjami i typami systemów operacyjnych. Obrazy z Marketplace są identyfikowane za pomocą wydawcy obrazu, oferty, jednostki SKU i wersji (zwykle wersja jest określony jako **najnowsze**.)

W poniższej tabeli przedstawiono, jak znaleźć informacje o obrazie:

|Metoda|Opis|
|---------|---------|
|Portal usługi Azure Stack|Wartości są podawane automatycznie po wybraniu obrazu, który ma zostać użyty.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|Interfejsy API REST     |[Wyświetl listę wydawców obrazów](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Wyświetl listę ofert obrazów](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Obraz listy jednostek SKU](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Istnieje możliwość przesłania i użycia własnego obrazu. Jeśli to zrobisz, nazwę wydawcy, oferta i jednostka SKU nie są używane.

### <a name="extensions"></a>Rozszerzenia

Rozszerzenia maszyn wirtualnych zapewniają dodatkowe możliwości maszyny Wirtualnej za pomocą konfiguracji po wdrożeniu i do automatyzacji zadań.
Typowe zadania można realizować przy użyciu różnych rozszerzeń:

- **Uruchamianie skryptów niestandardowych**  
    Rozszerzenie niestandardowego skryptu ułatwia konfigurowanie obciążeń na maszynie Wirtualnej poprzez uruchomienie skryptu, gdy maszyna wirtualna jest aprowizowana.

- **Wdrażanie i zarządzanie konfiguracjami**  
    Rozszerzenie PowerShell Desired State Configuration (DSC) ułatwia konfigurowanie DSC na maszynie Wirtualnej w celu zarządzania konfiguracjami i środowiskami.

- **Zbieranie danych diagnostycznych**  
    Rozszerzenie diagnostyki platformy Azure służy do konfigurowania maszyny Wirtualnej w celu zbierania danych diagnostycznych, który może służyć do monitorowania kondycji aplikacji.

### <a name="related-resources"></a>Powiązane zasoby

W poniższej tabeli przedstawiono zasoby są używane przez maszynę Wirtualną i muszą istnieć lub być tworzone podczas tworzenia maszyny Wirtualnej:

|Zasób|Wymagane|Opis|
|---------|---------|---------|
|Grupa zasobów|Yes|Maszyna wirtualna musi być zawarta w grupie zasobów.|
|Konto magazynu|Nie|Maszyna wirtualna nie wymaga konta magazynu do przechowywania wirtualnych dysków twardych, jeśli korzystają z dysków zarządzanych. <br>Maszyna wirtualna wymagane jest konto magazynu do przechowywania wirtualnych dysków twardych, jeśli przy użyciu dysków niezarządzanych.|
|Sieć wirtualna|Yes|Maszyna wirtualna musi należeć do sieci wirtualnej.|
|Publiczny adres IP|Nie|Maszyna wirtualna może mieć przypisany publiczny adres IP umożliwiający uzyskiwanie do niej dostępu zdalnego.|
|Interfejs sieciowy|Yes|Maszyna wirtualna wymaga interfejsu sieciowego do komunikacji w sieci.|
|Dyski z danymi|Nie|Maszyna wirtualna może zawierać dyski danych zwiększające jej pojemność.|

## <a name="create-your-first-vm"></a>Tworzenie pierwszej maszyny Wirtualnej

Masz kilka opcji, aby utworzyć Maszynę wirtualną. Wybór zależy od środowiska. Poniższa tabela zawiera informacje ułatwiające rozpoczęcie tworzenia maszyny Wirtualnej:

|Metoda|Artykuł|
|---------|---------|
|Portal usługi Azure Stack|Utwórz maszynę wirtualną Windows za pomocą portalu usługi Azure Stack<br>[Utwórz maszynę wirtualną systemu Linux przy użyciu portalu Azure Stack](azure-stack-quick-linux-portal.md)|
|Szablony|Szablony usługi Azure Stack Przewodnik Szybki Start znajdują się w lokalizacji:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell w usłudze Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)<br>[Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell w usłudze Azure Stack](azure-stack-quick-create-vm-linux-powershell.md)|
|Interfejs wiersza polecenia|[Utwórz maszynę wirtualną Windows przy użyciu interfejsu wiersza polecenia w usłudze Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Utwórz maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia w usłudze Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Zarządzaj maszyną wirtualną

Można zarządzać maszynami wirtualnymi przy użyciu portalu w przeglądarce internetowej, narzędzia wiersza polecenia z obsługą skryptów oraz bezpośrednio za pośrednictwem interfejsów API. Przedstawiono kilka typowych zadań zarządzania, które może wykonywać:

- Pobieranie informacji na temat maszyny Wirtualnej
- Łączenie z maszyną wirtualną
- Zarządzanie dostępnością
- Wykonywanie kopii zapasowych

### <a name="get-information-about-your-vm"></a>Uzyskaj informacje o maszynie Wirtualnej

Poniższej tabeli przedstawiono niektóre metody, których można uzyskać informacji o maszynie Wirtualnej.

|Metoda|Opis|
|---------|---------|
|Portal usługi Azure Stack|W menu Centrum kliknij maszyn wirtualnych, a następnie wybierz maszynę Wirtualną z listy. Na stronie dla maszyny Wirtualnej masz dostęp do podstawowych informacji, wartości ustawień i metryk monitorowania.|
|Azure PowerShell|Zarządzanie maszynami wirtualnymi jest podobny na platformie Azure i usługi Azure Stack. Aby uzyskać więcej informacji na temat przy użyciu programu PowerShell zobacz następujący temat platformy Azure:<br>[Tworzenie i zarządzanie maszynami wirtualnymi Windows za pomocą modułu Azure PowerShell](../../virtual-machines/windows/tutorial-manage-vm.md#understand-vm-sizes)|
|Zestawy SDK klienta|Zarządzanie za pomocą języka C# maszyn wirtualnych jest podobny na platformie Azure i usługi Azure Stack. Aby uzyskać więcej informacji zobacz następujący temat platformy Azure:<br>[Tworzenie i zarządzanie nimi Windows maszyn wirtualnych na platformie Azure przy użyciu języka C#](../../virtual-machines/windows/csharp.md)|

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Możesz użyć **Connect** przycisk w portalu usługi Azure Stack, aby nawiązać połączenie z maszyną Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

- [Uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md)
