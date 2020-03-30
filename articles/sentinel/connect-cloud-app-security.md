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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588369"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Łączenie danych z usługi Microsoft Cloud App Security 



Dzienniki usługi [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) można przesyłać strumieniowo za pomocą jednego kliknięcia. To połączenie umożliwia przesyłanie strumieniowe alertów z usługi Cloud App Security do usługi Azure Sentinel. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Aby przesyłać strumieniowo dzienniki usługi Cloud Discovery do usługi Azure Sentinel, [włącz usługę Azure Sentinel jako usługę SIEM w usłudze Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> Połknienie dzienników cloud discovery jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Łączenie się z zabezpieczeniami aplikacji w chmurze

Jeśli masz już usługę Cloud App Security, upewnij się, że jest [włączona w sieci](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Jeśli usługa Cloud App Security zostanie wdrożona i pochłonie dane, dane alertów można łatwo przesyłać strumieniowo do usługi Azure Sentinel.


1. W usłudze Azure Sentinel wybierz **łączniki danych**, kliknij kafelek **Usługi Cloud App Security** i wybierz pozycję Otwórz stronę **łącznika**.

1. Wybierz dzienniki, które chcesz przesyłać strumieniowo do usługi Azure Sentinel, możesz wybrać **alerty** i **dzienniki odnajdowania chmury** (wersja zapoznawcza). 

1. Kliknij pozycję **Połącz**.

1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla alertów usługi Cloud App Security, wyszukaj aplikację **SecurityAlert**.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Microsoft Cloud App Security z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).
