---
title: Uaktualnienie z podstawowego wewnętrznego do standardowego wewnętrznego — moduł równoważenia obciążenia platformy Azure
description: W tym artykule pokazano, jak uaktualnić wewnętrzny moduł równoważenia obciążenia platformy Azure z podstawowej jednostki SKU do standardowej jednostki SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: c2c909d8ef2be982d4dd4a70b5f35d03e8e71418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659972"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Uaktualnianie wewnętrznego modułu równoważenia obciążenia platformy Azure — nie jest wymagane połączenie wychodzące
[Azure Standard Load Balancer](load-balancer-overview.md) oferuje bogaty zestaw funkcji i wysoką dostępność dzięki nadmiarowości strefowej. Aby dowiedzieć się więcej o jednostce SKU modułu równoważenia obciążenia, zobacz [tabelę porównawczą](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Istnieją dwa etapy uaktualnienia:

1. Migrowanie konfiguracji
2. Dodawanie maszyn wirtualnych do pul zaplecza standardowego modułu równoważenia obciążenia

W tym artykule opisano migrację konfiguracji. Dodawanie maszyn wirtualnych do pul wewnętrznej bazy danych może się różnić w zależności od określonego środowiska. Jednak niektóre wysokiego szczebla, ogólne zalecenia [są dostarczane](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>Omówienie uaktualnienia

Dostępny jest skrypt programu Azure PowerShell, który wykonuje następujące czynności:

* Tworzy standardowy wewnętrzny moduł równoważenia obciążenia jednostki SKU w określonej lokalizacji. Należy zauważyć, że żadne [połączenie wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) nie zostanie dostarczone przez standardowy wewnętrzny moduł równoważenia obciążenia.
* Bezproblemowo kopiuje konfiguracje modułu równoważenia obciążenia podstawowej jednostki SKU do nowo utworzonego standardowego modułu równoważenia obciążenia.

### <a name="caveatslimitations"></a>Zastrzeżenia\Ograniczenia

* Skrypt obsługuje tylko uaktualnienie modułu wewnętrznego równoważenia obciążenia, w przypadku gdy nie jest wymagane połączenie wychodzące. Jeśli wymagane [jest połączenie wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) dla niektórych maszyn wirtualnych, zapoznaj się z tą [stroną,](upgrade-InternalBasic-To-PublicStandard.md) aby uzyskać instrukcje. 
* Standardowy moduł równoważenia obciążenia ma nowe adresy publiczne. Nie można bezproblemowo przenieść adresów IP skojarzonych z istniejącym podstawowym modułem równoważenia obciążenia do standardowego modułu równoważenia obciążenia, ponieważ mają one różne jednostki SKU.
* Jeśli standardowy moduł równoważenia obciążenia jest tworzony w innym regionie, nie będzie można skojarzyć maszyn wirtualnych istniejących w starym regionie z nowo utworzonym modułem równoważenia obciążenia standardowego. Aby obejść to ograniczenie, upewnij się, że utworzysz nową maszynę wirtualną w nowym regionie.
* Jeśli moduł równoważenia obciążenia nie ma żadnej konfiguracji ip frontendu lub puli wewnętrznej bazy danych, prawdopodobnie zostanie wyświetlony błąd z uruchomieniem skryptu. Upewnij się, że nie są puste.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0).
## <a name="use-the-script"></a>Korzystanie ze skryptu

Istnieją dwie opcje w zależności od lokalnej konfiguracji środowiska programu PowerShell i preferencji:

* Jeśli nie masz zainstalowanych modułów Az platformy Azure lub nie masz nic przeciwko odinstalowaniu modułów `Install-Script` usługi Azure Az, najlepszą opcją jest użycie opcji uruchomienia skryptu.
* Jeśli chcesz zachować moduły usługi Azure Az, najlepiej jest pobrać skrypt i uruchomić go bezpośrednio.

Aby ustalić, czy masz zainstalowane moduły `Get-InstalledModule -Name az`Az platformy Azure, uruchom program . Jeśli nie widzisz żadnych zainstalowanych modułów Az, `Install-Script` możesz użyć tej metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby użyć tej opcji, nie może mieć modułów Az platformy Azure zainstalowanych na komputerze. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Można odinstalować moduły usługi Azure Az lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzureILBUpgrade`

To polecenie instaluje również wymagane moduły Az.  

### <a name="install-using-the-script-directly"></a>Instalowanie bezpośrednio przy użyciu skryptu

Jeśli masz zainstalowane moduły usługi Azure Az i nie możesz ich odinstalować (lub nie chcesz ich odinstalowywać), możesz ręcznie pobrać skrypt za pomocą karty **Ręczne pobieranie** w linku do pobierania skryptu. Skrypt jest pobierany jako nieprzetworzony plik nupkg. Aby zainstalować skrypt z tego pliku nupkg, zobacz [Ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Służy `Connect-AzAccount` do łączenia się z platformą Azure.

1. Służy `Import-Module Az` do importowania modułów Az.

1. Sprawdź wymagane parametry:

   * **rgName: [Ciąg]: Wymagane** — jest to grupa zasobów dla istniejącego podstawowego modułu równoważenia obciążenia i nowego standardowego modułu równoważenia obciążenia. Aby znaleźć tę wartość ciągu, przejdź do witryny Azure portal, wybierz źródło podstawowego modułu równoważenia obciążenia i kliknij **omówienie** modułu równoważenia obciążenia. Grupa zasobów znajduje się na tej stronie.
   * **oldLBName: [Ciąg]: Wymagane** — jest to nazwa istniejącego salda podstawowego, który chcesz uaktualnić. 
   * **newlocation: [Ciąg]: Wymagane** — jest to lokalizacja, w której zostanie utworzony standardowy moduł równoważenia obciążenia. Zaleca się dziedziczenie tej samej lokalizacji wybranego podstawowego modułu równoważenia obciążenia do standardowego modułu równoważenia obciążenia w celu lepszego skojarzenia z innymi istniejącymi zasobami.
   * **newLBName: [Ciąg]: Wymagane** — jest to nazwa standardowego modułu równoważenia obciążenia, który ma zostać utworzony.
1. Uruchom skrypt przy użyciu odpowiednich parametrów. Może upłynąć od pięciu do siedmiu minut, aby zakończyć.

    **Przykład**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Dodawanie maszyn wirtualnych do pul zaplecza standardowego modułu równoważenia obciążenia

Najpierw należy dokładnie sprawdzić, czy skrypt pomyślnie utworzył nowy standardowy moduł równoważenia obciążenia wewnętrznego z dokładną konfiguracją migrowaną z podstawowego modułu wyważającego obciążenie wewnętrzne. Można to sprawdzić za pomocą witryny Azure portal.

Pamiętaj, aby wysłać niewielką ilość ruchu za pośrednictwem standardowego modułu równoważenia obciążenia jako test ręczny.
  
Oto kilka scenariuszy, jak dodać maszyny wirtualne do pul wewnętrznej bazy danych nowo utworzonego standardowego modułu równoważenia obciążenia wewnętrznego, a nasze zalecenia dla każdego z nich:

* **Przenoszenie istniejących maszyn wirtualnych z pul wewnętrznej bazy danych starego podstawowego modułu równoważenia obciążenia wewnętrznego do pul wewnętrznej bazy danych nowo utworzonego standardowego modułu równoważenia obciążenia wewnętrznego**.
    1. Aby wykonać zadania z tego przewodnika Szybki start, zaloguj się do witryny [Azure Portal](https://portal.azure.com).
 
    1. Wybierz **pozycję Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz z listy zasobów nowo utworzony **standardowy moduł równoważenia obciążenia.**
   
    1. W obszarze **Ustawienia**wybierz pozycję **Pule wewnętrznej bazy danych**.
   
    1. Wybierz pulę wewnętrznej bazy danych, która odpowiada puli wewnętrznej bazy danych modułu równoważenia obciążenia podstawowego, wybierz następującą wartość: 
      - **Maszyna wirtualna:** Rozwijana i wybierz maszyny wirtualne z pasującej puli wewnętrznej bazy danych modułu równoważenia obciążenia podstawowego.
    1. Wybierz **pozycję Zapisz**.
    >[!NOTE]
    >W przypadku maszyn wirtualnych, które mają publiczne adresy IP, należy najpierw utworzyć standardowe adresy IP, w przypadku gdy ten sam adres IP nie jest gwarantowany. Odłączyć maszyny wirtualne od podstawowych adresów IP i skojarzyć je z nowo utworzonych standardowych adresów IP. Następnie będzie można wykonać instrukcje, aby dodać maszyny wirtualne do puli wewnętrznej bazy danych modułu równoważenia obciążenia standardowego. 

* **Tworzenie nowych maszyn wirtualnych w celu dodania do pul wewnętrznej bazy danych nowo utworzonego standardowego modułu równoważenia obciążenia wewnętrznego**.
    * Więcej instrukcji dotyczących tworzenia maszyn wirtualnych i kojarzenia jej ze standardowym modułem równoważenia obciążenia można znaleźć [tutaj](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia ze skryptem programu Azure PowerShell w celu migracji konfiguracji z wersji 1 do wersji 2?

Tak. Zobacz [Zastrzeżenia/Ograniczenia](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Czy skrypt programu Azure PowerShell przełącza się również za ruch z mojego podstawowego modułu równoważenia obciążenia na nowo utworzony standardowy moduł równoważenia obciążenia?

Nie. Skrypt programu Azure PowerShell tylko migruje konfigurację. Rzeczywista migracja ruchu jest twoim obowiązkiem i kontrolą.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wpadłem na pewne problemy z wykorzystaniem tego skryptu. Jak mogę uzyskać pomoc?
  
Możesz wysłać wiadomość slbupgradesupport@microsoft.come-mail do , otwórz sprawę pomocy technicznej za pomocą pomocy technicznej platformy Azure lub wykonaj obie te usługi.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o standardowym równoważącym obciążenia](load-balancer-overview.md)
