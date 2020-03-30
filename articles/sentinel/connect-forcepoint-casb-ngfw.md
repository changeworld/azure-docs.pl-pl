---
title: Łączenie produktów Forcepoint z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć produkty Forcepoint z usługą Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588233"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Łączenie produktów Forcepoint z usługą Azure Sentinel

> [!IMPORTANT]
> Łącznik danych produktów Forcepoint w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


W tym artykule wyjaśniono, jak połączyć produkty Forcepoint z usługą Azure Sentinel. 

Łączniki danych Forcepoint umożliwiają łączenie forcepoint cloud access security broker i forcepoint następnej generacji dzienniki zapory z usługi Azure Sentinel. W ten sposób można automatycznie eksportować dzienniki zdefiniowane przez użytkownika do usługi Azure Sentinel w czasie rzeczywistym. Łącznik zapewnia wzbogacony wgląd w działania użytkowników rejestrowane przez produkty Forcepoint. Umożliwia również dalszą korelację z danymi z obciążeń platformy Azure i innych źródeł danych oraz zwiększa możliwości monitorowania za pomocą skoroszytów w usłudze Azure Sentinel.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forward Forcepoint loguje się do agenta Syslog 

Skonfiguruj produkt Forcepoint, aby przesyłał dalej wiadomości Syslog w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta Syslog.

1. Skonfiguruj produkt Forcepoint do integracji usługi Azure Sentinel zgodnie z opisem w następujących przewodnikach instalacji:
 - [Przewodnik po integracji programu FORCEPOINT CASB](https://frcpnt.com/casb-sentinel)
 - [Przewodnik po integracji NGFW forcepoint](https://frcpnt.com/ngfw-sentinel)

2. Wyszukaj CommonSecurityLog używać odpowiedniego schematu w log analytics z devicevendor nazwa zawiera "Forcepoint". 

3. Przejdź do [kroku 3: Sprawdź poprawność łączności](connect-cef-verify.md).



## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć produkty Forcepoint z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:

- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)

- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).

- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)