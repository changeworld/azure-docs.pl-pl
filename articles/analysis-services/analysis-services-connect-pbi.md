---
title: Połączenie do usług Azure Analysis Services przy użyciu usługi Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z serwerem usług Analysis Services dla platformy Azure przy użyciu usługi Power BI.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2ab13c0d36102c5cd75a5b297f77b23cae40b530
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596682"
---
# <a name="connect-with-power-bi"></a>Uzyskuj dostęp do usługi Power BI

Po utworzeniu serwera na platformie Azure i wdrożone modelu tabelarycznego, użytkownicy w Twojej organizacji są gotowe do łączenia i rozpocząć eksplorowanie danych. 

> [!TIP]
> Pamiętaj używać najnowszej wersji [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Połącz w programie Power BI Desktop

1. W programie Power BI Desktop, kliknij przycisk **Pobierz dane** > **Azure** > **usług Azure Analysis Service database**.

2. W **serwera**, wprowadź nazwę serwera. Należy uwzględnić pełny adres URL; na przykład asazure://westcentralus.asazure.windows.net/advworks.

3. W **bazy danych**, jeśli znasz nazwę bazy danych modelu tabelarycznego lub perspektywy, aby nawiązać połączenie, wklej go tutaj. W przeciwnym razie można pozostawić to pole puste i wybierz bazę danych lub perspektywy później.

4. Wybierz opcję połączenie, a następnie naciśnij klawisz **Connect**. 

    Zarówno **połączenia na żywo** i **importu** opcje są obsługiwane. Jednak zaleca się, że używasz połączenia na żywo, ponieważ tryb importu ma pewne ograniczenia; głównie wydajność serwera może mieć wpływ na podczas importowania. Ponadto, jeśli model ma być odświeżane w usłudze Power BI **zezwalają na dostęp z usługi Power BI** ustawienie ma zastosowanie tylko w przypadku wybrania **połączenia na żywo**.

5. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania. 

6. W **Nawigator**, rozwiń węzeł serwera, a następnie wybierz model lub chcesz nawiązać połączenie, a następnie kliknij przycisk perspektywy **Connect**. Kliknij modelu lub perspektywy, aby wyświetlić wszystkie obiekty w tym widoku.

    Model zostanie otwarty w programie Power BI Desktop z pustym raportem w widoku raportu. Na liście pól Wyświetla wszystkie obiekty modelu ukryty. Stan połączenia jest wyświetlany w prawym dolnym rogu.

## <a name="connect-in-power-bi-service"></a>Połączenia w usłudze Power BI (usługa)

1. Utwórz plik Power BI Desktop, który ma aktywne połączenie modelu na serwerze.
2. W [usługi Power BI](https://powerbi.microsoft.com), kliknij przycisk **Pobierz dane** > **pliki**następnie znajdź i zaznacz plik pbix.



## <a name="see-also"></a>Zobacz także
[Połącz do usług Azure Analysis Services](analysis-services-connect.md)   
[Biblioteki klienta](analysis-services-data-providers.md)

