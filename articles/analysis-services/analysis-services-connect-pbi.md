---
title: Łączenie się z usługami Analizy platformy Azure za pomocą usługi Power BI | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć się z serwerem usług Azure Analysis Services przy użyciu usługi Power BI. Po połączeniu użytkownicy mogą eksplorować dane modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6205c4189abfefc2ee9c4a273ebfd6773ea609b6
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411362"
---
# <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

Po utworzeniu serwera na platformie Azure i wdrożeniu modelu tabelaryczne użytkownicy w organizacji są gotowi połączyć się i rozpocząć eksplorowanie danych. 

> [!TIP]
> Pamiętaj, aby korzystać z najnowszej wersji programu [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Nawiązywanie połączenie w programie Power BI Desktop

1. W programie Power BI Desktop kliknij pozycję **Pobierz bazę** > danych usług Data**Azure** > **Analysis Services**.

2. W **pliku Server**wprowadź nazwę serwera. Pamiętaj, aby dołączyć pełny adres URL; na przykład asazure://westcentralus.asazure.windows.net/advworks.

3. W **bazie danych**, jeśli znasz nazwę bazy danych modelu tabelaryczne lub perspektywy, z którą chcesz się połączyć, wklej ją tutaj. W przeciwnym razie można pozostawić to pole puste i wybrać bazę danych lub perspektywę później.

4. Wybierz opcję połączenia, a następnie naciśnij klawisz **Połącz**. 

    Obsługiwane są opcje **Connect live** i **Import.** Zaleca się jednak użycie połączeń na żywo, ponieważ tryb importu ma pewne ograniczenia; w szczególności, wydajność serwera może mieć wpływ podczas importowania. Ponadto jeśli model ma być odświeżany w usłudze Power BI, ustawienie **Zezwalaj na dostęp z usługi Power BI** ma zastosowanie tylko wtedy, gdy wybierz pozycję **Połącz na żywo**.

5. Jeśli zostanie wyświetlony monit, wprowadź swoje dane logowania. 

6. W **aplikacji Navigator**rozwiń serwer, a następnie wybierz model lub perspektywę, z którą chcesz się połączyć, a następnie kliknij przycisk **Połącz**. Kliknij model lub perspektywę, aby wyświetlić wszystkie obiekty dla tego widoku.

    Model zostanie otwarty w programie Power BI Desktop z pustym raportem w widoku raportu. Na liście Pola zostaną wyświetlone wszystkie nieukryte obiekty modelu. Stan połączenia jest wyświetlany w prawym dolnym rogu.

## <a name="connect-in-power-bi-service"></a>Łączenie się w usłudze Power BI (usługa)

1. Utwórz plik programu Power BI Desktop, który ma aktywne połączenie z modelem na serwerze.
2. W [usłudze Power BI](https://powerbi.microsoft.com)kliknij pozycję Pobierz**pliki** **danych** > , a następnie znajdź i wybierz plik .pbix.

## <a name="see-also"></a>Zobacz też
[Łączenie się z usługami analizy platformy Azure](analysis-services-connect.md)   
[Biblioteki klienta](analysis-services-data-providers.md)

