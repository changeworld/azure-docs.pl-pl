---
title: Łączenie danych F5 BIG-IP z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane F5 BIG-IP z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588284"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Podłącz urządzenie F5 BIG-IP 

> [!IMPORTANT]
> Łącznik danych F5 BIG-IP w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Złącze F5 BIG-IP umożliwia łatwe łączenie wszystkich dzienników F5 BIG-IP z usługą Azure Sentinel, wyświetlanie skoroszytów, tworzenie niestandardowych alertów i usprawnianie badań. Zapewnia to lepszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Integracja między F5 BIG-IP i Azure Sentinel korzysta z interfejsu API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Konfigurowanie i podłączanie F5 BIG-IP 

F5 BIG-IP może integrować i eksportować dzienniki bezpośrednio do usługi Azure Sentinel.

1. W portalu Azure Sentinel kliknij pozycję **Łączniki danych** i wybierz **pozycję F5 BIG-IP,** a następnie **otwórz stronę łącznika**. 
1. Aby połączyć F5 BIG-IP, należy zaksięgować deklarację JSON do punktu końcowego interfejsu API systemu. Aby uzyskać instrukcje, jak to zrobić, zobacz [Integrowanie F5 BIG-IP z usługą Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Ze strony łącznika F5 BIG-IP skopiuj identyfikator obszaru roboczego i klucz podstawowy i wklej je zgodnie z instrukcjami w obszarze [Przesyłanie strumieniowe danych do usługi Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Po wykonaniu instrukcji F5 BIG-IP na stronie łącznika usługi Azure Sentinel są widoczne połączone typy danych.
1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla zdarzeń F5 BIG-IP, wyszukaj **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**i **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć F5 BIG-IP z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


