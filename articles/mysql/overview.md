---
title: Omówienie usługi Azure Database for usługa relacyjnej bazy danych MySQL
description: Omówienie usługi Azure Database for MySQL usługa relacyjnej bazy danych.
ms.service: mysql
author: ajlam
ms.author: andrela
ms.custom: mvc
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2852cab05fab8e15b7e60a22f54cc866d2f0f178
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226270"
---
# <a name="what-is-azure-database-for-mysql"></a>Co to jest Azure Database for MySQL?

Usługa Azure Database for MySQL to usługa relacyjnej bazy danych w chmurze firmy Microsoft, w oparciu o [MySQL Community Edition](https://www.mysql.com/products/community/) (dostępne w ramach licencji GPLv2) Aparat bazy danych, w wersji 5.6 i w wersji 5.7. Usługa Azure Database for MySQL oferuje:

- Wbudowana wysoka dostępność bez dodatkowych kosztów
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- Skalowanie zgodnie z potrzebami w ciągu kilku sekund.
- Zabezpieczenia zapewniające ochronę przesyłanych oraz magazynowanych poufnych danych
- Automatyczne tworzenie kopii zapasowych i funkcja przywracania do punktu w czasie do 35 dni
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Dzięki nim możesz skoncentrować się na szybkim tworzeniu aplikacji i skracaniu czasu wejścia na rynek, a nie na poświęcaniu cennego czasu i cennych zasobów na zarządzanie maszynami wirtualnymi i infrastrukturą. Ponadto możesz kontynuować tworzenie aplikacji przy użyciu tych samych wybranych przez siebie narzędzi typu open source oraz platformy w celu dostarczania zawartości z wymaganą szybkością i skutecznością bez konieczności uczenia się nowych umiejętności.

![Usługa Azure Database for MySQL — diagram koncepcyjny](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ten artykuł stanowi wprowadzenie do usługi Azure Database for MySQL — podstawowe pojęcia i funkcje dotyczące wydajności, skalowalności i możliwości zarządzania za pomocą łącza, aby poznać szczegóły. Zobacz te przewodniki Szybki start, aby rozpocząć pracę:

- [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure, zobacz:

- [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund
Usługa Azure Database for MySQL oferuje kilka warstwy usług: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowane pod kątem pamięci. Każda z warstw oferuje inną wydajność i różne możliwości w celu obsługi różnych obciążeń bazy danych, zarówno niewielkich, jak i ogromnych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz  [Warstwy cenowe](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Jak podjąć decyzję o tym, kiedy należy regulować w górę i w dół? Możesz użyć wbudowanych funkcji monitorowania wydajności i alertów w połączeniu z ocenami wydajności opartymi na rdzeniach wirtualnych. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania rdzeni wirtualnych w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Aby uzyskać szczegółowe informacje, zobacz [Alerty](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Zapewnienie działania aplikacji i firmy
Umowa dotycząca poziomu usług (SLA) o czołowej w branży dostępności 99,99% dla platformy Azure, która jest obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić działanie aplikacji przez 24 godziny na dobę, 7 dni w tygodniu. Z każdego serwera Azure Database for MySQL możesz skorzystać z wbudowanych rozwiązań zabezpieczeń, odporności na uszkodzenia i ochrony danych, które w przeciwnym razie trzeba byłoby kupić lub zaprojektować, tworzenia i zarządzania. Usługa Azure Database for MySQL — możesz użyć przywracania do punktu w czasie, aby odzyskać serwer do wcześniejszego stanu — nawet sprzed 35 dni.

## <a name="secure-your-data"></a>Zabezpieczanie danych
Usługi bazy danych platformy Azure mają tradycje zabezpieczeń danych, które przestrzega — Azure Database for MySQL, dzięki funkcjom ograniczania dostępu, ochrony danych magazynowanych i magazynowanych i ułatwiają monitorowanie aktywności. Odwiedź [Centrum zaufania Azure](https://www.microsoft.com/en-us/trustcenter/security), aby uzyskać informacje o zabezpieczeniach platformy Azure.

Usługa Azure Database for MySQL — używa szyfrowania magazynu dla danych magazynowanych i jest zgodne ze standardem FIPS 140-2. Dane, w tym kopie zapasowe, są szyfrowane na dysku (z wyjątkiem plików tymczasowych utworzonych przez aparat podczas uruchamiania zapytań). Usługa korzysta z 256-bitowego szyfru AES zawartego w szyfrowaniu magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.

Domyślnie usługa Azure Database for MySQL jest skonfigurowany do żądania [zabezpieczenia połączenia SSL](./concepts-ssl-connection-security.md) dla danych magazynowanych za pośrednictwem sieci. Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją. Opcjonalnie możesz wyłączyć wymaganie protokołu SSL dla połączeń z usługą bazy danych, jeśli Twoja aplikacja kliencka nie obsługuje łączności SSL.

## <a name="contacts"></a>Kontakty
W przypadku jakichkolwiek pytań lub sugestie, które mogą się pojawić w pracę z usługą Azure Database for MySQL, Wyślij wiadomość e-mail do usługi Azure Database dla MySQL zespołu ([ @Ask Azure DB dla MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Należy pamiętać, że nie jest to alias pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Kolejne kroki
Skoro już przeczytaj wprowadzenie do usługi Azure Database for MySQL i udzielenie odpowiedzi na pytanie "Co to jest Azure Database dla MySQL?", możesz przystąpić do:

- Odwiedzenie strony cennika zawierającej porównania kosztów i kalkulatory. [Cennik](https://azure.microsoft.com/pricing/details/mysql/)
- Rozpoczęcie pracy przez utworzenie pierwszego serwera. [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Tworzenie pierwszej aplikacji przy użyciu preferowanego języka: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [przejdź](./connect-go.md)
