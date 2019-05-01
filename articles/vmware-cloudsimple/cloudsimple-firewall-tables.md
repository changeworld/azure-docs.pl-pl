---
title: Zapory tabele — VMware Solution by CloudSimple - Azure
description: Więcej informacji na temat CloudSimple chmury prywatnej zapory tabel i reguły zapory.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577348"
---
# <a name="firewall-tables-overview"></a>Omówienie tabel zapory

Zapora tabeli wymieniono reguły do filtrowania ruchu sieciowego do i z zasobów w chmurze prywatnej. Można je zastosować do podsieci lub sieci VLAN. Reguły następnie kontrolować ruch sieciowy między źródłowej sieci lub adres IP i sieci docelowej lub adres IP.

## <a name="firewall-rules"></a>Reguły zapory

W poniższej tabeli opisano parametry w regule zapory.

| Właściwość | Szczegóły |
| ---------| --------|
| **Nazwa** | Nazwa, która jednoznacznie identyfikuje reguły zapory i jej przeznaczenie. |
| **Priorytet** | Liczbą z zakresu od 100 do 4096, mając 100 oznacza najwyższy priorytet. Reguły są przetwarzane w kolejności priorytetów. Kiedy ruch napotka reguły dopasowania, zatrzymuje przetwarzanie reguł. W rezultacie nie są przetwarzane wszystkie istniejące reguły o niższych priorytetach, które mają takie same atrybuty jak reguły o wyższych priorytetach.  Należy zadbać, aby uniknąć konfliktu reguł. |
| **Śledzenie stanu** | Śledzenia może być bezstanowe (Chmura prywatna, Internetu lub sieci VPN) lub stanowa (publiczny adres IP).  |
| **Protokół** | Opcje obejmują Any, TCP lub UDP. Jeśli potrzebujesz protokołu ICMP, należy użyć dowolnego. |
| **Kierunek** | Określa, czy ta reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego. |
| **Akcja** | Zezwalaj lub Odmów dla typu ruchu zdefiniowane w regule. |
| **Element źródłowy** | Adres IP, blok bezklasowego routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny.  Określania zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Port źródłowy** | Port z sieci, w której pochodzą dane.  Można określić pojedynczy port lub zakres portów, na przykład 443 lub nebo 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **miejsce docelowe** | Adres IP, blok bezklasowego routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny.  Określania zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.  |
| **Port docelowy** | Port, z którą przepływa ruch sieciowy.  Można określić pojedynczy port lub zakres portów, na przykład 443 lub nebo 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.|

### <a name="stateless"></a>Bezstanowe

Bezstanowe regułę porównaniem ich indywidualnych pakietów i filtruje je zgodnie z regułą.  
Dodatkowe zasady mogą być wymagane dla przepływu ruchu w odwrotnym kierunku.  Użyj bezstanowych reguł dla ruchu między następujące kwestie:

* Podsieci chmur prywatnych
* Lokalne podsieci i podsieć Chmura prywatna
* Ruchu internetowego z Chmurami prywatnymi

### <a name="stateful"></a>Stanowe

 Reguła stanowa zapoznała połączeń, które przechodzą przez go. Rekord przepływu tworzony jest dla istniejących połączeń. Komunikacja jest dozwolona lub zablokowana na podstawie stanu połączenia z rekordu przepływu.  Ta reguła służy do publicznych adresów IP do filtrowania ruchu z Internetu.

### <a name="default-rules"></a>Reguły domyślne

Następujące reguły domyślne są tworzone w każdej tabeli zapory.

|Priorytet|Name (Nazwa)|Śledzenie stanu|Direction|Typ ruchu|Protokół|Element źródłowy|Port źródłowy|Element docelowy|Port docelowy|Akcja|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Stanowe|Wychodzący|Ruch publiczny adres IP lub internet|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|
|65001|deny-all-from-internet|Stanowe|Przychodzący|Ruch publiczny adres IP lub internet|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zablokuj|
|65002|allow-all-to-intranet|Bezstanowe|Wychodzący|Chmura prywatna wewnętrznego lub ruchu sieci VPN|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|
|65003|allow-all-from-intranet|Bezstanowe|Przychodzący|Chmura prywatna wewnętrznego lub ruchu sieci VPN|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie zapory tabel i reguł](https://docs.azure.cloudsimple.com/firewall/)