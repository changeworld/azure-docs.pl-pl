---
title: Importowanie pliku programu Power BI Desktop do usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Opisuje sposób importowania pliku Power BI Desktop (pbix) przy użyciu portalu Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importowanie pliku Power BI Desktop

W usłudze Azure można utworzyć nowy model, importując Power BI Desktop (pbix) pliku. Metadane modelu danych z pamięci podręcznej i połączenia źródła danych są importowane. Raporty i wizualizacji nie zostały zaimportowane.

**Ograniczenia**   
- Pbix model mogą łączyć się bazy danych SQL Azure i usługi Azure SQL Data Warehouse źródeł danych tylko. 
- Pbix model nie może mieć na żywo lub połączeń zapytania bezpośredniego. 
- Importowania może zakończyć się niepowodzeniem, jeśli model danych plik pbix zawiera metadanych nie jest obsługiwane w usługach Analysis Services.

## <a name="to-import-from-pbix"></a>Aby zaimportować z pbix

1. Znajdujących się na serwerze **omówienie** > **Projektant stron sieci Web**, kliknij przycisk **Otwórz**.

    ![Tworzenie modelu w portalu Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. W **Projektant stron sieci Web** > **modele**, kliknij przycisk **+ Dodaj**.

    ![Tworzenie modelu w portalu Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. W **nowy model**, wpisz nazwę modelu, a następnie wybierz plik Power BI Desktop.

    ![Okno dialogowe nowego modelu w portalu Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. W **importu**Znajdź i zaznacz plik.

     ![Połącz okna dialogowego w portalu Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Zobacz także

[Tworzenie modelu w portalu Azure](analysis-services-create-model-portal.md)   
[Połącz do usług Azure Analysis Services](analysis-services-connect.md)  
