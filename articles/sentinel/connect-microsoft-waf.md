---
title: Połącz dane zapory aplikacji sieci web firmy Microsoft przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć dane zapory aplikacji sieci web firmy Microsoft na platformie Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 99c553145f0a600826e48bec437449f278b74fbf
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205646"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Połącz dane z zapory aplikacji sieci web firmy Microsoft

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Obejmuje strumieniowe przesyłanie dzienników z zapory aplikacji sieci web usługi Azure Application Gateway firmy Microsoft (WAF). Ta zapora aplikacji sieci Web chroni aplikacji przed lukami takich jak wstrzykiwanie kodu SQL i wykonywanie skryptów między witrynami i umożliwia dostosowywanie reguł w celu zmniejszenia liczby wyników fałszywie dodatnich. Wykonaj te instrukcje, aby przesyłać strumieniowo dzienniki zapory aplikacji sieci Web firmy Microsoft na platformie Azure przez wartownika.


## <a name="prerequisites"></a>Wymagania wstępne

- Istniejącego zasobu bramy aplikacji

## <a name="connect-to-microsoft-web-application-firewall"></a>Nawiązać połączenie z zapory aplikacji sieci web firmy Microsoft

Jeśli masz już zapory aplikacji sieci web firmy Microsoft, upewnij się, że masz istniejący zasób bramy.
Po wdrożeniu zapory aplikacji sieci web firmy Microsoft i pobieranie danych, dane alertu można łatwo przesyłane strumieniowo do usługi Azure przez wartownika.
    
1. W portalu Azure przez wartownika wybierz **łączników danych**.
1. Na stronie łączniki danych wybierz **zapory aplikacji sieci Web** kafelka.
1. Przejdź do [zasobu usługi Application Gateway](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) i wybierz polecenie zapory aplikacji internetowych.
    1. Wybierz **ustawień diagnostycznych**.
    1. Wybierz **+ Dodaj ustawienie diagnostyczne** pod tabelą.
    1. W **ustawień diagnostycznych** wpisz **nazwa** i wybierz **wysyłanie do usługi Log Analytics**.
    1. W obszarze **obszar roboczy usługi Log Analytics** wybierz obszar roboczy przez wartownika platformy Azure.
    1. Wybierz typy dzienników, które mają być analizowane. Firma Microsoft zaleca: ApplicationGatewayAccessLog i ApplicationGatewayFirewallLog.
1. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów zapory aplikacji sieci web firmy Microsoft, możesz wyszukać **AzureDiagnostics**.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączeń zapory aplikacji sieci web firmy Microsoft na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
