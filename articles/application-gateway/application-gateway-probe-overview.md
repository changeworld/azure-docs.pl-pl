---
title: Omówienie monitorowania dla usługi Azure Application Gateway Health
description: Dowiedz się więcej na temat funkcji monitoringu w usłudze Azure Application Gateway
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: victorh
ms.openlocfilehash: d0c425bcb9961fde9fb319991148c18c6a9ff57b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135191"
---
# <a name="application-gateway-health-monitoring-overview"></a>Omówienie monitorowania kondycji bramy aplikacji

Usługa Azure Application Gateway domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa dowolnego zasobu z puli uznawana za złą. Usługa Application Gateway w dalszym ciągu monitorować złej kondycji wystąpień i dodaje je do puli zaplecza w dobrej kondycji, gdy staną się dostępne i odpowiadać na sondy kondycji. Usługa Application gateway wysyła sondy kondycji za pomocą tego samego portu, który jest zdefiniowany w ustawieniach HTTP zaplecza. Ta konfiguracja gwarantuje, że sonda testuje tego samego portu, którego klienci będą stosowane do łączenia się z zapleczem.

![Przykładem sondy bramy aplikacji][1]

Oprócz używania, sondowanie kondycji domyślnej funkcji monitorowania, można również dostosować sondę kondycji, aby odpowiadał wymaganiom Twojej aplikacji. W tym artykule opisano domyślne i niestandardowe sondy kondycji.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Domyślnej funkcji badania kondycji

Bramy aplikacji automatycznie konfiguruje domyślnego badania kondycji, gdy nie skonfigurowano żadnej konfiguracji niestandardowej sondy. Monitorowanie zachowania działa poprzez wysłanie żądania HTTP do adresów IP skonfigurowane dla puli zaplecza. Dla domyślnej sondy Jeśli ustawienia http zaplecza są skonfigurowane do obsługi protokołu HTTPS, sondy używa protokołu HTTPS oraz aby sprawdzić kondycję zaplecza.

Na przykład: Brama aplikacji umożliwia serwerów zaplecza, A, B i C, aby odbierać ruch sieciowy protokołu HTTP na porcie 80. Domyślne monitorowanie kondycji sprawdza trzy serwery co 30 sekund w dobrej kondycji odpowiedzi HTTP. Dobra odpowiedzi HTTP ma [kod stanu](https://msdn.microsoft.com/library/aa287675.aspx) między 200 a 399.

Jeśli domyślnego wyboru sondowania serwera A nie powiedzie się, bramy aplikacji powoduje jej usunięcie z jej puli zaplecza i ruchu sieciowego, docierać do tego serwera. Domyślnej funkcji badania nadal kontynuuje pod kątem serwera co 30 sekund. Gdy serwer A pomyślnie odpowiada na jedno żądanie od domyślnego badania kondycji, dodaniu ponownie jako w dobrej kondycji do puli zaplecza i rozpoczęciu ruchu danych do serwera ponownie.

### <a name="probe-matching"></a>Badanie dopasowania

Domyślnie odpowiedź HTTP (S) z kodem stanu między 200 a 399 jest uważany za dobrej kondycji. Niestandardowe sondy kondycji obsługuje również dwa kryteria dopasowania. Spełniające kryteria można opcjonalnie zmodyfikuj to domyślna interpretacja, co stanowi odpowiedzi dobrej kondycji.

Poniżej są spełniające kryteria: 

- **Dopasuj kod stanu odpowiedzi HTTP** — kryterium do akceptowania dopasowywania sondy http odpowiedzi kod lub odpowiedzi kod zakresy określone przez użytkownika. Kody stanów odpowiedzi rozdzielonych przecinkami poszczególne lub zakres kodów stanu jest obsługiwana.
- **Dopasowanie treści odpowiedzi HTTP** — sondy czy wygląda w treści odpowiedzi HTTP i zgodności z użytkownikiem określony ciąg kryterium dopasowywania. Dopasowanie tylko szuka obecności użytkownika określonego ciągu w treści odpowiedzi i nie jest zgodny z wyrażeniem regularnym pełnej.

Kryteria dopasowania można określać z użyciem `New-AzApplicationGatewayProbeHealthResponseMatch` polecenia cmdlet.

Na przykład:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Po określeniu kryteria dopasowania, może być dołączane do sondowania przy użyciu konfiguracji `-Match` parametru w programie PowerShell.

### <a name="default-health-probe-settings"></a>Domyślne ustawienia sondy kondycji

| Właściwość sondy | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |http://127.0.0.1:\<port\>/ |Ścieżka adresu URL |
| Interval |30 |Czas w sekundach czas oczekiwania przed następnym sondy kondycji są wysyłane.|
| Limit czasu |30 |Czas w sekundach bramy application gateway czeka na odpowiedź sondy przed oznaczeniem sondy komunikat o złej kondycji. Jeśli sonda zwraca jako w dobrej kondycji, odpowiedniego zaplecza natychmiast jest oznaczony jako w dobrej kondycji.|
| Próg złej kondycji |3 |Określa, ile sondy do wysłania w przypadku, gdy wystąpi awaria sondy kondycji regularne. Tych sond kondycji dodatkowe są wysyłane w szybko, aby szybko ustalić kondycję wewnętrznej bazy danych i czeka na interwał sondowania. Serwer zaplecza jest oznaczony w dół po liczba niepowodzeń sondy kolejnych osiąga próg złej kondycji. |

> [!NOTE]
> Port jest tego samego portu jako ustawienia HTTP zaplecza.

Domyślnej funkcji badania sprawdza tylko http:\//127.0.0.1:\<portu\> do określenia stanu kondycji. Jeśli musisz skonfigurować sondę kondycji, aby przejść do niestandardowego adresu URL lub zmodyfikuj inne ustawienia, należy użyć niestandardowe sondy.

### <a name="probe-intervals"></a>Interwałów sondy

Wszystkie wystąpienia usługi Application Gateway probe wewnętrznej bazy danych, niezależnie od siebie nawzajem. Stosuje taką samą konfigurację sondowania do każdego wystąpienia bramy aplikacji. Na przykład jeśli brama aplikacji ma dwa wystąpienia w konfiguracji sondowania jest wysłanie sond kondycji co 30 sekund, oba wystąpienia wyśle sondy kondycji co 30 sekund.

Również w przypadku wielu odbiorników każdego odbiornika sondy zaplecza od siebie niezależne. Na przykład jeśli istnieją dwa odbiorniki wskazujące na tej samej puli zaplecza na dwóch różnych portów (skonfigurowane za pomocą dwóch ustawień http zaplecza) następnie każdego odbiornika sondy tego samego zaplecza niezależnie. W tym przypadku istnieją dwie sondy z każdego wystąpienia bramy aplikacji dla obu detektorów. Jeśli istnieją dwa wystąpienia usługi application gateway, w tym scenariuszu, maszyna wirtualna zaplecza widział cztery sondy na interwał sondowania skonfigurowany.

## <a name="custom-health-probe"></a>Sonda kondycji niestandardowe

Niestandardowe sondy umożliwiają zapewniają bardziej szczegółową kontrolę nad monitorowanie kondycji. Użycie sond niestandardowych, można skonfigurować interwał sondowania, adres URL i ścieżki do testowania i jak wiele odpowiedzi nie powiodło się, aby zaakceptować przed oznaczeniem wystąpienia pulę zaplecza o złej kondycji.

### <a name="custom-health-probe-settings"></a>Ustawienia sondy kondycji niestandardowe

Poniższa tabela zawiera definicje dla właściwości sondę kondycji niestandardowych.

| Właściwość sondy | Opis |
| --- | --- |
| Name (Nazwa) |Nazwa sondy. Ta nazwa jest używana do odwoływania się do sondowania w ustawieniach protokołu HTTP zaplecza. |
| Protocol |Protokół używany do wysyłania sondy. Sonda korzysta z protokołu, zdefiniowane w ustawieniach HTTP zaplecza |
| Host |Nazwa hosta, aby wysłać sondy. Dotyczy tylko wtedy, gdy połączenia obejmujące wiele lokacji jest skonfigurowany w usłudze Application Gateway, w przeciwnym razie użyj "127.0.0.1". Ta wartość jest inna niż nazwa hosta maszyny Wirtualnej. |
| Ścieżka |Ścieżka względna sondy. Nieprawidłowa ścieżka zaczyna się od "/". |
| Interval |Interwał sondy w sekundach. Ta wartość jest odstęp czasu między dwóch następujących po sobie sondy. |
| Limit czasu |Sonda limitu czasu w sekundach. Jeśli prawidłowe odpowiedzi nie zostanie odebrany w ramach tego limitu czasu, sondy jest oznaczony jako zakończony niepowodzeniem.  |
| Próg złej kondycji |Sonda liczbę ponownych prób. Serwer zaplecza jest oznaczony w dół po liczba niepowodzeń sondy kolejnych osiąga próg złej kondycji. |

> [!IMPORTANT]
> Jeśli usługa Application Gateway jest skonfigurowana dla jednej witryny, domyślnie hosta należy podać nazwę jako "127.0.0.1", chyba że inaczej skonfigurowane w niestandardowej sondy.
> Odwołania niestandardowej sondy są wysyłane do \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\>. Port używany będzie ten sam port, zgodnie z definicją w ustawieniach protokołu HTTP zaplecza.

## <a name="nsg-considerations"></a>Zagadnienia dotyczące sieciowej grupy zabezpieczeń

Jeśli na podsieci bramy aplikacji jest sieciowa grupa zabezpieczeń (NSG), zakresy portów 65503 65534 musi być otwarty w podsieci bramy aplikacji dla ruchu przychodzącego. Te porty są wymagane dla kondycji zaplecza interfejsu API do pracy.

Ponadto wychodzące połączenie z Internetem nie mogą zostać zablokowane, a ruch przychodzący pochodzące z AzureLoadBalancer tag muszą być dozwolone.

## <a name="next-steps"></a>Kolejne kroki
Po zapoznaniu się z informacji na temat monitorowania kondycji bramy Application Gateway, można skonfigurować [sondy kondycji niestandardowe](application-gateway-create-probe-portal.md) w witrynie Azure portal lub [sondy kondycji niestandardowe](application-gateway-create-probe-ps.md) przy użyciu programu PowerShell i usługi Azure Resource Manager model wdrażania.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
