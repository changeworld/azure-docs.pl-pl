---
title: Omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL
description: Zawiera omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/14/2018
ms.openlocfilehash: 318778a83c82b0ddb88f8bbd852442ab389fedb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680322"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co to jest usługa Azure Database for PostgreSQL?

Usługa Azure Database for PostgreSQL jest usługą relacyjnej bazy danych w chmurze firmy Microsoft utworzoną dla deweloperów i opartą na wersji społecznościowej aparatu bazy danych [PostgreSQL](https://www.postgresql.org/) typu open source w wersjach 9.5, 9.6 i 10. Zalety Azure Database for PostgreSQL:

- Wbudowana wysoka dostępność bez dodatkowych kosztów
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- Skalowanie zgodnie z potrzebami w ciągu kilku sekund
- Zabezpieczenia zapewniające ochronę przesyłanych oraz magazynowanych poufnych danych
- Automatyczne tworzenie kopii zapasowych i funkcja przywracania do punktu w czasie do 35 dni
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej

Wszystkie powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Te możliwości pozwalają skoncentrować się na szybkim tworzeniu aplikacji i skracaniu czasu wejścia na rynek, a nie na poświęcaniu cennego czasu i cennych zasobów na zarządzanie maszynami wirtualnymi i infrastrukturą. Ponadto możesz kontynuować tworzenie aplikacji przy użyciu tych samych wybranych przez siebie narzędzi typu open source oraz platformy i dostarczać zawartość z wymaganą szybkością i skutecznością bez konieczności uczenia się nowych umiejętności. 

Ten artykuł obejmuje wprowadzenie do podstawowych pojęć i funkcji usługi Azure Database for PostgreSQL związanych z wydajnością, skalowalnością i możliwościami zarządzania. Zobacz te przewodniki Szybki start, aby rozpocząć pracę:

- [Tworzenie serwera usługi Azure Database for PostgreSQL za pomocą witryny Azure Portal](quickstart-create-server-database-portal.md)
- [Tworzenie serwera usługi Azure Database for PostgreSQL za pomocą interfejsu wiersza polecenia platformy Azure](quickstart-create-server-database-azure-cli.md)

Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure, zobacz:

- [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund
Usługa Azure Database for PostgreSQL oferuje trzy warstwy cenowe: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowane pod kątem pamięci. Każda warstwa oferuje inne możliwości w zakresie zasobów, aby wspierać obciążenia bazy danych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz  [Warstwy cenowe](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Jak podjąć decyzję o tym, kiedy należy regulować w górę lub w dół? Należy użyć wbudowanych funkcji monitorowania i zgłaszania alertów platformy Azure. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności bądź pamięci. Aby uzyskać szczegółowe informacje, zobacz [Alerty](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Zapewnienie działania aplikacji i firmy
Umowa dotycząca poziomu usług (SLA) o czołowej w branży dostępności 99,99% dla platformy Azure, która jest obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić działanie aplikacji przez 24 godziny na dobę, 7 dni w tygodniu. Na każdym serwerze usługi Azure Database for PostgreSQL korzystasz z wbudowanych rozwiązań zabezpieczeń, odporności na uszkodzenia i ochrony danych, które w innej sytuacji trzeba byłoby kupić lub zaprojektować, zrealizować, a także nimi zarządzać. W usłudze Azure Database for PostgreSQL każda warstwa cenowa oferuje kompleksowy zestaw funkcji i opcji ciągłości działania, których można użyć, aby rozpocząć i kontynuować pracę. Możesz użyć [przywracania do punktu w czasie](howto-restore-server-portal.md), aby przywrócić bazę danych do wcześniejszego stanu — nawet sprzed 35 dni. Ponadto, jeśli centrum danych hostujące bazy danych ulegnie awarii, możesz przywrócić bazy danych z geograficznie nadmiarowych duplikatów najnowszych kopii zapasowych.

## <a name="secure-your-data"></a>Zabezpieczanie danych
Usługi baz danych platformy Azure mają tradycje zabezpieczeń danych, które usługa Azure Database for PostgreSQL podtrzymuje dzięki funkcjom ograniczania dostępu, ochrony przesyłanych i magazynowanych danych oraz monitorowania aktywności. Odwiedź [Centrum zaufania Azure](https://azure.microsoft.com/overview/trusted-cloud/), aby uzyskać informacje o zabezpieczeniach platformy Azure.

Usługa Azure Database for PostgreSQL używa szyfrowania magazynu dla magazynowanych danych i jest zgodna ze standardem FIPS 140-2. Dane, w tym kopie zapasowe, są szyfrowane na dysku (z wyjątkiem plików tymczasowych utworzonych przez aparat podczas uruchamiania zapytań). Usługa korzysta z 256-bitowego szyfru AES zawartego w szyfrowaniu magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.

W domyślnej konfiguracji usługa Azure Database for PostgreSQL wymaga [zabezpieczeń połączenia SSL](./concepts-ssl-connection-security.md) dla danych przesyłanych po sieci. Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją. Opcjonalnie możesz wyłączyć wymaganie protokołu SSL dla połączeń z usługą bazy danych, jeśli Twoja aplikacja kliencka nie obsługuje łączności SSL.

## <a name="contacts"></a>Kontakty
W przypadku pytań lub sugestii dotyczących pracy z usługą Azure Database for PostgreSQL prosimy o wysłanie wiadomości e-mail do zespołu ds. usługi Azure Database for PostgreSQL ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Należy pamiętać, że nie jest to alias pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:
- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Kolejne kroki
- Odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/postgresql/) zawierającą porównania kosztów i kalkulatory.
- Rozpocznij od [utworzenia pierwszej usługi Azure Database for PostgreSQL](./quickstart-create-server-database-portal.md).
- Skompiluj pierwszą aplikację w języku Python, PHP, Ruby, C\#, Java, Node.js: [Biblioteki połączeń](./concepts-connection-libraries.md)
