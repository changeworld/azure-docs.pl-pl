---
title: Blokowanie ruchu wychodzącego App Service Environment na platformie Azure
description: Opisuje sposób integrowania z zaporą platformy Azure w celu zabezpieczenia ruchu wychodzącego
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/29/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a3136939b27a5703aef8213225dee3d4d525754d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069487"
---
# <a name="locking-down-an-app-service-environment"></a>Blokowanie App Service Environment

App Service Environment (ASE) ma wiele zależności zewnętrznych wymaganych do poprawnego działania programu. Środowisko ASE działa w usłudze Azure Virtual Network (VNet). Klienci muszą zezwolić na ruch zależności środowiska ASE, który jest problemem dla klientów, którzy chcą zablokować wszystkie dane wyjściowe z sieci wirtualnej.

Istnieje wiele zależności przychodzących, które ma środowisko ASE. Ruch przychodzący zarządzania nie może być wysyłany przez urządzenie zapory. Adresy źródłowe dla tego ruchu są znane i są publikowane w dokumencie [App Service Environment Management addresss](https://docs.microsoft.com/azure/app-service/environment/management-addresses) . Można utworzyć reguły sieciowej grupy zabezpieczeń z tymi informacjami, aby zabezpieczyć ruch przychodzący.

Zależności wychodzące środowiska ASE są prawie całkowicie zdefiniowane przy użyciu nazw FQDN, które nie zawierają adresów statycznych. Brak adresów statycznych oznacza, że sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) nie mogą być używane do blokowania ruchu wychodzącego z środowiska ASE. Adresy zmieniają się często, ponieważ nie można skonfigurować reguł na podstawie bieżącej rozdzielczości i użyć jej do utworzenia sieciowych grup zabezpieczeń. 

Rozwiązanie służące do zabezpieczania adresów wychodzących polega na użyciu urządzenia zapory, które może kontrolować ruch wychodzący na podstawie nazw domen. Zapora platformy Azure może ograniczyć wychodzący ruch HTTP i HTTPS na podstawie nazwy FQDN lokalizacji docelowej.  

## <a name="system-architecture"></a>Architektura systemu

Wdrożenie środowiska ASE z ruchem wychodzącym przez urządzenie zapory wymaga zmiany tras w podsieci środowiska ASE. Trasy działają na poziomie IP. Jeśli nie masz ostrożnej definiowania tras, możesz wymusić ruch odpowiedzi protokołu TCP do źródła z innego adresu. Jest to nazywane routingiem asymetrycznym i spowoduje przerwanie protokołu TCP.

Muszą istnieć trasy zdefiniowane, aby ruch przychodzący do środowiska ASE mógł oddzwonić tak samo jak w przypadku ruchu. Dotyczy to żądań zarządzania dla ruchu przychodzącego i ma wartość true w przypadku żądań aplikacji dla ruchu przychodzącego.

Ruch do i ze środowiska ASE musi przestrzegać następujących konwencji

* Ruch do usługi Azure SQL, magazynu i centrum zdarzeń nie jest obsługiwany przy użyciu urządzenia zapory. Ten ruch musi być bezpośrednio skierowany do tych usług. W takim przypadku należy skonfigurować punkty końcowe usługi dla tych trzech usług. 
* Należy zdefiniować reguły tabeli tras, które wysyłają ruch przychodzący zarządzania z powrotem z miejsca, w którym został on odebrany.
* Należy zdefiniować reguły tabeli tras, które wysyłają ruch przychodzący aplikacji z powrotem z lokalizacji, w której został on odebrany. 
* Cały ruch wychodzący ze środowiska ASE można wysłać do urządzenia zapory za pomocą reguły tabeli tras.

![Przepływ połączenia środowiska ASE z zaporą platformy Azure][5]

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurowanie zapory platformy Azure przy użyciu środowiska ASE 

Kroki umożliwiające zablokowanie ruchu wychodzącego z istniejącego środowiska ASE przy użyciu zapory platformy Azure są następujące:

1. Włącz punkty końcowe usługi do serwera SQL, magazynu i centrum zdarzeń w podsieci środowiska ASE. W tym celu przejdź do portalu sieci > podsieci i wybierz pozycję Microsoft. EventHub, Microsoft. SQL i Microsoft. Storage z listy rozwijanej punkty końcowe usługi. Jeśli punkty końcowe usługi są włączone w usłudze Azure SQL, wszelkie zależności usługi Azure SQL, które są używane przez aplikacje, muszą być również skonfigurowane za pomocą punktów końcowych usługi. 

   ![Wybierz punkty końcowe usługi][2]
  
1. Utwórz podsieć o nazwie AzureFirewallSubnet w sieci wirtualnej, w której znajduje się środowisko ASE. Postępuj zgodnie z instrukcjami w [dokumentacji zapory platformy Azure](https://docs.microsoft.com/azure/firewall/) , aby utworzyć zaporę platformy Azure.
1. Z poziomu interfejsu użytkownika zapory platformy Azure > reguł > Kolekcja reguł aplikacji wybierz pozycję Dodaj kolekcję reguł aplikacji. Podaj nazwę, priorytet i ustaw wartość Zezwalaj. W sekcji Tagi FQDN Podaj nazwę, ustaw adresy źródłowe na * i wybierz tag App Service Environment FQDN i Windows Update. 
   
   ![Dodawanie reguły aplikacji][1]
   
1. Z poziomu interfejsu użytkownika zapory platformy Azure > reguł > kolekcji reguł sieciowych wybierz pozycję Dodaj kolekcję reguł sieci. Podaj nazwę, priorytet i ustaw wartość Zezwalaj. W sekcji reguły Podaj nazwę, wybierz opcję **wszystkie**, ustaw * na adres źródłowy i docelowy, a następnie ustaw porty na 123. Ta reguła umożliwia systemowi wykonywanie synchronizacji zegara przy użyciu protokołu NTP. Utwórz inną regułę w taki sam sposób, jak w przypadku portu 12000, aby pomóc w Klasyfikacja wszelkich problemów z systemem.

   ![Dodaj regułę sieci NTP][3]

1. Utwórz tabelę tras z adresami zarządzania [App Service Environment adresami zarządzania]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) z następnym przeskokiem Internetu. Wpisy tabeli tras są wymagane do uniknięcia problemów z routingiem asymetrycznym. Dodaj trasy dla zależności adresów IP zanotowanych poniżej w zależności od adresu IP z następnym przeskokiem Internetu. Dodaj trasę urządzenia wirtualnego do tabeli tras dla 0.0.0.0/0 przy następnym przeskoku do prywatnego adresu IP zapory platformy Azure. 

   ![Tworzenie tabeli tras][4]
   
1. Przypisz utworzoną tabelę tras do podsieci środowiska ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Wdrażanie środowiska ASE za zaporą

Kroki umożliwiające wdrożenie środowiska ASE za zaporą są takie same jak w przypadku konfigurowania istniejącego środowiska ASE przy użyciu zapory platformy Azure, z wyjątkiem sytuacji, w której konieczne będzie utworzenie podsieci środowiska ASE, a następnie wykonanie powyższych kroków. Aby utworzyć środowisko ASE w istniejącej podsieci, należy użyć szablonu Menedżer zasobów, zgodnie z opisem w dokumencie dotyczącym [tworzenia środowiska ASE z szablonem Menedżer zasobów](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Ruch aplikacji 

Powyższe kroki pozwolą, aby środowisko ASE mogło działać bez problemów. Nadal musisz skonfigurować elementy, aby sprostać wymaganiom Twojej aplikacji. Istnieją dwa problemy dla aplikacji w środowisku ASE skonfigurowanym za pomocą zapory platformy Azure.  

- Zależności aplikacji należy dodać do zapory platformy Azure lub tabeli tras. 
- Trasy muszą zostać utworzone dla ruchu aplikacji, aby uniknąć problemów z routingiem asymetrycznym

Jeśli aplikacje mają zależności, należy je dodać do zapory platformy Azure. Utwórz reguły aplikacji, aby umożliwić ruch HTTP/HTTPS i reguły sieciowe dla wszystkich innych elementów. 

Jeśli wiesz, z jakim zakresem adresów będzie pochodzą ruch żądania aplikacji, możesz dodać go do tabeli tras przypisanej do podsieci środowiska ASE. Jeśli zakres adresów jest duży lub nieokreślony, możesz użyć urządzenia sieciowego, takiego jak Application Gateway, aby podać jeden adres do dodania do tabeli tras. Aby uzyskać szczegółowe informacje na temat konfigurowania Application Gateway przy użyciu środowiska ILB ASE, przeczytaj artykuł [integrowanie ILB ASE z Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

To użycie Application Gateway to tylko jeden przykład konfiguracji systemu. Jeśli ta ścieżka była zgodna, należy dodać trasę do tabeli tras podsieci ASE, aby ruch odpowiedzi wysłany do Application Gateway mógł bezpośrednio przejść do sieci. 

## <a name="logging"></a>Rejestrowanie 

Zapora platformy Azure może wysyłać dzienniki do usługi Azure Storage, centrum zdarzeń lub dzienników Azure Monitor. Aby zintegrować aplikację z dowolnym obsługiwanym miejscem docelowym, przejdź do portalu zapory platformy Azure > dzienników diagnostycznych i Włącz dzienniki dla żądanego miejsca docelowego. W przypadku integracji z dziennikami Azure Monitor można zobaczyć rejestrowanie dowolnego ruchu wysyłanego do zapory platformy Azure. Aby zobaczyć, że ruch jest zabroniony, Otwórz Log Analytics Portal obszaru roboczego > Dzienniki i wprowadź zapytanie, takie jak 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrowanie zapory platformy Azure z dziennikami Azure Monitor jest bardzo przydatne podczas pierwszego uruchamiania aplikacji, gdy nie są znane wszystkie zależności aplikacji. Więcej informacji na temat dzienników Azure Monitor można znaleźć [w temacie Analizowanie danych dzienników w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Zależności

Poniższe informacje są wymagane tylko wtedy, gdy chcesz skonfigurować urządzenie zapory inne niż Zapora platformy Azure. 

- Usługi obsługujące punkt końcowy usługi należy skonfigurować za pomocą punktów końcowych usługi.
- Zależności adresów IP są związane z ruchem innym niż HTTP/S (ruch TCP i UDP)
- Punkty końcowe HTTP/HTTPS w nazwie FQDN można umieścić na urządzeniu zapory.
- Symbole wieloznaczne protokołu HTTP/HTTPS są zależnościami, które mogą być różne dla środowiska ASE w oparciu o wiele kwalifikatorów. 
- Zależności systemu Linux są uwzględniane tylko w sytuacji, gdy wdrażasz aplikacje Linux w środowisku ASE. Jeśli nie wdrażasz aplikacji systemu Linux w środowisku ASE, te adresy nie muszą być dodawane do zapory. 

#### <a name="service-endpoint-capable-dependencies"></a>Zależności obsługujące punkt końcowy usługi 

| Endpoint |
|----------|
| SQL Azure |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Zależności adresów IP

| Endpoint | Szczegóły |
|----------| ----- |
| \*:123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na porcie 123 |
| \*:12000 | Ten port jest używany w przypadku niektórych monitorowania systemu. W przypadku zablokowania niektóre problemy będą trudniejsze do klasyfikacja, ale środowisko ASE będzie nadal działać |
| 40.77.24.27:80 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |
| 40.77.24.27:443 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |
| 13.90.249.229:80 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |
| 13.90.249.229:443 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |
| 104.45.230.69:80 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |
| 104.45.230.69:443 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |
| 13.82.184.151:80 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |
| 13.82.184.151:443 | Jest to konieczne do monitorowania i generowania alertów dotyczących problemów z ASE |

Za pomocą zapory platformy Azure automatycznie otrzymujesz wszystko skonfigurowane przy użyciu tagów FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>Zależności HTTP/HTTPS w nazwie FQDN 

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
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Wieloznaczne zależności HTTP/HTTPS 

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
