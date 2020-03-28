---
title: Azure Cosmos Emulator pobierz i wydania informacji
description: Pobierz informacje o wydaniu emulatora usługi Azure Cosmos dla różnych wersji i informacje o pobieraniu.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77168650"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator — informacje o wersji i pobieraniu informacji

W tym artykule przedstawiono informacje o wersji emulatora usługi Azure Cosmos z listą aktualizacji funkcji, które zostały wprowadzone w każdej wersji. Zawiera również listę najnowszej wersji emulatora do pobrania i użycia.

## <a name="download"></a>Pobierz

| | |
|---------|---------|
|**Pobieranie MSI**|[Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Wprowadzenie**|[Rozwijaj lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="291"></a>2.9.1

- Ta wersja rozwiązuje problemy z parą w obsłudze interfejsu API kwerend i przywraca zgodność ze starszymi systemami operacyjnymi, takimi jak Windows Server 2012.

### <a name="290"></a>2.9.0

- W tej wersji dodano opcję ustawiania spójności na spójny prefiks i zwiększania maksymalnych limitów dla użytkowników i uprawnień.

### <a name="272"></a>2.7.2

- W tej wersji dodano obsługę serwera MongoDB w wersji 3.6 do emulatora kosmosu. Aby uruchomić punkt końcowy bazy danych MongoDB, który jest docelowy w wersji 3.6 usługi, uruchom emulator z wiersza polecenia administratora z opcją "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Ta wersja rozwiązuje regresji, która uniemożliwiła użytkownikom wykonywanie zapytań względem konta INTERFEJSU API SQL z emulatora podczas korzystania z .NET core lub x86 .NET klientów.

### <a name="246"></a>2.4.6

- Ta wersja zapewnia parzystość z funkcjami w usłudze Azure Cosmos od lipca 2019 r., z wyjątkami odnotowanymi w [programach Rozwijaj lokalnie za pomocą emulatora usługi Azure Cosmos.](local-emulator.md) Rozwiązuje również kilka błędów związanych z zamykaniem emulatora podczas wywoływania za pośrednictwem wiersza polecenia i wewnętrznych zastępów adresów IP dla klientów SDK przy użyciu łączności w trybie bezpośrednim.

### <a name="243"></a>2.4.3

- Domyślnie wyłączone uruchamianie usługi MongoDB. Domyślnie włączono tylko punkt końcowy SQL. Użytkownik musi uruchomić punkt końcowy ręcznie przy użyciu emulatora "/EnableMongoDbEndpoint" opcji wiersza polecenia. Teraz jest jak wszystkie inne punkty końcowe usługi, takie jak Gremlin, Cassandra i Table.
- Naprawiono błąd w emulatorze podczas uruchamiania "/AllowNetworkAccess", gdzie punkty końcowe Gremlin, Cassandra i Table nie były poprawnie obsługi żądań od klientów zewnętrznych.
- Dodaj porty połączeń bezpośrednich do ustawień reguł zapory.

### <a name="240"></a>2.4.0

- Naprawiono błąd związany z niepodstawaniem emulatora, gdy na komputerze-hoście były obecne aplikacje do monitorowania sieci, takie jak Pulse Client.
