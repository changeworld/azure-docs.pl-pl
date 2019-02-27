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
ms.openlocfilehash: 33ba01440f599e2f019db46c304b0658632d9342
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885284"
---
# <a name="import-or-export-configuration-data"></a>Importowanie lub eksportowanie danych konfiguracji

Danych platformy Azure obsługuje konfigurację aplikacji, importowanie i eksportowanie operacji. Umożliwiają one pracować z danymi konfiguracji w trybie zbiorczym, zgodnie z danych programu exchange między konfiguracji aplikacji oraz przechowywanie i kodu projektu. Na przykład możesz skonfigurować Magazyn konfiguracji aplikacji do testowania i drugi dla środowiska produkcyjnego i kopiowania ustawień aplikacji między nimi przy użyciu pliku tak, aby nie trzeba wprowadzić dane dwa razy.

Ten artykuł zawiera wskazówki do importowania i eksportowania danych za pomocą konfiguracji aplikacji.

## <a name="import-data"></a>Importowanie danych

Import powoduje przeniesienie magazynu danych, w konfiguracji aplikacji z istniejącym źródłem, zamiast ręcznego wprowadzania ich konfiguracji. Funkcja importowania służy do migrowania danych do magazynu konfiguracji aplikacji lub zagregowanych danych z wielu źródeł. Konfiguracja aplikacji obsługuje importowania z pliku JSON, YAML lub właściwości.

Możesz zaimportować dane przy użyciu [witryny Azure portal](https://aka.ms/azconfig/portal) lub [wiersza polecenia platformy Azure](./scripts/cli-import.md). W witrynie Azure portal wykonaj następujące kroki:

1. Przejdź do sklepu z aplikacjami konfiguracji, a następnie kliknij przycisk **Import/Export**.

2. W **importu** kartę, wybrać **źródła usługi** i **pliku konfiguracyjnego**.

3. Wybierz **dla języka** i **typu pliku**.

4. Kliknij pozycję **folderu** ikonę i przejdź do pliku, który chcesz zaimportować.

    ![Plik importu](./media/import-file.png)

5. Wybierz **Separator** i opcjonalnie wprowadź **prefiksu** do użycia dla importowanych nazwy kluczy.

6. Opcjonalnie wybierz **etykiety**.

7. Kliknij przycisk **Zastosuj** do ukończenia importowania.

    ![Plik importu jest pełny](./media/import-file-complete.png)

## <a name="export-data"></a>Eksportowanie danych

Eksportowanie zapisuje dane konfiguracji przechowywane w konfiguracji aplikacji do innego miejsca docelowego. Funkcja eksportu można użyć na przykład, aby zapisać dane w magazynie konfiguracji aplikacji do pliku, która zostanie osadzona w kodzie aplikacji podczas wdrażania.

Możesz wyeksportować dane za pomocą [witryny Azure portal](https://aka.ms/azconfig/portal) lub [wiersza polecenia platformy Azure](./scripts/cli-export.md). W witrynie Azure portal wykonaj następujące kroki:

1. Przejdź do sklepu z aplikacjami konfiguracji, a następnie kliknij przycisk **Import/Export**.

2. W **wyeksportować** kartę, wybrać **obiektem docelowym usługi** i **pliku konfiguracyjnego**.

3. Opcjonalnie wprowadź **prefiksu** i wybierz polecenie **etykiety** i punktu w czasie dla kluczy do wyeksportowania.

4. Wybierz **typu pliku** i **Separator**.

5. Kliknij przycisk **Zastosuj** aby zakończyć eksportowanie.

    ![Plik eksportu jest pełny](./media/export-file-complete.png)

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Tworzenie aplikacji sieci web ASP.NET](quickstart-aspnet-core-app.md)  
