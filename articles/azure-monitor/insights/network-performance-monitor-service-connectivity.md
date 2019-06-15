---
title: Network Performance Monitor rozwiązania w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Możliwości Monitor łączności usługi można użyć w Monitorze wydajności sieci, do monitorowania łączności sieciowej do dowolnego punktu końcowego, który ma otwartego portu TCP.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: absha
ms.openlocfilehash: c5285ac95a2f5813949f22aae3849fd7f55b1ada
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052092"
---
# <a name="service-connectivity-monitor"></a>Monitorowanie łączności usług

Można użyć możliwości usługi Monitor łączności w [rozwiązania Network Performance Monitor](network-performance-monitor.md) do monitorowania łączności sieciowej do dowolnego punktu końcowego, który ma otwartego portu TCP. Takie punkty końcowe obejmują witryn sieci Web, aplikacji SaaS, PaaS aplikacji i baz danych SQL. 

Można wykonywać następujące funkcje za pomocą Monitor łączności usługi: 

- Monitoruj łączność sieciową z aplikacjami i usługami sieciowymi z wielu oddziałów lub lokalizacji. Aplikacje i usługi sieciowe obejmują usługi Office 365, Dynamics CRM, wewnętrzne aplikacje line-of-business i baz danych SQL.
- Użyj wbudowanych testy, aby monitorować łączność sieciową z punktami końcowymi usługi Office 365 i Dynamics 365. 
- Określ czas odpowiedzi, opóźnienie sieci i utraty pakietów doświadczonym podczas nawiązywania połączenia z punktem końcowym.
- Określ, czy niską wydajność aplikacji występuje z powodu sieci lub ze względu na problem po stronie dostawcy aplikacji.
- Zidentyfikuj punkty aktywne w sieci, która może powodować niską wydajność aplikacji, wyświetlając opóźnienie pochodzącego z każdego przeskoku na mapie topologii.


![Monitorowanie łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację rozwiązania Network Performance Monitor, otwórz [rozwiązania Network Performance Monitor](network-performance-monitor.md) i wybierz **Konfiguruj**.

![Konfigurowanie rozwiązania Network Performance Monitor](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurowanie agentów usługi Log Analytics do monitorowania
Włącz następujące reguły zapory na węzłach, używany do monitorowania, dzięki czemu rozwiązanie może odnajdować topologii z węzłów z punktem końcowym usługi: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Tworzenie testów monitorowania łączności usługi 

Zacznij tworzyć testy do monitorowania łączności sieciowej do punktów końcowych usługi.

1. Wybierz **Monitor łączności usługi** kartę.
2. Wybierz **Dodaj Test**, a następnie wprowadź nazwę testu i opis. Możesz utworzyć maksymalna testy 450 danego obszaru roboczego. 
3. Wybierz typ testu:<br>

    * Wybierz **Web** do monitorowania łączności z usługą, który odpowiada na żądania HTTP/Https, np. Exchange.office365.com lub bing.com.<br>
    * Wybierz **sieci** do monitorowania łączności z usługą, która odpowiada na żądania protokołu TCP, ale nie odpowiada na żądania HTTP/Https, takie jak SQL server, serwer FTP lub SSH port. 
    * Na przykład: Aby utworzyć test sieci web, do konta usługi blob storage, wybierz **Web** i wprowadź element docelowy jako *yourstorageaccount*. blob.core.windows.net. Podobnie można utworzyć testy dla innych usługi table storage, usługi queue storage i Azure Files przy użyciu [tego łącza.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Jeśli nie chcesz do wykonywania pomiarów sieci, takich jak opóźnienie sieci i utraty pakietów, Odnajdywanie topologii wyczyść **wykonaj pomiary sieci** pole wyboru. Zachowaj ono zaznaczone, aby uzyskać maksymalne korzyści z możliwości. 
5. W **docelowej**, wprowadź adres IP/nazwę FQDN/adres URL, do którego chcesz monitorować łączność sieciową.
6. W **numer portu**, wprowadź numer portu z usług docelowych. 
7. W **częstotliwość testowania**, wprowadź wartość jak często mają testów do uruchomienia. 
8. Wybierz węzły, które chcesz monitorować łączność sieciową do usługi. Upewnij się, że liczba agentów, które dodano na test jest mniej niż 150. Każdy agent można przetestować maksymalną 150 punktów końcowych i agentami testowymi.

    >[!NOTE]
    > W przypadku węzłów na serwerze Windows możliwości używa żądania oparte na protokole TCP do wykonywania pomiarów sieci. W przypadku węzłów opartą na kliencie Windows możliwości używa żądania ICMP w zależności do wykonywania pomiarów sieci. W niektórych przypadkach aplikacja docelowa blokuje przychodzące żądania opartego na protokole ICMP, gdy węzły zostaną Windows oparte na kliencie. Rozwiązanie jest w stanie wykonać pomiary sieci. Zalecamy użycie węzły na serwerze Windows w takich przypadkach. 

9. Jeśli nie chcesz utworzyć zdarzenia dotyczące kondycji dla elementów, możesz wybrać, wyczyść **Włącz monitorowanie kondycji elementów docelowych objętych tym testem**. 
10. Wybierz opcję monitorowania warunków. Można ustawić niestandardowe progi generacji zdarzenie kondycji, wprowadzając wartości progowe. Zawsze, gdy wartość warunku przekroczy wartość progu wybranych dla wybranej sieci lub pary podsieci, jest generowane zdarzenie kondycji. 
11. Wybierz **Zapisz** Aby zapisać konfigurację. 

    ![Monitor łączności usługi konfiguracji testów](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Przewodnik 

Przejdź do widoku pulpitu nawigacyjnego rozwiązania Network Performance Monitor. Aby uzyskać podsumowanie kondycji inne testy utworzone, Przyjrzyj się **Monitor łączności usługi** strony. 

![Strona usługi Monitor łączności](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Wybierz Kafelek, aby wyświetlić szczegółowe informacje o testy na **testy** strony. W tabeli po lewej stronie można wyświetlić w momencie kondycję i wartości czas odpowiedzi usługi, opóźnienie sieci i utraty pakietów dla wszystkich testów. Użyj kontroli rejestratora stanu sieci, aby wyświetlić migawki sieci w innym czasie w przeszłości. Wybierz test w tabeli, które chcesz zbadać. Na wykresach w okienku po prawej stronie można wyświetlić trend historyczny dotyczący utraty, opóźnienia i wartości czas odpowiedzi. Wybierz **Szczegóły testu** link, aby wyświetlić wydajność z każdego węzła.

![Monitor łączności usługi testów](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

W **węzły testów** widok, można zaobserwować, łączność sieciową z każdego węzła. Wybierz węzeł, który ma spadek wydajności. Jest to węzeł, w którym zaobserwowano aplikacja działa wolno.

Określić, czy niską wydajność aplikacji, jest z powodu sieci lub problem po stronie dostawcy aplikacji, obserwując korelację między czas reakcji aplikacji i opóźnienie sieci. 

* **Problem aplikacji:** Wzrost czas odpowiedzi, ale spójność na opóźnienie sieci sugeruje, że sieć działa prawidłowo, i może to oznaczać ze względu na problem po stronie aplikacji. 

    ![Monitor łączności usługi problemu z aplikacją](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problem z siecią:** Wzrost czas odpowiedzi wyposażony w odpowiedniej kolekcji w opóźnienia sieci sugeruje, że wzrost czas odpowiedzi może być ze względu na zwiększenie opóźnienia sieci. 

    ![Problem z siecią Monitor łączności usługi](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Po ustaleniu, czy problem jest z powodu sieci, wybierz **topologii** link widoku, aby zidentyfikować problematycznych przeskoku na mapie topologii. Na poniższej ilustracji przedstawiono przykład. Poza 105 ms całkowity czas oczekiwania między węzłem, a punkt końcowy aplikacji, 96 ms z powodu jest przeskoku zaznaczone na czerwono. Po zidentyfikowaniu problematycznych przeskoków, możesz korzystać z działania naprawcze. 

![Monitor łączności usługi testów](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostyka 

Jeśli zauważysz nieprawidłowości, wykonaj następujące kroki:

* Jeśli czas odpowiedzi usługi, utrata połączenia sieciowego i opóźnieniu są pokazywane jako n/d, co najmniej jeden z następujących powodów przyczyną może być:

    - Aplikacja nie działa.
    - Węzeł, używany do sprawdzania łączności sieciowej z usługą nie działa.
    - Element docelowy w konfiguracji testu jest nieprawidłowy.
    - Węzeł nie ma łączności sieciowej.

* Jeśli czas odpowiedzi prawidłową usługę jest wyświetlana, ale utrata połączenia sieciowego, a także opóźnienia są wyświetlane jako n/d, co najmniej jeden z następujących powodów przyczyną może być:

    - Węzeł używany do sprawdzania łączności sieciowej do usługi w przypadku komputera klienta Windows, Usługa docelowa blokuje żądania protokołu ICMP lub Zapora sieci blokuje żądania protokołu ICMP, które pochodzą z węzła.
    - **Wykonaj pomiary sieci** pole jest puste w konfiguracji testu. 

* Jeśli czas odpowiedzi usługi to nazwa, ale utrata połączenia sieciowego, a także opóźnienia są prawidłowe, Usługa docelowa nie może być aplikacja sieci web. Edytuj konfigurację testu, a następnie wybierz typ testu jako **sieci** zamiast **Web**. 

* Jeśli aplikacja działa wolno, należy określić, czy niską wydajność aplikacji jest ze względu na problem, po stronie dostawcy aplikacji lub sieci.

## <a name="gcc-office-urls-for-us-government-customers"></a>Adresy URL z pakietu Office GCC dla klientów rządowych USA
Dla regionu US Government Wirginia wyłącznie adresy URL DOD są wbudowane NPM. Klienci korzystający z adresów URL GCC konieczne tworzenie niestandardowych testów i Dodaj każdy adres URL indywidualnie.

| Pole | GCC |
|:---   |:--- |
| Portal usługi Office 365 i udostępnianie | portal.apps.mil |
| Uwierzytelnianie usługi Office 365 i tożsamości | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * logowania us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice — s1-bn1a.microsoftonline.com <br> * adminwebservice — s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365 net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS Teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Kolejne kroki
[Przeszukiwanie dzienników](../../azure-monitor/log-query/log-query-overview.md) do wyświetlania rekordów danych wydajności szczegółowe sieci.
