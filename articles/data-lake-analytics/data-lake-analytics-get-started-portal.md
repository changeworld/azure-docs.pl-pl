---
title: Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal
description: Tworzenie konta usługi Data Lake Analytics i przesyłanie zadania w języku U-SQL przy użyciu witryny Azure Portal.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: get-started-article
ms.date: 03/21/2017
ms.openlocfilehash: fa6a0febfc268fe96063e962a208d73662f9815a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623914"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

W tym artykule opisano sposoby tworzenia kont usługi Azure Data Lake Analytics przy użyciu witryny Azure Portal, definiowania zadań w języku [U-SQL](data-lake-analytics-u-sql-get-started.md) oraz przesyłania zadań do usługi Data Lake Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka musisz dysponować **subskrypcją platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Tworzenie konta Data Lake Analytics

Teraz nastąpi równoczesne utworzenie kont Data Lake Analytics oraz Data Lake Store.  Ten krok jest prosty i jego wykonanie zajmuje tylko około 60 sekund.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób** >  **Dane + analiza** > **Data Lake Analytics**.
3. Wybierz wartości dla następujących elementów:
   * **Nazwa**: nazwa konta usługi Data Lake Analytics (dozwolone są tylko małe litery i cyfry).
   * **Subskrypcja**: wybierz subskrypcję platformy Azure używaną na potrzeby konta usługi Analytics.
   * **Grupa zasobów**. Wybierz istniejącą grupę zasobów platformy Azure lub utwórz nową.
   * **Lokalizacja**. Wybierz centrum danych platformy Azure na potrzeby konta usługi Data Lake Analytics.
   * **Data Lake Store**: postępuj zgodnie z instrukcjami, aby utworzyć nowe konto usługi Data Lake Store lub wybierz istniejące konto. 
4. Opcjonalnie wybierz warstwę cenową dla konta usługi Data Lake Analytics.
5. Kliknij przycisk **Utwórz**. 


## <a name="your-first-u-sql-script"></a>Pierwszy skrypt U-SQL

Poniższy tekst to bardzo prosty skrypt U-SQL. Ma on za zadanie tylko definiowanie małego zestawu danych w skrypcie, a następnie wpisanie tego zestawu danych do domyślnej usługi Data Lake Store jako pliku o nazwie `/data.csv`.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Przesyłanie zadania U-SQL

1. Na koncie usługi Data Lake Analytics wybierz pozycję **Nowe zadanie**.
2. Wklej tekst powyższego skryptu U-SQL. Wprowadź nazwę zadania. 
3. Wybierz przycisk **Prześlij**, aby uruchomić zadanie.   
4. Monitoruj **Stan** zadania i zaczekaj, aż zmieni się na **Powodzenie**.
5. Wybierz kartę **Dane**, a następnie kartę **Dane wyjściowe**. Wybierz plik wyjściowy o nazwie `data.csv` i przejrzyj dane wyjściowe.

## <a name="see-also"></a>Zobacz też

* Aby rozpocząć tworzenie aplikacji w języku U-SQL, zobacz artykuł [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informacje o zadaniach zarządzania znajdziesz w artykule [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md).
