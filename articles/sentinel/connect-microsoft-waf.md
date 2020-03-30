---
title: Łączenie danych zapory aplikacji sieci Web z usługą Azure Sentinel
description: Dowiedz się, jak połączyć dane zapory aplikacji sieci Web firmy Microsoft z usługą Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588182"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Łączenie danych z zapory aplikacji sieci Web firmy Microsoft



Dzienniki usługi Azure Application Gateway firmy Microsoft (WAF) można przesyłać strumieniowo z zapory aplikacji sieci Web usługi Azure Application Gateway. Ten system WAF chroni aplikacje przed typowymi lukami w zabezpieczeniach sieci Web, takimi jak iniekcja SQL i skrypty między witrynami, i umożliwia dostosowanie reguł w celu zmniejszenia liczby fałszywych alarmów. Postępuj zgodnie z tymi instrukcjami, aby przesyłać strumieniowo dzienniki zapory aplikacji microsoft web do usługi Azure Sentinel.


## <a name="prerequisites"></a>Wymagania wstępne

- Istniejący zasób bramy aplikacji

## <a name="connect-to-microsoft-web-application-firewall"></a>Łączenie się z zaporą aplikacji sieci Web firmy Microsoft

Jeśli masz już zaporę aplikacji sieci Web firmy Microsoft, upewnij się, że masz istniejący zasób bramy.
Po wdrożeniu zapory aplikacji sieci Web firmy Microsoft i uzyskaniu danych dane alertów można łatwo przesyłać strumieniowo do usługi Azure Sentinel.
    
1. W portalu Azure Sentinel wybierz pozycję **Łączniki danych**.
1. Na stronie Łączniki danych wybierz kafelek **WAF.**
1. Przejdź do [zasobu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) bramy aplikacji i wybierz usługę WAF.
    1. Wybierz **pozycję Ustawienia diagnostyczne**.
    1. Wybierz **+ Dodaj ustawienie diagnostyczne** pod tabelą.
    1. Na stronie **Ustawienia diagnostyczne** wpisz **nazwę** i wybierz pozycję **Wyślij do usługi Log Analytics**.
    1. W obszarze **roboczym analizy dzienników** wybierz obszar roboczy usługi Azure Sentinel.
    1. Wybierz typy dzienników, które chcesz analizować. Polecamy: ApplicationGatewayAccessLog i ApplicationGatewayFirewallLog.
1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla alertów zapory aplikacji sieci Web firmy Microsoft, wyszukaj stronę **AzureDiagnostics**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć zaporę aplikacji sieci Web firmy Microsoft z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
