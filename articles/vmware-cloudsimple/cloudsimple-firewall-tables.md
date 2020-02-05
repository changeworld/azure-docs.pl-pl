---
title: Azure VMware Solutions (Automatyczna synchronizacja) — tabele zapory
description: Dowiedz się więcej o automatycznej synchronizacji tabel zapory chmury prywatnej i reguł zapory.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 631ccb33e14586edce71752826f134c1e50819c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025048"
---
# <a name="firewall-tables-overview"></a>Tabele zapory — Omówienie

W tabeli zapory są wyświetlane reguły filtrowania ruchu sieciowego do i z usługi reautomatyczna synchronizacja zasobów w chmurze prywatnej. Tabele zapory można stosować do sieci VLAN/podsieci. Reguły kontrolują ruch sieciowy między siecią źródłową lub adresem IP a siecią docelową lub adresem IP.

## <a name="firewall-rules"></a>Reguły zapory

W poniższej tabeli opisano parametry w regule zapory.

| Właściwość | Szczegóły |
| ---------| --------|
| **Nazwa** | Nazwa, która jednoznacznie identyfikuje regułę zapory i jej przeznaczenie. |
| **Priorytet** | Liczba z zakresu od 100 do 4096, z 100 jest najwyższy priorytet. Reguły są przetwarzane w kolejności priorytetów. Gdy ruch napotyka zgodność z regułą, przetwarzanie reguł zostanie zatrzymane. W efekcie reguły o niższych priorytetach, które mają te same atrybuty co reguły o wyższych priorytetach nie są przetwarzane. Należy zachować ostrożność, aby uniknąć reguł powodujących konflikt. |
| **Śledzenie stanu** | Śledzenie może być bezstanowe (Automatyczna synchronizacja z chmurą prywatną, Internet lub sieć VPN) lub stanowe (publiczny adres IP).  |
| **Protokół** | Dostępne opcje to TCP lub UDP. Jeśli jest wymagany protokół ICMP, użyj dowolnego z nich. |
| **Kierunek** | Określa, czy ta reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego. |
| **Akcja** | Zezwalaj lub Odmów dla typu ruchu zdefiniowanego w regule. |
| **Element źródłowy** | Adres IP, bezklasowy blok routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny adres IP.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Port źródłowy** | Port, z którego pochodzi ruch sieciowy.  Można określić pojedynczy port lub zakres portów, taki jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Punktu** | Adres IP, bezklasowy blok routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny adres IP.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.  |
| **Port docelowy** | Port, do którego przepływy ruchu sieciowego.  Można określić pojedynczy port lub zakres portów, taki jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.|

### <a name="stateless"></a>Bezstanowe

Reguła bezstanowa sprawdza tylko pojedyncze pakiety i filtruje je na podstawie reguły. Do przepływu ruchu w odwrotnym kierunku mogą być wymagane dodatkowe reguły. Użyj reguł bezstanowych dla ruchu między następującymi punktami:

* Podsieci chmur prywatnych
* Podsieć lokalna i automatyczna synchronizacja chmury prywatnej
* Ruch internetowy z chmur prywatnych w ramach automatycznej synchronizacji

### <a name="stateful"></a>Stanowych

 Reguła stanowa ma świadomość połączeń, które przechodzą przez nią. Rekord przepływu tworzony jest dla istniejących połączeń. Komunikacja jest dozwolona lub zablokowana na podstawie stanu połączenia z rekordu przepływu. Użyj tego typu reguły dla publicznych adresów IP, aby filtrować ruch z Internetu.

### <a name="default-rules"></a>Reguły domyślne

W każdej tabeli zapory są tworzone następujące reguły domyślne.

|Priorytet|Nazwa|Śledzenie stanu|Kierunek|Typ ruchu|Protocol (Protokół)|Źródło|Port źródłowy|Cel|Port docelowy|Działanie|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Stanowych|Wychodzące|Publiczny adres IP lub ruch internetowy|Wszystko|Dowolne|Dowolne|Dowolne|Dowolne|Zezwól|
|65001|Odmów — wszystko-z Internetu|Stanowych|Przychodzące|Publiczny adres IP lub ruch internetowy|Wszystko|Dowolne|Dowolne|Dowolne|Dowolne|Odmów|
|65002|Zezwól na dostęp do sieci intranet|Bezstanowe|Wychodzące|Automatyczna synchronizacja w chmurze prywatnej ruchu wewnętrznego lub sieci VPN|Wszystko|Dowolne|Dowolne|Dowolne|Dowolne|Zezwól|
|65003|allow-all-from-intranet|Bezstanowe|Przychodzące|Automatyczna synchronizacja w chmurze prywatnej ruchu wewnętrznego lub sieci VPN|Wszystko|Dowolne|Dowolne|Dowolne|Dowolne|Zezwól|

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie tabel i reguł zapory](firewall.md)
