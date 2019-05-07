---
title: Połącz dane DNS w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć dane DNS w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6429568b33ece3ed4f26614e55e8c3069dd65d71
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204404"
---
# <a name="connect-your-domain-name-server"></a>Połączyć swój serwer nazwy domeny

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć wszystkie domeny nazwy serwera (DNS) systemem Windows na platformie Azure przez wartownika. Odbywa się to przez zainstalowanie agenta na komputerze DNS. Loguje się przy użyciu systemu DNS, możesz uzyskać zabezpieczeń, wydajności i operacji związanych z szczegółowych informacji w infrastrukturze DNS organizacji przez zbieranie, analizowanie, i korelowanie analityczne i dzienniki inspekcji i inne powiązane dane z serwerów DNS.

Po włączeniu połączenia dziennika DNS, możesz wykonywać następujące czynności:
- Identyfikacja klientów próbujących rozpoznać nazwy domen złośliwe
- Identyfikowanie starych rekordów
- Zidentyfikuj domeny często poszukiwanych nazw i próg klienci DNS
- Widok obciążenie żądaniami na serwerach DNS
- Widok dynamiczny niepowodzenia rejestracji DNS

## <a name="how-it-works"></a>Jak to działa

Połączenie DNS odbywa się przez zainstalowanie agenta na komputerze DNS. Agent pobiera zdarzenia z usługi DNS i przekazuje je do usługi Log Analytics.

## <a name="connect-your-dns-appliance"></a>Połącz urządzenie DNS

1. W portalu Azure przez wartownika wybierz **łączników danych** i wybierz polecenie **DNS** kafelka.
1. W przypadku maszyn z systemem DNS na platformie Azure:
    1. Kliknij przycisk **Pobierz i zainstaluj agenta dla maszyn wirtualnych Windows**.
    1. W **maszyn wirtualnych** listy, wybierz maszynę DNS ma być przesyłana strumieniowo do usługi Azure przez wartownika. Upewnij się, że jest to maszyny Wirtualnej z systemem Windows.
    1. W otwartym oknie dla tej maszyny Wirtualnej, kliknij przycisk **Connect**.  
    1. Kliknij przycisk **Włącz** w **łącznika DNS** okna. 

2. Jeśli maszyna DNS nie jest w Maszynie wirtualnej platformy Azure:
    1. Kliknij przycisk **Pobierz i zainstaluj agenta maszyn nienależących do platformy Azure Windows**.
    1. W **agent bezpośredni** okna, wybierz opcję **Pobierz Windows agent (64-bitowy)** lub **Pobierz Windows agent (32-bitowa)**.
    1. Zainstaluj agenta na komputerze DNS. Kopiuj **identyfikator obszaru roboczego**, **klucza podstawowego**, i **klucz pomocniczy** i korzystania z nich po wyświetleniu monitu podczas instalacji.

3. Aby użyć odpowiednich schematu w usłudze Log Analytics dla dzienników DNS, możesz wyszukać **DnsEvents**.

## <a name="validate"></a>Walidacja 

W usłudze Log Analytics Wyszukaj schematu **DnsEvents** i upewnij się, że zdarzenia.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń lokalnych DNS na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
