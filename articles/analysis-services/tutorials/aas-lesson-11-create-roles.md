---
title: Usługa Azure Analysis Services samouczek lekcja 11 Tworzenie ról | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia ról w projekcie samouczka usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5b89051cab7e89f79a2b62a392173e6dc234e48d
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189749"
---
# <a name="create-roles"></a>Tworzenie ról

W tej lekcji utworzysz role. Role zapewniają modelowy obiekt bazy danych i zabezpieczają dane, ograniczając dostęp tylko do tych użytkowników, którzy są członkami roli. Każda rola jest zdefiniowana z jednym uprawnieniem: Brak, Odczyt, Odczyt i procesu, proces lub administratora. Role można zdefiniować podczas tworzenia modelu przy użyciu menedżera ról. Po wdrożeniu modelu rolami można zarządzać przy użyciu programu SQL Server Management Studio (SSMS). Aby dowiedzieć się więcej, zobacz temat [Role](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> Utworzenie ról nie jest konieczne do ukończenia tego samouczka. Konto aktualnie użyte do logowania ma domyślnie przypisane uprawnienia administratora dotyczące modelu. Jednak aby inni użytkownicy z Twojej organizacji mogli przeglądać dane przy użyciu klienta raportowania, musisz utworzyć co najmniej jedną rolę z uprawnieniami do odczytu i dodać tych użytkowników jako członków.  
  
Utworzysz trzy role:  
  
-   **Sales Manager** (Menedżer sprzedaży) — ta rola może obejmować użytkowników w organizacji, którym chcesz nadać uprawnienia do odczytu wszystkich obiektów i danych modelu.  
  
-   **Sales Analyst US** (Analityk sprzedaży w Stanach Zjednoczonych) — ta rola może obejmować użytkowników w organizacji, którzy mają tylko przeglądać dane związane ze sprzedażą w Stanach Zjednoczonych. W przypadku tej roli należy użyć formuły języka DAX, aby zdefiniować *Filtr wierszy*, który ogranicza członków do przeglądania tylko danych dotyczących Stanów Zjednoczonych.  
  
-   **Administrator** — ta rola może obejmować użytkowników, którym chcesz nadać uprawnienia administratora zapewniające nieograniczony dostęp do bazy danych modelu i uprawnienia do wykonywania na niej zadań administracyjnych.  
  
Konta użytkowników i grup systemu Windows w organizacji są unikatowe, dlatego możesz dodać konta ze swojej organizacji do członków. Jednak w tym samouczku możesz również pozostawić członków pustych. Wpływ każdej roli jest testu później w lekcji 12: Analizuj w programie Excel.  
  
Szacowany czas trwania lekcji: **15 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten temat stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji, należy ukończyć lekcję poprzednią: [Lekcja 10: Tworzenie partycji](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Tworzenie ról  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Tworzenie roli użytkownika Sales Manager  
  
1.  W Eksploratorze modelu tabelarycznego kliknij prawym przyciskiem myszy pozycje **Role** > **Role**.  
  
2.  W Menedżerze ról kliknij przycisk **Nowa**.  
  
3.  Kliknij nową rolę, a następnie w kolumnie **Nazwa** zmień nazwę roli na **Sales Manager** (Menedżer sprzedaży).  
  
4.  W kolumnie **Uprawnienia** kliknij listę rozwijaną i wybierz uprawnienie **Odczyt**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Opcjonalnie: Kliknij kartę **Członkowie**, a następnie kliknij pozycję **Dodaj**. W oknie dialogowym **Wybieranie użytkowników lub grup** wprowadź grupy lub użytkowników systemu Windows ze swojej organizacji, których chcesz uwzględnić w tej roli.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Tworzenie roli użytkownika Sales Analyst US  
  
1.  W Menedżerze ról kliknij przycisk **Nowa**.    
  
2.  Zmień nazwę roli na **Sales Analyst US** (Analityk sprzedaży w Stanach Zjednoczonych).  
  
3.  Nadaj tej roli uprawnienie **Odczyt**.  
  
4.  Kliknij kartę Filtry wierszy, a następnie tylko dla tabeli **DimGeography** wpisz następującą formułę w kolumnie Filtr języka DAX:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Formuła filtra wierszy musi zostać rozpoznana jako wartość logiczna (PRAWDA/FAŁSZ). Za pomocą tej formuły określasz, że dla użytkownika mają być widoczne tylko wiersze, w których kod kraju/regionu ma wartość „US”.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Opcjonalnie: Kliknij kartę **Członkowie**, a następnie kliknij pozycję **Dodaj**. W oknie dialogowym **Wybieranie użytkowników lub grup** wprowadź grupy lub użytkowników systemu Windows ze swojej organizacji, których chcesz uwzględnić w tej roli.  
  
#### <a name="to-create-an-administrator-user-role"></a>Tworzenie roli użytkownika Administrator  
  
1.  Kliknij przycisk **Nowy**.  
  
2.  Zmień nazwę roli na **Administrator**.  
  
3.  Nadaj tej roli uprawnienie **Administrator**.  
  
4.  Opcjonalnie: Kliknij kartę **Członkowie**, a następnie kliknij pozycję **Dodaj**. W oknie dialogowym **Wybieranie użytkowników lub grup** wprowadź grupy lub użytkowników systemu Windows ze swojej organizacji, których chcesz uwzględnić w tej roli. 
  
  
## <a name="whats-next"></a>Co dalej?
[Lekcja 12 Analizuj w programie Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).

  
  
