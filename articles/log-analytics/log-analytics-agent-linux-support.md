---
title: Rozwiązywanie problemów z agentem systemu Linux analizy dzienników platformy Azure | Dokumentacja firmy Microsoft
description: Opisz objawy, przyczyny i rozwiązania typowych problemów z agentem Log Analytics w systemie Linux.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: baa81a52d4b007cd690a2b01df642cd3775f7d6b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044140"
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Jak rozwiązywać problemy z agentem systemu Linux dla usługi Log Analytics

Ten artykuł zawiera pomoc w rozwiązywaniu błędów może wystąpić z agentem systemu Linux dla usługi Log Analytics i sugeruje możliwe rozwiązania, aby je rozwiązać.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problem: Nie można połączyć za pośrednictwem serwera proxy do usługi Log Analytics

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Nieprawidłowy serwer proxy określony zestawu dokumentacji podczas dołączania
* Usługa Log Analytics i Azure Automation — punkty końcowe usługi nie są na liście dozwolonych w centrum danych 

### <a name="resolutions"></a>Rozwiązania
1. Reonboard do usługi Log Analytics za pomocą agenta pakietu OMS dla systemu Linux przy użyciu następującego polecenia z opcją `-v` włączone. Dzięki temu pełne dane wyjściowe z agenta, łącząc się za pośrednictwem serwera proxy do usługi OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Zapoznaj się z sekcją [zaktualizować ustawień serwera proxy](log-analytics-agent-manage.md#update-proxy-settings) można sprawdzić zostało poprawnie skonfigurowane agenta do komunikowania się za pośrednictwem serwera proxy.    
* Sprawdź, czy na liście dozwolonych następujące punkty końcowe usługi Log Analytics:

    |Zasób agenta| Porty | Kierunek |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Dla ruchu przychodzącego i wychodzącego |  
    |*.oms.opinsights.azure.com | Port 443| Dla ruchu przychodzącego i wychodzącego |  
    |*.blob.core.windows.net | Port 443| Dla ruchu przychodzącego i wychodzącego |  
    |*.azure-automation.net | Port 443| Dla ruchu przychodzącego i wychodzącego | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Komunikat o błędzie 403 podczas próby dołączyć

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Data i godzina jest nieprawidłowa na serwerze z systemem Linux 
* Identyfikator obszaru roboczego i klucz obszaru roboczego, używane są niepoprawne

### <a name="resolution"></a>Rozwiązanie

1. Sprawdź czas na serwerze Linux z datą polecenia. Jeśli czas +/-15 minut od bieżącego czasu dołączania kończy się niepowodzeniem. Aby poprawne to zaktualizuj datę i/lub strefy czasowej serwera systemu Linux. 
2. Sprawdź, czy zainstalowano najnowszą wersję agenta pakietu OMS dla systemu Linux.  Najnowszą wersję teraz powiadamia, że możesz Jeśli czasowego powoduje błąd dołączania.
3. Reonboard przy użyciu poprawny identyfikator obszaru roboczego i klucz obszaru roboczego, postępując zgodnie z instrukcjami instalacji we wcześniejszej części tego tematu.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Zostanie wyświetlony 500 i 404 błąd w pliku dziennika bezpośrednio po dołączeniu
Jest to znany problem występujący w pierwszym przekazywania danych z systemem Linux do obszaru roboczego usługi Log Analytics. Nie dotyczy to danych wysłanego lub usługi.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Nie widać żadnych danych w witrynie Azure portal

### <a name="probable-causes"></a>Prawdopodobne przyczyny

- Dołączanie do usługi Log Analytics nie powiodło się.
- Połączenie z usługą Log Analytics jest zablokowany.
- Agent usługi OMS dla danych z systemem Linux jest wykonywana kopia zapasowa

### <a name="resolutions"></a>Rozwiązania
1. Sprawdź, jeśli dołączania do usługi Log Analytics zakończyło się pomyślnie, sprawdzając, jeśli istnieje następujący plik: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Za pomocą Reonboard `omsadmin.sh` instrukcje wiersza polecenia
3. Jeśli używasz serwera proxy, skorzystaj z procedury opisanej rozpoznawanie serwera proxy, podany wcześniej.
4. W niektórych przypadkach gdy Agent pakietu OMS dla systemu Linux nie może komunikować się z usługą, dane na agencie znajduje się w kolejce do rozmiaru buforu pełnej, czyli 50 MB. Należy ponownie uruchomić agenta pakietu OMS dla systemu Linux, uruchamiając następujące polecenie: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ten problem jest rozwiązany w 1.1.0-28 wersji agenta i nowszych wersjach.

