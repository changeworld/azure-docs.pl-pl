---
title: Łączenie z usługami Azure Analysis Services z usługą Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z serwerem usług Azure Analysis Services przy użyciu usługi Power BI.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cb93c29ae4cb8d365cd128c52ee726c10c94bc19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61023578"
---
# <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

Po utworzeniu serwera na platformie Azure i wdrożyć model tabelaryczny, użytkownicy w organizacji są gotowe nawiązać połączenie i rozpocząć eksplorowanie danych. 

> [!TIP]
> Należy używać najnowszej wersji [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Nawiązywanie połączenie w programie Power BI Desktop

1. W programie Power BI Desktop kliknij pozycję **Pobierz dane** > **Azure** > **Baza danych usług Azure Analysis Services**.

2. W **serwera**, wprowadź nazwę serwera. Należy uwzględnić pełny adres URL; na przykład asazure://westcentralus.asazure.windows.net/advworks.

3. W **bazy danych**, jeśli znasz nazwę bazy danych modelu tabelarycznego lub perspektywy, aby nawiązać połączenie, wklej go tutaj. W przeciwnym razie można pozostawić to pole puste i później wybierz bazę danych lub perspektywę.

4. Wybierz opcję połączenie, a następnie naciśnij klawisz **Connect**. 

    Zarówno **Połącz na żywo** i **importu** opcje są obsługiwane. Jednak zaleca się używanie połączenia na żywo, ponieważ tryb importu mają pewne ograniczenia; w szczególności serwera może mieć wpływ na wydajność podczas importowania. Ponadto, jeśli model ma być odświeżane w usłudze Power BI **zezwolić na dostęp z usługi Power BI** ustawienie ma zastosowanie tylko wtedy, gdy wybór **Połącz na żywo**.

5. Po wyświetleniu monitu wprowadź poświadczenia logowania. 

6. W **Nawigator**, rozwiń węzeł serwera, a następnie wybierz model lub perspektywy, aby nawiązać połączenie, a następnie kliknij przycisk **Connect**. Kliknij model lub perspektywy, aby wyświetlić wszystkie obiekty dla tego widoku.

    Model zostanie otwarty w programie Power BI Desktop od pustego raportu w widoku raportu. Na liście pola zostaną wyświetlone wszystkie obiekty modelu-ukryte. Stan połączenia jest wyświetlany w prawym dolnym rogu.

## <a name="connect-in-power-bi-service"></a>Łączenie w usłudze Power BI (usługa)

1. Utwórz plik programu Power BI Desktop, który ma połączenie na żywo do modelu na serwerze.
2. W [usługi Power BI](https://powerbi.microsoft.com), kliknij przycisk **Pobierz dane** > **pliki**, a następnie znajdź i wybierz swój plik pbix.

## <a name="see-also"></a>Zobacz także
[Łączenie z usługami Azure Analysis Services](analysis-services-connect.md)   
[Biblioteki klienckie](analysis-services-data-providers.md)

