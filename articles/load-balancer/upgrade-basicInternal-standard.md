---
title: Uaktualnianie z podstawowego wewnętrznego do standardowego wewnętrznego — Azure Load Balancer
description: W tym artykule opisano sposób uaktualniania wewnętrznego Load Balancer platformy Azure z podstawowej jednostki SKU do standardowej jednostki SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: c2c909d8ef2be982d4dd4a70b5f35d03e8e71418
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659972"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Uaktualnij usługę Azure Internal Load Balancer — nie jest wymagane połączenie wychodzące
[Usługa Azure usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md) oferuje bogaty zestaw funkcji i wysokiej dostępności za pomocą nadmiarowości stref. Aby dowiedzieć się więcej na temat Load Balancer SKU, zobacz [tabela porównania](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Uaktualnienie obejmuje dwa etapy:

1. Migrowanie konfiguracji
2. Dodawanie maszyn wirtualnych do pul zaplecza usługa Load Balancer w warstwie Standardowa

W tym artykule opisano migrację konfiguracji. Dodawanie maszyn wirtualnych do pul zaplecza może się różnić w zależności od konkretnego środowiska. [Dostępne są](#add-vms-to-backend-pools-of-standard-load-balancer)jednak pewne ogólne zalecenia dotyczące wysokiego poziomu.

## <a name="upgrade-overview"></a>Omówienie uaktualnienia

Dostępny jest skrypt Azure PowerShell, który wykonuje następujące czynności:

* Tworzy standardowy wewnętrzny Load Balancer jednostki SKU w określonej lokalizacji. Należy pamiętać, że żadne [połączenie wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) nie zostanie dostarczone przez standardową Load Balancer wewnętrzny.
* Bezproblemowo kopiuje konfiguracje podstawowej jednostki SKU Load Balancer do nowo utworzonej usługa Load Balancer w warstwie Standardowa.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skrypt obsługuje tylko wewnętrzne uaktualnienie Load Balancer, w którym nie jest wymagane połączenie wychodzące. Jeśli wymagane jest [połączenie wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) dla niektórych maszyn wirtualnych, Skorzystaj z tej [strony](upgrade-InternalBasic-To-PublicStandard.md) , aby uzyskać instrukcje. 
* Usługa Load Balancer w warstwie Standardowa ma nowe adresy publiczne. Nie można przenieść adresów IP skojarzonych z istniejącymi podstawowymi Load Balancer bezproblemowo do usługa Load Balancer w warstwie Standardowa, ponieważ mają różne jednostki SKU.
* Jeśli w innym regionie zostanie utworzony standardowy moduł równoważenia obciążenia, nie będzie możliwe skojarzenie maszyn wirtualnych istniejących w starym regionie z nowo utworzonymi usługa Load Balancer w warstwie Standardowa. Aby obejść to ograniczenie, należy utworzyć nową maszynę wirtualną w nowym regionie.
* Jeśli Load Balancer nie ma żadnej konfiguracji adresu IP frontonu lub puli zaplecza, można napotkać błąd podczas uruchamiania skryptu. Upewnij się, że nie są puste.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0).
## <a name="use-the-script"></a>Używanie skryptu

W zależności od konfiguracji i preferencji lokalnego środowiska programu PowerShell dostępne są dwie opcje:

* Jeśli nie masz zainstalowanych modułów platformy Azure AZ lub nie chcesz odinstalować modułów Azure AZ modules, najlepszym rozwiązaniem jest użycie opcji `Install-Script`, aby uruchomić skrypt.
* Jeśli zachodzi potrzeba zachowania usługi Azure AZ modules, najlepszym trafieniem jest pobranie skryptu i uruchomienie go bezpośrednio.

Aby określić, czy masz zainstalowane moduły Azure AZ, uruchom `Get-InstalledModule -Name az`. Jeśli nie widzisz żadnych zainstalowanych modułów AZ, możesz użyć metody `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody install-Script

Aby można było użyć tej opcji, na komputerze nie trzeba mieć zainstalowanych modułów platformy Azure. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Możesz odinstalować usługę Azure AZ module lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzureILBUpgrade`

To polecenie instaluje również wymagane AZ modules.  

### <a name="install-using-the-script-directly"></a>Instalowanie przy użyciu skryptu bezpośrednio

Jeśli zainstalowano kilka modułów na platformie Azure i nie można ich odinstalować (lub nie chcesz ich odinstalować), możesz ręcznie pobrać skrypt, korzystając z karty **pobranej ręcznie** w linku pobierania skryptu. Skrypt zostanie pobrany jako plik pierwotny NUPKG. Aby zainstalować skrypt z tego pliku NUPKG, zobacz [ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Użyj `Connect-AzAccount`, aby nawiązać połączenie z platformą Azure.

1. Użyj `Import-Module Az` do zaimportowania AZ modules.

1. Badanie wymaganych parametrów:

   * **rgName: [ciąg]: wymagane** — jest to grupa zasobów dla istniejących Load Balancer podstawowych i nowych usługa Load Balancer w warstwie Standardowa. Aby znaleźć tę wartość ciągu, przejdź do Azure Portal, wybierz podstawowe źródło Load Balancer i kliknij **Przegląd** usługi równoważenia obciążenia. Grupa zasobów znajduje się na tej stronie.
   * **oldLBName: [ciąg]: wymagane** — jest to nazwa istniejącego modułu, który ma zostać uaktualniony. 
   * **newLocation: [ciąg]: wymagane** — lokalizacja, w której zostanie utworzony usługa Load Balancer w warstwie Standardowa. Zaleca się, aby dziedziczyć tę samą lokalizację wybranych Load Balancer podstawowych do usługa Load Balancer w warstwie Standardowa w celu lepszego skojarzenia z innymi istniejącymi zasobami.
   * **newLBName: [ciąg]: wymagane** — jest to nazwa usługa Load Balancer w warstwie Standardowa, która ma zostać utworzona.
1. Uruchom skrypt przy użyciu odpowiednich parametrów. Ukończenie tego procesu może potrwać od 5 do siedmiu minut.

    **Przykład**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Dodawanie maszyn wirtualnych do pul zaplecza usługa Load Balancer w warstwie Standardowa

Najpierw należy sprawdzić, czy skrypt pomyślnie utworzył nowy standardowy Load Balancer wewnętrzny z dokładną konfiguracją migrowane z poziomu podstawowej Load Balancer wewnętrznej. Można to sprawdzić z poziomu Azure Portal.

Należy pamiętać, aby wysłać mały ruch przez usługa Load Balancer w warstwie Standardowa jako test ręczny.
  
Poniżej przedstawiono kilka scenariuszy dodawania maszyn wirtualnych do pul zaplecza dla nowo utworzonych standardowych Load Balancer wewnętrznych, które można skonfigurować, i zaleceń dotyczących każdego z nich:

* **Przeniesienie istniejących maszyn wirtualnych z pul zaplecza starych podstawowych Load Balancer wewnętrznych do pul zaplecza nowo utworzonych standardowych Load Balancer wewnętrznych**.
    1. Aby wykonać zadania z tego przewodnika Szybki start, zaloguj się do witryny [Azure Portal](https://portal.azure.com).
 
    1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **nowo utworzoną usługa Load Balancer w warstwie Standardowa** z listy zasobów.
   
    1. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**.
   
    1. Wybierz pulę zaplecza, która pasuje do puli zaplecza podstawowego Load Balancer, wybierz następującą wartość: 
      - **Maszyna wirtualna**: Lista rozwijana i wybieranie maszyn wirtualnych z dopasowanej puli zaplecza Load Balancer podstawowej.
    1. Wybierz pozycję **Zapisz**.
    >[!NOTE]
    >W przypadku maszyn wirtualnych, które mają publiczne adresy IP, konieczne będzie utworzenie standardowych adresów w pierwszej kolejności, w której ten sam adres IP nie jest gwarantowany. Usuń skojarzenie maszyn wirtualnych z podstawowymi adresami IP i skojarz je z nowo utworzonymi standardowymi adresami. Następnie będzie można wykonać instrukcje dotyczące dodawania maszyn wirtualnych do puli zaplecza usługa Load Balancer w warstwie Standardowa. 

* **Tworzenie nowych maszyn wirtualnych w celu dodania ich do pul zaplecza nowo utworzonej standardowej Load Balancer wewnętrznej**.
    * Więcej instrukcji dotyczących tworzenia maszyn wirtualnych i kojarzenia ich z usługa Load Balancer w warstwie Standardowa można znaleźć [tutaj](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia dotyczące skryptu Azure PowerShell w celu migrowania konfiguracji z wersji od 1 do v2?

Tak. Zobacz [zastrzeżenia/ograniczenia](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Czy skrypt Azure PowerShell przełączany jest również ruch z mojego podstawowego Load Balancer do nowo utworzonego usługa Load Balancer w warstwie Standardowa?

Nie. Skrypt Azure PowerShell tylko migruje konfigurację. Rzeczywista migracja ruchu jest odpowiedzialna i w Twoim formancie.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wystąpił problem z używaniem tego skryptu. Jak uzyskać pomoc?
  
Możesz wysłać wiadomość e-mail do slbupgradesupport@microsoft.com, otworzyć sprawę pomocy technicznej z pomocą techniczną platformy Azure lub wykonać obie czynności.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md)
