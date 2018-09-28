---
title: Importowanie pliku programu Power BI Desktop do usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Opisuje sposób importowania pliku programu Power BI Desktop (pbix) przy użyciu witryny Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e164488a1bf176d5b6c0e28a84cd1ec22cae4cce
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423582"
---
# <a name="import-a-power-bi-desktop-file"></a>Importowanie pliku programu Power BI Desktop

Model danych w pliku programu Power BI Desktop (pbix) można zaimportować do usługi Azure Analysis Services. Metadane modelu danych w pamięci podręcznej i połączenia źródła danych są importowane. Raporty i wizualizacje nie są importowane. Zaimportowane dane, które są modele z programu Power BI Desktop, na poziomie zgodności 1400.

**Ograniczenia**   

- Importowanie pliku pbix używa funkcji projektanta internetowego w portalu, który jest **Podgląd**. Funkcja jest ograniczona. Dla bardziej zaawansowanych modeli programowania i testowania najlepiej jest używać programu Visual Studio (SSDT) i SQL Server Management Studio (SSMS).
- Jeśli model danych jest tworzona w programie Power BI Desktop lipca 2018 r. Zaktualizuj (2.60.5169.3201) lub nowszy, upewnij się, że nie funkcje wersji zapoznawczej. Funkcje w wersji zapoznawczej nie są jeszcze obsługiwane w usługach Azure Analysis Services.  
Jeśli zostanie wyświetlony następujący błąd podczas importowania, plik pbix ma włączone funkcje wersji zapoznawczej, które nie są jeszcze obsługiwane w usługach Azure Analysis Services.

    ![Ostrzeżenie poziomu zgodności](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)   
- Musi mieć uprawnienia administratora serwera, aby zaimportować z pliku pbix.
- Pbix model mogą łączyć się z **usługi Azure SQL Database** i **Azure SQL Data Warehouse** tylko źródła danych.
- Pbix model nie może mieć na żywo lub zapytania bezpośredniego połączenia. 


## <a name="to-import-from-pbix"></a>Aby zaimportować z pliku pbix

1. Na serwerze usługi **Przegląd** > **Projektant stron sieci Web**, kliknij przycisk **Otwórz**.

    ![Tworzenie modelu w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. W **Projektant stron sieci Web** > **modeli**, kliknij przycisk **+ Dodaj**.

    ![Tworzenie modelu w witrynie Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. W **nowy model**, wpisz nazwę modelu, a następnie wybierz plik programu Power BI Desktop.

    ![Okno dialogowe Nowy model w witrynie Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. W **importu**Znajdź i wybierz swój plik.

     ![Łączenie z okna dialogowego w witrynie Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>Zmienianie poświadczeń

Po zaimportowaniu modelu danych z pliku pbix, domyślnie, poświadczenia używane do połączenia ze źródłem danych można ustawić ServiceAccount. Po zaimportowaniu modelu z pliku pbix można zmienić poświadczenia, za pomocą następujących metod:

- Użyj lipca 2018 r. (wersja 17.8.1) lub nowsza wersja programu SSMS do edytowania poświadczeń. Jest to najprostszy sposób.
- Użyj TMSL [polecenia Alter](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) na [obiektu źródła danych](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl) zmodyfikować właściwość parametrów połączenia. 
- Otwórz model w programie Visual Studio, Edytuj poświadczenia dla połączenia źródła danych i ponownie Wdróż model.

Aby zmienić poświadczenia przy użyciu programu SSMS. 

1. W programie SSMS, rozwiń węzeł bazy danych > **połączeń**. 
2. Kliknij prawym przyciskiem myszy połączenie z bazą danych, a następnie kliknij przycisk **odświeżyć poświadczenia**. 

    ![Odśwież poświadczenia](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. W oknie dialogowym poświadczeń wybierz typ poświadczenia, a następnie wprowadź poświadczenia. Uwierzytelnienie SQL wybierz bazę danych. Konto organizacji (OAuth) wybierz konto Microsoft.
    ![Edytuj poświadczenia](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

Lipca 2018 r. wersję programu Power BI Desktop zawiera nową funkcję dla zmiany uprawnień źródła danych. Na **Home** kliknij pozycję **Edytuj zapytania**  > **ustawienia źródła danych**. Wybierz połączenie źródła danych, a następnie kliknij przycisk **Edytuj uprawnienia**.


## <a name="see-also"></a>Zobacz także

[Tworzenie modelu w portalu Azure](analysis-services-create-model-portal.md)   
[Łączenie z usługami Azure Analysis Services](analysis-services-connect.md)  
