---
title: Importowanie pliku programu Power BI Desktop do usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Opisuje sposób importowania pliku programu Power BI Desktop (pbix) przy użyciu witryny Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3dd90fc862e64812c0ba17bef74818d18788f4b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440992"
---
# <a name="import-a-power-bi-desktop-file"></a>Importowanie pliku programu Power BI Desktop

Model danych w pliku programu Power BI Desktop (pbix) można zaimportować do usługi Azure Analysis Services. Metadane modelu danych w pamięci podręcznej i połączenia źródła danych są importowane. Raporty i wizualizacje nie są importowane. Zaimportowane dane, które są modele z programu Power BI Desktop, na poziomie zgodności 1400.

**Ograniczenia**   
- Pbix model mogą łączyć się z **usługi Azure SQL Database** i **Azure SQL Data Warehouse** tylko źródła danych. 
- Pbix model nie może mieć na żywo lub zapytania bezpośredniego połączenia. 
- Import może zakończyć się niepowodzeniem, jeśli model danych plik pbix zawiera metadane, które nie są obsługiwane w usługach Analysis Services.

## <a name="to-import-from-pbix"></a>Aby zaimportować z pliku pbix

1. Na serwerze usługi **Przegląd** > **Projektant stron sieci Web**, kliknij przycisk **Otwórz**.

    ![Tworzenie modelu w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. W **Projektant stron sieci Web** > **modeli**, kliknij przycisk **+ Dodaj**.

    ![Tworzenie modelu w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. W **nowy model**, wpisz nazwę modelu, a następnie wybierz plik programu Power BI Desktop.

    ![Okno dialogowe Nowy model w witrynie Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. W **importu**Znajdź i wybierz swój plik.

     ![Łączenie z okna dialogowego w witrynie Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Zobacz także

[Tworzenie modelu w portalu Azure](analysis-services-create-model-portal.md)   
[Łączenie z usługami Azure Analysis Services](analysis-services-connect.md)  
