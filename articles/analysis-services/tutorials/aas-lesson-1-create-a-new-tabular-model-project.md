---
title: 'Usługa Azure Analysis Services samouczek Lekcja 1: Tworzenie nowego projektu modelu tabelarycznego | Dokumentacja firmy Microsoft'
description: Opisuje sposób tworzenia nowego projektu samouczka usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3291721847d34b0fa9a6259bfeb6ec6fa06ed2b5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188015"
---
# <a name="create-a-tabular-model-project"></a>Tworzenie projektu modelu tabelarycznego

W tej lekcji używasz programu Visual Studio z projektów usługi Analysis Services lub SQL Server Data Tools (SSDT) do tworzenia nowego projektu modelu tabelarycznego na poziomie zgodności 1400. Po utworzeniu nowego projektu będzie można rozpocząć dodawanie danych i tworzenie modelu. W tej lekcji zostanie również pokrótce omówione środowisko tworzenia modelu tabelarycznego w programie Visual Studio.  
  
Szacowany czas trwania lekcji: **10 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten temat jest pierwszą lekcją samouczka poświęconego tworzeniu modelu tabelarycznego. Aby ukończyć tę lekcję, trzeba spełnić kilka wymagań wstępnych. Aby dowiedzieć się więcej, zobacz temat [Azure Analysis Services – samouczek Adventure Works](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Tworzenie nowego projektu modelu tabelarycznego  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Tworzenie nowego projektu modelu tabelarycznego  
  
1.  W programie Visual Studio, w menu **Plik** kliknij pozycję **Nowy** > **Projekt**.  
  
2.  W oknie dialogowym **Nowy projekt** rozwiń pozycje **Zainstalowano** > **Analiza biznesowa** > **Analysis Services**, a następnie kliknij pozycję **Projekt tabelaryczny usług Analysis Services**.  
  
3.  W polu **Nazwa** wpisz **AW Internet Sales** (Sprzedaż internetowa AW), a następnie określ lokalizację plików projektu.  
  
    Domyślnie **Nazwa rozwiązania** jest taka sama, jak nazwa projektu, ale możesz wpisać inną nazwę rozwiązania.  
  
4.  Kliknij przycisk **OK**.  
  
5.  W oknie dialogowym **Projektant modeli tabelarycznych** wybierz opcję **Zintegrowany obszar roboczy**.  
  
    Podczas tworzenia modelu obszar roboczy hostuje bazę danych modelu tabelarycznego o takiej samej nazwie, co projekt. Zintegrowany obszar roboczy oznacza, że program Visual Studio używa wbudowanego wystąpienia, eliminując konieczność instalowania osobnego wystąpienia serwera usług Analysis Services tylko na potrzeby tworzenia modelu.
      
6.  W polu **Poziom zgodności** wybierz pozycję **SQL Server 2017/Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Jeśli nie widzisz pozycji SQL Server 2017/Azure Analysis Services (1400) w polu listy Poziom zgodności, nie używasz najnowszej wersji programu SQL Server Data Tools. Aby uzyskać najnowszą wersję, zobacz temat [Instalowanie programu SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
      
  
## <a name="understanding-the-visual-studio-tabular-model-authoring-environment"></a>Informacje o środowisku tworzenia modelu tabelarycznego programu Visual Studio  
Po utworzeniu nowego projektu modelu tabelarycznego możemy się przyjrzeć środowisku tworzenia modelu tabelarycznego w programie Visual Studio.  
  
Utworzony projekt zostanie otwarty w programie Visual Studio. W **Eksploratorze modeli tabelarycznych** po prawej stronie jest wyświetlany widok drzewa obiektów modelu. Ponieważ dane nie zostały jeszcze zaimportowane, foldery są puste. Możesz kliknąć prawym przyciskiem myszy folder obiektu, aby wykonać akcje, podobnie jak pasek menu. W kolejnych krokach tego samouczka będziesz używać Eksploratora modeli tabelarycznych do nawigowania po różnych obiektach w projekcie modelu.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Kliknij kartę **Eksplorator rozwiązań**. Widoczny będzie plik **Model.bim**. Jeśli nie widzisz po lewej stronie okna projektanta (pustego okna z kartą Model.bim), w **Eksploratorze rozwiązań** w obszarze **AW Internet Sales Project** (Projekt sprzedaży internetowej AW) kliknij dwukrotnie plik **Model.bim**. Plik Model.bim zawiera metadane Twojego projektu modelu. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Kliknij plik **Model.bim**. W oknie **Właściwości** wyświetlane są właściwości modelu, z których najważniejsza jest właściwość **Tryb zapytania bezpośredniego**. Ta właściwość określa, czy model jest wdrożony w trybie w pamięci (wyłączona) czy w trybie zapytania bezpośredniego (włączona). Na potrzeby tego samouczka utworzysz i wdrożysz model w trybie w pamięci.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Po utworzeniu projektu modelu niektóre właściwości modelu są ustawiane automatycznie zgodnie z ustawieniami modelowania danych, które można określić w oknie dialogowym **Opcje** dostępnym w menu **Narzędzia**. Właściwości Kopia zapasowa danych, Przechowywanie obszaru roboczego i Serwer obszaru roboczego określają, jak i gdzie odbywa się tworzenie kopii zapasowej bazy danych obszaru roboczego (bazy danych utworzonego modelu), a także jej przechowywanie w pamięci i kompilacja. W razie potrzeby możesz zmienić te ustawienia później, ale na razie pozostaw je bez zmian.  

W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt **AW Internet Sales** (Sprzedaż internetowa AW), a następnie kliknij przycisk **Właściwości**. Zostanie wyświetlone okno dialogowe **Strony właściwości AW Internet Sales**. Niektóre z tych właściwości ustawisz później podczas wdrażania modelu.  
  
Po zainstalowaniu projektów usług Analysis Services lub program SSDT kilka nowych pozycji menu zostały dodane do środowiska Visual Studio. Kliknij menu **Model**. Z poziomu tego menu możesz importować dane, odświeżać dane obszaru roboczego, przeglądać model w programie Excel, tworzyć perspektywy i role, wybierać widok modelu i ustawiać opcje obliczania. Kliknij menu **Tabela**. Z poziomu tego menu możesz tworzyć relacje i zarządzać nimi, określać ustawienia tabeli dat, tworzyć partycje i edytować właściwości tabeli. Po kliknięciu menu **Kolumna** możesz dodawać i usuwać kolumny w tabeli, blokować kolumny i określać kolejność sortowania. Visual Studio dodaje również niektóre przyciski do paska. Najbardziej przydatna jest funkcja autosumowania, która pozwala utworzyć standardową miarę agregacji dla wybranej kolumny. Inne przyciski paska narzędzi zapewniają szybki dostęp do często używanych funkcji i poleceń.  
  
Zapoznaj się z oknami dialogowymi i umiejscowieniem różnych funkcji związanych z tworzeniem modeli tabelarycznych. Chociaż niektóre elementy nie są jeszcze aktywne, pozwoli Ci to zorientować się w środowisku tworzenia modelu tabelarycznego.  
  

## <a name="whats-next"></a>Co dalej?
[Lekcja 2: Pobieranie danych](../tutorials/aas-lesson-2-get-data.md).

  
  
  
