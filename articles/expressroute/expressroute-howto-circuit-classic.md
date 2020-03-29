---
title: 'Usługa Azure ExpressRoute: modyfikowanie obwodu: Program PowerShell:classic'
description: W tym artykule otrzymasz od użytkownika po czynnościach, które mają na celu sprawdzenie stanu, zaktualizowanie lub usunięcie i anulowanie obsługi administracyjnej obwodu klasycznego modelu wdrażania usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931974"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modyfikowanie obwodu usługi ExpressRoute przy użyciu programu PowerShell (klasyczny)

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

W tym artykule otrzymasz od użytkownika po czynnościach, które mają na celu sprawdzenie stanu, zaktualizowanie lub usunięcie i anulowanie obsługi administracyjnej obwodu klasycznego modelu wdrażania usługi ExpressRoute. Dotyczy on klasycznego modelu wdrożenia.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Uzyskaj stan obwodu

Te informacje można pobrać w dowolnym `Get-AzureCircuit` momencie za pomocą polecenia cmdlet. Wykonywanie połączenia bez żadnych parametrów zawiera listę wszystkich obwodów.

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

Można uzyskać informacje na temat określonego obwodu usługi ExpressRoute, przekazując klucz usługi jako parametr do wywołania.

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

Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujący przykład:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modyfikowanie obwodu

Można zmodyfikować niektóre właściwości obwodu usługi ExpressRoute bez wpływu na łączność.

Możesz wykonać następujące zadania bez przestojów:

* Włącz lub wyłącz dodatek Premium Usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Zwiększ przepustowość obwodu usługi ExpressRoute, pod warunkiem, że na porcie jest dostępna pojemność. Obniżenie przepustowości obwodu nie jest obsługiwane.
* Zmień plan pomiaru z Dane taryfowe na Nieograniczone dane. Zmiana planu pomiaru z nieograniczonych danych na dane taryfowe nie jest obsługiwana.
* Można włączyć i wyłączyć *zezwalaj na operacje klasyczne*.

Więcej informacji na temat ograniczeń i ograniczeń można znaleźć w [często zadawanych pytaniach dotyczących usługi ExpressRoute.](expressroute-faqs.md)

### <a name="enable-the-expressroute-premium-add-on"></a>Włączanie dodatku Premium usługi ExpressRoute

Dodatek Premium usługi ExpressRoute premium dla istniejącego obwodu można włączyć przy użyciu następującego polecenia cmdlet programu PowerShell:

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

Twój obwód będzie teraz mieć włączone funkcje dodatku Premium Usługi ExpressRoute. Po pomyślnym uruchomieniu polecenia rozpoczyna się rozliczanie funkcji dodatku premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Wyłączanie dodatku Premium usługi ExpressRoute

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż to, co jest dozwolone dla obwodu standardowego.
>
>

#### <a name="considerations"></a>Zagadnienia do rozważenia

* Upewnij się, że liczba sieci wirtualnych połączonych z obwódem jest mniejsza niż 10 przed obniżeniem wersji premium do standardu. Jeśli tego nie zrobisz, żądanie aktualizacji nie powiedzie się i zostaną naliczone stawki premium.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli tego nie zrobisz, żądanie aktualizacji nie powiedzie się i zostaną naliczone stawki premium.
* Tabela tras musi być mniejsza niż 4000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli marszruty jest większy niż 4000 tras, sesja BGP zostanie porzucona i nie zostanie ponownie przypisana, dopóki liczba anonsowanych prefiksów nie spadnie poniżej 4000.

#### <a name="to-disable-the-premium-add-on"></a>Aby wyłączyć dodatek premium

Dodatek Premium usługi ExpressRoute premium dla istniejącego obwodu można wyłączyć przy użyciu następującego polecenia cmdlet programu PowerShell:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aktualizowanie przepustowości obwodu usługi ExpressRoute

Sprawdź często zadawane [pytania dotyczące usługi ExpressRoute,](expressroute-faqs.md) aby uzyskać obsługiwane opcje przepustowości dla swojego dostawcy. Można wybrać dowolny rozmiar, który jest większy niż rozmiar istniejącego obwodu, tak długo, jak port fizyczny (na którym jest tworzony obwód) pozwala.

> [!IMPORTANT]
> Może być trzeba ponownie utworzyć obwód usługi ExpressRoute, jeśli istnieje niewystarczająca pojemność na istniejącym porcie. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dostępnej dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowości obwodu usługi ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania obsługi administracyjnej obwodu usługi ExpressRoute, a następnie ponownego obsługi administracyjnej nowego obwodu usługi ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Ponowne rozmiary obwodu

Po podjęciu decyzji, jaki rozmiar jest potrzebny, można użyć następującego polecenia, aby zmienić rozmiar obwodu:

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

Po utworzeniu obwodu po stronie firmy Microsoft należy skontaktować się z dostawcą łączności, aby zaktualizować konfiguracje po ich stronie, aby dopasować tę zmianę. Od tego momentu rozpoczyna się fakturowanie dla zaktualizowanej opcji przepustowości.

Jeśli podczas zwiększania przepustowości obwodu zostanie wyświetlony następujący błąd, oznacza to, że na porcie fizycznym, na którym jest tworzony istniejący obwód, nie ma wystarczającej przepustowości. Należy usunąć ten obwód i utworzyć nowy obwód o rozmiarze, którego potrzebujesz.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Usuwanie obsługi administracyjnej i usuwanie obwodu

### <a name="considerations"></a>Zagadnienia do rozważenia

* Aby ta operacja powiodła się, należy odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Sprawdź, czy masz jakieś sieci wirtualne, które są połączone z obwodem, jeśli ta operacja nie powiedzie się.
* Jeśli stan inicjowania obsługi administracyjnej dostawcy usług obwodu usługi ExpressRoute to **inicjowanie obsługi administracyjnej** lub **inicjowana obsługa,** należy współpracować z dostawcą usług w celu anulowania obsługi administracyjnej obwodu po ich stronie. Nadal rezerwujemy zasoby i rozliczamy cię do czasu zakończenia przez usługodawcę anulowania obsługi administracyjnej obwodu i powiadomi nas o tym.
* Jeśli dostawca usług wyrejestrował obwód (stan inicjowania obsługi administracyjnej dostawcy usług jest ustawiony na **Nie aprowizowana),** można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

#### <a name="delete-a-circuit"></a>Usuwanie obwodu

Obwód usługi ExpressRoute można usunąć, uruchamiając następujące polecenie:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
