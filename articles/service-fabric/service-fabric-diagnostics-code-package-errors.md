---
title: Usługa Azure Service Fabric diagnozowanie błędów pakietu kodu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe błędy pakietu kodu za pomocą usługi Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276953"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Diagnozowanie typowych błędów pakietu kodu z usługą Service Fabric

W tym artykule pokazano, co to oznacza dla pakietu kodu nieoczekiwane zakończenie i zapewnia wgląd w możliwych przyczyn typowe kody błędów, a także kroki rozwiązywania problemów, które potencjalnie mogą rozwiązać ten problem.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Gdy procesie lub kontenerze nieoczekiwane zakończenie?

Usługa Service Fabric odbiera żądanie, aby uruchomić pakiet kodu, rozpoczyna, przygotowywanie środowiska w systemie lokalnym, na podstawie opcji konfiguracji w aplikacji i manifestów usługi. Te czynności mogą obejmować zarezerwowanie punkty końcowe sieci lub zasobów, konfigurowanie reguł zapory lub Konfigurowanie ograniczeń nadzoru zasobów. Po środowisko został poprawnie skonfigurowany, Usługa Service Fabric próbuje wyświetlić pakiet kodu. Ten krok jest traktowany jako pomyślne, jeśli system operacyjny lub kontener środowiska uruchomieniowego zgłasza, że procesie lub kontenerze został aktywowany pomyślnie. Jeśli aktywacja nie powiodła się, powinien zostać wyświetlony komunikat kondycji SFX, który jest wyświetlany komunikat

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Gdy pakiet kodu ma zostało pomyślnie wznowione, usługi Service Fabric rozpoczyna, monitorowanie cały okres ich istnienia. Na tym etapie procesu lub kontenera można zakończyć w dowolnym momencie z kilku powodów. Może mieć nie można zainicjować biblioteki DLL, system operacyjny może zabrakło miejsca na stercie pulpitu, itp. Jeśli pakiet kodu jest zakończony, powinien zostać wyświetlony komunikat kondycji SFX, który jest wyświetlany komunikat

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Kod zakończenia jest podana w tym kondycji komunikat jest tylko sugeruje, dostarczone przez proces/kontenera, aby Dlaczego zakończone i może zostać wygenerowane przez dowolny poziom stosu. Jest trudne do zrozumienia, ten kod wyjścia był powiązany, na przykład błąd systemu operacyjnego, wydania .NET, czy został zgłoszony przez kod. W rezultacie w tym artykule może służyć jako punkt wyjścia do diagnozowania źródła kodów zakończenia i występują możliwe rozwiązania, mając na uwadze te to ogólne rozwiązania typowych scenariuszy i mogą nie mieć zastosowania do błędu należy.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Jak sprawdzić, jeśli Usługa Service Fabric zakończony Mój pakiet kodu?

Usługa Service Fabric mogą być odpowiedzialne przerywa pakietu kodu dla różnych powodów. Na przykład może zdecydować, można umieścić pakietu kodu w innym węźle na potrzeby równoważenia obciążenia do celów. Aby sprawdzić, jeśli pakiet kodu został zakończony przez usługę Service Fabric, jeśli występują z kodów zakończenia w poniższej tabeli:

>[!NOTE]
> Jeśli proces/kontenera kończy działanie z kodem zakończenia innych niż te, w poniższej tabeli, usługi Service Fabric nie jest odpowiedzialne za zakończenia.

Kod zakończenia | Opis
--------- | -----------
7147 | Te kody błędów wskazują, że usługi Service Fabric zostanie poprawnie zamknięte procesu/kontenera wysyłając sygnał klawisze Ctrl + C.
7148 | Te kody błędów wskazują zakończenia procesu/kontenera usługi Service Fabric. Czasami tego kodu błędu może wskazywać, że proces/kontenera nie odpowiedział w odpowiednim po wysłaniu sygnału klawisze Ctrl + C, więc musi zostać skasowane.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Inne typowe kody błędów i ich potencjalne rozwiązania

Kod zakończenia | Wartość szesnastkowa | Krótki opis | Główna przyczyna | Potencjalnych awarii
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Ten błąd potencjalnie może oznaczać, że komputer ma za mało miejsca na stercie pulpitu. To jest bardzo prawdopodobne, jeśli masz dużą liczbę procesy należące do aplikacji uruchomionej w węźle. | Jeśli program nie został zbudowany odpowiada na sygnały klawisze Ctrl + C, można włączyć ustawienie "EnableActivateNoWindow" w manifeście klastra. Włączenie tego ustawienia oznacza pakietu kodu będzie działać bez okna graficznego interfejsu użytkownika i nie będzie odbierać sygnały klawisze Ctrl + C, ale zredukuje ilość miejsca sterty pulpitu, które zużywa poszczególnych procesów. Jeśli pakiet kodu musi odbierać sygnały klawisze Ctrl + C, można zwiększyć rozmiar sterty pulpitu tego węzła.
3762504530 | 0xe0434352 | ND | Ta wartość jest kod błędu dla nieobsługiwanego wyjątku z kodu zarządzanego (czyli .NET). | Jeśli widzisz ten kod wyjścia, oznacza to, że aplikacja zgłoszony wyjątek, który pozostaje nieobsłużony i zakończyła proces. Dzienniki aplikacji i zrzuty debugowania powinny być pierwszym krokiem do określania, co było przyczyną błędu.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [diagnozowanie innych typowych scenariuszy](service-fabric-diagnostics-common-scenarios.md)
* Bardziej szczegółowe omówienie dzienniki usługi Azure Monitor i informuje, czytając [co to jest dzienniki usługi Azure Monitor?](../operations-management-suite/operations-management-suite-overview.md)
* Dowiedz się więcej na temat dzienników usługi Azure Monitor [alerty](../log-analytics/log-analytics-alerts.md) ułatwiające wykrywanie i przeprowadzanie diagnostyki.
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach dzienniki usługi Azure Monitor
