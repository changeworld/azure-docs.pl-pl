---
title: Tworzenie modelu tabelarycznego przy użyciu projektanta sieci Web usługi Azure Analysis Services | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia modelu tabelarycznego usług Azure Analysis Services przy użyciu projektanta sieci Web w witrynie Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0b613a978c15715519ecc0f130ff6ff3ee84e306
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186969"
---
# <a name="create-a-model-in-azure-portal"></a>Tworzenie modelu w witrynie Azure portal

Z funkcji projektanta (wersja zapoznawcza) w sieci web usług Azure Analysis Services w witrynie Azure portal umożliwia szybkie i łatwe do tworzenia i edytowania modele tabelaryczne i zapytania modelu danych bezpośrednio w przeglądarce. 

> [!IMPORTANT]
> Ta funkcja jest przestarzała. Może być usunięty lub znacznie zmodyfikowany w ramach przyszłej aktualizacji. Zalecane jest, że należy przerwać korzystanie z tej funkcji nowych i istniejących projektów, aby zachować zgodność z przyszłymi aktualizacjami. Dla bardziej zaawansowanych modeli programowania i testowania najlepiej jest używać programu Visual Studio (SSDT) i SQL Server Management Studio (SSMS).


## <a name="before-you-begin"></a>Przed rozpoczęciem

- Serwer usług Azure Analysis Services musi być w warstwie standardowa lub dewelopera. Nowe modele utworzone przy użyciu narzędzia Projektant sieci Web są zapytania bezpośredniego, które są obsługiwane tylko przez te warstwy.
- Usługi Azure SQL Database, Azure SQL Data Warehouse lub plik programu Power BI Desktop (pbix) jako źródła danych. Nowe modele utworzone na podstawie pomocy technicznej usługi Power BI Desktop plików usługi Azure SQL Database i Azure SQL Data Warehouse.
- Konto programu SQL Server i hasło dla połączenia ze źródłami danych Azure SQL Database lub Azure SQL Data Warehouse.
- Musi mieć uprawnienia administratora serwera, aby utworzyć nowy model. Do edytowania i wykonywanie zapytań modelu przy użyciu narzędzia Projektant wymagane są uprawnienia administratora bazy danych.

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


