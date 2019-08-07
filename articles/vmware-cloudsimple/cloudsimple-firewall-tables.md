---
title: Tabele zapory — rozwiązanie VMware według CloudSimple — Azure
description: Dowiedz się więcej na temat tabel i reguł zapory chmury prywatnej CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d25aa9252f061cee7f4cffdca42f00d84f719a3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812667"
---
# <a name="firewall-tables-overview"></a>Tabele zapory — Omówienie

W tabeli zapory są wyświetlane reguły filtrowania ruchu sieciowego do i z zasobów w chmurze prywatnej. Można je zastosować do sieci VLAN lub podsieci. Reguły kontrolują ruch sieciowy między siecią źródłową lub adresem IP oraz siecią docelową lub adresem IP.

## <a name="firewall-rules"></a>Reguły zapory

W poniższej tabeli opisano parametry w regule zapory.

| Właściwość | Szczegóły |
| ---------| --------|
| **Nazwa** | Nazwa, która jednoznacznie identyfikuje regułę zapory i jej przeznaczenie. |
| **Priorytet** | Liczba z zakresu od 100 do 4096, z 100 jest najwyższy priorytet. Reguły są przetwarzane w kolejności priorytetów. Gdy ruch jest zgodny z regułą, przetwarzanie reguł zostanie zatrzymane. W związku z tym wszystkie reguły istniejące z niższymi priorytetami, które mają takie same atrybuty jak reguły o wyższych priorytetach nie są przetwarzane.  Należy zachować ostrożność, aby uniknąć reguł powodujących konflikt. |
| **Śledzenie stanu** | Śledzenie może być bezstanowe (Chmura prywatna, Internet lub sieć VPN) lub stanowa (Public IP).  |
| **Protokół** | Dostępne opcje to TCP lub UDP. Jeśli jest wymagany protokół ICMP, użyj dowolnego z nich. |
| **Kierunek** | Określa, czy ta reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego. |
| **Akcja** | Zezwalaj lub Odmów dla typu ruchu zdefiniowanego w regule. |
| **Element źródłowy** | Adres IP, bezklasowy blok routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Port źródłowy** | Port, z którego pochodzi ruch sieciowy.  Można określić pojedynczy port lub zakres portów, taki jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Punktu** | Adres IP, bezklasowy blok routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.  |
| **Port docelowy** | Port, do którego przepływy ruchu sieciowego.  Można określić pojedynczy port lub zakres portów, taki jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.|

### <a name="stateless"></a>Bezstanowe

Reguła bezstanowa sprawdza tylko pojedyncze pakiety i filtruje je na podstawie reguły.  
Do przepływu ruchu w odwrotnym kierunku mogą być wymagane dodatkowe reguły.  Użyj reguł bezstanowych dla ruchu między następującymi punktami:

* Podsieci chmur prywatnych
* Podsieć lokalna i podsieć chmury prywatnej
* Ruch internetowy z chmur prywatnych

### <a name="stateful"></a>Stanowych

 Reguła stanowa ma świadomość połączeń, które przechodzą przez nią. Rekord przepływu tworzony jest dla istniejących połączeń. Komunikacja jest dozwolona lub zablokowana na podstawie stanu połączenia z rekordu przepływu.  Użyj tego typu reguły dla publicznych adresów IP, aby filtrować ruch z Internetu.

### <a name="default-rules"></a>Reguły domyślne

W każdej tabeli zapory są tworzone następujące reguły domyślne.

|Priority|Name (Nazwa)|Śledzenie stanu|Direction|Typ ruchu|Protocol|Source|Port źródłowy|Element docelowy|Port docelowy|Action|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Stanowych|Wychodzące|Publiczny adres IP lub ruch internetowy|Wszyscy|Any|Any|Any|Any|Allow|
|65001|Odmów — wszystko-z Internetu|Stanowych|Przychodzący|Publiczny adres IP lub ruch internetowy|Wszyscy|Any|Any|Any|Any|Zablokuj|
|65002|Zezwól na dostęp do sieci intranet|Bezstanowe|Wychodzące|Ruch wewnętrzny lub sieci VPN w chmurze prywatnej|Wszyscy|Any|Any|Any|Any|Allow|
|65003|allow-all-from-intranet|Bezstanowe|Przychodzący|Ruch wewnętrzny lub sieci VPN w chmurze prywatnej|Wszyscy|Any|Any|Any|Any|Allow|

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie tabel i reguł zapory](https://docs.azure.cloudsimple.com/firewall/)