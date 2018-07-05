---
title: Tworzenie modelu tabelarycznego przy użyciu projektanta sieci Web usługi Azure Analysis Services | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia modelu tabelarycznego usług Azure Analysis Services przy użyciu projektanta sieci Web w witrynie Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 17ff6ebed615971b4157831431d9e2395ca68b48
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441679"
---
# <a name="create-a-model-in-azure-portal"></a>Tworzenie modelu w witrynie Azure portal

Z funkcji projektanta (wersja zapoznawcza) w sieci web usług Azure Analysis Services w witrynie Azure portal umożliwia szybkie i łatwe do tworzenia i edytowania modele tabelaryczne i zapytania modelu danych bezpośrednio w przeglądarce. 

Należy pamiętać, Projektant stron sieci web jest **Podgląd**. Funkcja jest ograniczona. Dla bardziej zaawansowanych modeli programowania i testowania najlepiej jest używać programu Visual Studio (SSDT) i SQL Server Management Studio (SSMS).

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Serwer usługi Azure Analysis Services w warstwie standardowa lub dewelopera. Nowe modele utworzone przy użyciu narzędzia Projektant sieci Web są zapytania bezpośredniego, które są obsługiwane tylko przez te warstwy.
- Usługi Azure SQL Database, Azure SQL Data Warehouse lub plik programu Power BI Desktop (pbix) jako źródła danych. Nowe modele utworzone na podstawie pomocy technicznej usługi Power BI Desktop plików usługi Azure SQL Database i Azure SQL Data Warehouse.
- Konto programu SQL Server i hasło dla połączenia ze źródłami danych Azure SQL Database lub Azure SQL Data Warehouse.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Aby utworzyć nowy model tabelaryczny

1. Na serwerze **Przegląd** > **Projektant stron sieci Web**, kliknij przycisk **Otwórz**.

    ![Tworzenie modelu w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. W **Projektant stron sieci Web** > **modeli**, kliknij przycisk **+ Dodaj**.

    ![Tworzenie modelu w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. W **nowy model**, wpisz nazwę modelu, a następnie wybierz źródło danych.

    ![Okno dialogowe Nowy model w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. W **Connect**, wprowadź właściwości połączenia. Nazwa użytkownika i hasło musi być kontem programu SQL Server.

     ![Łączenie z okna dialogowego w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. W **tabele i widoki**, wybierz tabele do uwzględnienia w modelu, a następnie kliknij przycisk **Utwórz**. Relacje są tworzone automatycznie między tabelami parą kluczy.

     ![Wybieranie tabel i widoków](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Nowy model zostanie wyświetlony w przeglądarce. W tym miejscu możesz wykonywać następujące czynności:   

- Wykonywanie zapytań o dane w modelu, przeciągając pola do projektanta zapytań i filtrów.
- Utwórz nowe miary w tabelach.
- Edytowanie metadanych modelu przy użyciu edytora json.
- Otworzyć modelu w Visual Studio (SSDT), usługa Power BI Desktop lub Excel.

![Wybieranie tabel i widoków](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Podczas edytowania metadanych modelu, lub Utwórz nowe miary w przeglądarce, te zmiany są zapisywane na modelu na platformie Azure. Jeśli pracujesz również na model w programie SSDT, Power BI Desktop lub Excel, model można uzyskać zsynchronizowane.


## <a name="next-steps"></a>Kolejne kroki 
[Zarządzanie rolami bazy danych i użytkowników](analysis-services-database-users.md)  
[Łączenie z programem Excel](analysis-services-connect-excel.md)  


