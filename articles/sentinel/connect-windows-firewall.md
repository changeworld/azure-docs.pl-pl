---
title: Łączenie danych zapory systemu Windows z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane zapory systemu Windows z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588063"
---
# <a name="connect-windows-firewall"></a>Łączenie z zaporą systemu Windows



Łącznik zapory systemu Windows umożliwia łatwe łączenie dzienników zapór systemu Windows, jeśli są one połączone z obszarem roboczym usługi Azure Sentinel. To połączenie umożliwia wyświetlanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i usprawnianie badania. Zapewnia to lepszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Rozwiązanie zbiera zdarzenia zapory systemu Windows z komputerów z systemem Windows, na których jest zainstalowany agent usługi Log Analytics. 


> [!NOTE]
> - Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.
> - Jeśli usługa Azure Sentinel i Usługa Azure Security Center są zbierane w tym samym obszarze roboczym, nie ma potrzeby włączania rozwiązania Zapory systemu Windows za pośrednictwem tego łącznika. Jeśli mimo to została włączona, nie spowoduje to zduplikowanych danych. 

## <a name="enable-the-connector"></a>Włączanie złącza 

1. W portalu Azure Sentinel wybierz **pozycję Łączniki danych,** a następnie kliknij kafelek **zapory systemu Windows.** 
1.  Jeśli komputery z systemem Windows znajdują się na platformie Azure:
    1. Kliknij **pozycję Zainstaluj agenta na maszynie wirtualnej systemu Azure Windows**.
    1. Na liście **Maszyny wirtualne** wybierz komputer z systemem Windows, który chcesz przesyłać strumieniowo do usługi Azure Sentinel. Upewnij się, że jest to maszyna wirtualna systemu Windows.
    1. W oknie, które otwiera się dla tej maszyny Wirtualnej, kliknij przycisk **Połącz**.  
    1. Kliknij **pozycję Włącz** w oknie **łącznika zapory systemu Windows.** 

2. Jeśli komputer z systemem Windows nie jest maszyną wirtualną platformy Azure:
    1. Kliknij **pozycję Zainstaluj agenta na komputerach innych niż Azure**.
    1. W oknie **Agent bezpośredni** wybierz opcję **Pobierz agenta systemu Windows (64 bit)** lub **Pobierz agenta systemu Windows (32 bity).**
    1. Zainstaluj agenta na komputerze z systemem Windows. Skopiuj **identyfikator obszaru roboczego,** **klucz podstawowy**i **klucz pomocniczy** i użyj ich po wyświetleniu monitu podczas instalacji.

4. Wybierz typy danych, które mają być przesyłane strumieniowo.
5. Kliknij **pozycję Zainstaluj rozwiązanie**.
6. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla zapory systemu Windows, wyszukaj hasło **SecurityEvent**.

## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć zaporę systemu Windows z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).

