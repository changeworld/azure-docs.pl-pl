---
title: 'Azure ExpressRoute: modyfikowanie obwodu: PowerShell: klasyczny'
description: W tym artykule przedstawiono kroki, aby sprawdzić stan, update lub delete i anulować aprowizację obwodu usługi ExpressRoute modelu wdrożenia klasycznego.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931974"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modyfikowanie obwodu ExpressRoute za pomocą programu PowerShell (wersja klasyczna)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

W tym artykule przedstawiono kroki, aby sprawdzić stan, update lub delete i anulować aprowizację obwodu usługi ExpressRoute modelu wdrożenia klasycznego. Dotyczy on klasycznego modelu wdrożenia.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

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

Możesz uzyskać informacji na temat określonego obwodu usługi ExpressRoute, przekazując klucz usługi jako parametr do wywołania.

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

Aby uzyskać szczegółowy opis wszystkich parametrów, uruchomieniem następującego przykładu:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modyfikowanie obwodu

Można modyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność.

Możesz wykonać następujące zadania bez przerw w dostępności:

* Włącz lub Wyłącz dodatek ExpressRoute premium dla obwodu usługi ExpressRoute.
* Zwiększyć przepustowość obwodu usługi ExpressRoute, pod warunkiem, że pojemność dostępna na porcie. Obniżenie przepustowości obwodu nie jest obsługiwane.
* Zmień plan zliczania z plan taryfowy z danymi na dane nieograniczone. Zmiana planu zliczania z danymi nieograniczonymi plan taryfowy z danymi nie jest obsługiwana.
* Można włączać i wyłączać *Zezwalaj na klasyczne operacje*.

Zapoznaj się [ExpressRoute — często zadawane pytania](expressroute-faqs.md) Aby uzyskać więcej informacji na temat limity i ograniczenia.

### <a name="enable-the-expressroute-premium-add-on"></a>Włącz dodatek ExpressRoute premium

Należy włączyć dodatek premium usługi ExpressRoute dla istniejącego obwodu za pomocą następującego polecenia cmdlet programu PowerShell:

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

Obwód usługi będą teraz mieć funkcje dodatku premium usługi ExpressRoute, włączone. Zaraz po pomyślnym wykonaniu polecenia, rozpoczyna się okres rozliczeniowy możliwości dodatku premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Wyłącz dodatek ExpressRoute premium

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż co to jest dozwolone w przypadku obwód standardowy.
>
>

#### <a name="considerations"></a>Zagadnienia do rozważenia

* Upewnij się, że liczba sieci wirtualnych połączonych z obwodem usługi jest mniejsza niż 10, zanim obniżanie poziomu z wersji premium na standardową. Jeśli tego nie zrobisz, Twoje żądanie aktualizacji nie powiedzie się i opłaty są naliczane zgodnie ze stawkami premium.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli tego nie zrobisz, Twoje żądanie aktualizacji nie powiedzie się i opłaty są naliczane zgodnie ze stawkami premium.
* Tabela tras muszą być mniej niż 4000 tras do prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras jest większa niż 4000, sesji protokołu BGP spada i nie będzie reenabled, dopóki liczba prefiksów anonsowanych spadnie poniżej 4000.

#### <a name="to-disable-the-premium-add-on"></a>Aby wyłączyć dodatek premium

Możesz wyłączyć dodatek premium usługi ExpressRoute dla istniejącego obwodu za pomocą następującego polecenia cmdlet programu PowerShell:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aktualizacja przepustowości obwodu usługi ExpressRoute

Sprawdź [ExpressRoute — często zadawane pytania](expressroute-faqs.md) obsługiwane opcje przepustowości dla dostawcy. Można wybrać dowolnego rozmiaru, który jest większy niż rozmiar istniejącego obwodu, tak długo, jak umożliwia port fizyczny (w którym została utworzona obwodu).

> [!IMPORTANT]
> Może być konieczne odtworzenie obwód usługi ExpressRoute, jeśli istnieje niewystarczająca wydajność przy użyciu istniejącego portu. Nie można uaktualnić obwodu, jeśli w tej lokalizacji jest dostępna nie dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez przerw w działaniu. Obniżenie przepustowości wymaga anulować aprowizację obwodu usługi ExpressRoute, a następnie ponownie udostępnić nowego obwodu usługi ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Zmiana rozmiaru obwodu

Po podjęciu decyzji rozmiar, jakiego potrzebujesz, służy następujące polecenie zmiany rozmiaru obwodu:

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

Gdy Twój obwód ma zostały wielkości po stronie firmy Microsoft, musisz skontaktować się z dostawcą połączenia, można zaktualizować konfiguracji po ich stronie odpowiadający tej zmiany. Rozliczanie rozpoczyna się dla opcji przepustowości zaktualizowane od tej pory.

Jeśli zobaczysz następujący błąd podczas zwiększania przepustowości obwodu, oznacza to, zostanie pozostawiony nie wystarczającą przepustowość na porcie fizycznego, tworzona dla istniejącego obwodu. Należy usunąć ten obwód i utworzenia nowego obwodu o rozmiarze, których potrzebujesz.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Anulowanie aprowizacji i usuwanie obwodu

### <a name="considerations"></a>Zagadnienia do rozważenia

* Należy odłączyć wszystkie sieci wirtualne z obwodem usługi ExpressRoute dla tej operacji. Sprawdź, czy masz wszystkie sieci wirtualne, które są połączone z obwodem, jeśli operacja zakończy się niepowodzeniem.
* Jeśli dostawca usług obwodu usługi ExpressRoute, w stanie inicjowania obsługi **aprowizacji** lub **Aprowizowana** należy skontaktować się z dostawcą usługi, aby anulować aprowizację obwodu po ich stronie. Firma Microsoft nadal rezerwowania zasobów oraz są naliczane, dopóki dostawcy usług wykonuje anulowanie aprowizacji obwodu i będzie powiadamiał.
* Jeśli dostawca usług ma anulowanie aprowizacji obwodu (stan aprowizacji dostawcy usług jest równa **nie zainicjowano obsługi administracyjnej**), następnie można usunąć obwodu. Spowoduje to zatrzymanie naliczania opłat za obwód.

#### <a name="delete-a-circuit"></a>Usuwanie obwodu

Możesz usunąć obwód usługi ExpressRoute, uruchamiając następujące polecenie:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
