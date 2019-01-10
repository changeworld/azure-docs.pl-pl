---
title: 'Usługa Azure Analysis Services samouczek lekcja 13: Wdrażanie | Dokumentacja firmy Microsoft'
description: W tym artykule opisano, jak wdrożyć projekt samouczka do usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 780853644125c8fa6d5edcef642cfad724516ef2
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188389"
---
# <a name="deploy"></a>Wdrażanie

W tej lekcji skonfigurujemy właściwości wdrożenia, określając docelowy serwer usług Azure Analysis Services dla wdrożenia oraz nazwę modelu. Następnie wdrożymy model dla tego wystąpienia. Po wdrożeniu modelu użytkownicy mogą łączyć się z nim przy użyciu aplikacji klienckiej do raportowania. Aby dowiedzieć się więcej, zobacz artykuł [Deploy to Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy) (Wdrażanie w usługach Azure Analysis Services).  
  
Szacowany czas trwania lekcji: **5 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten artykuł stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji, należy ukończyć lekcję poprzednią: [Lekcja 12 Analizuj w programie Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Wdrożenie na zdalnym serwerze usług Analysis Services wymaga [uprawnień administratora](../analysis-services-server-admins.md).  

> [!IMPORTANT]  
> Jeśli przykładowa baza danych AdventureWorksDW2014 została zainstalowana na lokalnym serwerze SQL i model jest wdrażany na serwerze usług Azure Analysis Services, wymagana jest [lokalna brama danych](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Wdrażanie modelu  
  
#### <a name="to-configure-deployment-properties"></a>Aby skonfigurować właściwości wdrożenia  

  
1.  W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt **AW Internet Sales**, a następnie kliknij **Właściwości**.  
  
2.  W oknie dialogowym **Strony właściwości projektu AW Internet Sales** wprowadź we właściwości **Serwer** w obszarze **Serwer wdrażania** pełną nazwę serwera.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  We właściwości **Baza danych** wpisz **Adventure Works Internet Sales**.  
  
4.  We właściwości **Nazwa modelu** wpisz **Adventure Works Internet Sales Model**.  
  
5.  Sprawdź wybrane opcje, a następnie kliknij przycisk **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Aby wdrożyć projekt Adventure Works Internet Sales
  
1.  W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt **AW Internet Sales** > **Kompiluj**.  

2.  Kliknij prawym przyciskiem myszy projekt **AW Internet Sales** > **Wdróż**.

    Podczas wdrażania usług Azure Analysis Services może zostać wyświetlona prośba o wprowadzenie konta. Wprowadź swoje konto organizacyjne i hasło, na przykład nancy@adventureworks.com. To konto musi być wpisane należeć do grupy Administratorzy na serwerze.
  
    Zostanie wyświetlone okno dialogowe Wdrażanie pokazujące stan wdrożenia metadanych i każdej tabeli zawartej w modelu.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Po pomyślnym zakończeniu wdrażania kliknij przycisk **Zamknij**.  
  

Ta lekcja przedstawia najpopularniejszą i najłatwiejszą metodę wdrażania modelu tabelarycznego przy użyciu programu SSDT. Zaawansowane opcje wdrażania, na przykład Kreator wdrażania czy automatyzacja z użyciem języków XMLA i AMO, zapewniają większą elastyczność, spójność i możliwość zaplanowanego wdrażania. Aby dowiedzieć się więcej, zobacz [Tabular model solution deployment (Wdrażanie rozwiązania modelu tabelarycznego)](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular).

## <a name="conclusion"></a>Podsumowanie  
Gratulacje! Tworzenie i wdrażanie pierwszego modelu tabelarycznego usług Analysis Services zostało ukończone. Ten samouczek pomógł przy wykonywaniu typowych zadań związanych z tworzeniem modelu tabelarycznego. Skoro model Adventure Works Internet Sales został wdrożony, można użyć programu SQL Server Management Studio do zarządzania modelem, a także utworzyć skrypty procesu i plan tworzenia kopii zapasowych. Użytkownicy mogą teraz również łączyć się z modelem, używając aplikacji klienckiej do raportowania (np. Microsoft Excel lub Power BI).  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Co dalej?
[Łączenie z programem Power BI Desktop](../analysis-services-connect-pbi.md)   
[Lekcja uzupełniająca — zabezpieczenia dynamiczne](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Lekcja uzupełniająca — wiersze szczegółów](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Lekcja uzupełniająca — niewyrównane hierarchie](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
