---
title: Łączenie danych zapory aplikacji sieci Web firmy Microsoft z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć dane zapory aplikacji sieci Web firmy Microsoft z platformą Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 9d85b5a72c2e37719348d61250d167eb9a5688a1
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240021"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Łączenie danych z zapory aplikacji sieci Web firmy Microsoft



Dzienniki można przesyłać strumieniowo z zapory aplikacji sieci Web firmy Microsoft (WAF) platformy Application Gateway Azure. Ta WAF chroni aplikacje przed typowymi lukami w zabezpieczeniach sieci Web, takimi jak iniekcja kodu SQL i skrypty między lokacjami, oraz pozwala na dostosowanie reguł w celu ograniczenia fałszywych wartości dodatnich. Postępuj zgodnie z tymi instrukcjami w celu przesyłania strumieniowego dzienników zapory aplikacji sieci Web firmy Microsoft do usługi Azure wskaźnikowej.


## <a name="prerequisites"></a>Wymagania wstępne

- Istniejący zasób bramy aplikacji

## <a name="connect-to-microsoft-web-application-firewall"></a>Nawiązywanie połączenia z zaporą aplikacji sieci Web firmy Microsoft

Jeśli masz już zaporę aplikacji sieci Web firmy Microsoft, upewnij się, że masz istniejący zasób bramy.
Po wdrożeniu i pobieraniu danych przez zaporę aplikacji sieci Web firmy Microsoft dane alertów mogą być łatwo przesyłane strumieniowo do usługi Azure wskaźnikowej.
    
1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych**.
1. Na stronie łączniki danych wybierz kafelek **WAF** .
1. Przejdź do [zasobu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) Application Gateway i wybierz pozycję WAF.
    1. Wybierz pozycję **Ustawienia diagnostyczne**.
    1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne** w tabeli.
    1. Na stronie **Ustawienia diagnostyczne** wpisz **nazwę** i wybierz pozycję **Wyślij do log Analytics**.
    1. W obszarze **obszar roboczy log Analytics** wybierz obszar roboczy wskaźnik platformy Azure.
    1. Wybierz typy dzienników, które chcesz analizować. Zalecamy: ApplicationGatewayAccessLog i ApplicationGatewayFirewallLog.
1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów zapory aplikacji sieci Web firmy Microsoft, Wyszukaj pozycję **AzureDiagnostics**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie pokazano, jak połączyć zaporę aplikacji sieci Web firmy Microsoft z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
