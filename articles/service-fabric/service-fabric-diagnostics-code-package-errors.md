---
title: Diagnozowanie typowych błędów pakietu kodu przy użyciu sieci szkieletowej usług
description: Dowiedz się, jak rozwiązywać typowe błędy pakietu kodu za pomocą sieci szkieletowej usług Azure
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463099"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnozowanie typowych błędów pakietu kodu przy użyciu sieci szkieletowej usług

W tym artykule opisano, co to znaczy dla pakietu kodu, aby zakończyć nieoczekiwanie. Zapewnia wgląd w możliwe przyczyny typowych kodów błędów, wraz z krokami rozwiązywania problemów.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Kiedy proces lub kontener kończy się nieoczekiwanie?

Gdy usługa Azure Service Fabric odbiera żądanie uruchomienia pakietu kodu, rozpoczyna przygotowywanie środowiska w systemie lokalnym zgodnie z opcjami określonymi w manifestach aplikacji i usługi. Te przygotowania mogą obejmować rezerwowanie punktów końcowych sieci lub zasobów, konfigurowanie reguł zapory lub konfigurowanie ograniczeń ładu zasobów. 

Po środowisku został poprawnie skonfigurowany, sieci szkieletowej usług próbuje wywołać pakiet kodu. Ten krok jest uważany za pomyślny, jeśli system operacyjny lub środowisko wykonawcze kontenera zgłasza, że proces lub kontener został pomyślnie aktywowany. Jeśli aktywacja nie powiedzie się, powinien zostać wyświetlony komunikat o kondycji w SFX, który przypomina następujące:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Po pomyślnym aktywowaniu pakietu kodu sieci szkieletowej usług rozpoczyna monitorowanie jego istnienia. W tym momencie proces lub kontener można zakończyć w dowolnym momencie z wielu powodów. Na przykład może nie można zainicjować biblioteki DLL lub systemu operacyjnego może zabraknąć miejsca na stercie pulpitu. Jeśli pakiet kodu został zakończony, powinien zostać wyświetlony następujący komunikat o kondycji w SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Kod zakończenia w tym komunikacie kondycji jest jedyną wskazówką, że proces lub kontener zawiera o tym, dlaczego został zakończony. Może być generowany przez dowolny poziom stosu. Na przykład ten kod zakończenia może być związane z błędem systemu operacyjnego lub .NET problem lub może być podniesiona przez kod. Ten artykuł służy jako punkt wyjścia do diagnozowania źródła kodów zakończenia zakończenia i możliwych rozwiązań. Należy jednak pamiętać, że są to ogólne rozwiązania typowych scenariuszy i mogą nie dotyczyć błędu, który widzisz.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Jak sprawdzić, czy usługa Service Fabric zakończyła mój pakiet kodu?

Sieci szkieletowej usług może być odpowiedzialny za zakończenie pakietu kodu z różnych powodów. Na przykład może zdecydować o umieszczeniu pakietu kodu w innym węźle do celów równoważenia obciążenia. Można sprawdzić, czy sieci szkieletowej usług rozwiązał pakiet kodu, jeśli widzisz którykolwiek z kodów zakończenia w poniższej tabeli.

>[!NOTE]
> Jeśli proces lub kontener kończy się kodem zakończenia innym niż kody w poniższej tabeli, sieci szkieletowej usług nie ponosi odpowiedzialności za jego zakończenie.

Kod zakończenia | Opis
--------- | -----------
7147 | Wskazuje, że sieci szkieletowej usług bezpiecznie zamknąć proces lub kontener, wysyłając go Ctrl +C sygnału.
7148 | Wskazuje, że sieci szkieletowej usług zakończył proces lub kontener. Czasami ten kod błędu wskazuje, że proces lub kontener nie odpowiedział w odpowiednim czasie po wysłaniu sygnału Ctrl+C i musiał zostać zakończony.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Inne typowe kody błędów i ich potencjalne poprawki

Kod zakończenia | Wartość szesnastkowa | Krótki opis | Główna przyczyna | Potencjalna poprawka
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Ten błąd czasami oznacza, że na komputerze zabrakło miejsca na sterty pulpitu. Ta przyczyna jest szczególnie prawdopodobne, jeśli masz wiele procesów, które należą do aplikacji uruchomionej w węźle. | Jeśli program nie został zbudowany w celu reagowania na sygnały Ctrl+C, można włączyć ustawienie **EnableActivateNoWindow** w manifeście klastra. Włączenie tego ustawienia oznacza, że pakiet kodu będzie działać bez okna graficznego i nie będzie odbierać sygnałów Ctrl+C. Ta akcja zmniejsza również ilość miejsca na stercie pulpitu, z których korzysta każdy proces. Jeśli pakiet kodu musi odbierać sygnały Ctrl+C, można zwiększyć rozmiar sterty pulpitu węzła.
3762504530 | 0xe0434352 | Nie dotyczy | Ta wartość reprezentuje kod błędu dla nieobsługiwał wyjątek z kodu zarządzanego (czyli .NET). | Ten kod zakończenia wskazuje, że aplikacja zgłosiła wyjątek, który pozostaje nieobsługiowany i który zakończył proces. Jako pierwszy krok w określaniu, co wyzwoliło ten błąd, debuguj dzienniki aplikacji i pliki zrzutu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [diagnozowaniu innych typowych scenariuszy](service-fabric-diagnostics-common-scenarios.md).
* Zapoznaj się z bardziej szczegółowym omówieniem dzienników usługi Azure Monitor i oferowanych przez nich usług, czytając [omówienie usługi Azure Monitor.](../operations-management-suite/operations-management-suite-overview.md)
* Dowiedz się więcej o dziennikach usługi Azure [Monitor, które pomagają](../log-analytics/log-analytics-alerts.md) w wykrywaniu i diagnostyce.
* Zapoznaj się z funkcjami [wyszukiwania i wykonywania zapytań w dziennikach](../log-analytics/log-analytics-log-searches.md) dziennika oferowanych w ramach dzienników usługi Azure Monitor.
