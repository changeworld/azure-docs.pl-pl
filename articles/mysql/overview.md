---
title: Omówienie bazy danych Azure usługa relacyjnej bazy danych MySQL
description: Omówienie bazy danych Azure usługa relacyjnej bazy danych MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.custom: mvc
ms.openlocfilehash: 3b7ae08446e672c218e7278cff4f77739286fead
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-azure-database-for-mysql"></a>Co to jest Azure bazy danych MySQL?
Bazy danych platformy Azure dla programu MySQL to usługa relacyjnej bazy danych w chmurze firmy Microsoft, na podstawie [MySQL Community Edition](https://www.mysql.com/products/community/) aparatu bazy danych. Bazy danych platformy Azure dla programu MySQL zapewnia:

- Wbudowana wysoka dostępność bez dodatkowych kosztów
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- Skalowanie na bieżąco w ciągu kilku sekund
- Zabezpieczenia zapewniające ochronę przesyłanych oraz magazynowanych poufnych danych
- Automatyczne tworzenie kopii zapasowych i funkcja przywracania do punktu w czasie do 35 dni
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej

Te możliwości wymagają prawie nie administracji i wszystkie są udostępniane bez ponoszenia dodatkowych kosztów. Umożliwiają one skupić się na tworzenie aplikacji szybki i skraca czas na rynek zamiast przydzielania cenny czas i zasoby do zarządzania maszynami wirtualnymi i infrastruktury. Ponadto można utworzyć aplikację za pomocą narzędzi typu open source i platformy wybór dostarczać o szybkości i wydajności wymaga dane biznesowe, wszystko to bez konieczności rozwijania nowych umiejętności.

![Azure bazy danych MySQL diagram koncepcyjny](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ten artykuł zawiera wprowadzenie do bazy danych Azure dla programu MySQL podstawowych pojęć i funkcje dotyczące wydajności, skalowalności i możliwości zarządzania, wraz z łączami, aby poznać szczegóły. Zobacz te przewodniki Szybki start, aby rozpocząć pracę:
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure, zobacz:
- [Przykładów dla interfejsu wiersza polecenia platformy Azure dla bazy danych Azure dla programu MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund
Baza danych Azure dla usługi MySQL zapewnia kilka warstwy usług: Basic, ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Każda z warstw oferuje inną wydajność i różne możliwości w celu obsługi różnych obciążeń bazy danych, zarówno niewielkich, jak i ogromnych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz [Warstwy cenowe](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Jak podjąć decyzję o tym, kiedy należy regulować w górę i w dół? Możesz użyć wbudowanych wydajności monitorowania oraz alertów funkcji, z ocen wydajności oparte na vCores. Za pomocą tych narzędzi, można szybko ocenić wpływ vCores skalowania w górę lub w dół na podstawie Twoich potrzeb wydajności bieżącego lub planowane. Aby uzyskać szczegółowe informacje, zobacz [Alerty](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Zapewnienie działania aplikacji i firmy
Azure w branży 99,99% dostępności Umowa dotycząca poziomu usług (SLA), obsługiwane przez usługę globalnej sieci centrów danych zarządzany przez firmę Microsoft, pomaga zapewnić działanie aplikacji 24/7. Z każdym Azure bazy danych MySQL serwera możesz korzystać z zabezpieczeń, odporność na uszkodzenia i ochrony danych, w przeciwnym razie należy kupić lub projektowanie, tworzenie i zarządzanie. Z bazy danych Azure dla programu MySQL w momencie przywracania służy do odzyskania serwera do wcześniejszego stanu, nawet sprzed 35 dni.

## <a name="secure-your-data"></a>Zabezpieczanie danych
Usługi Azure bazy danych ma tradycję zabezpieczeń danych, który podtrzymuje bazy danych Azure dla programu MySQL, dzięki funkcjom ograniczania dostępu, ochrony danych na rest i w ruchu, które ułatwiają monitorowanie aktywności. Odwiedź [Centrum zaufania Azure](https://www.microsoft.com/en-us/trustcenter/security), aby uzyskać informacje o zabezpieczeniach platformy Azure.

Bazy danych Azure dla usługi MySQL używa szyfrowania magazynu dla danych na rest. Dane, w tym kopie zapasowe, są szyfrowane na dysku (z wyjątkiem plików tymczasowych, które są tworzone przez aparat podczas uruchamiania zapytań). Usługa korzysta z 256-bitowego szyfru AES zawartego w szyfrowaniu magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.

Domyślnie program Azure bazy danych dla usługi MySQL jest skonfigurowany do żądania [zabezpieczeń połączenia SSL](./concepts-ssl-connection-security.md) dla danych w ruchu w sieci. Wymuszanie połączenia SSL między serwerem bazy danych i aplikacji klienckich pomaga chronić przed atakami "man w środku" szyfrując strumienia danych między serwerem i aplikacji.  Opcjonalnie możesz wyłączyć wymaganie protokołu SSL dla połączeń z usługą bazy danych, jeśli Twoja aplikacja kliencka nie obsługuje łączności SSL.

## <a name="next-steps"></a>Kolejne kroki
Po przeczytaniu wprowadzenia do bazy danych platformy Azure dla programu MySQL i udzielenie odpowiedzi na pytanie "Co to jest Azure bazy danych na MySQL?", możesz przystąpić do:
- Zobacz stronę cen. koszt porównania i kalkulatory. [Cennik](https://azure.microsoft.com/pricing/details/mysql/)
- Rozpoczynanie pracy przez utworzenie pierwszego serwera. [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Tworzenie pierwszej aplikacji za pomocą preferowany język: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [przejdź](./connect-go.md)
