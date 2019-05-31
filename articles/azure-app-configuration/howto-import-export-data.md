---
title: Importowanie lub eksportowanie danych z konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie importowania lub eksportowania danych do lub z konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 377c5088d39821e87412c517540b3190b0a14a00
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393285"
---
# <a name="import-or-export-configuration-data"></a>Importowanie lub eksportowanie danych konfiguracji

Danych platformy Azure obsługuje konfigurację aplikacji, importowanie i eksportowanie operacji. Zastosowanie tych operacji do pracy z danymi konfiguracji zbiorcze i wymiany danych między magazynu konfiguracji aplikacji i kod projektu. Na przykład można skonfigurować Magazyn konfiguracji aplikacji do testowania i drugi dla środowiska produkcyjnego. Możesz następnie skopiować ustawienia aplikacji między nimi przy użyciu pliku tak, aby nie trzeba wprowadzać dane dwa razy.

Ten artykuł zawiera wskazówki do importowania i eksportowania danych za pomocą konfiguracji aplikacji.

## <a name="import-data"></a>Importowanie danych

Import powoduje przeniesienie konfiguracji magazynu danych, w konfiguracji aplikacji z istniejącym źródłem, a nie ręcznie wprowadzić informacje. Aby przeprowadzić migrację danych do magazynu konfiguracji aplikacji lub zagregowanych danych z wielu źródeł, należy użyć funkcji importowania. Konfiguracja aplikacji obsługuje importowania z pliku JSON, YAML lub właściwości.

Importowanie danych przy użyciu [witryny Azure portal](https://portal.azure.com) lub [wiersza polecenia platformy Azure](./scripts/cli-import.md). W witrynie Azure portal wykonaj następujące kroki:

1. Przejdź do sklepu z aplikacjami konfiguracji, a następnie wybierz pozycję **Import/Export**.

2. Na **importu** zaznacz **źródła usługi** > **pliku konfiguracyjnego**.

3. Wybierz **dla języka** > **typu pliku**.

4. Wybierz **folderu** ikonę i przejdź do pliku, który chcesz zaimportować.

    ![Plik importu](./media/import-file.png)

5. Wybierz **Separator**i opcjonalnie wprowadź **prefiksu** do użycia dla importowanych nazwy kluczy.

6. Opcjonalnie można zaznaczyć **etykiety**.

7. Wybierz **Zastosuj** zakończenie importowania.

    ![Plik importu jest gotowy](./media/import-file-complete.png)

## <a name="export-data"></a>Eksportowanie danych

Eksportowanie zapisuje dane konfiguracji przechowywane w konfiguracji aplikacji do innego miejsca docelowego. Funkcja eksportu, na przykład, aby zapisać dane w magazynie konfiguracji aplikacji do pliku, który jest osadzony w kodzie aplikacji podczas wdrażania.

Eksportowanie danych przy użyciu [witryny Azure portal](https://portal.azure.com) lub [wiersza polecenia platformy Azure](./scripts/cli-export.md). W witrynie Azure portal wykonaj następujące kroki:

1. Przejdź do sklepu z aplikacjami konfiguracji, a następnie wybierz pozycję **Import/Export**.

2. Na **wyeksportować** zaznacz **obiektem docelowym usługi** > **pliku konfiguracyjnego**.

3. Opcjonalnie wprowadź **prefiksu** i wybierz **etykiety** i punktu w czasie dla kluczy do wyeksportowania.

4. Wybierz **typu pliku** > **Separator**.

5. Wybierz **Zastosuj** do zakończenia eksportowania.

    ![Plik eksportu jest gotowy](./media/export-file-complete.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej platformy ASP.NET Core](./quickstart-aspnet-core-app.md)  
