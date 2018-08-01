---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: e23579479c61810d651bebae7b486b53aaaf0d42
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361375"
---
### <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest w pełni stanowych zapory jako — usługa wbudowaną wysoką dostępność i skalowalność chmury bez ograniczeń. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Zaporę platformy Azure jest obecnie w publicznej wersji zapoznawczej.

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Jakie funkcje są obsługiwane w wersji zapoznawczej zapory usługi Azure?  

* Zapory stanowe jako usługa
* Wbudowana wysoka dostępność z nieograniczoną skalowalnością w chmurze
* Filtrowanie według nazw FQDN 
* Reguły filtrowania ruchu sieciowego
* Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego
* Centralnie tworzenie, wymuszanie i dziennika aplikacji i sieci zasad łączności różnych subskrypcji platformy Azure z sieciami wirtualnymi
* Pełna integracja z usługą Azure Monitor na potrzeby rejestrowania i analizy 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Jak dołączyć Azure zapory publicznej wersji zapoznawczej?

Zaporę platformy Azure jest obecnie zarządzane publicznej wersji zapoznawczej, które możesz dołączyć za pomocą polecenia programu PowerShell element Register-AzureRmProviderFeature, zgodnie z opisem w dokumentacji platformy Azure zapory publicznej wersji zapoznawczej.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Co to jest cennik dla zapory usługi Azure?

Zaporę platformy Azure ma koszt stały + koszty zmiennej. Ceny są poniżej, a te jest dalsze stosowany rabat 50% w publicznej wersji zapoznawczej.

* Stała opłata: $1.25/firewall/hour
* Zmiennej opłaty: 0,03 USD/GB przetworzonych przez zapory (ruch przychodzący lub wychodzący).

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Co to jest model typowe wdrożenie dla zapory usługi Azure?

Choć jest możliwe do wdrożenia zapory usługi Azure w dowolnej sieci Wirtualnej, klienci będą zwykle wdrażania zapory platformy Azure w centralnej sieci Wirtualnej i komunikacji równorzędnej innych sieci wirtualnych do niego w modelu Centrum & szprychy. Następnie można ustawić trasy domyślnej z wirtualnych sieciach równorzędnych, aby wskazywał tej centralnej sieci Wirtualnej zapory.

### <a name="how-can-i-install-the-azure-firewall"></a>Jak zainstalować zapory usługi Azure?

Zaporę platformy Azure można skonfigurować za pomocą witryny Azure portal, programu PowerShell, interfejsu API REST lub szablonów. Zobacz [samouczek: Wdrażanie i konfigurowanie zapory platformy Azure przy użyciu witryny Azure portal](../articles/firewall/tutorial-firewall-deploy-portal.md) instrukcje krok po kroku.

### <a name="what-are-some-azure-firewall-concepts"></a>Jakie są niektóre pojęcia zapory platformy Azure?

Zapora usługi Azure obsługuje reguł i kolekcji reguł. Kolekcja reguł jest zestaw reguł, który współużytkuje te same kolejności i priorytet. Kolekcje reguł są wykonywane w kolejności według ich priorytetu, kolekcji reguł sieci mają wyższy priorytet niż kolekcje reguły aplikacji, są przerywa wszystkie reguły.
Istnieją dwa rodzaje kolekcji reguł:

* Reguły aplikacji: można skonfigurować w pełni kwalifikowanych nazw domen (FQDN), które są dostępne z podsieci. 
* Reguł sieciowych: umożliwia skonfigurowanie reguł zawierających adres źródłowy, protokół, port docelowy i adresu docelowego. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Zaporę usługi Azure obsługuje filtrowanie ruchu przychodzącego?

Zapora publicznej wersji zapoznawczej witryny Azure obsługuje tylko wychodzącego filtrowania. Dla ruchu przychodzącego ochrony dla protokołów innych niż HTTP/S (np: RDP i SSH, FTP) wstępnie zaplanowano dla platformy Azure GA. zapory  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Rejestrowanie i analizowanie jest obsługiwana przez zaporę usługi Azure?

Zaporę platformy Azure jest zintegrowana z usługą Azure Monitor do przeglądania i analizowania dzienników zapory. Dzienniki mogą być wysyłane do usługi Log Analytics, Azure Storage lub Centrum zdarzeń. Mogą być analizowane w usłudze Log Analytics lub przez różnych narzędzi, takich jak program Excel i Power BI. Zobacz [samouczek: dzienniki zapory usługi Azure Monitor](../articles/firewall/tutorial-diagnostics.md) Aby uzyskać więcej informacji.

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Jak działa zapory usługi Azure względem istniejącej, takich jak urządzenia WUS w portalu marketplace?

Zaporę platformy Azure to usługa Zapora podstawowa, która rozwiązywania przy użyciu określonych scenariuszy. Oczekuje się, konieczne może być zarówno urządzeń WUS innych firm i zapory usługi Azure. Współpracując z lepiej jest to główny priorytet.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaka jest różnica między brama aplikacji zapory aplikacji internetowych i zapory usługi Azure?

Zapora aplikacji sieci Web (WAF) to funkcja usługi Application Gateway, która zapewnia scentralizowaną ochronę ruchu przychodzącego w aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach. Zaporę platformy Azure zapewnia ochrony na poziomie sieciowego ruchu wychodzącego dla wszystkich portów i protokołów oraz ochrony na poziomie aplikacji dla ruchu wychodzącego HTTP/s Ogólna dostępność zapory usługi Azure planowana jest wstępnie ochrony dla ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład protokołu RDP, SSH, FTP)

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>Jaka jest różnica między grupami zabezpieczeń sieci (NSG) i zapory usługi Azure?

Usługa zapory usługi Azure uzupełniają funkcje grupy zabezpieczeń sieci i jednocześnie oferuje lepsze zabezpieczenia sieci ochronę w głębi. Grupa NSG dostarcza rozproszonych ruchu warstwy sieci, filtrowanie, aby ograniczyć ruch do zasobów w sieciach wirtualnych w ramach każdej subskrypcji.  Zaporę platformy Azure jest pełni stanowe, scentralizowane sieci zapory as-a-service, zapewniając ochrony na poziomie sieci i aplikacji w różnych subskrypcjach i sieci wirtualne (Vnet). 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak skonfigurować zaporę usługi Azure za pomocą moje punkty końcowe usługi?

Bezpieczny dostęp do usług PaaS zalecamy punktów końcowych usługi. Klienci platformy Azure zapory można włączyć punkty końcowe usługi w podsieci zapory usługi Azure i je wyłączyć na połączone sieci wirtualne będące szprychami korzystających z obie funkcje — zabezpieczenia punktu końcowego usługi i centralnego rejestrowania dla całego ruchu.

### <a name="what-are-the-known-service-limits"></a>Jakie są limity znane usługi?

* Zapora systemu Azure ma zmienny limit 1000 TB/Zapora/miesiąc. 
* Zapory platformy Azure, która działa w centralnej sieci Wirtualnej podlega ograniczenia komunikacji równorzędnej sieci Wirtualnej: maksimum wynoszące 50 sieci wirtualne będące szprychami.  
* Zaporę platformy Azure nie działa z globalnej komunikacji równorzędnej, więc powinien mieć co najmniej jedno wdrożenie zapory na region.
* Zaporę platformy Azure obsługuje 10 k stosowanie reguł i reguł sieci usługi 10 tys.