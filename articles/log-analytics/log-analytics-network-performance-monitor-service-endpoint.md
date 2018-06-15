---
title: Sieci monitora wydajności rozwiązania Azure Log Analytics | Dokumentacja firmy Microsoft
description: Użyj możliwości Menedżer punktu końcowego usługi w Monitorze wydajności w sieci, aby monitorować łączność sieciową z dowolnego punktu końcowego, który został otwarty port TCP.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: b21d711e59ddc762eaf72f49e501d9f324d75105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240538"
---
# <a name="service-endpoint-monitor"></a>Monitor punktu końcowego usługi

Korzystając z możliwości Monitor punktu końcowego usługi w [monitora wydajności sieci](log-analytics-network-performance-monitor.md) monitorować łączność sieciową z dowolnego punktu końcowego, który został otwarty port TCP. Takie punkty końcowe obejmują witryn sieci Web, aplikacji SaaS PaaS aplikacji i baz danych. 

Można wykonywać następujące funkcje związane z monitorem punktu końcowego usługi: 

- Monitoruje łączność sieciową z wielu oddziałów lub lokalizacje aplikacji i usług sieciowych. Aplikacje i usługi sieciowe obejmują usługi Office 365, Dynamics CRM, wewnętrznych aplikacji biznesowych z i baz danych.
- Użyj wbudowanego testów monitorowania punktów końcowych usługi Office 365 i Dynamics 365 łączność sieciową. 
- Określ czas odpowiedzi, opóźnienie sieci i doświadczeni utraty pakietów, podczas nawiązywania połączenia z punktem końcowym.
- Określić, czy niską wydajnością jest z powodu sieci lub z powodu problemu z niektórych na końcu dostawcy aplikacji.
- Zidentyfikuj punkty aktywne w sieci, które mogą być przyczyną niską wydajnością wyświetlając opóźnienia przekazanych przez każdego przeskoku w formie mapy topologii.


![Monitor punktu końcowego usługi](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację monitora wydajności w sieci, otwórz [rozwiązania monitora wydajności sieci](log-analytics-network-performance-monitor.md) i wybierz **Konfiguruj**.

![Konfigurowanie monitora wydajności sieci](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Konfigurowanie usługi Operations Management Suite agentów do monitorowania
Włącz następujące reguły zapory w węzłach służący do monitorowania, aby rozwiązanie umożliwia odnalezienie topologii z węzłów z punktem końcowym usługi: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Tworzenie testów Monitor punktu końcowego usługi 

Rozpocznij tworzenie testów monitorowania punktów końcowych usługi łączność sieciową.

1. Wybierz **Monitor punktu końcowego usługi** kartę.
2. Wybierz **Dodaj Test**, a następnie wprowadź nazwę testu i opis. 
3. Wybierz typ testu:<br>

    * Wybierz **Web** monitorować łączność z usługą, która odpowiada na żądania HTTP/S, takie jak outlook.office365.com lub bing.com.<br>
    * Wybierz **sieci** monitorować łączność z usługą, która odpowiada na żądania protokołu TCP, ale nie odpowiada na żądania HTTP/S, takich jak SQL server, serwer FTP lub portu SSH. 
4. Jeśli nie chcesz przeprowadzać pomiarów sieci, takich jak opóźnienie sieci, utraty pakietów i topologii odnajdowania, wyczyść **wykonać pomiarów sieci** pole wyboru. Zachowaj on wybrany do pobrania maksymalnych korzyści z możliwości. 
5. W **docelowego**, wprowadź adres IP/nazwa FQDN/adres URL, do którego chcesz monitorować łączność sieciową.
6. W **numer portu**, wprowadź numer portu z usług docelowych. 
7. W **częstotliwość testów**, wprowadź wartość dla jak często mają testów do uruchomienia. 
8. Wybierz węzeł, z których chcesz monitorować łączność sieciową usługi. 

    >[!NOTE]
    > Dla węzłów na serwerze z systemem Windows możliwość używa żądań TCP do przeprowadzania pomiarów sieci. Dla węzłów opartą na kliencie systemu Windows możliwość używa żądań protokołu ICMP do przeprowadzania pomiarów sieci. W niektórych przypadkach aplikacji docelowej blokuje przychodzące żądania na podstawie protokołu ICMP, czy węzły są oparte na kliencie systemu Windows. Rozwiązanie jest w stanie wykonać pomiarów sieci. Zalecane jest użycie węzły na serwerze z systemem Windows w takich przypadkach. 

9. Jeśli nie chcesz utworzyć zdarzenia kondycji dla elementów zostanie wybrana, wyczyść **Włącz monitorowanie kondycji w elementy docelowe objętych ten test**. 
10. Wybierz warunki monitorowania. Można ustawić progami niestandardowymi generacji kondycji zdarzenie, wprowadzając wartości progowe. Zawsze, gdy wartość warunku wykraczają poza jego próg dla wybranej sieci lub parę podsieć, jest generowane zdarzenie kondycji. 
11. Wybierz **zapisać** Aby zapisać konfigurację. 

    ![Monitor punktu końcowego usługi konfiguracji testów](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Przewodnik 

Przejdź do widoku pulpitu nawigacyjnego monitora wydajności sieci. Aby uzyskać podsumowanie kondycji inne testy został utworzony, obejrzyj **Monitor punktu końcowego usługi** strony. 

![Monitor punktu końcowego usługi strony](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Wybierz Kafelek, aby wyświetlić szczegóły testów na **testy** strony. W tabeli po lewej stronie można wyświetlić w momencie kondycji i wartość czas odpowiedzi usługi, opóźnienie sieci i utraty pakietów, do wszystkich testów. Formant Rejestrator stanu sieci umożliwia wyświetlanie migawki sieci w późniejszym terminie w przeszłości. Wybierz test w tabeli, która ma być badany. Na wykresach w okienku po prawej stronie można wyświetlić trendów historycznych utraty, opóźnienia i wartości czas odpowiedzi. Wybierz **Szczegóły testu** łącze, aby wyświetlić dane wydajności z każdego węzła.

![Monitor punktu końcowego usługi testów](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

W **węzły testowego** widoku można obserwować łączność sieciową z każdego węzła. Wybierz węzeł, który ma spadek wydajności. To jest węzeł, w którym zaobserwowano aplikacja działa wolno.

Określić, czy niską wydajnością jest ze względu na problem po stronie dostawcy aplikacji lub sieci, obserwując korelacji między czas odpowiedzi aplikacji i opóźnienie sieci. 

* **Problem aplikacji:** kolekcji w czasie odpowiedzi, ale spójności opóźnienia sieci sugeruje, że sieć działa prawidłowo i problem może być spowodowane problemem po stronie aplikacji. 

    ![Monitor punktu końcowego usługi problemu z aplikacją](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **Sieci problem:** kolekcji czas odpowiedzi wraz z odpowiedniej kolekcji opóźnienia sieci sugeruje, że wzrost opóźnienia sieci mogą wynikać z wzrost czasu odpowiedzi. 

    ![Monitor punktu końcowego usługi problem z siecią](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Po ustaleniu, że problem dotyczy z powodu sieci, wybierz **topologii** link do wyświetlenia do identyfikowania powodującymi przeskoku na mapy topologii. Przykład przedstawiono na poniższej ilustracji. Poza opóźnienie całkowite 105 ms między węzłem a punkt końcowy aplikacji 96 ms z powodu jest przeskoku oznaczone na czerwono. Po zidentyfikowaniu powodującymi przeskoku można podjąć działania naprawcze. 

![Monitor punktu końcowego usługi testów](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostyka 

Jeśli zauważysz nieprawidłowości, wykonaj następujące kroki:

* Jeśli czas odpowiedzi usługi, utratę sieci lub opóźnienia są wyświetlane jako NA, co najmniej jeden z następujących powodów przyczyną może być:

    - Aplikacja jest wyłączony.
    - Węzeł używany do sprawdzania łączności z usługą jest wyłączony.
    - Element docelowy w konfiguracji testu jest niepoprawny.
    - Węzeł nie ma łączności sieciowej.

* Jeśli jest wyświetlany czas odpowiedzi prawidłową usługę, ale utraty sieci, a także opóźnienia są wyświetlane jako NA co najmniej jeden z następujących powodów przyczyną może być:

    - Jeśli węzeł używany do sprawdzania łączności z usługą jest komputer kliencki z systemem Windows, Usługa docelowa blokuje żądania protokołu ICMP lub sieci, Zapora blokuje żądania protokołu ICMP, które pochodzą z węzła.
    - **Wykonać pomiarów sieci** zaznaczenie jest puste w konfiguracji testu. 

* Jeśli NA to czasu odpowiedzi usługi, ale utraty sieci, a także opóźnienia są prawidłowe, Usługa docelowa może nie być aplikacji sieci web. Zmień konfigurację testu i wybierz typ testu jako **sieci** zamiast **Web**. 

* Jeśli aplikacja działa wolno, należy określić, czy niską wydajnością, ze względu na problem po stronie dostawcy aplikacji lub sieci.


## <a name="next-steps"></a>Kolejne kroki
[Wyszukaj dzienniki](log-analytics-log-searches.md) do wyświetlania rekordów danych wydajności szczegółowe sieci.
