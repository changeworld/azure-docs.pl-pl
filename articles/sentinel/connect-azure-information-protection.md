---
title: Zbieranie danych usługi Azure Information Protection w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane z usługi Azure Information Protection w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 37c6902d4604ca68b67ec4a2c9dc7cde6c67ac05
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246634"
---
# <a name="collect-data-from-azure-information-protection"></a>Zbieranie danych z usługi Azure Information Protection

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo dzienniki z [usługi Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do platformy Azure przez wartownika za pomocą jednego kliknięcia. Usługa Azure Information Protection pomaga chronić dane, czy jest on przechowywany w chmurze, jak i w infrastrukturze lokalnej i sterowania i pomocy dotyczące zabezpieczania poczty e-mail, dokumenty i dane poufne, które są udostępniane poza firmę. Od łatwej klasyfikacji po osadzone etykiety i uprawnienia ochrona danych działa przez cały czas za pomocą usługi Azure Information Protection. Po połączeniu usługi Azure Information Protection do platformy Azure przez wartownika, możesz strumienia wszystkie alerty z usługi Azure Information Protection do platformy Azure przez wartownika.


## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik mający administratora globalnego, administratora zabezpieczeń lub uprawnienia do ochrony informacji


## <a name="connect-to-azure-information-protection"></a>Nawiązać połączenie z usługi Azure Information Protection

Jeśli masz już usługę Azure Information Protection, upewnij się, że jest [włączone w sieci](https://docs.microsoft.com/azure/information-protection/activate-service).
Jeśli wdrożono usługę Azure Information Protection i pobieranie danych, dane alertu można łatwo przesyłane strumieniowo do usługi Azure przez wartownika.


1. Na platformie Azure przez wartownika, wybierz **zbierania danych** a następnie kliknij przycisk **usługi Azure Information Protection** kafelka.

2. Przejdź do [portalu usługi Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. W obszarze **połączenia**, skonfiguruj przesyłanie strumieniowe dzienników z usługi Azure Information Protection do platformy Azure przez wartownika, klikając [Skonfiguruj analizę](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Wybierz obszar roboczy, w którym została wdrożona, przez wartownika platformy Azure. 

5. Kliknij przycisk **OK**.

6. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów usługi Azure Information Protection, możesz wyszukać **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Azure Information Protection do platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
