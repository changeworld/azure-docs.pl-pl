---
title: Łączenie danych narzędzia Azure ATP z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane narzędzia Azure ATP z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588590"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Łączenie danych z zaawansowanej ochrony przed zagrożeniami platformy Azure (ATP)

> [!IMPORTANT]
> Łącznik danych usługi Azure Advanced Threat Protection w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzienniki usługi [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) można przesyłać strumieniowo za pomocą jednego kliknięcia.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Musisz być klientem w wersji zapoznawczej narzędzia Azure ATP i włączyć integrację między usługą Azure ATP i microsoft cloud app security. Aby uzyskać więcej informacji, zobacz [Integracja usługi Azure Advanced Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Łączenie się z usługą Azure ATP

Upewnij się, że wersja [zapoznawcza](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)narzędzia Azure ATP jest włączona w sieci .
Jeśli usługa Azure ATP zostanie wdrożona i pobuje dane, podejrzane alerty można łatwo przesyłać strumieniowo do usługi Azure Sentinel. Może upłynąć do 24 godzin, aby alerty rozpocząć przesyłanie strumieniowe do usługi Azure Sentinel.


1. Aby połączyć narzędzie Azure ATP z usługą Azure Sentinel, należy najpierw włączyć integrację między usługą Azure ATP a usługą Microsoft Cloud App Security. Aby uzyskać informacje na temat tego, jak to zrobić, zobacz [Integracja zaawansowanej ochrony przed zagrożeniami platformy Azure](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. W usłudze Azure Sentinel wybierz **łączniki danych,** a następnie kliknij kafelek **usługi Azure Advanced Threat Protection (Preview).**

1. Można wybrać, czy alerty z narzędzia Azure ATP mają automatycznie generować zdarzenia w usłudze Azure Sentinel. W obszarze **Tworzenie zdarzeń** wybierz **pozycję Włącz,** aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analytics,** a następnie **Aktywne reguły**.

1. Kliknij pozycję **Połącz**.

1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla alertów usługi Azure ATP, wyszukaj **securityalert**.

> [!NOTE]
> Jeśli alerty są większe niż 30 KB, usługa Azure Sentinel przestaje wyświetlać pole Jednostki w alertach.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Azure Advanced Threat Protection z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).

