---
title: Importowanie pliku programu Power BI Desktop do usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Opisuje sposób importowania pliku Power BI Desktop (pbix) przy użyciu portalu Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aea6f3efcf3740527c43b75a30caadf6b2a8b623
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601075"
---
# <a name="import-a-power-bi-desktop-file"></a>Importowanie pliku Power BI Desktop

Model danych w pliku Power BI Desktop (pbix) można zaimportować do usług Azure Analysis Services. Metadane modelu danych z pamięci podręcznej i połączenia źródła danych są importowane. Raporty i wizualizacji nie zostały zaimportowane. Importowane dane są modeli z Power BI Desktop 1400 poziom zgodności.

**Ograniczenia**   
- Pbix model mogą łączyć się z **bazy danych SQL Azure** i **magazyn danych SQL Azure** tylko źródła danych. 
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
