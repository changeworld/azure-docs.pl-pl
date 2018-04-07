---
title: Obsługa zapory DB rozwiązania Cosmos & IP Azure kontrola dostępu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zasad kontroli dostępu dla adresu IP dla strony Obsługa zapory na kontach bazy danych Azure DB rozwiązania Cosmos.
keywords: Kontrola dostępu do adresu IP, obsługi zapory
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
documentationcenter: ''
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: sngun
ms.openlocfilehash: 4d5743703f3a1d98b720bd92a30c91549bbf89c0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-firewall-support"></a>Obsługa zapory w usłudze Azure DB rozwiązania Cosmos
Aby zabezpieczyć dane przechowywane na koncie Azure DB rozwiązania Cosmos bazy danych, bazy danych Azure rozwiązania Cosmos udostępnił pomocy technicznej na podstawie klucza tajnego [modelu autoryzacji](https://msdn.microsoft.com/library/azure/dn783368.aspx) , która zawiera kod uwierzytelniania wiadomości Hash-based silne (HMAC). Oprócz modelu autoryzacji na podstawie tajnego bazy danych Azure rozwiązania Cosmos obsługuje teraz, zasady kontroli dostępu na podstawie adresu IP dla strony Obsługa zapory dla ruchu przychodzącego zmiennych. Ten model jest podobne do reguł zapory systemu tradycyjne bazy danych i zapewnia dodatkowy poziom zabezpieczeń do bazy danych Azure rozwiązania Cosmos konta bazy danych. Z tym modelem można teraz skonfigurować konto bazy danych DB rozwiązania Cosmos Azure można uzyskiwać tylko z zatwierdzonych zbiór maszyny i/lub usług w chmurze. Dostęp do zasobów bazy danych Azure rozwiązania Cosmos z tych zestawów zatwierdzonych komputery i usługi wymagają nadal obiekt wywołujący, aby przedstawić token prawidłowego autoryzacji.

## <a name="ip-access-control-overview"></a>Omówienie kontroli dostępu dla adresu IP
Domyślnie konto bazy danych Azure DB rozwiązania Cosmos jest dostępny z publicznej sieci internet, tak długo, jak długo żądanie towarzyszy token prawidłowego autoryzacji. Aby skonfigurować kontroli dostępu na podstawie zasad IP, użytkownik musi podać zbiór adresów IP i zakresów adresów IP w postaci CIDR do uwzględnienia jako lista dozwolonych adresów IP klienta, konto bazy danych. Po zastosowaniu tej konfiguracji wszystkich żądań wysyłanych z komputerów spoza tej listy dozwolonych są blokowane przez serwer.  Na poniższym diagramie przedstawiono połączenia przetwarzania przepływu kontroli dostępu na podstawie adresu IP:

![Diagram przedstawiający proces łączenia dla kontroli dostępu na podstawie adresu IP](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Konfigurowanie zasad kontroli dostępu IP
Zasady kontroli dostępu IP można ustawić w portalu Azure lub programistycznie za pomocą [interfejsu wiersza polecenia Azure](cli-samples.md), [programu Azure Powershell](powershell-samples.md), lub [interfejsu API REST](/rest/api/cosmos-db/) aktualizując **ipRangeFilter** właściwości. 

Aby ustawić zasady kontroli dostępu do adresu IP w portalu Azure, przejdź do strony konta bazy danych rozwiązania Cosmos Azure, kliknij przycisk **zapory** w menu nawigacji następnie zmienić **zezwolić na dostęp z** do wartości  **Wybrane sieci**, a następnie kliknij przycisk **zapisać**. 

![Zrzut ekranu pokazujący sposób otworzyć stronę zapory w portalu Azure](./media/firewall-support/azure-portal-firewall.png)

Po IP kontroli dostępu w portalu zapewnia możliwość określenia adresów IP i zakresów, a także przełączników, aby umożliwić dostęp do innych usług platformy Azure i portalu Azure. Dodatkowe informacje na temat tych przełączników znajduje się w poniższych sekcjach.

> [!NOTE]
> Przez włączenie IP zasady kontroli dostępu dla konta bazy danych Azure rozwiązania Cosmos bazy danych, dostęp do konta bazy danych Azure rozwiązania Cosmos bazy danych z komputerów poza skonfigurowanego dozwolone listę zakresów adresów IP są zablokowane. Z tym modelem przeglądanie operacji płaszczyzna danych z portalu będzie również zablokowany do zapewnienia integralności kontroli dostępu.

## <a name="connections-from-the-azure-portal"></a>Połączenia z portalu Azure 

Po włączeniu zasady kontroli dostępu IP programowo, należy dodać adres IP portalu Azure, aby **ipRangeFilter** właściwości w celu uzyskania dostępu. Portal adresy IP są:

|Region|Adres IP|
|------|----------|
|Wszystkie regiony, z wyjątkiem tych określonych poniżej|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Niemcy|51.4.229.218|
|Chiny|139.217.8.252|
|Rząd USA|52.244.48.71|

Dostęp do portalu Azure jest domyślnie włączona po zmianie ustawienia zapory, aby **wybrane sieci** w portalu Azure. 

![Zrzut ekranu pokazujący sposób włączania dostępu do portalu Azure](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-other-azure-paas-services"></a>Połączenia z innymi usługami Azure PaaS 
Na platformie Azure PaaS usług takich jak Azure Stream analytics, usługi Azure Functions i usłudze Azure App Service są używane w połączeniu z bazy danych Azure rozwiązania Cosmos. Aby włączyć dostęp do bazy danych Azure rozwiązania Cosmos konto bazy danych z tych usług, których adresy IP nie są łatwo dostępne, należy dodać adres IP 0.0.0.0 do listy dozwolonych adresów IP skojarzonych z Twoim kontem bazy danych Azure DB rozwiązania Cosmos programowo. 

Dostęp do innych usług platformy Azure jest domyślnie włączona po zmianie ustawienia zapory, aby **wybrane sieci** w portalu Azure. 

![Zrzut ekranu pokazujący sposób otworzyć stronę zapory w portalu Azure](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Połączenia z bieżącym IP

Aby ułatwić projektowanie, portalu Azure ułatwia Identyfikuj i Dodaj adres IP komputera klienta do listy dozwolonych, dzięki czemu aplikacje działające na tym komputerze można uzyskać dostęp do konta bazy danych Azure rozwiązania Cosmos. Adres IP klienta tutaj wykryto widziany przez portal. Może to być adres IP klienta na komputerze, ale może być również adres IP bramy sieci. Pamiętaj usunąć go przed przejściem do środowiska produkcyjnego.

Aby włączyć użytkownika bieżącego adresu IP, wybierz **dodać mojego bieżącego adresu IP**, który dodaje bieżącego IP na liście adresów IP, a następnie kliknij **zapisać**.

![Zrzut ekranu pokazujący sposób konfigurowania ustawień zapory dla bieżącego adresu IP](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Połączenia z usługi w chmurze
Na platformie Azure usługi w chmurze są często stosowana metoda do hostowania warstwy środkowej logiki usługi przy użyciu bazy danych Azure rozwiązania Cosmos. Aby włączyć dostęp do bazy danych Azure rozwiązania Cosmos konta bazy danych z usługi w chmurze, publiczny adres IP usługi w chmurze należy dodać do listy dozwolonych adresów IP skojarzonych z Twoim kontem bazy danych Azure DB rozwiązania Cosmos przez [Konfigurowanie dostępu do adresu IP kontrolowanie zasad](#configure-ip-policy). Gwarantuje to, że wszystkich wystąpień roli usługi w chmurze dostęp do Twojego konta bazy danych Azure DB rozwiązania Cosmos. Można pobrać adresy IP dla usług w chmurze w portalu Azure, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP w usłudze w chmurze wyświetlane w portalu Azure](./media/firewall-support/public-ip-addresses.png)

Podczas skalowanie usługi w chmurze, dodając rolę dodatkowe wystąpienia tych nowych wystąpień automatycznie mają dostęp do bazy danych Azure rozwiązania Cosmos konto bazy danych, ponieważ są one częścią tej samej usługi w chmurze.

## <a name="connections-from-virtual-machines"></a>Połączenia z maszyn wirtualnych
[Maszyny wirtualne](https://azure.microsoft.com/services/virtual-machines/) lub [zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) może również służyć do hostowania warstwy środkowej usług przy użyciu bazy danych Azure rozwiązania Cosmos.  Skonfiguruj konto bazy danych DB rozwiązania Cosmos Azure, aby zezwolić na dostęp z maszyn wirtualnych, publicznych adresów IP maszyny wirtualnej i/lub maszyny wirtualnej zestawu skali musi być skonfigurowany jako jeden z dozwolonych adresów IP dla konta bazy danych Azure DB rozwiązania Cosmos przez [Konfigurowanie zasad kontroli dostępu IP](#configure-ip-policy). Można pobrać adresy IP dla maszyn wirtualnych w portalu Azure, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający publicznego adresu IP dla maszyny wirtualnej, wyświetlane w portalu Azure](./media/firewall-support/public-ip-addresses-dns.png)

Po dodaniu wystąpień dodatkowych maszyn wirtualnych do grupy są automatycznie udostępniane dostęp do Twojego konta bazy danych Azure DB rozwiązania Cosmos.

## <a name="connections-from-the-internet"></a>Połączenia z Internetu
Gdy uzyskujesz dostęp do bazy danych Azure rozwiązania Cosmos konto bazy danych z komputera w Internecie, adres IP klienta lub zakres adresów IP komputera, należy dodać do listy dozwolonych adresów IP do bazy danych Azure rozwiązania Cosmos konta bazy danych. 

## <a name="troubleshooting-the-ip-access-control-policy"></a>Rozwiązywanie problemów z zasady kontroli dostępu do adresu IP
### <a name="portal-operations"></a>Operacje portalu
Przez włączenie IP zasady kontroli dostępu dla konta bazy danych Azure rozwiązania Cosmos bazy danych, dostęp do konta bazy danych Azure rozwiązania Cosmos bazy danych z komputerów poza skonfigurowanego dozwolone listę zakresów adresów IP są zablokowane. W związku z tym jeśli chcesz włączyć danych portalu płaszczyzny operacje, takie jak przeglądanie kolekcje i kwerendy dokumentów, musisz jawnie zezwolić na korzystanie z dostępu do portalu Azure **zapory** w portalu. 

![Zrzut ekranu pokazujący sposób, aby umożliwić dostęp do portalu Azure](./media/firewall-support/azure-portal-firewall.png)

### <a name="sdk--rest-api"></a>Zestaw SDK & Rest API
Dla zabezpieczeń ze względu na dostęp za pomocą zestawu SDK lub interfejsu API REST z komputerów nie ma na liście dozwolonych zwróci ogólnego 404 odpowiedzi nie znaleziono z nie dodatkowych szczegółów. Sprawdź IP skonfigurowanych dla tego konta bazy danych Azure DB rozwiązania Cosmos listy dozwolonych, aby upewnić się, że konfiguracja prawidłowe zasady zostanie zastosowana do bazy danych Azure rozwiązania Cosmos konta bazy danych.

## <a name="next-steps"></a>Kolejne kroki
Informacje o porady dotyczące wydajności związane z siecią, zobacz [porady dotyczące wydajności](performance-tips.md).

