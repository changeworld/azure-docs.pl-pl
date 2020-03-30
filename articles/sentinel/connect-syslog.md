---
title: Łączenie danych syslogu z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane Syslog z usługą Azure Sentinel.
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
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588080"
---
# <a name="connect-your-external-solution-using-syslog"></a>Podłącz rozwiązanie zewnętrzne za pomocą sysloga

Można podłączyć dowolne urządzenie lokalne, które obsługuje Syslog do usługi Azure Sentinel. Odbywa się to przy użyciu agenta opartego na komputerze z systemem Linux między urządzeniem a usługą Azure Sentinel. Jeśli komputer z systemem Linux jest na platformie Azure, można przesyłać strumieniowo dzienniki z urządzenia lub aplikacji do dedykowanego obszaru roboczego utworzonego na platformie Azure i połączyć go. Jeśli komputer z systemem Linux nie jest na platformie Azure, można przesyłać strumieniowo dzienniki z urządzenia do dedykowanej lokalnej maszyny Wirtualnej lub komputera, na którym można zainstalować agenta dla systemu Linux. 

> [!NOTE]
> Jeśli urządzenie obsługuje technologię Syslog CEF, połączenie jest pełniejsty i należy wybrać tę opcję i postępować zgodnie z instrukcjami w [łączeniu danych z cef](connect-common-event-format.md).

## <a name="how-it-works"></a>Jak to działa

Syslog to protokół rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Aplikacje będą wysyłać wiadomości, które mogą być przechowywane na komputerze lokalnym lub dostarczane do modułu zbierającego Syslog. Po zainstalowaniu agenta usługi Log Analytics dla systemu Linux konfiguruje lokalny demon Syslog do przekazywania wiadomości do agenta. Agent następnie wysyła wiadomość do usługi Azure Monitor, gdzie tworzony jest odpowiedni rekord.

Aby uzyskać więcej informacji, zobacz [Źródła danych Syslog w usłudze Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - Agent może zbierać dzienniki z wielu źródeł, ale musi być zainstalowany na dedykowanym komputerze proxy.
> - Jeśli chcesz obsługiwać łączniki dla cef i Syslog na tej samej maszynie Wirtualnej, wykonaj następujące kroki, aby uniknąć powielania danych:
>    1. Postępuj zgodnie z instrukcjami, aby [podłączyć urządzenie CEF](connect-common-event-format.md).
>    2. Aby połączyć dane Syslog, przejdź do **Settings** > **Ustawienia ustawienia obszaru roboczego Ustawienia** > **Zaawansowane ustawienia** > **Dane** > **Syslog** i ustawić obiekty i ich priorytety, tak aby nie były one te same udogodnienia i właściwości używane w konfiguracji CEF. <br></br>Jeśli wybierzesz **Zastosuj poniżej konfiguracji do moich komputerów,** stosuje te ustawienia do wszystkich maszyn wirtualnych podłączonych do tego obszaru roboczego.


## <a name="connect-your-syslog-appliance"></a>Podłączanie urządzenia Syslog

1. W usłudze Azure Sentinel wybierz **łączniki danych,** a następnie wybierz łącznik **Syslog.**

2. Na bloku **Syslog** wybierz pozycję **Otwórz stronę łącznika**.

3. Zainstaluj agenta Linuksa:
    
    - Jeśli twoja maszyna wirtualna systemu Linux jest na platformie Azure, wybierz **pozycję Pobierz i zainstaluj agenta na maszynie wirtualnej systemu Azure Linux**. W bloku **Maszyny wirtualne** wybierz maszyny wirtualne, na które ma zainstalować agenta, a następnie kliknij przycisk **Połącz**.
    - Jeśli komputera z systemem Linux nie ma na platformie Azure, wybierz **opcję Pobierz i zainstaluj agenta na komputerze z systemem Linux nienawiązanych z platformą Azure.** W bloku **agenta bezpośredniego** skopiuj polecenie **do pobrania i wbudowanego agenta dla linuksa** i uruchom go na komputerze. 
    
   > [!NOTE]
   > Upewnij się, że skonfigurowano ustawienia zabezpieczeń dla tych komputerów zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować ustawienia sieciowe tak, aby były zgodne z zasadami zabezpieczeń sieciowych organizacji, a także zmienić porty i protokoły w demonie, aby dostosować je do wymagań dotyczących zabezpieczeń.

4. Wybierz **pozycję Otwórz konfigurację ustawień zaawansowanych obszaru roboczego**.

5. W bloku **Ustawienia zaawansowane** wybierz pozycję**Syslog** **danych** > . Następnie dodaj urządzenia do złącza do zbierania.
    
    Dodaj obiekty, które urządzenie syslog zawiera w nagłówkach dziennika. Tę konfigurację można zobaczyć w urządzeniu Syslog w aplikacji `/etc/rsyslog.d/security-config-omsagent.conf` **Syslog-d** w folderze oraz w **r-Syslog** z `/etc/syslog-ng/security-config-omsagent.conf`pliku .
    
    Jeśli chcesz użyć nietypowego wykrywania logowania SSH z danymi, które zbierasz, dodaj **auth** i **authpriv**. Zobacz [poniższą sekcję,](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) aby uzyskać dodatkowe informacje.

6. Po dodaniu wszystkich obiektów, które chcesz monitorować i dostosowaniu wszystkich opcji ważności dla każdego z nich, zaznacz pole wyboru **Zastosuj poniżej konfiguracji do moich komputerów**.

7. Wybierz **pozycję Zapisz**. 

8. Na urządzeniu syslog upewnij się, że wysyłasz określone obiekty.

9. Aby użyć odpowiedniego schematu w usłudze Azure Monitor dla dzienników syslog, wyszukaj **plik Syslog**.

10. Funkcji Kusto opisanej w [using functions in Azure Monitor log queries](../azure-monitor/log-query/functions.md) to parse your Syslog messages. Następnie można zapisać je jako nową funkcję usługi Log Analytics do użycia jako nowy typ danych.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurowanie łącznika Syslog do nienormalności wykrywania logowania SSH

> [!IMPORTANT]
> Wykrywanie logowania anomalii SSH jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Sentinel może zastosować uczenie maszynowe (ML) do danych syslogu w celu zidentyfikowania nietypowej aktywności logowania secure shell (SSH). Scenariusze obejmują:

- Niemożliwe podróży - gdy dwa udane zdarzenia logowania występują z dwóch lokalizacji, które są niemożliwe do osiągnięcia w ramach czasowych dwóch zdarzeń logowania.
- Nieoczekiwana lokalizacja — lokalizacja, z której wystąpiło zdarzenie pomyślnego logowania, jest podejrzana. Na przykład lokalizacja nie była ostatnio widziana.
 
Wykrywanie to wymaga określonej konfiguracji łącznika danych Syslog: 

1. W przypadku kroku 5 w poprzedniej procedurze upewnij się, że zarówno **auth,** jak i **authpriv** są wybierane jako urządzenia do monitorowania. Zachowaj domyślne ustawienia opcji ważności, tak aby wszystkie były zaznaczone. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Udogodnienia wymagane do nietypowego wykrywania logowania SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Pozostaw wystarczająco dużo czasu na zebranie informacji syslog. Następnie przejdź do **usługi Azure Sentinel — dzienniki**i skopiuj i wklej następującą kwerendę:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    W razie potrzeby zmień **zakres czasu** i wybierz pozycję **Uruchom**.
    
    Jeśli wynikowa liczba wynosi zero, upewnij się, że konfiguracja łącznika i że monitorowane komputery mają pomyślną aktywność logowania dla okresu określonego dla zapytania.
    
    Jeśli wynikowa liczba jest większa niż zero, dane syslog jest odpowiedni dla nietypowego wykrywania logowania SSH. Włącz wykrywanie z**szablonów** > reguł **analytics** >  **(podgląd) Wykrywanie logowania anomalii SSH**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia lokalne Syslog z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)

