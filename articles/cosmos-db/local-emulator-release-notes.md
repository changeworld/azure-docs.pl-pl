---
title: Usługa Azure Pobierz Emulator usługi Cosmos i informacje o wersji
description: Przeczytaj informacje o wersji emulatora usługi Azure Cosmos i pobierz go.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332122"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Na użytek emulatora usługi Azure Cosmos lokalne programowanie i testowanie

Ten artykuł przedstawia informacje o wersji emulatora usługi Azure Cosmos listę aktualizacji funkcji, które zostały wprowadzone w każdej wersji. Wyświetla ona również najnowsza wersja emulatora do pobrania i użycia.

## <a name="download"></a>Do pobrania

| | |
|---------|---------|
|**Pobieranie pliku MSI**|[Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Wprowadzenie**|[Programowanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="243"></a>2.4.3

- Wyłączone uruchamianie usługi MongoDB domyślnie. Punkt końcowy SQL jest włączona domyślnie. Użytkownik musi uruchomić ręcznie przy użyciu emulatora usługi punktu końcowego "/ EnableMongoDbEndpoint" opcji wiersza polecenia. Teraz jest podobnie jak wszystkie inne punkty końcowe usługi, takie jak Gremlin, Cassandra i tabeli.
- Usunięto usterkę w emulatorze, podczas uruchamiania za pomocą "/ AllowNetworkAccess" gdzie Gremlin, Cassandra i tabeli punktów końcowych nie zostały prawidłowo obsługi żądań od klientów zewnętrznych.
- Ustawienia reguł zapory, należy dodać porty bezpośrednie połączenie.

### <a name="240"></a>2.4.0

- Rozwiązano problem z emulatorem, których nie można uruchomić, gdy aplikacje monitorowania sieci, np. klient Pulse znajdują się na komputerze-hoście.
