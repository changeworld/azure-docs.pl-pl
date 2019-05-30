---
title: Omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL
description: Zawiera omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: f4023fa84215a0319669de0d812d8306b62278e3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073271"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co to jest usługa Azure Database for PostgreSQL?
Usługa Azure Database for PostgreSQL jest usługą relacyjnej bazy danych w chmurze firmy Microsoft utworzoną dla deweloperów. Jest on oparty na wersji społecznościowej typu open source [PostgreSQL](https://www.postgresql.org/) aparat bazy danych i jest dostępny w dwie opcje wdrożenia: Pojedynczy serwer i na dużą skalę (Citus) (wersja zapoznawcza).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL — pojedynczy serwer
Opcję wdrożenia pojedynczego serwera, która zapewnia:

- Wbudowana wysoka dostępność bez dodatkowych kosztów (99,99% SLA)
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- Skalowanie pionowe zgodnie z potrzebami w ciągu kilku sekund
- Monitorowanie i alerty, aby szybko ocenić wpływ skalowania
- Zabezpieczenia zapewniające ochronę przesyłanych oraz magazynowanych poufnych danych
- Automatyczne tworzenie kopii zapasowych i funkcja przywracania do punktu w czasie do 35 dni
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej

Wszystkie powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Umożliwiają one pozwala skupić się na szybkie opracowywanie aplikacji i skracaniu czasu wejścia na rynek, a nie trzeba spędzać całych oszczędzi cenny czas i zasoby do zarządzania maszynami wirtualnymi i infrastrukturą. Można kontynuować do tworzenia aplikacji przy użyciu narzędzi typu open source i dowolnie wybranej platformie bez konieczności uczenia się nowych umiejętności.

Opcję wdrożenia pojedynczego serwera, która oferuje trzy warstwy cenowe: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowane pod kątem pamięci. Każda warstwa oferuje inne możliwości w zakresie zasobów, aby wspierać obciążenia bazy danych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz  [Warstwy cenowe](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)
Opcja (Citus) na dużą skalę w poziomie skaluje zapytania na wielu komputerach za pomocą fragmentowania. Silnik zapytania parallelizes przychodzące zapytania SQL na tych serwerach szybszych odpowiedzi na dużych zestawach danych. Służy ona aplikacje, które wymagają większej skalowalności i wydajności, zazwyczaj obciążeń, które są zbliża się do — lub już przekracza — 100 GB danych.

Opcja wdrażania w Hiperskali (Citus) zapewnia:

- Skalowanie na wiele komputerów za pomocą dzielenia na fragmenty w poziomie
- Przetwarzania równoległego zapytań na tych serwerach szybszych odpowiedzi na dużych zestawach danych
- Niezrównane możliwości obsługi dla aplikacji wielodostępnych, analiza operacyjna w czasie rzeczywistym i obciążeniami transakcyjnymi wysokiej przepływności

Aplikacje opracowane dla PostgreSQL można uruchamiania rozproszonych zapytań w Hiperskali (Citus) ze standardem [biblioteki połączeń](./concepts-connection-libraries.md) i minimalnych zmianach.

Należy zauważyć, że na dużą skalę (Citus) w publicznej wersji zapoznawczej i w związku z tym jeszcze nie oferuje umowy SLA.

## <a name="data-security"></a>Bezpieczeństwo danych
Azure Database for PostgreSQL podtrzymuje usługi Azure database tradycje zabezpieczeń danych. Ma funkcje, które ograniczania dostępu, ochrony danych magazynowanych i magazynowanych i monitorowania aktywności. Odwiedź [Centrum zaufania Azure](https://azure.microsoft.com/overview/trusted-cloud/), aby uzyskać informacje o zabezpieczeniach platformy Azure.

Usługa Azure Database for PostgreSQL używa szyfrowania magazynu dla magazynowanych danych i jest zgodna ze standardem FIPS 140-2. Dane, w tym kopie zapasowe, są szyfrowane na dysku. Usługa korzysta z szyfrowania AES 256-bitowy, zawarte w szyfrowaniu magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć. Domyślnie usługa Azure Database for postgresql w warstwie wymaga bezpiecznych połączeń dla danych magazynowanych, zarówno w sieci, jak i między aplikacją bazy danych i klientów.

## <a name="contacts"></a>Kontakty
Przypadku jakichkolwiek pytań lub sugestie dotyczące pracy z usługą Azure Database for PostgreSQL, Wyślij wiadomość e-mail do usługi Azure Database dla PostgreSQL zespołu ([ @Ask Azure DB dla PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ten adres jest pytania ogólne zamiast biletami pomocy technicznej.

Ponadto należy wziąć pod uwagę następujące punkty kontaktu, zgodnie z potrzebami:
- Skontaktuj się z pomocą techniczną platformy Azure lub rozwiązać problem z Twoim kontem [bilet w witrynie Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Kolejne kroki
- Odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/postgresql/) zawierającą porównania kosztów i kalkulatory.
- Zacznij od utworzenia pierwszej bazy danych Azure database for PostgreSQL [pojedynczego serwera](./quickstart-create-server-database-portal.md) lub [na dużą skalę (Citus) (wersja zapoznawcza)](./quickstart-create-hyperscale-portal.md)
- Skompiluj pierwszą aplikację w języku Python, PHP, Ruby, C\#, Java, Node.js: [Biblioteki połączeń](./concepts-connection-libraries.md)
