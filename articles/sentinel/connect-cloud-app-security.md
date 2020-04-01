---
title: Łączenie danych zabezpieczeń aplikacji w chmurze z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane usługi Cloud App Security z usługą Azure Sentinel.
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422138"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Łączenie danych z usługi Microsoft Cloud App Security 



Łącznik [microsoft cloud app security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) umożliwia strumieniowe alerty i [dzienniki odnajdywania chmury](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) z usługi MCAS do usługi Azure Sentinel. Pozwoli to uzyskać wgląd w aplikacje w chmurze, uzyskać zaawansowane analizy w celu identyfikacji i zwalczania cyberzagrożeń oraz kontrolowania sposobu podróżowania danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia do odczytu i zapisu w obszarze roboczym.
- Użytkownik musi mieć uprawnienia administratora globalnego lub administratora zabezpieczeń w dzierżawie obszaru roboczego.
- Aby przesyłać strumieniowo dzienniki usługi Cloud Discovery do usługi Azure Sentinel, [włącz usługę Azure Sentinel jako usługę SIEM w usłudze Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> Połknienie dzienników cloud discovery jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Łączenie się z zabezpieczeniami aplikacji w chmurze

Jeśli masz już usługę Cloud App Security, upewnij się, że jest [włączona w sieci](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Jeśli usługa Cloud App Security zostanie wdrożona i pochłonie dane, dane alertów można łatwo przesyłać strumieniowo do usługi Azure Sentinel.


1. Z menu nawigacji usługi Azure Sentinel wybierz polecenie **Łączniki danych**. Na liście łączników kliknij kafelek **programu Microsoft Cloud App Security,** a następnie przycisk Otwórz stronę **łącznika** w prawym dolnym dolnym czasie.

1. Wybierz dzienniki, które chcesz przesyłać strumieniowo do usługi Azure Sentinel; można wybrać **alerty** i **dzienniki odnajdywania w chmurze** (podgląd). 

1. Kliknij **pozycję Zastosuj zmiany**.

1. Aby użyć odpowiedniego schematu w alertach usługi Log `SecurityAlert` Analytics for Cloud App Security, wpisz w oknie kwerendy. W przypadku schematu dzienników `McasShadowItReporting`odnajdowania chmury wpisz .

> [!NOTE]
> Odnajdowanie w chmurze pomaga wykrywać i identyfikować trendy, agregując dane leżące u podstaw połączeń użytkowników z aplikacjami w chmurze.
>
> Ponieważ dane cloud discovery są agregowane na dzień, należy pamiętać, że do 24 godzin wartości najnowszych danych nie zostaną odzwierciedlone w usłudze Azure Sentinel. W przypadku, gdy badanie niskiego poziomu wymaga bardziej natychmiastowych danych, należy to zrobić bezpośrednio w urządzeniu źródłowym lub usłudze, w której znajdują się surowe dane.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Microsoft Cloud App Security z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij wykrywanie zagrożeń za pomocą usługi Azure Sentinel przy użyciu [wbudowanych](tutorial-detect-threats.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.
