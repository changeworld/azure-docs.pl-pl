---
title: Informacje o wersji i pobraniu emulatora usługi Azure Cosmos
description: Przeczytaj informacje o wersji emulatora usługi Azure Cosmos i Pobierz go.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 03163d75681bee7434e2b7576a678b1094d47b94
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973712"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos emulator — informacje o wersji i pobrania

W tym artykule przedstawiono informacje o wersji emulatora usługi Azure Cosmos z listą aktualizacji funkcji, które zostały wprowadzone w poszczególnych wersjach. Znajduje się w nim również Najnowsza wersja emulatora do pobrania i użycia.

## <a name="download"></a>Pobierz

| | |
|---------|---------|
|**Pobieranie pliku MSI**|[Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Wprowadzenie**|[Opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="246"></a>2.4.6

- Ta wersja zapewnia parzystość dzięki funkcjom w usłudze Azure Cosmos z lipca 2019 z wyjątkami zanotowanymi w artykule [opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md). Naprawia również kilka usterek związanych z zamknięciem emulatora, gdy są wywoływane za pomocą wiersza polecenia i wewnętrznych zastąpień adresów IP dla klientów zestawu SDK używających łączności w trybie bezpośrednim.

### <a name="243"></a>zasadniczy

- Domyślnie wyłączone uruchamianie usługi MongoDB. Tylko punkt końcowy SQL jest domyślnie włączony. Użytkownik musi ręcznie uruchomić punkt końcowy przy użyciu opcji wiersza polecenia "/EnableMongoDbEndpoint" emulatora. Teraz podobnie jak w przypadku wszystkich innych punktów końcowych usługi, takich jak Gremlin, Cassandra i Table.
- Rozwiązano usterkę w emulatorze, gdy rozpoczyna się od "/AllowNetworkAccess", gdzie punkty końcowe Gremlin, Cassandra i Table nie obsługiwały prawidłowo żądań od klientów zewnętrznych.
- Dodaj bezpośrednie porty połączeń do ustawień reguł zapory.

### <a name="240"></a>2.4.0

- Rozwiązano problem z uruchomieniem emulatora, gdy na komputerze hosta znajdują się aplikacje do monitorowania sieci, na przykład klient pulsu.
