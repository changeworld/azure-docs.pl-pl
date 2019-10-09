---
title: Omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL
description: Zawiera omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177962"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co to jest Azure Database for PostgreSQL?
Azure Database for PostgreSQL to usługa relacyjnej bazy danych w chmurze firmy Microsoft skompilowanej dla deweloperów. Jest on oparty na wersji społecznościowej aparatu bazy danych [PostgreSQL](https://www.postgresql.org/) typu open source i jest dostępny w dwóch opcjach wdrażania: Single Server i (wersja zapoznawcza).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL — pojedynczy serwer
Opcja wdrożenia pojedynczego serwera oferuje następujące funkcje:

- Wbudowana wysoka dostępność bez dodatkowych kosztów (99,99% umowy SLA)
- Przewidywalna wydajność przy użyciu cen z płatnością zgodnie z rzeczywistym użyciem
- Skalowanie w pionie w ciągu kilku sekund
- Monitorowanie i alerty pozwalające szybko ocenić wpływ skalowania
- Zabezpieczanie w celu ochrony poufnych danych w czasie spoczynku i ruchowo
- Automatyczne kopie zapasowe i przywracanie do punktu w czasie przez maksymalnie 35 dni
- Bezpieczeństwo i zgodność klasy korporacyjnej

Wszystkie te możliwości wymagają niemal braku administracji, a wszystkie są udostępniane bez dodatkowych kosztów. Umożliwiają one skoncentrowanie się na szybkim tworzeniu aplikacji i skróceniu czasu wprowadzenia na rynek, a nie poświęcają zbyt wiele czasu na zarządzanie maszynami wirtualnymi i infrastrukturą. Możesz kontynuować opracowywanie aplikacji przy użyciu wybranych przez siebie narzędzi typu "open source", bez konieczności uczenia się nowych umiejętności.

Opcja wdrożenia pojedynczego serwera oferuje trzy warstwy cenowe: podstawowe, Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Każda warstwa oferuje różne możliwości zasobów do obsługi obciążeń bazy danych. Możesz utworzyć swoją pierwszą aplikację na małej bazie danych za kilka dolarów miesięcznie, a następnie dostosować skalę, aby spełniała potrzeby rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste reagowanie na szybko zmieniające się wymagania dotyczące zasobów. Płacisz tylko za potrzebne zasoby i tylko wtedy, gdy ich potrzebujesz. Aby uzyskać szczegółowe informacje, zobacz [warstwy cenowe](concepts-pricing-tiers.md) .

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL-Citus (wersja zapoznawcza)
Opcja Citus) w poziomie skaluje zapytania na wielu maszynach przy użyciu fragmentowania. Aparat zapytań parallelizes przychodzące zapytania SQL między tymi serwerami w celu przyspieszenia odpowiedzi w dużych zestawach danych. Obsługuje ona aplikacje, które wymagają większej skali i wydajności, zazwyczaj obciążeń, które zbliżają się do--lub już przekraczają--100 GB danych.

Opcja wdrożenia Citus (preskaling) oferuje następujące funkcje:

- Skalowanie w poziomie na wielu maszynach przy użyciu fragmentowania
- Przetwarzanie równoległe zapytania na tych serwerach w celu uzyskania szybszych odpowiedzi w dużych zestawach danych
- Doskonała pomoc techniczna dla aplikacji wielodostępnych, analizy operacyjnej w czasie rzeczywistym oraz obciążeń transakcyjnych o wysokiej przepływności

Aplikacje skompilowane dla PostgreSQL mogą uruchamiać zapytania rozproszone na potrzeby tworzenia i skalowania (Citus) przy użyciu standardowych [bibliotek połączeń](./concepts-connection-libraries.md) i minimalnych zmian.

Należy pamiętać, że funkcja Citus jest dostępna w publicznej wersji zapoznawczej, w związku z czym nie oferuje jeszcze umowy SLA.

## <a name="data-security"></a>Bezpieczeństwo danych
Azure Database for PostgreSQL utrzymuje tradycję zabezpieczeń danych usług Azure Database Services. Zawiera funkcje, które ograniczają dostęp, chronią i obniżają dane oraz ułatwiają monitorowanie aktywności. Odwiedź [Centrum zaufania Azure](https://azure.microsoft.com/overview/trusted-cloud/) , aby uzyskać informacje o zabezpieczeniach platformy platformy Azure.

Usługa Azure Database for PostgreSQL używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu danych w spoczynku. Dane, w tym kopie zapasowe, są szyfrowane na dysku z wyjątkiem plików tymczasowych tworzonych podczas wykonywania zapytań. Usługa używa szyfru AES 256-bit zawartego w szyfrowaniu usługi Azure Storage, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć. Domyślnie usługa Azure Database for PostgreSQL wymaga bezpiecznego połączenia danych w ruchu między sieciami i między bazą danych a aplikacją kliencką.

## <a name="contacts"></a>Kontakty
Aby dowiedzieć się więcej na temat pytań lub sugestii dotyczących pracy z Azure Database for PostgreSQL, Wyślij wiadomość e-mail do zespołu Azure Database for PostgreSQL ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ten adres jest przeznaczony dla ogólnych pytań zamiast biletów pomocy technicznej.

Ponadto należy wziąć pod uwagę następujące kwestie dotyczące kontaktu:
- Aby skontaktować się z pomocą techniczną platformy Azure lub rozwiązać problem związany z Twoim kontem, należy wysłać [bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby przekazać opinię lub zażądać nowych funkcji, Utwórz wpis za pośrednictwem usługi [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Następne kroki
- Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/postgresql/) , aby obporównania kosztów i kalkulatory.
- Zacznij od utworzenia pierwszej Azure Database for PostgreSQL [pojedynczego serwera](./quickstart-create-server-database-portal.md) lub [przedskalowania (Citus) (wersja zapoznawcza)](./quickstart-create-hyperscale-portal.md)
- Tworzenie pierwszej aplikacji w języku Python, PHP, Ruby, C @ no__t-0, Java, Node. js: [biblioteki połączeń](./concepts-connection-libraries.md)
