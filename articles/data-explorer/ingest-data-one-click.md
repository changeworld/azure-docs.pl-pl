---
title: Ponowne przyjmanie za pomocą jednego kliknięcia umożliwia wyłudzenie danych do Eksploratora danych platformy Azure
description: Omówienie pozyskiwania (ładowania) danych do usługi Azure Data Explorer po prostu przy użyciu pozyskiwania jednym kliknięciem.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521584"
---
# <a name="what-is-one-click-ingestion"></a>Co to jest połk, w tym połk, czyli jedno kliknięcie? 

Pozyskiwanie jednym kliknięciem umożliwia szybkie pozyskiwanie danych i automatyczne sugerowanie tabel i struktur mapowania na podstawie źródła danych w Eksploratorze danych platformy Azure. 

Za pomocą interfejsu użytkownika sieci Web usługi Azure Data Explorer można pozyskiwania danych z magazynu (plik obiektu blob), pliku lokalnego lub kontenera (do 10 000 obiektów blob). Można również zdefiniować siatkę zdarzeń w kontenerze do ciągłego pozyskiwania. Dane można pobrać do istniejącej lub nowej tabeli w [formacie](#file-formats)JSON, CSV i innych . Jedno kliknięcie pozyskiwania może sugerować strukturę dla nowej tabeli i tabeli mapowania, na podstawie źródła danych i zapewnić intuicyjną platformę do dostosowania struktury tabeli istniejącego mapowania tabeli i tabeli. Jedno kliknięcie pochłonie dane do tabeli w ciągu zaledwie kilku minut.

Łk 1 kliknięcia jest szczególnie przydatne podczas pozyskiwania danych po raz pierwszy lub gdy schemat danych jest nieznany.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Tworzenie [klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md).
* Zaloguj się do interfejsu [internetowego usługi Azure Data Explorer](https://dataexplorer.azure.com/) i dodaj połączenie do [klastra](/azure/data-explorer/web-query-data#add-clusters).

## <a name="file-formats"></a>Formaty plików

Pozyskiwanie jednym kliknięciem obsługuje pozyskiwanie nowej tabeli z danych źródłowych w dowolnym z następujących formatów:
* JSON
* CSV
* Tsv
* Scsv ( scsv )
* SOHSV ( SOHSV )
* TSVE (właso)
* Psv

## <a name="one-click-ingestion-wizard"></a>Kreator pozyskiwania jednym kliknięciem

Kreator pozyskiwania jednym kliknięciem prowadzi użytkownika przez proces pozyskiwania jednym kliknięciem. 

> [!Note]
> W tej sekcji opisano kreatora w ogóle. Wybrane opcje zależą od tego, czy połkujesz do nowej, czy istniejącej tabeli. Aby uzyskać więcej informacji, zobacz:
    > * Połknąć do [nowego stołu](one-click-ingestion-new-table.md)
    > * Połknąć do [istniejącej tabeli](one-click-ingestion-existing-table.md) 
    
1. Aby uzyskać dostęp do kreatora, kliknij prawym przyciskiem myszy *wiersz bazy danych* lub *tabeli* w lewym menu interfejsu użytkownika internetowego usługi Azure Data Explorer i wybierz pozycję **Pozyskuj nowe dane (wersja zapoznawcza)**.

    ![Wybieranie pozyskiwania jednym kliknięciem w interfejsie użytkownika sieci Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. Kreator przeprowadzi Cię przez następujące opcje:
       * Połknąć do [istniejącej tabeli](one-click-ingestion-existing-table.md)
       * Połknąć do [nowego stołu](one-click-ingestion-new-table.md)
       * Połkniesz dane z: * Magazyn obiektów Blob * Plik lokalny * Kontener
       * Wprowadź rozmiar próbki od 1 do 10 000 wierszy (tylko z kontenera)
       
1. Po pomyślnym wybraniu źródła danych zostanie wyświetlony podgląd danych. 
    W przypadku pozyskiwania danych z kontenera można je filtrować, aby były pogoń za tylko plikami z określonymi prefiksami lub rozszerzeniami plików. Na przykład można na przykład pozyskiwania tylko plików z nazwami plików zaczynającymi się od słowa *Europe*lub tylko plików z rozszerzeniem *.json*. 

1. Kliknij **pozycję Edytuj schemat**. W przypadku pozyskiwania danych do określonej tabeli można mapować kolumny źródłowe do kolumn docelowych i zdecydować, czy mają być uwzględniane nazwy kolumn.

1. Rozpocznij proces pozyskiwania danych.

> [!Note]
> Jeśli źródłem danych jest kontener, należy pamiętać, że zasady pozyskiwania danych (wsadowe) usługi Azure Data Explorer są przeznaczone do optymalizacji procesu pozyskiwania. Domyślnie zasady są skonfigurowane do 5 minut lub 500 MB danych, więc może wystąpić opóźnienie. Zobacz [zasady przetwarzania wsadowego](/azure/kusto/concepts/batchingpolicy) dla opcji agregacji. Po spożyciu danych z innych źródeł, połkowanie będzie miało natychmiastowy efekt.

## <a name="next-steps"></a>Następne kroki

* Zdecyduj, czy do pozyskiwania danych do [istniejącej tabeli](one-click-ingestion-existing-table.md) lub nowej tabeli użyjesz pozyskiwania jednym [kliknięciem.](one-click-ingestion-new-table.md)
* [Zapytanie o dane w usłudze Azure Data Explorer web ui](/azure/data-explorer/web-query-data)
* [Pisanie zapytań dla Eksploratora danych platformy Azure przy użyciu języka zapytań Kusto](/azure/data-explorer/write-queries)