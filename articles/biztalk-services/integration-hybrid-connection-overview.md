---
title: Połączenia hybrydowe — omówienie | Microsoft Docs
description: Informacje na temat połączeń hybrydowych, zabezpieczeń, portów TCP i obsługiwanych konfiguracji. MABS, WABS.
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 347ea75673336574f7517f2f7d0c802b0ed16560
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918301"
---
# <a name="hybrid-connections-overview"></a>Połączenia hybrydowe — omówienie

> [!IMPORTANT]
> Połączenia hybrydowe BizTalk zostały wycofane i zastąpione połączeniami hybrydowymi usługi App Service. Aby uzyskać więcej informacji, m.in. o tym, jak zarządzać istniejącymi połączeniami hybrydowymi BizTalk, zobacz artykuł [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) (Połączenia hybrydowe usługi Azure App Service).

Wprowadzenie do połączeń hybrydowych, lista obsługiwanych konfiguracji i lista wymaganych portów TCP.

## <a name="what-is-a-hybrid-connection"></a>Czym jest połączenie hybrydowe
Połączenia hybrydowe są funkcją usługi Azure BizTalk Services. Połączenia hybrydowe zapewniają łatwy i dogodny sposób łączenia funkcji Web Apps w usłudze Azure App Service (dawniej nazywanej Websites) oraz funkcji Mobile Apps w usłudze Azure App Service (dawniej nazywanej Mobile Services) z zasobami lokalnymi za zaporą.

![Połączenia hybrydowe][HCImage]

Korzyści wynikające z użycia połączeń hybrydowych:

* Funkcje Web Apps i Mobile Apps mogą w bezpieczny sposób uzyskać dostęp do istniejących lokalnych danych i usług.
* Wiele aplikacji internetowych lub aplikacji mobilnych może współdzielić połączenie hybrydowe, aby uzyskać dostęp do zasobu lokalnego.
* Do uzyskania dostępu do sieci potrzebne są minimalne porty TCP.
* Aplikacje korzystające z połączeń hybrydowych uzyskują dostęp tylko do określonych zasobów lokalnych opublikowanych za pośrednictwem połączenia hybrydowego.
* Mogą łączyć się z dowolnym zasobem lokalnym, który używa statycznego portu TCP, takim jak program SQL Server, MySQL, interfejsy API protokołu HTTP sieci Web i większość niestandardowych usług sieci Web.
  
  > [!NOTE]
  > Usługi oparte na protokole TCP, które używają portów dynamicznych (na przykład FTP w trybie pasywnym lub pasywnym trybie rozszerzonym) nie są obecnie obsługiwane. Protokół LDAP również nie jest obsługiwany. Protokół LDAP używa statycznego portu TCP, ale może również używać protokołu UDP. W związku z tym nie jest obsługiwany.
  > 
  > 
* Można ich używać ze wszystkimi środowiskami obsługiwanymi przez funkcję Web Apps (.NET, PHP, Java, Python, Node.js) oraz funkcję Mobile Apps (Node.js, .NET).
* Funkcje Web Apps oraz Mobile Apps mogą uzyskać dostęp do zasobów lokalnych w dokładnie taki sam sposób, jakby miało to miejsce, gdyby aplikacja internetowa lub aplikacja mobilna znajdowały się w sieci lokalnej. Na przykład takie same parametry połączenia użyte lokalnie mogą być również używane na platformie Azure.

Połączenia hybrydowe zapewniają także administratorom przedsiębiorstw widoczność zasobów firmy, do których uzyskują dostęp aplikacje hybrydowe, i kontrolę nad tymi zasobami, np.:

* Za pomocą ustawień zasad grupy administratorzy mogą zezwolić na połączenia hybrydowe w sieci, jak również określić zasoby, do których można uzyskać dostęp za pośrednictwem aplikacji hybrydowych.
* Dzienniki zdarzeń i inspekcji w sieci firmowej zapewniają widoczność zasobów, do których uzyskują dostęp połączenia hybrydowe.

## <a name="example-scenarios"></a>Przykładowe scenariusze
Połączenia hybrydowe obsługują następujące kombinacje środowisk i aplikacji:

* Dostęp do programu SQL Server w środowisku .NET
* Dostęp do usług protokołu HTTP/HTTPS z klientem WebClient w środowisku .NET
* Dostęp do programów SQL Server, MySQL w środowisku PHP
* Dostęp do programów SQL Server, MySQL i Oracle w środowisku Java
* Dostęp do usług protokołu HTTP/HTTPS w środowisku Java

Podczas korzystania z połączeń hybrydowych w celu uzyskania dostępu do lokalnego programu SQL Server należy uwzględnić następujące warunki:

* Nazwane wystąpienia programu SQL Express muszą być skonfigurowane do używania portów statycznych. Domyślnie nazwane wystąpienia programu SQL Express używają portów dynamicznych.
* Domyślne wystąpienia programu SQL Express używają portu statycznego, ale musi być włączony protokół TCP. Domyślnie protokół TCP nie jest włączony.
* Podczas korzystania z klastrowania lub grup dostępności tryb `MultiSubnetFailover=true` nie jest obecnie obsługiwany.
* Parametr `ApplicationIntent=ReadOnly` nie jest obecnie obsługiwany.
* Uwierzytelnianie SQL może być wymagane jako metoda autoryzacji typu end-to-end obsługiwana przez aplikację Azure i lokalny serwer SQL.

## <a name="security-and-ports"></a>Zabezpieczenie i porty
Połączenia hybrydowe używają autoryzacji za pomocą sygnatury dostępu współdzielonego (SAS) w celu zabezpieczenia połączeń z aplikacji Azure i lokalnego Menedżera połączeń hybrydowych z połączeniem hybrydowym. Zostają utworzone oddzielne klucze połączenia dla aplikacji i lokalnego Menedżera połączeń hybrydowych. Te klucze połączenia można niezależnie przywracać i odwoływać.

Połączenia hybrydowe zapewniają bezproblemową i bezpieczną dystrybucję kluczy do aplikacji i lokalnego Menedżera połączeń hybrydowych.

Zobacz temat [Create and Manage Hybrid Connections](integration-hybrid-connection-create-manage.md) (Tworzenie połączeń hybrydowych i zarządzanie nimi).

*Autoryzacja aplikacji jest oddzielona od połączenia hybrydowego*. Można użyć dowolnej metody autoryzacji. Metoda autoryzacji zależy od metod autoryzacji typu end-to-end obsługiwanych w chmurze Azure i składnikach lokalnych. Na przykład aplikacja Azure uzyskuje dostęp do lokalnego programu SQL Server. W tym scenariuszu autoryzacja SQL może być metodą autoryzacji obsługiwaną na całej trasie.

#### <a name="tcp-ports"></a>Porty TCP
Połączenia hybrydowe wymagają tylko łączności wychodzącej za pośrednictwem protokołu TCP lub HTTP z sieci prywatnej. Nie trzeba otwierać żadnych portów zapory ani zmieniać konfiguracji obwodu sieci, aby zezwolić na połączenie przychodzące do sieci.

Połączenia hybrydowe używają następujących portów TCP:

| Port | Do czego służy |
| --- | --- |
| 9350 – 9354 |Te porty służą do transmisji danych. Menedżer usługi Service Bus Relay sonduje port 9350 w celu ustalenia, czy łączność TCP jest dostępna. Jeśli jest dostępna, menedżer zakłada, że port 9352 jest również dostępny. Ruch w sieci jest przekazywany za pośrednictwem portu 9352. <br/><br/>Zezwalaj na połączenia wychodzące przez te porty. |
| 5671 |Gdy port 9352 używany do ruchu w sieci, port 5671 jest używany jako kanał kontrolny. <br/><br/>Zezwalaj na połączenia wychodzące przez ten port. |
| 80, 443 |Te porty są używane do niektórych żądań danych wysyłanych do platformy Azure. Ponadto, jeśli nie można użyć portów 9352 i 5671, *wtedy* porty 80 i 443 są portami rezerwowymi służącymi do transmisji danych i kanału kontrolnego.<br/><br/>Zezwalaj na połączenia wychodzące przez te porty. <br/><br/>**Uwaga** Nie zaleca się używania ich jako portów rezerwowych zamiast innych portów TCP. Dla kanałów danych używany jest protokół HTTP/WebSocket, a nie natywny protokół TCP. Może to spowodować obniżenie wydajności. |

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie połączeń hybrydowych i zarządzanie nimi](integration-hybrid-connection-create-manage.md)

## <a name="see-also"></a>Zobacz też
[Interfejs API REST do zarządzania usługi BizTalk Services na platformie Microsoft Azure](/previous-versions/azure/reference/dn232347(v=azure.100))  
[BizTalk Services: Zestawienie wersji](biztalk-editions-feature-chart.md)  
[Tworzenie usługi BizTalk](biztalk-provision-services.md)  
[BizTalk Services: Karty Pulpit nawigacyjny, Monitor i Skala](biztalk-dashboard-monitor-scale-tabs.md)  

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
