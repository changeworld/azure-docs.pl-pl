---
title: Połącz dane zapory Windows Azure przez wartownika w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych zapory Windows Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: a863910ee338da5655e9f3b5610b0a8049b8b2a9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620778"
---
# <a name="connect-windows-firewall"></a>Łączenie z zaporą systemu Windows

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik zapory Windows pozwala na łatwe łączenie dzienników zapory Windows, jeśli są one połączone z obszarem roboczym usługi Azure przez wartownika. To połączenie umożliwia wyświetlanie pulpitów nawigacyjnych, tworzyć niestandardowe alerty i lepsze badanie. To zapewnia lepszy wgląd w sieci swojej organizacji i zwiększa możliwości operacji zabezpieczeń. Rozwiązanie zbiera zdarzenia zapory Windows z komputerów Windows, na których zainstalowano agenta usługi Log Analytics. 


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="enable-the-connector"></a>Włącz łącznik 

1. W portalu Azure przez wartownika wybierz **łączników danych** a następnie kliknij polecenie **zapory Windows** kafelka. 
1.  W przypadku maszyn z systemem Windows na platformie Azure:
    1. Kliknij przycisk **Instalowanie agenta na maszynie wirtualnej Windows Azure**.
    1. W **maszyn wirtualnych** , wybierz komputer Windows ma być przesyłana strumieniowo do usługi Azure przez wartownika na liście. Upewnij się, że jest to maszyny Wirtualnej z systemem Windows.
    1. W otwartym oknie dla tej maszyny Wirtualnej, kliknij przycisk **Connect**.  
    1. Kliknij przycisk **Włącz** w **Windows zapory łącznika** okna. 

2. Jeśli komputer Windows nie jest Maszyną wirtualną platformy Azure:
    1. Kliknij przycisk **Instalowanie agenta na komputerach nienależących do platformy Azure**.
    1. W **agent bezpośredni** okna, wybierz opcję **Pobierz Windows agent (64-bitowy)** lub **Pobierz Windows agent (32-bitowa)** .
    1. Zainstaluj agenta na komputerze Windows. Kopiuj **identyfikator obszaru roboczego**, **klucza podstawowego**, i **klucz pomocniczy** i korzystania z nich po wyświetleniu monitu podczas instalacji.

4. Wybierz typy danych, które ma być przesyłana strumieniowo.
5. Kliknij przycisk **zainstalować rozwiązanie**.
6. Aby użyć odpowiednich schematu w usłudze Log Analytics zapory Windows, wyszukaj **SecurityEvent**.

## <a name="validate-connectivity"></a>Zweryfikuj łączność

Może upłynąć zgłaszane 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób połączyć zapory Windows na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

