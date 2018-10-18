---
title: Omówienie usługi relacyjnej bazy danych Azure Database for MariaDB
description: Omówienie usługi relacyjnej bazy danych Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 3649a173d5707179ca8547a8169b7d308c4f7f1c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249166"
---
# <a name="what-is-azure-database-for-mariadb"></a>Czym jest usługa Azure Database for MariaDB?
Usługa Azure Database for MariaDB jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na aparacie bazy danych [MariaDB Community Edition](https://mariadb.org/download/). Ta usługa jest dostępna w publicznej wersji zapoznawczej. Usługa Azure Database for MariaDB udostępnia następujące funkcje:

- Wbudowana wysoka dostępność bez dodatkowych kosztów
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- Skalowanie zgodnie z potrzebami w ciągu kilku sekund.
- Zabezpieczenia zapewniające ochronę przesyłanych oraz magazynowanych poufnych danych
- Automatyczne tworzenie kopii zapasowych i funkcja przywracania do punktu w czasie do 35 dni
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Dzięki nim możesz skoncentrować się na szybkim tworzeniu aplikacji i skracaniu czasu wejścia na rynek, a nie na poświęcaniu cennego czasu i cennych zasobów na zarządzanie maszynami wirtualnymi i infrastrukturą. Ponadto możesz kontynuować tworzenie aplikacji przy użyciu tych samych wybranych przez siebie narzędzi typu open source oraz platformy w celu dostarczania zawartości z wymaganą szybkością i skutecznością bez konieczności uczenia się nowych umiejętności.

Ten artykuł obejmuje wprowadzenie do podstawowych pojęć i funkcji usługi Azure Database for MariaDB związanych z wydajnością, skalowalnością i możliwościami zarządzania, a także zawiera linki do szczegółowych informacji. Zobacz te przewodniki Szybki start, aby rozpocząć pracę:
- [Tworzenie serwera usługi Azure Database for MariaDB za pomocą witryny Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Tworzenie serwera usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund
W wersji zapoznawczej usługa Azure Database for MariaDB oferuje kilka warstw usług: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowana pod kątem pamięci. Każda z warstw oferuje inną wydajność i różne możliwości w celu obsługi różnych obciążeń bazy danych, zarówno niewielkich, jak i ogromnych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz [Warstwy cenowe](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Jak podjąć decyzję o tym, kiedy należy regulować w górę i w dół? Możesz użyć wbudowanych funkcji monitorowania wydajności i alertów w połączeniu z ocenami wydajności opartymi na rdzeniach wirtualnych. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania rdzeni wirtualnych w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Zapewnienie działania aplikacji i firmy
Umowa dotycząca poziomu usług (SLA) o czołowej w branży dostępności 99,99% dla platformy Azure (nieoferowana w publicznej wersji zapoznawczej), która jest obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić działanie aplikacji przez 24 godziny na dobę, 7 dni w tygodniu. Na każdym serwerze usługi Azure Database for MariaDB korzystasz z wbudowanych rozwiązań zabezpieczeń, odporności na uszkodzenia i ochrony danych, które w innej sytuacji trzeba byłoby kupić lub zaprojektować, zrealizować, a także nimi zarządzać. Dzięki usłudze Azure Database for MariaDB możesz użyć przywracania do punktu w czasie, aby odzyskać wcześniejszy stan serwera — nawet sprzed 35 dni.

## <a name="secure-your-data"></a>Zabezpieczanie danych
Usługi baz danych platformy Azure mają tradycje zabezpieczeń danych, które usługa Azure Database for MariaDB podtrzymuje dzięki funkcjom ograniczania dostępu, ochrony przesyłanych i magazynowanych danych oraz monitorowania aktywności. Odwiedź [Centrum zaufania Azure](https://www.microsoft.com/en-us/trustcenter/security), aby uzyskać informacje o zabezpieczeniach platformy Azure.

Usługa Azure Database for MariaDB używa szyfrowania magazynu dla magazynowanych danych. Dane, w tym kopie zapasowe, są szyfrowane na dysku (z wyjątkiem plików tymczasowych utworzonych przez aparat podczas uruchamiania zapytań). Usługa korzysta z 256-bitowego szyfru AES zawartego w szyfrowaniu magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.

W domyślnej konfiguracji usługa Azure Database for MariaDB wymaga [zabezpieczeń połączenia SSL](./concepts-ssl-connection-security.md) dla danych przesyłanych po sieci. Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją. Opcjonalnie możesz wyłączyć wymaganie protokołu SSL dla połączeń z usługą bazy danych, jeśli Twoja aplikacja kliencka nie obsługuje łączności SSL.

## <a name="contacts"></a>Kontakty
W przypadku pytań lub sugestii dotyczących pracy z usługą Azure Database for MariaDB prosimy o wysłanie wiadomości e-mail do zespołu ds. usługi Azure Database for MariaDB ([@Ask Azure DB for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)). Należy pamiętać, że nie jest to alias pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:
- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Następne kroki
Po przeczytaniu wprowadzenia do usługi Azure Database for MariaDB i zapoznaniu się z nią możesz wykonać następujące czynności:
- Odwiedzenie strony cennika zawierającej porównania kosztów i kalkulatory. [Cennik](https://azure.microsoft.com/pricing/details/mariadb/)
- Rozpoczęcie pracy przez utworzenie pierwszego serwera. [Tworzenie serwera usługi Azure Database for MariaDB za pomocą witryny Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
