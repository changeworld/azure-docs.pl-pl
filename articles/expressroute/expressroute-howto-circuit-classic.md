---
title: 'Modyfikowania obwodu usługi expressroute: środowiska PowerShell: klasyczny Portal Azure | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono kroki, aby sprawdzić stan, update lub delete i anulowanie zastrzeżenia obwodu ExpressRoute wdrażania klasycznego modelu.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 457bb74fa15d31fecbf668038ac880cafb8a897d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "24102838"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modyfikowania obwodu usługi expressroute, przy użyciu programu PowerShell (klasyczne)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

W tym artykule przedstawiono również sposób sprawdzania stanu, update lub delete i anulowanie zastrzeżenia obwodu usługi ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zainstaluj najnowsze wersje moduły programu PowerShell Azure usługi zarządzania (ko), postępuj zgodnie z instrukcjami [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) wskazówki krok po kroku dotyczące sposobu konfigurowania komputera do użycia Moduły programu Azure PowerShell.

## <a name="get-the-status-of-a-circuit"></a>Pobierz stan obwodu

Te informacje w dowolnym momencie można pobrać za pomocą `Get-AzureCircuit` polecenia cmdlet. Wywołania bez parametrów wyświetla listę wszystkich obwodów.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Można uzyskać informacji o określonych obwodu ExpressRoute przez przekazanie klucza usługi jako parametru do wywołania.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając w poniższym przykładzie:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modyfikowanie obwodu

Można zmodyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność.

Można wykonać następujące zadania bez przestojów:

* Włącz lub Wyłącz dodatek premium usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Zwiększyć przepustowość obwodu ExpressRoute dostarczane na port jest dostępna pojemność. Przepustowości obwodu zmiana wersji na starszą nie jest obsługiwane. 
* Zmiana zliczania planu naliczane danych nieograniczone danych. Zmiany planu zliczania dane nieograniczone naliczane danych nie jest obsługiwana.
* Można włączyć lub wyłączyć *operacje klasycznego*.

Zapoznaj się [ExpressRoute — często zadawane pytania](expressroute-faqs.md) Aby uzyskać więcej informacji na temat limity i ograniczenia.

### <a name="enable-the-expressroute-premium-add-on"></a>Włącz dodatek usługi ExpressRoute w warstwie premium

Dodatek usługi ExpressRoute w warstwie premium dla istniejącego obwodu można włączyć za pomocą następującego polecenia cmdlet programu PowerShell:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Obwodu mają teraz włączonymi funkcjami dodatek premium usługi ExpressRoute. Zaraz po pomyślnym uruchomieniu polecenia rozpoczyna rozliczeń dla funkcji dodatku premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Wyłącz dodatek usługi ExpressRoute w warstwie premium

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż co to jest dozwolone dla standardowych obwodu.
> 
> 

#### <a name="considerations"></a>Zagadnienia do rozważenia

* Upewnij się, liczba sieci wirtualnych powiązany obwodu jest mniejsza niż 10, aby obniżyć z premium standard. Jeśli nie zrobisz, żądanie aktualizacji nie powiedzie się i są rozliczane stawki premium.
* Należy odłączyć wszystkie sieci wirtualne w różnych regionach geograficznymi. Jeśli nie, żądanie aktualizacji nie powiedzie się i są rozliczane stawki premium.
* Tabela tras musi być mniejsza niż 4000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras jest większa niż 4000 tras, sesji BGP porzuca i nie będzie reenabled, aż przejdzie liczby prefiksów anonsowanych poniżej 4000.

#### <a name="to-disable-the-premium-add-on"></a>Aby wyłączyć dodatek w warstwie premium

Dodatek usługi ExpressRoute w warstwie premium dla istniejącego obwodu można wyłączyć za pomocą następującego polecenia cmdlet programu PowerShell:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aktualizacja przepustowości obwodu ExpressRoute

Sprawdź [ExpressRoute — często zadawane pytania](expressroute-faqs.md) obsługiwane opcje przepustowości dla dostawcy. Można wybrać żadnych ma rozmiar większy niż rozmiar z istniejącym obwodem tak długo, jak zezwala na fizyczny port (na którym jest tworzony obwodu).

> [!IMPORTANT]
> Może być konieczne ponowne utworzenie obwodu usługi expressroute w przypadku niewystarczającego pojemności na istniejącego portu. Nie można uaktualnić obwodu, jeśli nie bez dodatkowej pojemności dostępnej w tej lokalizacji.
>
> Nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez zakłóceń. Zmiana wersji na starszą przepustowości wymaga anulowanie zastrzeżenia obwodu ExpressRoute, a następnie Udostępnij ponownie nowy obwód usługi ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Zmień rozmiar obwodu

Po wybraniu rozmiar, jaki należy można Użyj następującego polecenia, aby zmienić rozmiar obwodu:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Po obwodu ma zostać o rozmiarze po stronie firmy Microsoft, należy skontaktować się dostawcą połączenia można zaktualizować konfiguracji na bok odpowiadające tej zmiany. Karta rozpoczyna się dla opcji przepustowości zaktualizowane z tego punktu.

Jeśli zostanie wyświetlony następujący błąd podczas zwiększyć przepustowość obwodu, to pozostaje nie wystarczającą przepustowość na fizyczny port, na której jest tworzony z istniejącym obwodem. Należy usunąć ten obwód i utworzyć nowy obwód rozmiaru, które są potrzebne.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Anulowanie zastrzeżenia i usunąć obwód

### <a name="considerations"></a>Zagadnienia do rozważenia

* Należy odłączyć wszystkie sieci wirtualne z obwodem usługi ExpressRoute, ta operacja powiodła się. Sprawdź, czy masz żadnych sieci wirtualnych, które są połączone z obwodem, jeśli ta operacja nie powiedzie się.
* Jeśli dostawca usługi obwodu ExpressRoute stan inicjowania obsługi jest **inicjowania obsługi administracyjnej** lub **obsługiwane administracyjnie** należy skontaktować się z dostawcą usług na anulowanie zastrzeżenia obwód w bok. Będziemy nadal zarezerwować zasobów i obciążać Cię do czasu dostawcy usług wykonuje anulowania obsługi obwodu i powiadomienia NAS.
* Jeśli usługodawca została anulowana obwodu (ustawiono dostawcę usługi stan inicjowania obsługi **nieudostępniane**), następnie można usunąć obwodu. Powoduje to zatrzymanie rozliczeń dla obwodu.

#### <a name="delete-a-circuit"></a>Usunąć obwód

Można usunąć obwodu usługi ExpressRoute, uruchamiając następujące polecenie:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```