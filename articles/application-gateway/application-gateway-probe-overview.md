---
title: Omówienie monitorowania kondycji bramy aplikacji platformy Azure
description: Usługa Azure Application Gateway monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa wszystkie zasoby uznane za złej kondycji z puli.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335861"
---
# <a name="application-gateway-health-monitoring-overview"></a>Omówienie monitorowania kondycji bramy aplikacji

Usługa Azure Application Gateway domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa wszystkie zasoby uznane za złej kondycji z puli. Usługa Application Gateway w dalszym ciągu monitoruje wystąpienia w złej kondycji i dodaje je do puli zaplecza w dobrej kondycji, gdy staną się dostępne i zaczną odpowiadać na sondy kondycji. Brama aplikacji wysyła sondy kondycji z tym samym portem, który jest zdefiniowany w ustawieniach HTTP zaplecza. Ta konfiguracja zapewnia, że sonda testuje ten sam port, który klienci będą używać do łączenia się z wewnętrznej bazy danych. 

Źródłowy adres IP Brama aplikacji używa dla sond kondycji zależy od puli wewnętrznej bazy danych:
 
- Jeśli pula wewnętrznej bazy danych jest publicznym punktem końcowym, adres źródłowy jest publicznym adresem IP portalu aplikacji.
- Jeśli pula wewnętrznej bazy danych jest prywatnym punktem końcowym, źródłowy adres IP pochodzi z prywatnej przestrzeni adresowej IP bramy aplikacji.


![przykład sondy bramy aplikacji][1]

Oprócz używania domyślnego monitorowania sondy kondycji, można również dostosować sondę kondycji do wymagań aplikacji. W tym artykule zostaną uwzględnione zarówno domyślne, jak i niestandardowe sondy kondycji.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Domyślna sonda kondycji

Brama aplikacji automatycznie konfiguruje domyślną sondę kondycji, gdy nie skonfigurujesz żadnej niestandardowej konfiguracji sondy. Zachowanie monitorowania działa poprzez wykonanie żądania HTTP do adresów IP skonfigurowanych dla puli zaplecza. W przypadku sond domyślnych, jeśli ustawienia http wewnętrznej bazy danych są skonfigurowane dla protokołu HTTPS, sonda używa protokołu HTTPS, a także do testowania kondycji zaplecza.

Na przykład: Brama aplikacji jest skonfigurowana do używania serwerów zaplecza A, B i C do odbierania ruchu sieciowego HTTP na porcie 80. Domyślne monitorowanie kondycji testuje trzy serwery co 30 sekund pod kątem zdrowej odpowiedzi HTTP. Zdrowa odpowiedź HTTP ma [kod stanu](https://msdn.microsoft.com/library/aa287675.aspx) między 200 a 399.

Jeśli domyślne sprawdzanie sondy nie powiedzie się dla serwera A, brama aplikacji usuwa go z puli zaplecza, a ruch sieciowy przestanie przepływać do tego serwera. Domyślna sonda nadal sprawdza serwer A co 30 sekund. Gdy serwer A odpowiada pomyślnie na jedno żądanie z domyślnej sondy kondycji, jest dodawany z powrotem jako w dobrej kondycji do puli zaplecza, a ruch zaczyna ponownie przepływać do serwera.

### <a name="probe-matching"></a>Dopasowywanie sondy

Domyślnie odpowiedź HTTP(S) z kodem stanu między 200 a 399 jest uważana za w dobrej kondycji. Niestandardowe sondy kondycji dodatkowo obsługują dwa pasujące kryteria. Kryteria dopasowywania mogą służyć do opcjonalnej modyfikacji domyślnej interpretacji tego, co sprawia, że odpowiedź w dobrej kondycji.

Następujące kryteria są zgodne: 

- **Dopasowanie kodu stanu odpowiedzi HTTP** — kryterium dopasowywania sondy do akceptowania kodu odpowiedzi http określonego przez użytkownika lub zakresów kodu odpowiedzi. Obsługiwane są indywidualne kody stanu odpowiedzi oddzielone przecinkami lub zakres kodu stanu.
- **Dopasowanie treści odpowiedzi HTTP** — kryterium dopasowywania sondy, które analizuje treść odpowiedzi HTTP i dopasowuje do ciągu określonego przez użytkownika. Dopasowanie tylko wyszukuje obecność określonego przez użytkownika ciągu w treści odpowiedzi i nie jest zgodne z pełnym wyrażeniem regularnym.

Kryteria dopasowania można określić za pomocą polecenia `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet.

Przykład:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Po określeniu kryteriów dopasowania można dołączyć do konfiguracji `-Match` sondy przy użyciu parametru w programie PowerShell.

### <a name="default-health-probe-settings"></a>Domyślne ustawienia sondy kondycji

| Probe, właściwość | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |http://127.0.0.1:\<port\>/ |Ścieżka adresu URL |
| Interval |30 |Ilość czasu w sekundach oczekiwania przed wysłaniem następnej sondy kondycji.|
| Limit czasu |30 |Ilość czasu w sekundach bramy aplikacji czeka na odpowiedź sondy przed oznaczeniem sondy jako złej kondycji. Jeśli sonda zwraca jako zdrowy, odpowiednie wewnętrznej bazy danych jest natychmiast oznaczone jako zdrowe.|
| Niezdrowy próg |3 |Określa, ile sond do wysłania w przypadku awarii sondy kondycji regularne. Te dodatkowe sondy kondycji są wysyłane w krótkim odstępie czasu, aby szybko określić kondycję wewnętrznej bazy danych i nie czekaj na interwał sondy. Ten behaivor jest tylko w wersji 1 SKU. W przypadku jednostki SKU w wersji 2 sondy kondycji czekać interwał. Serwer zaplecza jest oznaczony w dół po kolejnych liczba awarii sondy osiągnie próg złej kondycji. |

> [!NOTE]
> Port jest tym samym portem co ustawienia HTTP zaplecza.

Domyślna sonda wygląda\/tylko na http: /127.0.0.1:\<port\> w celu określenia stanu kondycji. Jeśli musisz skonfigurować sondę kondycji, aby przejść do niestandardowego adresu URL lub zmodyfikować inne ustawienia, należy użyć sond niestandardowych.

### <a name="probe-intervals"></a>Interwały sondy

Wszystkie wystąpienia bramy aplikacji sondy wewnętrznej bazy danych niezależnie od siebie. Ta sama konfiguracja sondy ma zastosowanie do każdego wystąpienia bramy aplikacji. Na przykład jeśli konfiguracja sondy jest wysyłanie sond kondycji co 30 sekund, a brama aplikacji ma dwa wystąpienia, a następnie oba wystąpienia wysyłają sondy kondycji co 30 sekund.

Również jeśli istnieje wiele odbiorników, a następnie każdy odbiornik sonduje wewnętrznej bazy danych niezależnie od siebie. Na przykład jeśli istnieją dwa odbiorniki wskazujące tę samą pulę wewnętrznej bazy danych na dwóch różnych portach (skonfigurowane przez dwa ustawienia wewnętrznej bazy danych http), a następnie każdy odbiornik sonduje tę samą wewnętrznej bazy danych niezależnie. W takim przypadku istnieją dwie sondy z każdego wystąpienia bramy aplikacji dla dwóch odbiorników. Jeśli istnieją dwa wystąpienia bramy aplikacji w tym scenariuszu, wewnętrznej bazy danych maszyny wirtualnej zobaczy cztery sondy na skonfigurowany interwał sondy.

## <a name="custom-health-probe"></a>Niestandardowa sonda kondycji

Niestandardowe sondy umożliwiają bardziej szczegółową kontrolę nad monitorowaniem kondycji. Korzystając z niestandardowych sond, można skonfigurować interwał sondy, adres URL i ścieżkę do testowania i ile odpowiedzi nie powiodło się do zaakceptowania przed oznaczeniem wystąpienia puli zaplecza jako złej kondycji.

### <a name="custom-health-probe-settings"></a>Niestandardowe ustawienia sondy kondycji

Poniższa tabela zawiera definicje właściwości niestandardowej sondy kondycji.

| Probe, właściwość | Opis |
| --- | --- |
| Nazwa |Nazwa sondy. Ta nazwa jest używana do odwoływania się do sondy w ustawieniach HTTP zaplecza. |
| Protocol (Protokół) |Protokół używany do wysyłania sondy. Sonda używa protokołu zdefiniowanego w ustawieniach HTTP zaplecza |
| Host |Nazwa hosta do wysłania sondy. Ma zastosowanie tylko wtedy, gdy w u portalu aplikacji skonfigurowano wiele lokacji, w przeciwnym razie użyj "127.0.0.1". Ta wartość różni się od nazwy hosta maszyny Wirtualnej. |
| Ścieżka |Ścieżka względna sondy. Prawidłowa ścieżka zaczyna się od '/'. |
| Interval |Interwał sondowania w sekundach. Ta wartość jest przedział czasu między dwoma kolejnymi sondami. |
| Limit czasu |Limit czasu sondy w sekundach. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda jest oznaczona jako nie powiodła się.  |
| Niezdrowy próg |Liczba ponownych prób sondy. Serwer zaplecza jest oznaczony w dół po kolejnych liczba awarii sondy osiągnie próg złej kondycji. |

> [!IMPORTANT]
> Jeśli brama aplikacji jest skonfigurowana dla jednej lokacji, domyślnie nazwa hosta powinna być określona jako "127.0.0.1", chyba że skonfigurowano inaczej w sondzie niestandardowej.
> Dla odwołania niestandardowa sonda\<jest\>\<wysyłana\>do \<protokołu\>:// host : ścieżka portu\>\<. Używany port będzie tym samym portem, co zdefiniowano w ustawieniach HTTP zaplecza.

## <a name="nsg-considerations"></a>Zagadnienia dotyczące nsg

Należy zezwolić na przychodzący ruch internetowy na portach TCP 65503-65534 dla jednostki SKU bramy aplikacji w wersji 1 i portów TCP 65200-65535 dla jednostki SKU w wersji 2 z podsiecią docelową jako **dowolny** i źródłowy jako tag usługi **GatewayManager.** Ten zakres portów jest wymagany do komunikacji z infrastrukturą platformy Azure.

Ponadto nie można zablokować wychodzącej łączności internetowej z Internetem, a ruch przychodzący pochodzący z tagu **AzureLoadBalancer** musi być dozwolony.

Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji bramy aplikacji](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet).

## <a name="next-steps"></a>Następne kroki
Po zapoznaniu się z monitorowaniem kondycji bramy aplikacji można skonfigurować [niestandardową sondę kondycji](application-gateway-create-probe-portal.md) w witrynie Azure portal lub [niestandardową sondę kondycji](application-gateway-create-probe-ps.md) przy użyciu programu PowerShell i modelu wdrażania usługi Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
