---
title: Zablokowanie ruchu wychodzącego środowiska App Service — platformy Azure
description: Opisuje sposób integrowania z zapory usługi Azure, aby zabezpieczyć ruch wychodzący
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6dae2d40650b9fdb8df2d3bdb74b2df78639dc11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058049"
---
# <a name="locking-down-an-app-service-environment"></a>Blokowanie środowiska usługi App Service

App Service Environment (ASE) ma wiele elementów zewnętrznych, które wymaga dostępu do do prawidłowego. Środowisko ASE znajduje się w klienta usługi Azure Virtual Network (VNet). Klienci muszą zezwalać na ruchu zależności środowiska ASE, która jest problemem dla klientów, które chcesz zablokować wszystkie ruch wychodzący z sieci wirtualnej.

Istnieje kilka zależności dla ruchu przychodzącego, które ma środowisko ASE. Nie można wysłać ruchu przychodzącego zarządzania za pomocą urządzenia zapory. Źródłowe adresy dla tego ruchu są znane i są publikowane w [adresy zarządzania środowiska App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentu. Reguły sieciowej grupy zabezpieczeń można utworzyć za pomocą tych informacji do zabezpieczenia ruchu przychodzącego.

Wychodzące zależności środowiska ASE prawie całkowicie są definiowane przy użyciu nazw FQDN, które nie mają adresów statycznych spodem. Brak statycznych adresów oznacza, że sieciowe grupy zabezpieczeń (NSG) nie można zablokować ruch wychodzący ze środowiska ASE. Zmianie adresów wystarczająco często nie jeden skonfigurować reguły na podstawie bieżącego rozdzielczości i użycie w celu utworzenia grup zabezpieczeń sieci. 

Rozwiązania do zabezpieczania wychodzące adresy zaletą korzystania z urządzenia zapory, które można kontrolować ruch wychodzący na podstawie nazw domen. Zaporę platformy Azure, można ograniczyć ruchu wychodzącego ruchu HTTP i HTTPS na podstawie nazwy FQDN miejsca docelowego.  

## <a name="system-architecture"></a>Architektura systemu

Wdrażania środowiska ASE z przechodzenia przez urządzenie zapory ruchu wychodzącego konieczna jest zmiana tras w podsieci środowiska ASE. Trasy działają na poziomie IP. Jeśli nie jesteś ostrożność podczas definiowania trasy, możesz wymusić TCP ruchu odpowiedzi do źródła spod innego adresu. Jest to nazywane routingiem asymetrycznym i spowoduje awarię, TCP.

Musi to być trasy zdefiniowane tak, aby ruch przychodzący do środowiska ASE potrzebują pomocy eksperta ponownie czy materiał taki sam sposób ruch. Dotyczy to żądań przychodzących zarządzania i jest wartość true dla żądań przychodzących aplikacji.

Ruch do i z ASE należy przestrzegać następujących konwencji

* Ruch do usługi Azure SQL, magazynu i Centrum zdarzeń nie są obsługiwane przy użyciu urządzenia zapory. Musi zostać wysłany ten ruch bezpośrednio do tych usług. Jest to sposób należy to zrobić do skonfigurowania punktów końcowych usługi dla tych trzech usług. 
* Reguły dotyczące tabeli tras muszą być zdefiniowane wysyłających ruch przychodzący zarządzania powrót po awarii, z której pochodzi.
* Reguły dotyczące tabeli tras muszą być zdefiniowane wysyłających ruch przychodzący aplikacji powrót po awarii, z której pochodzi. 
* Cały pozostały ruch, pozostawiając ASE mogą być wysyłane do urządzenia zapory przy użyciu reguły tabeli tras.

![Środowisko ASE przy użyciu przepływu połączenia zapory usługi Azure][5]

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurowanie zapory platformy Azure za pomocą środowiska ASE 

Dostępne są następujące kroki, aby zablokować ruch wychodzący ze środowiska ASE istniejących za pomocą zapory platformy Azure:

1. Włącz punkty końcowe usługi SQL, magazynu i Centrum zdarzeń w podsieci środowiska ASE. Aby to zrobić, przejdź do portalu usługi sieciowe > podsieci i wybierz pozycję elementu Microsoft.EventHub Microsoft.SQL i Microsoft.Storage z listy rozwijanej punktów końcowych usługi. W przypadku usługi włączonych punktów końcowych do bazy danych SQL Azure wszelkie zależności usługi Azure SQL, które aplikacje mają musi być skonfigurowany z również punktami końcowymi usługi. 

   ![Wybierz punkty końcowe usługi][2]
  
1. Utwórz podsieć o nazwie AzureFirewallSubnet w sieci wirtualnej, w której istnieje środowisko ASE. Postępuj zgodnie z instrukcjami [dokumentacji zapory usługi Azure](https://docs.microsoft.com/azure/firewall/) do utworzenia zapory platformy Azure.
1. Z poziomu interfejsu użytkownika zapory platformy Azure > zasady > kolekcji reguł aplikacji, wybierz kolekcję reguł aplikacji Dodaj. Podaj nazwę, priorytetu i ustaw Zezwalaj. W sekcji tagi pełni kwalifikowaną nazwę domeny, podaj nazwę, ustaw źródłowe adresy * i wybierz Tag FQDN środowiska usługi aplikacji i aktualizacji Windows. 
   
   ![Dodawanie reguły aplikacji][1]
   
1. Z poziomu interfejsu użytkownika zapory platformy Azure > zasady > sieci kolekcji reguł, wybierz kolekcję, Dodaj sieciowe reguły. Podaj nazwę, priorytet i ustaw Zezwalaj. W sekcji reguły, podaj nazwę, wybierz **wszelkie**Ustaw * do źródłowych i docelowych adresów i Ustaw porty 123. Ta reguła umożliwia systemowi Przeprowadź synchronizację zegara przy użyciu NTP. Utwórz inną regułę ten sam sposób, na port 12000 ułatwia klasyfikowanie problemów systemu.

   ![Dodaj regułę sieciowej NTP][3]

1. Utwórz tabelę tras z adresami zarządzania z [adresy zarządzania środowiska App Service Environment]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) z następnym przeskokiem do Internetu. Wpisy tabeli trasy są wymagane, aby uniknąć problemów z routingiem asymetrycznym. Dodawanie tras do zależności adresów IP wymienionych poniżej, w zależności adresu IP z następnym przeskokiem do Internetu. Dodaj trasę urządzenie wirtualne do tabeli tras dla 0.0.0.0/0 za pomocą następnego przeskoku jest prywatny adres IP swojej zapory usługi Azure. 

   ![Tworzenie tabeli tras][4]
   
1. Przypisz tabeli tras, który został utworzony do podsieci środowiska ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Wdrażania środowiska ASE za zaporą

Kroki wdrażania środowiska ASE zaporą są takie same jak konfigurowanie istniejące środowisko ASE przy użyciu zapory usługi Azure, chyba że użytkownik będzie konieczne tworzenie podsieci środowiska ASE, a następnie wykonaj poprzednie kroki. Aby utworzyć środowisko ASE w wcześniej istniejącej podsieci, należy użyć szablonu usługi Resource Manager, zgodnie z opisem w dokumencie na [tworzenia środowiska ASE przy użyciu szablonu usługi Resource Manager](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Ruch aplikacji 

Powyższych czynności umożliwi środowisku ASE działają bez problemów. Nadal należy skonfigurować rzeczy, które należy uwzględnić wymagania w zakresie aplikacji. Istnieją dwa problemy w przypadku aplikacji w środowisku ASE, który jest skonfigurowany z zapory usługi Azure.  

- Zależności aplikacji, należy dodać do zapory usługi Azure lub tabeli tras. 
- Trasy musi zostać utworzony dla ruchu aplikacji uniknąć problemów z routingu asymetrycznego

Twoje aplikacje są zależne, muszą zostać dodane do zapory usługi Azure. Tworzenie reguł aplikacji, które zezwalają na ruch HTTP/HTTPS i reguł sieciowych dla wszystkich innych. 

Jeśli znasz zakresu adresów, który ruch związany z żądaniami aplikacji będą pochodzić z, można dodać, że do tabeli tras przypisany do podsieci środowiska ASE. W przypadku dużych lub nieokreślony zakres adresów można użyć urządzenia sieciowego, takich jak Application Gateway zapewniają jednego adresu do dodania do tabeli tras. Aby uzyskać więcej informacji na temat konfigurowania bramy aplikacji przy użyciu środowiska ASE wewnętrznego modułu równoważenia obciążenia, przeczytaj [integrowanie środowiska ASE wewnętrznego modułu równoważenia obciążenia przy użyciu bramy aplikacji](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

To zastosowanie usługi Application Gateway jest tylko jeden przykład sposobu konfiguracji systemu. W przypadku użycia tej ścieżki, czy musisz dodać trasę do tabeli tras podsieci środowiska ASE, więc ruchu odpowiedzi wysyłane do usługi Application Gateway będzie tam przejść bezpośrednio. 

## <a name="logging"></a>Rejestrowanie 

Zapora usługi Azure może wysyłać dzienniki do usługi Azure Storage, Centrum zdarzeń lub dzienniki usługi Azure Monitor. Aby zintegrować aplikację przy użyciu dowolnej obsługiwanej lokalizacji docelowej, przejdź do portalu Azure zapory > dzienniki diagnostyczne i włączyć dzienniki Aby uzyskać żądany lokalizacji docelowej. Jeśli możesz zintegrować przy użyciu dzienników usługi Azure Monitor, a następnie możesz zobaczyć rejestrowania wszelki ruch wysyłany do zapory usługi Azure. Aby sprawdzić ruch, który jest pozbawiona, otwórz portalu obszaru roboczego usługi Log Analytics > Dzienniki i wprowadzić kwerendę, np. 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrowanie zapory platformy Azure przy użyciu dzienników usługi Azure Monitor jest bardzo przydatne w przypadku najpierw pobierania aplikacji działać po użytkownik nie rozpoznają wszystkie zależności aplikacji. Dowiedz się więcej na temat dzienników usługi Azure Monitor z [Analizuj dane dzienników w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Zależności

Następujące informacje są tylko wymagane, jeśli chcesz skonfigurować urządzenie zapory niż Zapora usługi Azure. 

- Punkty końcowe usługi należy skonfigurować usługi zdolne do punktu końcowego usługi.
- Spełniono adresu IP dla ruchu innego niż do protokołu HTTP/S (ruch TCP i UDP)
- Punktów końcowych HTTP/HTTPS nazwy FQDN można umieścić w urządzenia zapory.
- Symbol wieloznaczny HTTP/HTTPS punkty końcowe są zależności, które mogą się różnić, za pomocą środowiska ASE na podstawie liczby kwalifikatorów. 
- Zależności systemu Linux są tylko problemem, Jeżeli wdrażasz aplikacje systemu Linux w środowisku ASE. Gdy nie wdrażasz aplikacje systemu Linux w środowisku ASE, te adresy nie muszą zostać dodane do zapory. 

#### <a name="service-endpoint-capable-dependencies"></a>Zależności zdolne do punktu końcowego usługi 

| Endpoint |
|----------|
| Azure SQL |
| Azure Storage |
| Centrum zdarzeń Azure |

#### <a name="ip-address-dependencies"></a>Adres IP zależności

| Endpoint | Szczegóły |
|----------| ----- |
| \*:123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na port 123 |
| \*:12000 | Ten port jest używany dla niektórych monitorowanie systemu. Jeśli zablokowany, a następnie niektóre problemy, będzie trudniejsze do klasyfikacji, ale środowisko ASE będzie w dalszym ciągu działać |
| 40.77.24.27:80 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |
| 40.77.24.27:443 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |
| 13.90.249.229:80 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |
| 13.90.249.229:443 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |
| 104.45.230.69:80 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |
| 104.45.230.69:443 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |
| 13.82.184.151:80 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |
| 13.82.184.151:443 | Potrzebne do monitorowania i alertów na problemy w środowisku ASE |

Za pomocą zapory platformy Azure automatycznie zapewnia wszystko, co poniżej skonfigurowany przy użyciu tagów w pełni kwalifikowaną nazwę domeny. 

#### <a name="fqdn-httphttps-dependencies"></a>Zależności w pełni kwalifikowaną nazwę domeny HTTP/HTTPS 

| Endpoint |
|----------|
|graph.windows.net:443 |
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
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Symbol wieloznaczny HTTP/HTTPS zależności 

| Endpoint |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |

#### <a name="linux-dependencies"></a>Zależności systemu Linux 

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

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
