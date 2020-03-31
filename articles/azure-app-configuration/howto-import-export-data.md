---
title: Importowanie lub eksportowanie danych za pomocą konfiguracji aplikacji platformy Azure
description: Dowiedz się, jak importować lub eksportować dane do lub z konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056864"
---
# <a name="import-or-export-configuration-data"></a>Importowanie lub eksportowanie danych konfiguracji

Usługa Azure App Configuration obsługuje operacje importowania i eksportowania danych. Użyj tych operacji do pracy z danymi konfiguracji luzem i wymiany danych między magazynem konfiguracji aplikacji i projektem kodu. Na przykład można skonfigurować jeden magazyn konfiguracji aplikacji do testowania i inny dla procesów produkcyjnych. Można skopiować ustawienia aplikacji między nimi, dzięki czemu nie trzeba wprowadzać danych dwa razy.

Ten artykuł zawiera przewodnik dotyczący importowania i eksportowania danych za pomocą konfiguracji aplikacji. Jeśli chcesz skonfigurować ciągłą synchronizację z repozytorium GitHub, zapoznaj się z naszą [akcją GitHub.](https://aka.ms/azconfig-gha1)

## <a name="import-data"></a>Importowanie danych

Import przynosi dane konfiguracji do magazynu konfiguracji aplikacji z istniejącego źródła. Funkcja importowania służy do migracji danych do magazynu konfiguracji aplikacji lub agregowania danych z wielu źródeł. Konfiguracja aplikacji obsługuje importowanie z pliku JSON, YAML lub właściwości.

Importuj dane przy użyciu [witryny Azure portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-import.md). W witrynie Azure portal wykonaj następujące kroki:

1. Przejdź do sklepu konfiguracji aplikacji i wybierz **polecenie Importuj/eksportuj** z menu **Operacje.**

1. Na karcie **Importowanie** wybierz pozycję**Plik konfiguracji** **usługi źródłowej** > .

1. Wybierz **opcję Dla języka** i wybierz żądany typ danych wejściowych.

1. Wybierz ikonę **Folder** i przejdź do pliku, aby zaimportować.

    ![Importuj plik](./media/import-file.png)

1. Wybierz **separator**i opcjonalnie wprowadź **prefiks** do użycia dla importowanych nazw kluczy.

1. Opcjonalnie wybierz **etykietę**.

1. Wybierz **pozycję Zastosuj,** aby zakończyć importowanie.

    ![Importowanie pliku zakończone](./media/import-file-complete.png)

## <a name="export-data"></a>Eksportowanie danych

Eksportuj zapisy zapisuje dane konfiguracyjne przechowywane w konfiguracji aplikacji do innego miejsca docelowego. Użyj funkcji eksportu, na przykład, aby zapisać dane w magazynie konfiguracji aplikacji do pliku, który jest osadzony z kodem aplikacji podczas wdrażania.

Eksportuj dane przy użyciu [witryny Azure portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-export.md). W witrynie Azure portal wykonaj następujące kroki:

1. Przejdź do sklepu konfiguracji aplikacji i wybierz pozycję **Importuj/eksportuj**.

1. Na karcie **Eksportowanie** wybierz pozycję**Plik konfiguracji** **usługi docelowej** > .

1. Opcjonalnie wprowadź **prefiks** i wybierz **etykietę** i punkt w czasie dla kluczy do wyeksportowania.

1. Wybierz **File type** > **separator**typu pliku .

1. Wybierz **pozycję Zastosuj,** aby zakończyć eksport.

    ![Eksportowanie pliku zakończonego](./media/export-file-complete.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej ASP.NET Core](./quickstart-aspnet-core-app.md)  
