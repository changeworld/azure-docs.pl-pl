---
title: Łączenie Cloud App Security danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć Cloud App Security dane z platformą Azure — wskaźnikiem.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588369"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Łączenie danych z Microsoft Cloud App Security 



Można przesyłać strumieniowo dzienniki z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do usługi Azure wskaźnikowego za pomocą jednego kliknięcia. To połączenie umożliwia przesyłanie strumieniowe alertów z Cloud App Security do usługi Azure wskaźnikowej. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Aby przesłać strumieniowo Cloud Discovery dzienniki do usługi Azure wskaźnikowej, [Włącz platformę Azure jako Siem w Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Pozyskiwanie dzienników Cloud Discovery jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Połącz z Cloud App Security

Jeśli masz już Cloud App Security, upewnij się, że jest [włączona w sieci](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Jeśli Cloud App Security zostanie wdrożona i pozyskuje dane, dane alertów można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**, kliknij kafelek **Cloud App Security** i wybierz pozycję **Otwórz stronę łącznika**.

1. Wybierz dzienniki, które chcesz przesłać do usługi Azure — wskaźnik produkcji, możesz wybrać **alerty** i **dzienniki Cloud Discovery** (wersja zapoznawcza). 

1. Kliknij przycisk **Connect** (Połącz).

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Cloud App Security, Wyszukaj pozycję **SecurityAlert**.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Microsoft Cloud App Security z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
