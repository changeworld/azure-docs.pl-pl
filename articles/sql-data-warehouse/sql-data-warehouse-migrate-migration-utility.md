---
title: "Migracja: Narzędzie do migracji magazynu danych | Dokumentacja firmy Microsoft"
description: "Przeprowadź migrację do usługi SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: e8a8a84153a950f2d1bc002b34c83dc5ed8a5eb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="data-warehouse-migration-utility-preview"></a>Narzędzie migracji magazynu danych (wersja zapoznawcza)
> [!div class="op_single_selector"]
> * [Pobierz narzędzie do migracji][Download Migration Utility]
> 
> 

Narzędzie migracji magazynu danych to narzędzie przeznaczone do migracji schematu i danych z programu SQL Server i bazy danych SQL Azure do usługi Azure SQL Data Warehouse. Podczas migracji schematu narzędzie automatycznie mapuje odpowiedniego schematu ze źródła do miejsca docelowego. Po przeprowadzeniu migracji schematu narzędzi zapewnia możliwość przenoszenia danych za pomocą automatycznie wygenerowanych skryptów.

Oprócz migracji danych i schemat to narzędzie udostępnia opcję, aby wygenerować raporty zgodności, które zawierają podsumowanie niezgodności między wystąpieniami źródłowe i docelowe, które mogłyby uniemożliwiać prostsze migracji.

## <a name="get-started"></a>Rozpoczęcie pracy
Jako warunek wstępny dla instalacji należy narzędzie wiersza polecenia BCP, aby uruchomić skrypty migracji i pakietu Office, aby wyświetlić raport zgodności. Po uruchomieniu pliku wykonywalnego, który jest pobierany wyświetli monit o zaakceptowanie standardowe umowy EULA, zanim będzie można zainstalować narzędzie.

Ponadto, aby uruchomić Utiliy migracji, należy to następujących uprawnień w bazie danych, które chcesz migrować: tworzenie bazy danych, ALTER DATABASE dowolnego lub wszystkich definicji WIDOKU.

### <a name="launching-the-tool-and-connecting"></a>Uruchamianie narzędzia i łączenia
Uruchom narzędzie, klikając ikony pulpitu, który jest wyświetlany po instalacji. Po otwarciu narzędzia, zostanie wyświetlony monit o strony początkowego połączenia, w którym można wybrać źródłowe i docelowe dla narzędzia migracji. W tej chwili obsługujemy programu SQL Server i bazy danych SQL Azure jako źródła i magazynu danych SQL jako miejsca docelowego. Po wybraniu to użytkownik jest proszony o połączenie z serwerem źródłowym wypełnianie nazwy serwera i uwierzytelniania, a następnie klikając przycisk "Połącz".

Po uwierzytelnieniu narzędzie zostaną wyświetlone listy baz danych, które znajdują się na serwerze, które są połączone z. Wybierając bazy danych, która ma zostać migracji i klikając wybrany migracji można rozpocząć migrację.

## <a name="migration-report"></a>Raport migracji
Wybieranie "Sprawdzanie zgodności bazy danych" w narzędziu spowoduje wygenerowanie raportu podsumowania wszystkich niezgodności obiektu w bazie danych żądanej do migracji. Szerszych listę niektóre funkcje programu SQL Server, który nie znajduje się w magazynie danych programu SQL można znaleźć w naszych [dokumentacja dotycząca migracji][migration documentation]. Po wygenerowaniu raport można zapisać i otworzyć raport w programie Excel.

Należy pamiętać, że podczas generowania schematu migracji, większość problemy zidentyfikowane jako "Object" zostaną dostosowane, aby umożliwić natychmiastowe migracji danych. Przejrzyj zmiany upewnij się, że nie chcesz wprowadzenia dodatkowych zmian przed zastosowaniem schematu.

## <a name="migrate-schema"></a>Migrowanie schematu
Po połączeniu, wybierając "Schema migracji" wygeneruje skryptu migracji schematu dla wybranych tabel. Porty tego skryptu struktury tabeli niezgodne dane mapy typów do bardziej zgodne formularzy i tworzy poświadczenia zabezpieczeń i schematu, jeśli jest to określane przez użytkownika w ustawieniach migracji. Ten kod może być uruchamiane dla docelowego wystąpienia SQL Data Warehouse, zapisywane do pliku, skopiowany do Schowka lub nawet edycji wiersza przed podjęciem dalszych działań.  

Jakie zostały zanotowane powyżej, podczas migracji Przejrzyj schematu migracji zmiany, które narzędzie wprowadził w celu zapewnienia, że należy uważnie zapoznać się je.  

## <a name="migrate-data"></a>Migrowanie danych
Klikając opcję "Dane migracji" może wygenerować BCP skrypty, które zostanie przeniesione dane najpierw do plików prostych na serwerze, a następnie bezpośrednio do magazynu danych SQL. Firma Microsoft zaleca tego procesu przenoszenia niewielkich ilości danych i jako ponownych prób nie są wbudowane i błędów mogą wystąpić w przypadku utraty połączenia sieciowego. Aby można było uruchomić ten, musisz mieć zainstalowane narzędzie wiersza polecenia BCP i schematu dla danych musi już zostały utworzone.

Po wypełnieniu powyżej parametry musisz po prostu kliknij przycisk uruchamiania migracji i zestaw dwa pakiety zostaną wygenerowane do określonej lokalizacji. Aby wyeksportować dane ze źródła migracji do plików prostych, uruchom plik eksportu i uruchom plik importu w celu zaimportowania danych do usługi SQL Data Warehouse.

## <a name="next-steps"></a>Kolejne kroki
Teraz, po migracji niektórych danych, sprawdź informacje dotyczące sposobu [opracowanie][develop].

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://www.microsoft.com/en-us/download/details.aspx?id=49100
