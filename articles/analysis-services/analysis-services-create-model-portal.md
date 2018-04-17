---
title: Tworzenie modelu tabelarycznego przy użyciu narzędzia Projektant sieci Web Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć modelu tabelarycznego usług Azure Analysis Services przy użyciu narzędzia Projektant sieci Web w portalu Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 30a7f78e7bf13e6e6197e95b266dfd0d6b8f83c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-model-in-azure-portal"></a>Tworzenie modelu w portalu Azure

Funkcja projektanta (wersja zapoznawcza) w sieci web usług Azure Analysis Services w portalu Azure zapewnia szybki i łatwy sposób tworzenia i edytowania modele tabelaryczne i zapytania modelu danych bezpośrednio w przeglądarce. 

Należy pamiętać, Projektant stron sieci web jest **Podgląd**. Gdy nowych funkcji jest dodawany cały czas w wersji zapoznawczej, funkcje są ograniczone. Do bardziej zaawansowanego modelu programowania i testowania najlepiej jest używać programu Visual Studio (SSDT) i SQL Server Management Studio (SSMS).

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Serwer usług Azure Analysis Services w warstwy standardowa lub dewelopera. Nowe modele utworzone przy użyciu narzędzia Projektant sieci Web są zapytania bezpośredniego, obsługiwane tylko przez te warstwy.
- Baza danych SQL Azure, Magazyn danych SQL Azure lub pliku Power BI Desktop (pbix) jako źródła danych. Nowe modele utworzone na podstawie obsługi plików bazy danych SQL Azure, Magazyn danych SQL Azure, Oracle i Teradata Power BI Desktop źródeł danych.
- Konto programu SQL Server i hasło do nawiązywania połączenia z bazą danych SQL Azure lub usługi Azure SQL Data Warehouse źródeł danych.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Aby utworzyć nowy model tabelaryczny

1. Na serwerze **omówienie** > **Projektant stron sieci Web**, kliknij przycisk **Otwórz**.

    ![Tworzenie modelu w portalu Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. W **Projektant stron sieci Web** > **modele**, kliknij przycisk **+ Dodaj**.

    ![Tworzenie modelu w portalu Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. W **nowy model**, wpisz nazwę modelu, a następnie wybierz źródło danych.

    ![Okno dialogowe nowego modelu w portalu Azure](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. W **Connect**, wprowadź właściwości połączenia. Nazwa użytkownika i hasło musi być kontem programu SQL Server.

     ![Połącz okna dialogowego w portalu Azure](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. W **tabele i widoki**, wybierz tabele, aby dołączyć do modelu, a następnie kliknij przycisk **Utwórz**. Automatycznie tworzyć relacji między tabelami w pary kluczy.

     ![Wybierz tabele i widoki](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Nowy model zostanie wyświetlona w przeglądarce. W tym miejscu można wykonywać następujące czynności:   

- Zapytania modelu danych przez przeciąganie pól do projektanta zapytań i dodawanie filtrów.
- Utwórz nowe miary w tabelach.
- Edytowanie metadanych modelu przy użyciu edytora json.
- Otwórz model w programie Visual Studio (SSDT), Power BI Desktop lub programu Excel.

![Wybierz tabele i widoki](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Podczas edytowania metadanych modelu lub utworzyć nowe miary w przeglądarce, te zmiany są zapisywane do modelu w systemie Azure. Jeśli pracujesz także do modelu w SSDT, Power BI Desktop lub programu Excel, modelu można uzyskać zsynchronizowane.


## <a name="next-steps"></a>Kolejne kroki 
[Zarządzanie ról bazy danych i użytkowników](analysis-services-database-users.md)  
[Łączenie z programem Excel](analysis-services-connect-excel.md)  


