---
title: Jak rozwiązywać problemy przy użyciu agenta usługi Log Analytics for Windows z | Dokumentacja firmy Microsoft
description: Opisz objawy, przyczyny i rozwiązania typowych problemów z agentem usługi Log Analytics w celu Windows w usłudze Azure Monitor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120112"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Jak rozwiązywać problemy przy użyciu agenta usługi Log Analytics dla Windows 

Ten artykuł zawiera pomocy Rozwiązywanie problemów z błędami, które mogą wystąpić przy użyciu agenta usługi Log Analytics dla Windows w usłudze Azure Monitor i sugeruje możliwe rozwiązania, aby je rozwiązać.

Jeśli żadna z powyższych czynności działa, następujących kanałów pomocy technicznej dostępne są również:

* Korzyści z pomocy technicznej klientom planu Premier mogą Otwórz żądanie obsługi z [Premier](https://premier.microsoft.com/).
* Klienci z umowami pomocy technicznej platformy Azure mogą otworzyć żądania pomocy technicznej [w witrynie Azure portal](https://manage.windowsazure.com/?getsupport=true).
* Odwiedź stronę Log Analytics opinii, aby Przegląd przesłane pomysły i usterek [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) lub nowy plik. 

## <a name="important-troubleshooting-sources"></a>Ważne źródeł rozwiązywania problemów

 Aby ułatwić rozwiązywanie problemów związanych z agentem usługi Log Analytics dla Windows, agent rejestruje zdarzenia w dzienniku zdarzeń Windows, w szczególności w obszarze *aplikacji i Menedżera Services\Operations*.  

## <a name="connectivity-issues"></a>Problemy z łącznością

Jeśli agent komunikuje się za pośrednictwem serwera proxy lub zapory, może to być ograniczenia w miejscu, co uniemożliwia komunikację z komputerem źródłowym a usługa Azure Monitor. Jeśli komunikacja jest blokowana, błędnej konfiguracji, rejestracji z obszarem roboczym może zakończyć się niepowodzeniem podczas próby zainstalowania agenta, skonfiguruj agenta po instalacji, aby zgłosić do dodatkowy obszar roboczy lub komunikacji agenta nie powiedzie się po pomyślnej rejestracji. W tej sekcji opisano metody służące do rozwiązywania tego rodzaju problem z agentem Windows. 

Sprawdź, czy zapora lub serwer proxy, jest skonfigurowane i umożliwiają następujących portów i adresów URL opisanych w poniższej tabeli. Ponadto upewnij się, że inspekcji HTTP nie jest włączona dla ruchu w sieci web jako bezpieczny kanał TLS między agentem i usługi Azure Monitor może zablokować.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Wychodzące|Yes |  
|*.oms.opinsights.azure.com |Port 443 |Wychodzące|Yes |  
|*.blob.core.windows.net |Port 443 |Wychodzące|Tak |  
|*.azure-automation.net |Port 443 |Wychodzące|Tak |  

Uzyskać zapory wymagane dla platformy Azure Government, zobacz [zarządzania platformy Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Istnieje kilka sposobów, możesz sprawdzić, jeśli agent komunikuje się pomyślnie z usługą Azure Monitor.

- Włącz [oceny usługi Azure Log Analytics Agent Health](../insights/solution-agenthealth.md) w obszarze roboczym. Na pulpicie nawigacyjnym kondycji agenta, należy wyświetlić **liczba nieodpowiadających agentów** kolumnę, aby szybko sprawdzić, czy agent jest wyświetlana.  

- Uruchom następujące zapytanie, aby potwierdzić, że agent wysyła pulsu do obszaru roboczego, który jest skonfigurowany do raportu. Zastąp <ComputerName> z rzeczywistą nazwę komputera.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Jeśli komputer pomyślnie komunikuje się z usługą, zapytanie powinno zwrócić wynik. Jeśli zapytanie nie zwróciło wynik, najpierw sprawdź, czy agent jest skonfigurowany do raportu na prawidłowym obszarem roboczym. Jeśli jest skonfigurowana prawidłowo, przejdź do kroku 3 i wyszukiwanie w dzienniku zdarzeń Windows, aby ustalić, czy agent jest rejestrowanie, z jakim problemem może być uniemożliwiające jego komunikacji z usługą Azure Monitor.

- Inną metodą, aby zidentyfikować problem z łącznością jest uruchamiając **TestCloudConnectivity** narzędzia. Narzędzie jest instalowany domyślnie z agentem w folderze *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. W wierszu polecenia z podwyższonym poziomem uprawnień przejdź do folderu, a następnie uruchom narzędzie. Narzędzie zwraca wyniki i wyróżnienia, gdy test zakończył się niepowodzeniem, (na przykład, jeśli był on powiązany z określonego portu/adres URL, który został zablokowany). 

    ![Wyniki wykonania narzędzie TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtr *programu Operations Manager* dziennika zdarzeń przez **źródła zdarzeń** - *modułów usługi kondycji*, *HealthService*, i *Łącznika usługi* i Filtruj według **poziom zdarzenia** *ostrzeżenie* i *błąd* Aby upewnić się, jeśli zapisane zdarzenia Poniższa tabela. Jeśli są one, przejrzyj kroki rozwiązania uwzględnione dla każdego zdarzenia możliwe.

    |Identyfikator zdarzenia |source |Opis |Rozwiązanie |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Usługa kondycji |Połączenie z usługą agenta nie powiodła się. |Ten błąd może wystąpić, gdy agent nie może komunikować się bezpośrednio lub za pośrednictwem serwera proxy i zapory serwera w usłudze Azure Monitor. Sprawdź ustawienia serwera proxy agenta lub że Zapora/serwera proxy sieci zezwala na ruch TCP z komputera z usługą.|
    |2138 |Modułów usługi kondycji |Serwer proxy wymaga uwierzytelniania |Skonfiguruj ustawienia serwera proxy agenta i określ nazwę użytkownika/hasło wymagane do uwierzytelniania przy użyciu serwera proxy. |
    |2129 |Modułów usługi kondycji |Nie powiodło się połączenie się/Niepowodzenie negocjacji protokołu SSL |Sprawdź ustawienia protokołu TCP/IP dla karty sieciowej i ustawień serwera proxy agenta.|
    |2127 |Modułów usługi kondycji |Błąd wysyłania danych odebrany kod błędu: |Jeśli występuje on tylko okresowo w trakcie dnia, można po prostu anomalii losowego, który można zignorować. Monitorowanie, aby zrozumieć, jak często zdarza się. Jeśli zdarza się to często w ciągu dnia, najpierw sprawdzić konfigurację sieci i ustawienia serwera proxy. Jeśli kod błędu HTTP 404 w opisie i jest agent próbuje wysłać dane do usługi po raz pierwszy, będzie ona zawierała 500 Błąd wewnętrzny kod błędu 404. 404 oznacza, że nie można odnaleźć, co oznacza, że nadal trwa aprowizowanie obszar przechowywania dla nowego obszaru roboczego. Na następne ponowienie próby będą pomyślnie zapisu danych do obszaru roboczego zgodnie z oczekiwaniami. Błąd HTTP 403 może wskazywać uprawnień lub poświadczeń problem. Ma więcej informacji, dołączone do błędu 403, aby pomóc w rozwiązaniu problemu.|
    |4000 |Łącznik usługi |Rozpoznawanie nazw DNS nie powiodło się |Komputer nie można rozpoznać adresu internetowego używanego podczas wysyłania danych do usługi. Może to być ustawień programu rozpoznawania nazw DNS na komputerze, ustawienia serwera proxy niepoprawny lub może być tymczasowy problem DNS u swojego dostawcy. Jeśli wystąpi okresowo, może być spowodowane przez przejściowy problem związany z siecią.|
    |4001 |Łącznik usługi |Połączenie z usługą nie powiodło się. |Ten błąd może wystąpić, gdy agent nie może komunikować się bezpośrednio lub za pośrednictwem serwera proxy i zapory serwera w usłudze Azure Monitor. Sprawdź ustawienia serwera proxy agenta lub że Zapora/serwera proxy sieci zezwala na ruch TCP z komputera z usługą.|
    |4002 |Łącznik usługi |Usługa zwróciła kod stanu HTTP 403 w odpowiedzi na kwerendę. Skontaktuj się z administratorem usługi kondycji usługi. Zapytanie zostanie ponowione później. |Ten błąd jest napisany w fazie wstępnej rejestracji agenta, a zostanie wyświetlony podobny do następującego adresu URL: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Błąd kodu oznacza 403 Zabronione i może być spowodowany błędnie identyfikator obszaru roboczego lub klucza lub data i godzina jest nieprawidłowa na tym komputerze. Jeśli czas +/-15 minut od bieżącego czasu dołączania kończy się niepowodzeniem. Aby rozwiązać ten problem, zaktualizuj datę i/lub strefy czasowej systemu Windows.|

## <a name="data-collection-issues"></a>Problemy z kolekcją danych

Po zainstalowaniu agenta i raporty skonfigurowany obszar roboczy lub z obszarów roboczych, może przestać odbieranie konfiguracji, zbieranie lub przekazywania wydajności, dzienniki lub inne dane do usługi w zależności od tego, co jest włączona i przeznaczone dla komputera. Jest to konieczne określić, czy:

- Jest określonego typu danych lub wszystkie dane, które nie jest dostępne w obszarze roboczym?
- Typ danych określony przez rozwiązanie lub określony jako część Konfiguracja zbierania danych obszaru roboczego?
- Jak wiele komputerów dotyczy problem? Jest to jeden lub wiele komputerów wysyłających zgłoszenia do obszaru roboczego?
- Była praca również zatrzymać o określonej porze dnia i jej nigdy nie było zebranych? 
- Zapytanie wyszukiwania w dzienniku, które są używane jest poprawny składniowo? 
- Agent nigdy nie otrzymała konfiguracji z usługi Azure Monitor?

Rozwiązywanie problemów z pierwszym krokiem jest ustalenie, jeśli komputer jest wysyłanie zdarzeń pulsu.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Jeżeli zapytanie zwraca wyniki, należy ustalić, czy określonego typu danych nie zebrane i przekazane do usługi. Może to być spowodowane przez agenta, nie odbiera zaktualizowanej konfiguracji od usługi lub inny symptom, uniemożliwiając normalnego działania agenta. Wykonaj poniższe kroki, aby kontynuować rozwiązywanie.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze, a następnie ponownie uruchom usługę agenta, wpisując `net stop healthservice && net start healthservice`.
2. Otwórz *programu Operations Manager* dziennika zdarzeń i wyszukaj **identyfikatorów zdarzeń** *7023 7024, 7025, 7028* i *1210* z **zdarzeń źródło** *HealthService*.  Te zdarzenia wskazują, agent pomyślnie otrzymuje konfiguracji z usługi Azure Monitor i aktywnie monitorowanych komputerów. Opis zdarzenia dla zdarzenia, które 1210 identyfikator zostaną również określone w ostatni wiersz wszystkie rozwiązania i szczegółowe informacje, które znajdują się w zakresie monitorowania na agencie.  

    ![Opis 1210 identyfikator zdarzenia](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Jeśli po kilku minutach nie widzisz oczekiwanych danych, w wynikach kwerendy lub wizualizacji, w zależności od Jeśli przeglądasz dane z rozwiązania lub szczegółowych informacji z *programu Operations Manager* dziennika zdarzeń, wyszukiwanie **zdarzeń źródła** *HealthService* i *modułów usługi kondycji* i Filtruj według **poziom zdarzenia** *ostrzeżenie* i *Błąd* aby upewnić się, jeśli został zapisany zdarzenia z poniższej tabeli.

    |Identyfikator zdarzenia |source |Opis |Rozwiązanie |
    |---------|-------|------------|
    |8000 |HealthService |To zdarzenie określą, jeśli przepływ pracy związanych z wydajnością, zdarzenia lub inny typ danych zebranych nie może przekazywać do usługi w celu pozyskiwania do obszaru roboczego. | Identyfikator zdarzenia 2136 ze źródła usługi kondycji są zapisywane wraz z tego zdarzenia i wskazać, czy agent nie może komunikować się z usługą, prawdopodobnie z powodu błędnej konfiguracji ustawień serwera proxy i uwierzytelniania, zaniku połączenia sieciowego lub Zapora sieciowa / Serwer proxy nie zezwala na ruch TCP z komputera z usługą.| 
    |10102 i 10103 |Modułów usługi kondycji |Przepływ pracy nie można rozpoznać źródła danych. |Może to występować, jeśli określony licznik wydajności lub wystąpienie nie istnieje na komputerze lub jest nieprawidłowo zdefiniowany w ustawieniach danych obszaru roboczego. Jeśli po użytkownik określił [licznika wydajności](data-sources-performance-counters.md#configuring-performance-counters)Sprawdź informacji o określonych obserwowanych poprawny format, a istnieje na komputerach docelowych. |
    |26002 |Modułów usługi kondycji |Przepływ pracy nie można rozpoznać źródła danych. |Może to wystąpić, jeśli nie istnieje w określonym dzienniku zdarzeń Windows na komputerze. Ten błąd można bezpiecznie zignorować Jeśli komputer nie oczekuje się do tego dziennika zdarzeń, w przeciwnym razie zarejestrowane, jeśli jest określony przez użytkownika mają [dziennika zdarzeń](data-sources-windows-events.md#configuring-windows-event-logs), sprawdź określone informacje są poprawne. |

