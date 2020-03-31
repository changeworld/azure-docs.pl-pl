---
title: Łączność usługi usługi Monitor wydajności sieci — usługa Azure Log Analytics
description: Za pomocą funkcji Monitor łączności usług w Monitorze wydajności sieci można monitorować łączność sieciową z dowolnym punktem końcowym, który ma otwarty port TCP.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 93f3820b7cf1db85b9ff4cd514fe22efc75a90d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249245"
---
# <a name="service-connectivity-monitor"></a>Monitorowanie łączności usług

Za pomocą monitora łączności usług w [Monitorze wydajności sieci](network-performance-monitor.md) można monitorować łączność sieciową z dowolnym punktem końcowym, który ma otwarty port TCP. Takie punkty końcowe obejmują witryny sieci Web, aplikacje SaaS, aplikacje PaaS i bazy danych SQL. 

Za pomocą Monitora łączności usługi można wykonywać następujące funkcje: 

- Monitoruj łączność sieciową z aplikacjami i usługami sieciowymi z wielu oddziałów lub lokalizacji. Aplikacje i usługi sieciowe obejmują usługę Office 365, program Dynamics CRM, wewnętrzne aplikacje biznesowe i bazy danych SQL.
- Wbudowane testy są używane do monitorowania łączności sieciowej z punktami końcowymi usługi Office 365 i Dynamics 365. 
- Określ czas odpowiedzi, opóźnienie sieci i utratę pakietów doświadczane podczas łączenia się z punktem końcowym.
- Określ, czy niska wydajność aplikacji jest ze względu na sieć lub z powodu pewnego problemu na końcu dostawcy aplikacji.
- Zidentyfikuj punkty sieci, które mogą powodować niską wydajność aplikacji, wyświetlając opóźnienie dodane przez każdy przeskok na mapie topologii.


![Monitorowanie łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację Monitora wydajności sieci, otwórz [rozwiązanie Monitor wydajności sieci](network-performance-monitor.md) i wybierz pozycję **Konfiguruj**.

![Konfigurowanie rozwiązania Network Performance Monitor](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurowanie agentów usługi Log Analytics do monitorowania
Włącz następujące reguły zapory w węzłach używanych do monitorowania, aby rozwiązanie można odnajdywać topologii z węzłów do punktu końcowego usługi: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Tworzenie testów Monitora łączności usług 

Rozpocznij tworzenie testów w celu monitorowania łączności sieciowej z punktami końcowymi usługi.

1. Wybierz **kartę Monitor łączności usług.**
2. Wybierz **dodaj test**i wprowadź nazwę testu i opis. Można utworzyć maksymalnie 450 testów na obszar roboczy. 
3. Wybierz typ testu:<br>

    * Wybierz **sieć Web,** aby monitorować łączność z usługą odpowiadającą na żądania HTTP/S, takie jak outlook.office365.com lub bing.com.<br>
    * Wybierz **opcję Sieć,** aby monitorować łączność z usługą, która odpowiada na żądania TCP, ale nie odpowiada na żądania HTTP/S, takie jak serwer SQL, serwer FTP lub port SSH. 
    * Na przykład: Aby utworzyć test sieci web na koncie magazynu obiektów blob, wybierz pozycję **Web** i wprowadź obiekt *docelowy*jako konto blob.core.windows.net. Podobnie można utworzyć testy dla innego magazynu tabel, magazynu kolejek i usługi Azure Files przy użyciu [tego łącza.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Jeśli nie chcesz wykonywać pomiarów sieciowych, takich jak opóźnienie sieci, utrata pakietów i odnajdowanie topologii, wyczyść pole wyboru **Wykonaj pomiary sieci.** Zachowaj go wybraną, aby uzyskać maksymalne korzyści z możliwości. 
5. W **kierować**wprowadź adres URL/FQDN/IP, z którym chcesz monitorować łączność sieciową.
6. W **numerze portu**wprowadź numer portu usługi docelowej. 
7. W **polu Częstotliwość testu**wprowadź wartość częstotliwości, jak często ma być uruchamiany test. 
8. Wybierz węzły, z których chcesz monitorować łączność sieciową z usługą. Upewnij się, że liczba agentów dodanych na test jest mniejsza niż 150. Każdy agent może przetestować maksymalnie 150 punktów końcowych/agentów.

    >[!NOTE]
    > W przypadku węzłów opartych na systemie Windows funkcja ta umożliwia wykonywanie pomiarów sieciowych za pomocą żądań opartych na protokół TCP. W przypadku węzłów opartych na kliencie systemu Windows do wykonywania pomiarów sieciowych można używać żądań opartych na ICMP. W niektórych przypadkach aplikacja docelowa blokuje przychodzące żądania oparte na ICMP, gdy węzły są oparte na kliencie systemu Windows. Rozwiązanie nie jest w stanie wykonać pomiarów sieciowych. W takich przypadkach zaleca się używanie węzłów opartych na systemie Windows. 

9. Jeśli nie chcesz tworzyć zdarzeń dotyczących kondycji dla wybranych elementów, wyczyść **pozycję Włącz monitorowanie kondycji w celach objętych tym testem**. 
10. Wybierz warunki monitorowania. Można ustawić niestandardowe progi dla generowania zdarzeń kondycji, wprowadzając wartości progowe. Za każdym razem, gdy wartość warunku przekracza wybrany próg dla wybranej pary sieci lub podsieci, generowane jest zdarzenie kondycji. 
11. Wybierz **pozycję Zapisz,** aby zapisać konfigurację. 

    ![Konfiguracje testowe Monitora łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Przewodnik 

Przejdź do widoku pulpitu nawigacyjnego Monitora wydajności sieci. Aby uzyskać podsumowanie kondycji różnych testów, które zostały utworzone, zajrzyj na stronie **Monitor łączności usługi.** 

![Strona Monitor łączności usługi](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Wybierz kafelek, aby wyświetlić szczegóły testów na **testach** strony. W tabeli po lewej stronie można wyświetlić kondycję punktu w czasie i wartość czasu odpowiedzi usługi, opóźnienia sieci i utraty pakietów dla wszystkich testów. Użyj kontrolki Rejestrator stanu sieci, aby wyświetlić migawkę sieci w innym czasie w przeszłości. Wybierz test w tabeli, który chcesz zbadać. Na wykresach w okienku po prawej stronie można wyświetlić historyczny trend wartości utraty, opóźnienia i czasu odpowiedzi. Wybierz **łącze Szczegóły testu,** aby wyświetlić wydajność z każdego węzła.

![Testy Monitora łączności usługowej](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

W widoku **Węzły testowe** można obserwować łączność sieciową z każdego węzła. Wybierz węzeł, który ma spadek wydajności. Jest to węzeł, w którym aplikacja jest obserwowana działa wolno.

Określ, czy niska wydajność aplikacji jest z powodu sieci lub problemu na końcu dostawcy aplikacji, obserwując korelację między czasem odpowiedzi aplikacji i opóźnienie sieci. 

* **Problem z aplikacją:** Skok w czasie odpowiedzi, ale spójność opóźnienia sieci sugeruje, że sieć działa poprawnie i problem może być spowodowane problemem na końcu aplikacji. 

    ![Problem z aplikacją Monitor łączności usługi](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problem z siecią:** Skok czasu odpowiedzi wraz z odpowiednim skokiem opóźnienia sieci sugeruje, że wzrost czasu odpowiedzi może być spowodowany wzrostem opóźnienia sieci. 

    ![Problem z siecią Monitor łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Po ustaleniu, że problem jest z powodu sieci, wybierz łącze widoku **Topologia,** aby zidentyfikować kłopotliwy przeskok na mapie topologii. Przykład jest pokazany na poniższej ilustracji. Z całkowitego opóźnienia 105 ms między węzłem a punktem końcowym aplikacji 96 ms jest spowodowane przeskokem zaznaczonym na czerwono. Po zidentyfikowaniu kłopotliwego przeskoku można podjąć działania naprawcze. 

![Testy Monitora łączności usługowej](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostyka 

Jeśli zauważysz nieprawidłowości, wykonaj następujące kroki:

* Jeśli czas odpowiedzi usługi, utrata sieci i opóźnienie są wyświetlane jako NA, przyczyną może być co najmniej jeden z następujących powodów:

    - Aplikacja jest wyłączna.
    - Węzeł używany do sprawdzania łączności sieciowej z usługą jest wyłączany.
    - Obiekt docelowy wprowadzony w konfiguracji testowej jest niepoprawny.
    - Węzeł nie ma żadnej łączności sieciowej.

* Jeśli wyświetlany jest prawidłowy czas odpowiedzi usługi, ale utrata sieci, a także opóźnienie są wyświetlane jako NA, przyczyną może być co najmniej jeden z następujących powodów:

    - Jeśli węzłem używanym do sprawdzania łączności sieciowej z usługą jest komputer kliencki systemu Windows, usługa docelowa blokuje żądania ICMP lub zapora sieciowa blokuje żądania ICMP pochodzące z węzła.
    - Pole wyboru **Wykonywanie pomiarów sieciowych** jest puste w konfiguracji testowej. 

* Jeśli czas odpowiedzi usługi jest NA, ale utrata sieci, a także opóźnienie są prawidłowe, usługa docelowa może nie być aplikacją sieci web. Edytuj konfigurację testu i wybierz typ testu jako **Sieć** zamiast **sieci Web**. 

* Jeśli aplikacja działa wolno, należy określić, czy niska wydajność aplikacji jest z powodu sieci lub problem na końcu dostawcy aplikacji.

## <a name="gcc-office-urls-for-us-government-customers"></a>Adresy URL biura GCC dla klientów instytucji rządowych w STANACH Zjednoczonych
W regionie Wirginia rządowa stanów Usa tylko adresy URL dod są wbudowane npm. Klienci korzystający z adresów URL GCC muszą tworzyć niestandardowe testy i dodawać każdy adres URL indywidualnie.

| Pole | GCC |
|:---   |:--- |
| Portal usługi Office 365 i udostępnione | portal.apps.mil |
| 365 y00 ysuchów i tożsamość usługi Office 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www .office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| Zespoły MS | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Następne kroki
[Wyszukaj dzienniki,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe rekordy danych wydajności sieci.
