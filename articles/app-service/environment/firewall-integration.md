---
title: Blokowanie ruchu wychodzącego
description: Dowiedz się, jak zintegrować się z Zaporą platformy Azure, aby zabezpieczyć ruch wychodzący z poziomu środowiska usługi app service.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 84fcb9076bbc1e75d46d9a6682c96035576ae09e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475446"
---
# <a name="locking-down-an-app-service-environment"></a>Blokowanie środowiska usługi aplikacji

Środowisko usługi aplikacji (ASE) ma wiele zależności zewnętrznych, które wymaga dostępu do w celu poprawnego działania. Środowisko ASE mieszka w sieci wirtualnej platformy Azure (vnet). Klienci muszą zezwolić na ruch zależności ASE, co jest problemem dla klientów, którzy chcą zablokować wszystkie wyjścia z ich sieci wirtualnej.

Istnieje wiele przychodzących punktów końcowych, które są używane do zarządzania ASE. Ruch zarządzania przychodzący nie może być wysyłany za pośrednictwem urządzenia zapory. Adresy źródłowe dla tego ruchu są znane i są publikowane w dokumencie [adresy adresów adresów środowiska usługi app service.](https://docs.microsoft.com/azure/app-service/environment/management-addresses) Istnieje również tag usługi o nazwie AppServiceManagement, który może być używany z grupami zabezpieczeń sieciowych (NSG) w celu zabezpieczenia ruchu przychodzącego.

Zależności wychodzące ASE są prawie całkowicie zdefiniowane za pomocą sieci FQDN, które nie mają adresów statycznych za nimi. Brak adresów statycznych oznacza, że sieciowe grupy zabezpieczeń nie mogą być używane do blokowania ruchu wychodzącego z ase. Adresy zmieniają się na tyle często, że nie można skonfigurować reguł na podstawie bieżącej rozdzielczości i użyć ich do utworzenia sieciowych grup zabezpieczeń. 

Rozwiązanie do zabezpieczania adresów wychodzących polega na użyciu urządzenia zapory, które może kontrolować ruch wychodzący na podstawie nazw domen. Zapora platformy Azure może ograniczyć ruch wychodzący HTTP i HTTPS na podstawie fqdn miejsca docelowego.  

## <a name="system-architecture"></a>Architektura systemu

Wdrażanie ase z ruchu wychodzącego przechodzącego przez urządzenie zapory wymaga zmiany tras w podsieci ASE. Trasy działają na poziomie IP. Jeśli nie jesteś ostrożny w definiowaniu tras, możesz wymusić ruch odpowiedzi TCP do źródła z innego adresu. Gdy adres odpowiedzi różni się od ruchu adresu został wysłany do, problem jest nazywany routingu asymetrycznego i spowoduje przerwanie TCP.

Muszą istnieć trasy zdefiniowane, tak aby ruch przychodzący do ASE mógł odpowiedzieć w taki sam sposób, w jaki ruch został wyjętych. Trasy muszą być zdefiniowane dla przychodzących żądań zarządzania i dla żądań aplikacji przychodzących.

Ruch do i z ASE musi być przestrzegany przez następujące konwencje

* Ruch do usługi Azure SQL, Magazyn i Centrum zdarzeń nie są obsługiwane przy użyciu urządzenia zapory. Ten ruch musi być wysyłany bezpośrednio do tych usług. Sposobem na to, aby tak się stało, jest skonfigurowanie punktów końcowych usługi dla tych trzech usług. 
* Należy zdefiniować reguły tabeli tras, które wysyłają przychodzący ruch zarządzania z powrotem z miejsca, w którym pochodzi.
* Reguły tabeli tras muszą być zdefiniowane, które wysyłają ruch aplikacji przychodzących z powrotem z miejsca, w którym pochodzi. 
* Cały inny ruch opuszczający ase można wysłać do urządzenia zapory z regułą tabeli tras.

![ASE z przepływem połączenia Zapory platformy Azure][5]

## <a name="locking-down-inbound-management-traffic"></a>Blokowanie ruchu zarządzania przychodzącymi

Jeśli podsieć ASE nie ma jeszcze przypisanej sieciowej sieciowej sieciowej, utwórz ją. W sieciowej sieciowej sieciowej ustaw pierwszą regułę zezwalania na ruch z tagu usługi o nazwie AppServiceManagement na portach 454, 455. Reguła zezwalania na dostęp z appservicemanagement tag jest jedyną rzeczą, która jest wymagana od publicznych usług IP do zarządzania ase. Adresy, które są za tym tagiem usługi są używane tylko do administrowania usługą Azure App Service. Ruch zarządzania, który przepływa przez te połączenia jest szyfrowany i zabezpieczony certyfikatami uwierzytelniania. Typowy ruch na tym kanale obejmuje takie rzeczy jak polecenia zainicjowane przez klienta i sondy kondycji. 

Ases, które są wykonane za pośrednictwem portalu z nową podsieci są wykonane z sieciowej sieciowej, która zawiera regułę zezwalania dla AppServiceManagement tagu.  

Program ASE musi również zezwalać na żądania przychodzące z tagu Moduł równoważenia obciążenia na porcie 16001. Żądania z modułu równoważenia obciążenia na porcie 16001 są zachować kontrolę między modułem równoważenia obciążenia i frontów ASE. Jeśli port 16001 jest zablokowany, twój ase przejdzie w złej kondycji.

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurowanie zapory platformy Azure przy pracy z programem ASE 

Kroki, aby zablokować wyjście z istniejącego ASE z Zaporą platformy Azure są następujące:

1. Włącz punkty końcowe usługi do SQL, Storage i Event Hub w podsieci ASE. Aby włączyć punkty końcowe usługi, przejdź do portalu sieciowego > podsieci i wybierz pozycję Microsoft.EventHub, Microsoft.SQL i Microsoft.Storage z listy rozwijanej Punkty końcowe usługi. Jeśli masz punkty końcowe usługi włączone do usługi Azure SQL, wszelkie zależności SQL platformy Azure, które mają aplikacje muszą być skonfigurowane z punktami końcowymi usługi, jak również. 

   ![wybieranie punktów końcowych usługi][2]
  
1. Utwórz podsieć o nazwie AzureFirewallSubnet w sieci wirtualnej, w której istnieje ase. Postępuj zgodnie ze wskazówkami w [dokumentacji Zapory platformy Azure,](https://docs.microsoft.com/azure/firewall/) aby utworzyć zaporę platformy Azure.

1. Z kolekcji reguł > reguł > usługi Azure Firewall > wybierz pozycję Dodaj kolekcję reguł aplikacji. Podaj nazwę, priorytet i ustaw zezwalaj. W sekcji znaczniki FQDN podaj nazwę, ustaw adresy źródłowe na * i wybierz tag FQDN środowiska usługi app service i usługę Windows Update. 
   
   ![Dodaj regułę aplikacji][1]
   
1. Z kolekcji reguł interfejsu użytkownika > > usługi Azure Firewall > wybierz pozycję Dodaj kolekcję reguł sieciowych. Podaj nazwę, priorytet i ustaw zezwalaj. W sekcji Reguły w obszarze Adresy IP podaj nazwę, wybierz ptocol **any**, set * na Adresy źródłowe i docelowe i ustaw porty na 123. Ta reguła umożliwia systemowi wykonywanie synchronizacji zegara przy użyciu ntp. Utwórz inną regułę w ten sam sposób, aby port 12000, aby ułatwić klasyfikowanie wszelkich problemów z systemem. 

   ![Dodaj regułę sieci NTP][3]
   
1. Z kolekcji reguł interfejsu użytkownika > > usługi Azure Firewall > wybierz pozycję Dodaj kolekcję reguł sieciowych. Podaj nazwę, priorytet i ustaw zezwalaj. W sekcji Reguły w obszarze Tagi usług podaj nazwę, wybierz protokół **Any**, set * na Adresy źródłowe, wybierz tag usługi AzureMonitor i ustaw porty na 80, 443. Ta reguła umożliwia systemowi dostarczanie usługi Azure Monitor informacji o kondycji i metrykach.

   ![Dodaj regułę sieci tagów usługi NTP][6]
   
1. Utwórz tabelę tras z adresami zarządzania z [adresów zarządzania środowiskiem usługi App Service]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) z następnym przeskokiem Internetu. Wpisy tabeli marszruty są wymagane, aby uniknąć problemów z routingiem asymetrycznym. Dodaj trasy dla zależności adresów IP wymienionych poniżej w zależnościach adresów IP z następnym przeskokiem Internetu. Dodaj trasę urządzenia wirtualnego do tabeli tras dla 0.0.0.0/0, przy czym następnym przeskokiem jest prywatny adres IP zapory platformy Azure. 

   ![Tworzenie tabeli tras][4]
   
1. Przypisz utworzoną tabelę tras do podsieci ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Wdrażanie ase za zaporą

Kroki, aby wdrożyć ase za zaporą są takie same jak konfigurowanie istniejącego ASE z zaporą platformy Azure, z wyjątkiem trzeba będzie utworzyć podsieci ASE, a następnie wykonaj poprzednie kroki. Aby utworzyć ase w istniejącej podsieci, należy użyć szablonu Menedżera zasobów zgodnie z opisem w dokumencie [na temat Tworzenie ase z szablonem Menedżera zasobów](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Ruch aplikacji 

Powyższe kroki pozwolą twojemu ASE działać bez problemów. Nadal trzeba skonfigurować rzeczy, aby dostosować się do potrzeb aplikacji. Istnieją dwa problemy dla aplikacji w ase, który jest skonfigurowany z Zaporą platformy Azure.  

- Zależności aplikacji należy dodać do zapory platformy Azure lub tabeli tras. 
- Trasy muszą być tworzone dla ruchu aplikacji, aby uniknąć problemów z routingiem asymetrycznym

Jeśli aplikacje mają zależności, należy je dodać do zapory platformy Azure. Utwórz reguły aplikacji, aby zezwolić na ruch HTTP/HTTPS i reguły sieciowe dla wszystkich innych. 

Jeśli znasz zakres adresów, z którego będzie pochodził ruch żądania aplikacji, możesz dodać go do tabeli trasy przypisanej do podsieci ASE. Jeśli zakres adresów jest duży lub nieokreślony, można użyć urządzenia sieciowego, takiego jak brama aplikacji, aby podać jeden adres do dodania do tabeli trasy. Aby uzyskać szczegółowe informacje na temat konfigurowania bramy aplikacji za pomocą ase równoważenia obciążenia sieciowego, przeczytaj artykuł [Integracja ase ilb z bramą aplikacji](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

To użycie bramy aplikacji jest tylko jednym z przykładów konfigurowania systemu. Jeśli podążasz tą ścieżką, należy dodać trasę do tabeli trasy podsieci ASE, aby ruch odpowiedzi wysyłany do bramy aplikacji przechodził tam bezpośrednio. 

## <a name="logging"></a>Rejestrowanie 

Zapora platformy Azure może wysyłać dzienniki do dzienników usługi Azure Storage, Event Hub lub Azure Monitor. Aby zintegrować aplikację z dowolnym obsługiwanym miejscem docelowym, przejdź do portalu Zapory Azure > dzienniki diagnostyczne i włącz dzienniki dla żądanego miejsca docelowego. Jeśli integrujesz się z dziennikami usługi Azure Monitor, możesz wyświetlić rejestrowanie dla każdego ruchu wysyłanego do Zapory platformy Azure. Aby wyświetlić odrzucony ruch, otwórz portal obszaru roboczego usługi Log Analytics > dzienniki i wprowadź kwerendę, taką jak 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integracja zapory platformy Azure z dziennikami usługi Azure Monitor jest przydatna podczas pierwszego uzyskiwania aplikacji działającej, gdy nie znasz wszystkich zależności aplikacji. Więcej informacji o dziennikach usługi Azure Monitor można uzyskać na podstawie [analizy danych dziennika w usłudze Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Zależności

Poniższe informacje są wymagane tylko wtedy, gdy chcesz skonfigurować urządzenie zapory inne niż Zapora platformy Azure. 

- Usługi obsługujące punkt końcowy usługi powinny być skonfigurowane z punktami końcowymi usługi.
- Zależności adresów IP dotyczą ruchu innego niż HTTP/S (zarówno ruchu TCP, jak i UDP)
- Punkty końcowe HTTP/HTTPS usługi FQDN można umieszczać w urządzeniu zapory.
- Wieloznaczne punkty końcowe HTTP/HTTPS to zależności, które mogą się różnić w zależności od środowiska ASE na podstawie wielu kwalifikatorów. 
- Zależności linuksa są problemem tylko wtedy, gdy wdrażasz aplikacje Systemu Linux w systemie ASE. Jeśli nie wdrażasz aplikacji systemu Linux w systemie ASE, adresy te nie muszą być dodawane do zapory. 

#### <a name="service-endpoint-capable-dependencies"></a>Zależności z punktu końcowego usługi 

| Endpoint |
|----------|
| Azure SQL |
| Azure Storage |
| Centrum zdarzeń Azure |

#### <a name="ip-address-dependencies"></a>Zależności adresów IP

| Endpoint | Szczegóły |
|----------| ----- |
| \*:123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na porcie 123 |
| \*:12000 | Ten port jest używany do monitorowania systemu. Jeśli zostanie zablokowany, niektóre problemy będą trudniejsze do triage, ale ASE będzie nadal działać |
| 40.77.24.27:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 40.77.24.27:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.90.249.229:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.90.249.229:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 104.45.230.69:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 104.45.230.69:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.82.184.151:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.82.184.151:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |

Za pomocą zapory platformy Azure automatycznie pobierzesz wszystko poniżej skonfigurowane za pomocą tagów FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>Zależności HTTP/HTTPS usługi FQDN 

| Endpoint |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Zależności HTTP/HTTPS z symbolami wieloznaczymi 

| Endpoint |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*management.azure.com:443 . |
| \*update.microsoft.com:443 . |
| \*windowsupdate.microsoft.com:443 . |
| \*identity.azure.net:443 .identity.azure.net:443 |

#### <a name="linux-dependencies"></a>Zależności linuksa 

| Endpoint |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
| \*cdn.mscr.io:443 .cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>Zależności gov usa

W przypadku ases w regionach gov usa, postępuj zgodnie z instrukcjami w [konfigurowanie zapory azure z ase](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase) sekcji tego dokumentu, aby skonfigurować Zaporę platformy Azure z ASE.

Jeśli chcesz używać urządzenia innego niż Azure Firewall w usłudze US Gov 

* Usługi obsługujące punkt końcowy usługi powinny być skonfigurowane z punktami końcowymi usługi.
* Punkty końcowe HTTP/HTTPS usługi FQDN można umieszczać w urządzeniu zapory.
* Wieloznaczne punkty końcowe HTTP/HTTPS to zależności, które mogą się różnić w zależności od środowiska ASE na podstawie wielu kwalifikatorów.

Linux nie jest dostępny w regionach us Gov i dlatego nie jest wymieniony jako opcjonalna konfiguracja.

#### <a name="service-endpoint-capable-dependencies"></a>Zależności z punktu końcowego usługi ####

| Endpoint |
|----------|
| Azure SQL |
| Azure Storage |
| Centrum zdarzeń Azure |

#### <a name="ip-address-dependencies"></a>Zależności adresów IP

| Endpoint | Szczegóły |
|----------| ----- |
| \*:123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na porcie 123 |
| \*:12000 | Ten port jest używany do monitorowania systemu. Jeśli zostanie zablokowany, niektóre problemy będą trudniejsze do triage, ale ASE będzie nadal działać |
| 40.77.24.27:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 40.77.24.27:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.90.249.229:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.90.249.229:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 104.45.230.69:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 104.45.230.69:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.82.184.151:80 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |
| 13.82.184.151:443 | Potrzebne do monitorowania i ostrzegania o problemach z ASE |

#### <a name="dependencies"></a>Zależności ####

| Endpoint |
|----------|
| \*ctldl.windowsupdate.com:80 .ctldl.windowsupdate.com:80 |
| \*management.usgovcloudapi.net:80 . |
| \*update.microsoft.com:80 .update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*management.usgovcloudapi.net:443 .management.usgovcloudapi.net:443 |
|\*update.microsoft.com:443 . |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
