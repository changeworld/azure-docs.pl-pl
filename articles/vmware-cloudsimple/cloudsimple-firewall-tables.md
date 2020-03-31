---
title: Rozwiązanie Azure VMware by CloudSimple — tabele zapory
description: Dowiedz się więcej o tabelach zapory chmury prywatnej Cloud Cloud cloud cloud i regułach zapory.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025048"
---
# <a name="firewall-tables-overview"></a>Omówienie tabel zapory

Tabela zapory zawiera reguły filtrowania ruchu sieciowego do i z zasobów chmury prywatnej. Tabele zapory można zastosować do sieci VLAN/podsieci. Reguły kontrolują ruch sieciowy między siecią źródłową lub adresem IP a siecią docelową lub adresem IP.

## <a name="firewall-rules"></a>Reguły zapory

W poniższej tabeli opisano parametry reguły zapory.

| Właściwość | Szczegóły |
| ---------| --------|
| **Nazwa** | Nazwa, która jednoznacznie identyfikuje regułę zapory i jej przeznaczenie. |
| **Priorytet** | Liczba między 100 a 4096, przy czym 100 jest najwyższym priorytetem. Reguły są przetwarzane w kolejności priorytetu. Gdy ruch napotka regułę, przetwarzanie reguł zostanie zatrzymane. W rezultacie reguły o niższych priorytetach, które mają takie same atrybuty jak reguły o wyższych priorytetach, nie są przetwarzane.  Należy uważać, aby uniknąć sprzecznych zasad. |
| **Śledzenie stanu** | Śledzenie może być bezstanowe (Private Cloud, Internet lub VPN) lub stanowe (publiczny adres IP).  |
| **Protokół** | Opcje obejmują Any, TCP lub UDP. Jeśli potrzebujesz ICMP, użyj dowolnego. |
| **Kierunek** | Określa, czy ta reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego. |
| **Akcja** | Zezwalaj lub odrzucaj dla typu ruchu zdefiniowanego w regule. |
| **Źródła** | Adres IP, bezklasowy blok routingu między domenami (CIDR) (na przykład 10.0.0.0/24) lub Dowolny.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Port źródłowy** | Port, z którego pochodzi ruch sieciowy.  Można określić pojedynczy port lub zakres portów, takich jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Docelowy** | Adres IP, bezklasowy blok routingu między domenami (CIDR) (na przykład 10.0.0.0/24) lub Dowolny.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.  |
| **Port docelowy** | Port, do którego przepływa ruch sieciowy.  Można określić pojedynczy port lub zakres portów, takich jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.|

### <a name="stateless"></a>Bezstanowe

Reguła bezstanowa analizuje tylko pojedyncze pakiety i filtruje je na podstawie reguły.  
Dodatkowe reguły mogą być wymagane dla przepływu ruchu w odwrotnym kierunku.  Użyj reguł bezstanowych dla ruchu między następującymi punktami:

* Podsieci chmur prywatnych
* Podsieć lokalna i podsieć w chmurze prywatnej
* Ruch internetowy z prywatnych chmur

### <a name="stateful"></a>Stanowa

 Reguła stanowa jest świadoma połączeń, które przechodzą przez nią. Rekord przepływu tworzony jest dla istniejących połączeń. Komunikacja jest dozwolona lub zablokowana na podstawie stanu połączenia z rekordu przepływu.  Ten typ reguły służy do filtrowania ruchu z Internetu przez publiczne adresy IP.

### <a name="default-rules"></a>Reguły domyślne

W każdej tabeli zapory tworzone są następujące reguły domyślne.

|Priorytet|Nazwa|Śledzenie stanu|Kierunek|Typ ruchu|Protocol (Protokół)|Element źródłowy|Port źródłowy|Element docelowy|Port docelowy|Akcja|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Stanowa|Wychodzący|Publiczny ruch IP lub internetowy|Wszystkie|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|
|65001|odmówienia internetu|Stanowa|Przychodzący|Publiczny ruch IP lub internetowy|Wszystkie|Dowolne|Dowolne|Dowolne|Dowolne|Zablokuj|
|65002|zezwalaj na wszystko do intranetu|Bezstanowe|Wychodzący|Ruch wewnętrzny w chmurze prywatnej lub VPN|Wszystkie|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|
|65003|zezwalaj na wszystko z intranetu|Bezstanowe|Przychodzący|Ruch wewnętrzny w chmurze prywatnej lub VPN|Wszystkie|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie tabel i reguł zapory](firewall.md)
