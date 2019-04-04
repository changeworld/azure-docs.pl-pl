---
title: Tworzenie i zarządzanie nimi połączeń hybrydowych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć połączenie hybrydowe, zarządzać połączeniem i zainstaluj Menedżera połączeń hybrydowych. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 9d659262195fef0cc6871bac409dd5914b70f401
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916125"
---
# <a name="create-and-manage-hybrid-connections"></a>Tworzenie połączeń hybrydowych i zarządzanie nimi

> [!IMPORTANT]
> Połączenia hybrydowe BizTalk zostały wycofane i zastąpione połączeniami hybrydowymi usługi App Service. Aby uzyskać więcej informacji, m.in. o tym, jak zarządzać istniejącymi połączeniami hybrydowymi BizTalk, zobacz artykuł [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) (Połączenia hybrydowe usługi Azure App Service).
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Omówienie kroków
1. Tworzenie połączenia hybrydowego, wprowadzając **nazwy hosta** lub **FQDN** z lokalnymi zasobami w sieci prywatnej.
2. Połącz swoje aplikacje sieci web platformy Azure lub usługi Azure mobile apps z połączeniem hybrydowym.
3. Zainstaluj Menedżera połączeń hybrydowych zasobu w środowisku lokalnym, a następnie nawiązać połączenie z określonego połączenia hybrydowego. Azure portal udostępnia środowisko jednym kliknięciem, aby zainstalować i połączyć.
4. Zarządzanie połączeń hybrydowych i ich klucze połączenia.

Ten temat zawiera następujące kroki. 

> [!IMPORTANT]
> Istnieje możliwość ustawić punkt końcowy połączenia hybrydowego adresu IP. Jeśli używasz adresu IP, może być lub może nie skontaktować się z zasobami lokalnymi, w zależności od klienta. Na komputerze klienckim, wykonując wyszukiwanie DNS zależy od połączenia hybrydowego. W większości przypadków **klienta** kod aplikacji. Jeśli klient nie wykonuje wyszukiwania DNS (go nie próbuje rozpoznać adresu IP, tak jakby był on nazwę domeny (x.x.x.x)), a następnie ruchu sieciowego nie są wysyłane za pośrednictwem połączenia hybrydowego.
> 
> Na przykład (pseudokodzie), należy zdefiniować **10.4.5.6** jako hosta lokalnego:
> 
> **Działa w sposób następujący scenariusz:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on premises host`
> 
> **Poniższy scenariusz nie działa:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Tworzenie połączenia hybrydowego
Można utworzyć połączenie hybrydowe w [połączeń hybrydowych usługi Azure App Service](../app-service/app-service-hybrid-connections.md) **lub** przy użyciu [interfejsów API REST usługi BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Informacje dodatkowe
* Można tworzyć wiele połączeń hybrydowych. Zobacz [usługi BizTalk Services: Wykres przedstawiający wersje](biztalk-editions-feature-chart.md) liczbę dozwolonych połączeń. 
* Każde połączenie hybrydowe jest tworzony przy użyciu pary ciągów połączenia: Klucze aplikacji, które WYSYŁAĆ i klucze, które NASŁUCHUJĄ lokalnych. Każdej pary ma podstawowy i klucz pomocniczy. 

## <a name="LinkWebSite"></a>Łączenie aplikacji mobilnej lub aplikacji sieci Web w usłudze Azure App Service
Aby połączyć aplikację sieci Web lub aplikacji mobilnej w usłudze Azure App Service z istniejącego połączenia hybrydowe, wybierz **użyć istniejącego połączenia hybrydowego** w bloku połączeń hybrydowych. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Zainstaluj Menedżera połączeń hybrydowych w środowisku lokalnym
Po utworzeniu połączenia hybrydowego, należy zainstalować Menedżera połączeń hybrydowych na zasobami lokalnymi. Można go pobrać z aplikacji sieci web platformy Azure lub usługi BizTalk. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Połączenia hybrydowe usługi Azure App Service](../app-service/app-service-hybrid-connections.md) jest również odpowiedni zasób.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Informacje dodatkowe
* Menedżer połączeń hybrydowych, można zainstalować w następujących systemach operacyjnych:
  
  * Windows Server 2008 R2 (.NET Framework 4.5 + i Windows Management Framework 4.0 + wymagane)
  * Windows Server 2012 (Windows Management Framework 4.0 + wymagane)
  * Windows Server 2012 R2
* Po zainstalowaniu Menedżera połączeń hybrydowych, mają miejsce następujące zdarzenia: 
  
  * Połączenie hybrydowe, hostowanej na platformie Azure jest automatycznie konfigurowany do użycia podstawowe parametry połączenia aplikacji. 
  * Zasób On-Premises jest automatycznie konfigurowany do użycia podstawowe parametry połączenia między lokalnymi.
* Menedżer połączeń hybrydowych, należy użyć lokalnego prawidłowe parametry połączenia do autoryzacji. Azure Web Apps lub funkcji Mobile Apps, należy użyć parametrów połączenia prawidłową aplikację do autoryzacji.
* Możesz skalować połączeń hybrydowych, instalując inne wystąpienie Menedżera połączeń hybrydowych na innym serwerze. Konfigurowanie odbiornika w środowisku lokalnym, aby użyć tego samego adresu jako pierwszy odbiornik w środowisku lokalnym. W takiej sytuacji ruch jest losowo rozproszonej (działanie okrężne) między odbiorników active w środowisku lokalnym. 

## <a name="ManageHybridConnection"></a>Zarządzanie połączeń hybrydowych

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Połączenia hybrydowe usługi Azure App Service](../app-service/app-service-hybrid-connections.md) jest również odpowiedni zasób.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Kopiuj/ponowne generowanie parametrów połączenia hybrydowego

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Połączenia hybrydowe usługi Azure App Service](../app-service/app-service-hybrid-connections.md) jest również odpowiedni zasób.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Wykorzystanie zasad grupy do kontrolowania zasobów lokalnych, używany przez połączenie hybrydowe
1. Pobierz [szablonów administracyjnych Menedżera połączeń hybrydowych](https://www.microsoft.com/download/details.aspx?id=42963).
2. Wyodrębnij pliki.
3. Na komputerze, który modyfikuje zasad grupy wykonaj następujące czynności:  
   
   * Kopiowanie. Pliki ADMX *%WINROOT%\PolicyDefinitions* folderu.
   * Kopiowanie. Pliki ŚILS *%WINROOT%\PolicyDefinitions\en-us* folderu.

Po skopiowaniu służy Edytor zasad grupy do zmiany zasad.

## <a name="next"></a>Następne kroki
[Omówienie połączeń hybrydowych](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Zobacz też
[Interfejs API REST do zarządzania usługi BizTalk Services na platformie Microsoft Azure](/previous-versions/azure/reference/dn232347(v=azure.100))  
[BizTalk Services: Zestawienie wersji](biztalk-editions-feature-chart.md)  
[Tworzenie usługi BizTalk](biztalk-provision-services.md)  
[BizTalk Services: Karty Pulpit nawigacyjny, Monitor i Skala](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
