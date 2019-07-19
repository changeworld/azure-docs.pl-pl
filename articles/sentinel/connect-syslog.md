---
title: Łączenie danych dziennika systemowego z platformą Azure wskaźnikiem wersji Preview | Microsoft Docs
description: Dowiedz się, jak połączyć dane dziennika systemu z platformą Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: fef9fa128d2ebb84fb82579f254735fdb9aa7ee2
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881064"
---
# <a name="connect-your-external-solution-using-syslog"></a>Łączenie rozwiązania zewnętrznego przy użyciu dziennika systemowego

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można podłączyć urządzenie lokalne obsługujące dziennik systemowy do platformy Azure. Odbywa się to przy użyciu agenta opartego na komputerze z systemem Linux między urządzeniem a platformą Azure. Jeśli maszyna z systemem Linux znajduje się na platformie Azure, możesz przesłać strumieniowo dzienniki z urządzenia lub aplikacji do dedykowanego obszaru roboczego tworzonego na platformie Azure i połączyć go. Jeśli maszyna z systemem Linux nie znajduje się na platformie Azure, możesz przesłać strumieniowo dzienniki z urządzenia do dedykowanej maszyny wirtualnej lub na maszynie, na której jest instalowany Agent programu dla systemu Linux. 

> [!NOTE]
> Jeśli urządzenie obsługuje dziennik systemowy CEF, połączenie jest bardziej kompletne i należy wybrać tę opcję i postępować zgodnie z instrukcjami w temacie [łączenie danych z CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Jak to działa

SYSLOG jest protokołem rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Aplikacje będzie wysyłać komunikaty, które mogą być przechowywane na komputerze lokalnym lub dostarczane do modułu zbierającego Syslog. Po zainstalowaniu agenta Log Analytics dla systemu Linux program skonfiguruje lokalny demon dziennika systemowego, aby przekazywać komunikaty do agenta. Następnie Agent wysyła komunikat do Azure Monitor, w którym zostanie utworzony odpowiedni rekord.

Aby uzyskać więcej informacji, zobacz [źródła danych dziennika systemowego w Azure monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> Agent może zbierać dzienniki z wielu źródeł, ale muszą być zainstalowane na dedykowanym komputerze proxy.

## <a name="connect-your-syslog-appliance"></a>Połącz urządzenie dziennika systemu

1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych** i wybierz wiersz **dziennika** systemowego w tabeli i w okienku dziennik systemowy po prawej stronie, a następnie kliknij pozycję **Otwórz stronę łącznika**.
2. Jeśli maszyna z systemem Linux znajduje się na platformie Azure, wybierz pozycję **Pobierz i Zainstaluj agenta na maszynie wirtualnej platformy Azure z systemem Linux**. W oknie maszyny wirtualne wybierz maszyny, na których chcesz zainstalować agenta, a następnie kliknij pozycję **Połącz** w górnej części ekranu.
1. Jeśli komputer z systemem Linux nie znajduje się na platformie Azure, wybierz pozycję **Pobierz i Zainstaluj agenta na maszynie spoza platformy Azure**. W oknie **agenta bezpośredniego** Skopiuj polecenie w obszarze **Pobierz i Dołącz agenta dla systemu Linux** i uruchom je na komputerze. 
1. W obszarze **Konfigurowanie dzienników do połączenia** w oknie instalatora łącznika dziennika systemowego postępuj zgodnie z instrukcjami:
    1. Kliknij link, aby **otworzyć konfigurację ustawień zaawansowanych obszaru roboczego**. 
    1. Wybierz pozycję **dane**, a następnie **Dziennik**systemowy.
    1. Następnie w tabeli ustaw, które obiekty mają być zbierane przez dziennik systemowy. Należy dodać lub wybrać obiekty, które urządzenie dziennika systemu zawiera w jego nagłówkach dziennika. Tę konfigurację można zobaczyć w urządzeniu dziennika systemowego w folderze dziennika:/etc/rsyslog.d/Security-config-omsagent.conf i w obszarze r-dziennik systemu w obszarze/etc/syslog-ng/Security-config-omsagent.conf. 
       > [!NOTE]
       > Po zaznaczeniu pola wyboru, aby **zastosować poniższą konfigurację do moich maszyn**, ta konfiguracja będzie miała zastosowanie do wszystkich maszyn z systemem Linux podłączonych do tego obszaru roboczego. Tę konfigurację można zobaczyć na komputerze dziennika systemu w obszarze 
1. Kliknij przycisk **naciśnij tutaj, aby otworzyć blok konfiguracja**.
1. Wybierz pozycję **dane** , a następnie **Dziennik**systemowy.
   - Upewnij się, że każda z funkcji wysyłanych przez dziennik systemowy znajduje się w tabeli. Dla każdej funkcji, która ma być monitorowana, ustaw ważność. Kliknij przycisk **zastosować**.
1. Upewnij się, że wysyłasz te obiekty na komputerze dziennika systemowego. 

1. Aby użyć odpowiedniego schematu w Log Analytics dla dzienników dziennika systemowego, Wyszukaj **Dziennik**systemowy.
1. Można użyć funkcji Kusto opisanej w temacie using Functions [in Azure Monitor Log zapytania](../azure-monitor/log-query/functions.md) , aby przeanalizować komunikaty dziennika systemowego, a następnie zapisać je jako nową funkcję log Analytics, a następnie użyć funkcji jako nowego typu danych.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia lokalnych urządzeń dziennika systemu z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
