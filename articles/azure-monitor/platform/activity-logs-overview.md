---
title: Przegląd dziennika aktywności platformy Azure
description: Aby dowiedzieć się o dzienniku aktywności platformy Azure i jak go używać do zrozumienia zdarzeń występujących w ramach subskrypcji platformy Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 94465e95dbf5f2eb381c124349bf8fda6622a6c2
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650295"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorowanie aktywności subskrypcji z dziennika aktywności platformy Azure

**Dziennika aktywności platformy Azure** jest Dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. W tym zakresie danych z usługi Azure Resource Manager danych operacyjnych do aktualizacji dla zdarzeń kondycji usługi. Dziennik aktywności była wcześniej znana jako "Dzienniki inspekcji" lub "Operacyjne dzienniki" od zdarzeń kategorii administracyjnej raporty płaszczyznę kontroli dla subskrypcji. Przy użyciu dziennika aktywności, można określić "co, kto i kiedy" dla dowolnego zapisu (PUT, POST, DELETE) wykonywanych na zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. Dziennik aktywności nie obejmują operacji odczytu (GET) ani operacji dotyczących zasobów, które używają klasycznego / modelu "RDFE".

![Dzienniki aktywności w programie vs innych typów dzienników](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)

Rysunek 1: Dzienniki aktywności w programie vs innych typów dzienników

Dziennik aktywności różni się od [dzienniki diagnostyczne](diagnostic-logs-overview.md). Dzienniki aktywności udostępniają dane dotyczące operacji dla zasobu z zewnątrz ("płaszczyzna kontroli"). Dzienniki diagnostyczne są emitowane przez zasób i podaj informacje o działaniu tego zasobu ("płaszczyzny danych").

> [!WARNING]
> Dziennik aktywności platformy Azure jest przede wszystkim dla działań, które wystąpią w usłudze Azure Resource Manager. Żądania nie Śledź zasobów przy użyciu modelu klasyczną/frontonu REDDOG. Niektóre typy zasobów klasycznego mają dostawcy zasobów serwera proxy w usłudze Azure Resource Manager (na przykład Microsoft.ClassicCompute). Jeśli kontaktujesz się z typem zasobu klasycznym za pośrednictwem usługi Azure Resource Manager przy użyciu tych dostawców zasobów serwera proxy, operacje są wyświetlane w dzienniku aktywności. Gdy wchodzisz w interakcję z typem zasobu Classic poza serwery proxy usługi Azure Resource Manager, akcji tylko są rejestrowane w dzienniku operacji. Dziennik operacji można przeglądać w osobnej sekcji portalu.
>
>

Możesz pobrać zdarzenia z dziennika aktywności przy użyciu witryny Azure portal, interfejsu wiersza polecenia, w przypadku poleceń cmdlet programu PowerShell i interfejsu API REST usługi Azure Monitor.

> [!NOTE]
> [Nowszych alertów](../../azure-monitor/platform/alerts-overview.md) oferują udoskonalone środowisko tworzenia i zarządzania działaniami po zalogowaniu się reguły alertów.  [Dowiedz się więcej](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="categories-in-the-activity-log"></a>Kategorie w dzienniku aktywności
Dziennik aktywności zawiera kilka kategorii danych. Aby uzyskać szczegółowe informacje o wypełniana z tych kategorii [znajduje się w artykule](../../azure-monitor/platform/activity-log-schema.md). Należą do nich:
* **Administracyjne** — ta kategoria zawiera rekord wszystkich tworzenia, aktualizowania, usuwania i akcji operacje wykonywane przy użyciu usługi Resource Manager. Typy zdarzeń, które powinny zostać wyświetlone tej kategorii należą "Tworzenie maszyny wirtualnej" i "Usuń sieciową grupę zabezpieczeń" każdej akcji podjętej przez użytkownika lub aplikacji przy użyciu usługi Resource Manager ma formę operacji na określonego typu zasobu. W przypadku typu operacji zapisu, usuń lub akcję, rekordy początkowego i powodzenie lub niepowodzenie tej operacji są rejestrowane w kategorii administracyjnej. Kategoria administracyjna także wszelkie zmiany do kontroli dostępu opartej na rolach w ramach subskrypcji.
* **Kondycja usługi** — ta kategoria zawiera rekord wszelkie zdarzenia kondycji usługi, które miały miejsce w systemie Azure. Jest przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii, "SQL Azure w regionie wschodnie stany USA występuje Przestój." Zdarzenia usługi Service health są dostępne w pięciu odmian: Czynności, wspomagana odzyskiwania, zdarzenia, konserwacji, informacje lub zabezpieczeń i są wyświetlane tylko w przypadku zasobów w subskrypcji, która będzie mieć wpływ na zdarzenie.
* **Usługa Resource Health** — ta kategoria zawiera rekord wszystkie zdarzenia dotyczące kondycji zasobów, które wystąpiły z zasobami platformy Azure. Przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii jest "Zmieniono na niedostępny stan kondycji maszyny wirtualnej." Zdarzenia dotyczące kondycji zasobów może reprezentować jedną z czterech stanów kondycji: Dostępne, niedostępne, obniżonej wydajności i nieznany. Ponadto zdarzenia dotyczące kondycji zasobów można sklasyfikować jako są inicjowane platformy lub Zainicjowanie przez użytkownika.
* **Alert** — ta kategoria zawiera rekord wszystkich aktywacje alertów platformy Azure. Jest przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii, "procent użycia procesora CPU na myVM została ponad 80 dla ostatnich 5 minut." Z różnych systemów Azure ma koncepcji alertów — możesz zdefiniować regułę jakieś i Otrzymuj powiadomienie, gdy warunki zgodne z tą regułą. Każdym obsługiwanym typem alertów platformy Azure "aktywuje," lub warunki są spełnione, aby wygenerować powiadomienie, rekord aktywacji są również wypychane do tej kategorii dziennika aktywności.
* **Automatyczne skalowanie** — ta kategoria zawiera rekord wszystkie zdarzenia związane z działaniem aparat skalowania automatycznego na podstawie ustawień automatycznego skalowania, wszelkie zdefiniowane w ramach subskrypcji. Przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii jest "Skalowania automatycznego skalowania w górę akcja nie powiodła się". Przy użyciu skalowania automatycznego, możesz automatycznie skalować w poziomie lub skalowanie liczby wystąpień w obsługiwany typ zasobu na podstawie czasu dzień danych i/lub obciążenia (metryk) przy użyciu ustawienia automatycznego skalowania. Po spełnieniu warunków do skalowania w górę lub w dół, start i zakończyło się powodzeniem lub niepowodzeniem zdarzenia są rejestrowane w tej kategorii.
* **Zalecenie** — ta kategoria zawiera zdarzenia zalecenia z usługi Azure Advisor.
* **Zabezpieczenia** — ta kategoria zawiera rekord wszystkie alerty wygenerowane przez usługę Azure Security Center. Przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii są ""podejrzane podwójne rozszerzenie pliku wykonywane.
* **Zasady** — ta kategoria zawiera rekordy operacji wszystkie efekt działania wykonywane przez usługę Azure Policy. Przykłady typów zdarzeń, które powinny zostać wyświetlone tej kategorii inspekcji i Odmów. Każdej akcji podjętej przez zasady są modelowane jako operacja dotycząca zasobu.

## <a name="event-schema-per-category"></a>Schemat zdarzeń według kategorii

[Zobacz ten artykuł, aby zrozumieć schemat zdarzeń dziennika aktywności dla każdej kategorii.](../../azure-monitor/platform/activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Co można zrobić z dziennika aktywności

Oto kilka rzeczy, które można zrobić z dziennika aktywności:

![Dziennik aktywności platformy Azure](./media/activity-logs-overview/Activity_Log_Overview_v3.png)


* Zapytania i wyświetlić ją w **witryny Azure portal**.
* [Tworzenie alertów dotyczących zdarzenia dziennika aktywności.](../../azure-monitor/platform/activity-log-alerts.md)
* [Stream jego **Centrum zdarzeń** ](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowe, takie jak Power BI.
* Analizować je w usłudze Power BI przy użyciu [ **pakiet zawartości usługi Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Zapisać go w celu **konta magazynu** inspekcji archiwizacji lub ręcznie](../../azure-monitor/platform/archive-activity-log.md). Można określić przy użyciu czasu (w dniach) przechowywania **profilu dziennika**.
* Wykonuje zapytania za pomocą polecenia Cmdlet programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Zapytanie dziennika aktywności w witrynie Azure portal

W witrynie Azure portal można wyświetlić dziennik aktywności w kilku miejscach:
* **Dziennika aktywności** której będziesz mieć dostęp przez wyszukiwanie w dzienniku aktywności w **wszystkich usług** w okienku nawigacji po lewej stronie.
* **Monitor** pojawia się domyślnie w okienku nawigacji po lewej stronie. Dziennik aktywności jest jedną sekcję usługi Azure Monitor.
* Większość **zasobów**, na przykład blok konfiguracji dla maszyny wirtualnej. Dziennik aktywności jest sekcji na większości bloków zasobów i klikając je automatycznie filtruje zdarzenia, które do tych powiązanych z tym określonym zasobem.

W witrynie Azure portal można filtrować według tych pól dziennik aktywności:
* TimeSpan — godzina rozpoczęcia i zakończenia zdarzenia.
* Kategoria — Kategoria zdarzenia, zgodnie z powyższym opisem.
* Subskrypcja — jedną lub więcej nazw subskrypcji platformy Azure.
* Grupy zasobów — co najmniej jeden zasób grupy w ramach tych subskrypcji.
* Zasób (nazwa) — Nazwa określonego zasobu.
* Typ zasobu — Typ zasobu, na przykład Microsoft.Compute/virtualmachines.
* Nazwa operacji — nazwy operacji usługi Azure Resource Manager, na przykład Microsoft.SQL/servers/Write.
* Ważność — poziom ważności zdarzenia (komunikat o charakterze informacyjnym, ostrzeżenie, błąd krytyczny).
* Zdarzenie zainicjowane przez — "komputer wywołujący" lub użytkownik, który wykonał operację.
* Otwórz wyszukiwanie — jest to pole wyszukiwania tekstu Otwórz, wyszukująca ciąg we wszystkich polach we wszystkich przypadkach.

Po zdefiniowaniu zestaw filtrów kwerendy można przypiąć do pulpitu nawigacyjnego platformy Azure, aby zawsze nadzorować określonych zdarzeń.

Aby uzyskać jeszcze większe możliwości, możesz kliknąć **dzienniki** ikonę, która wyświetla dane dziennika aktywności w [zbieranie i analizowanie dzienników aktywności rozwiązania](../../azure-monitor/platform/collect-activity-logs.md). Blok dziennika aktywności oferuje środowisko podstawowa filtru/przeglądania dzienników, ale umożliwia funkcji dzienniki usługi Azure Monitor przestawić, zapytania i wizualizować dane w sposób bardziej wydajne.

## <a name="export-the-activity-log-with-a-log-profile"></a>Eksportuj Dziennik aktywności, za pomocą profilu dziennika

A **profilu dziennika** kontroluje sposób dziennik aktywności jest eksportowana. Przy użyciu profilu dziennika, można skonfigurować:

* Gdzie mają być wysyłane dziennika aktywności (konto magazynu lub Event Hubs)
* Powinny być przesyłane kategorie zdarzeń (zapis, usuwanie i akcji). *Różni się znaczenie "category" profile dziennika i zdarzenia dziennika aktywności. W profilu dziennika "Category" reprezentuje typ operacji (zapis, usuwanie i akcji). W zdarzenia dziennika aktywności właściwość "category" reprezentuje źródło lub typ zdarzenia (na przykład administracji, ServiceHealth, alerty i więcej).*
* Które regiony (lokalizacje) powinny być wyeksportowane. Upewnij się, że zawierają "global", ponieważ wiele zdarzeń w dzienniku aktywności są zdarzenia globalne.
* Jak długo dziennika aktywności powinny być przechowywane na koncie magazynu.
    - Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni z zakresu od 1 do 2147483647.
    - Jeśli ustawiono zasady przechowywania, ale przechowywania dzienników na koncie magazynu jest wyłączona (na przykład, jeśli tylko są zaznaczone opcje usługi Event Hubs lub usługi Log Analytics), zasad przechowywania przyniosło żadnego skutku.
    - Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu.

Możesz użyć magazynu konta lub event hub przestrzeni nazw, która nie znajduje się w tej samej subskrypcji co emitowane dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć odpowiedni dostęp RBAC do obu subskrypcji.

> [!NOTE]
>  Obecnie nie można zarchiwizować dane na koncie magazynu, który znajduje się za zabezpieczonej sieci wirtualnej.

> [!WARNING]
> Format danych dziennika w ramach konta magazynu zmieniony na JSON wierszy od 1 listopada 2018 r. [W tym artykule znajdziesz opis skutków tej zmiany oraz instrukcje aktualizacji narzędzi w celu zapewnienia obsługi nowego formatu.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md)
>
>

Te ustawienia można skonfigurować za pomocą opcji "Export" w bloku dziennika aktywności w portalu. Również mogą być konfigurowane programowo [przy użyciu interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), w przypadku poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. Subskrypcja może mieć tylko jeden profil dziennika.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Konfigurowanie profilów dziennika przy użyciu witryny Azure portal

Można przesyłać strumieniowo dziennik aktywności do Centrum zdarzeń lub przechowywać je w ramach konta magazynu przy użyciu opcji "Eksportuj do Centrum zdarzeń" w witrynie Azure portal.

1. Przejdź do **dziennika aktywności** za pomocą menu po lewej stronie portalu.

    ![Przejdź do dziennika aktywności w portalu](./media/activity-logs-overview/activity-logs-portal-navigate-v2.png)
2. Kliknij przycisk **Eksportuj do Centrum zdarzeń** znajdujący się u góry bloku.

    ![Przycisk Eksportuj w portalu](./media/activity-logs-overview/activity-logs-portal-export-v2.png)
3. W wyświetlonym bloku możesz wybrać:
   * regiony, dla których chcesz eksportowanie zdarzeń
   * Konto magazynu, do której chcesz zapisać zdarzenia
   * Liczba dni, aby zachować te zdarzenia w magazynie. Ustawienie wartości 0 dni, które zawsze zachowuje dzienniki.
   * Namespace usługi Service Bus, w którym chcesz Centrum zdarzeń do utworzenia tych zdarzeń do przesyłania strumieniowego.

     ![Eksportuj Dziennik aktywności bloku](./media/activity-logs-overview/activity-logs-portal-export-blade.png)
4. Kliknij przycisk **Zapisz** można zapisać tych ustawień. Ustawienia są natychmiastowo stosowane do subskrypcji.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Konfigurowanie profilów dziennika przy użyciu poleceń cmdlet programu Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

#### <a name="get-existing-log-profile"></a>Pobieranie istniejącego profilu dziennika

```powershell
Get-AzLogProfile
```

#### <a name="add-a-log-profile"></a>Dodawanie profilu dziennika

```powershell
Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| Name |Yes |Nazwa profilu dziennika. |
| StorageAccountId |Nie |Identyfikator zasobu konta magazynu, w którym można zapisać w dzienniku aktywności. |
| serviceBusRuleId |Nie |Identyfikator reguły usługi Service Bus dla przestrzeni nazw usługi Service Bus chcesz mieć centra zdarzeń utworzonych w. Jest to ciąg w formacie: `{service bus resource ID}/authorizationrules/{key name}`. |
| Lokalizacja |Yes |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. |
| RetentionInDays |Yes |Liczba dni dla zdarzenia, które powinny zostać zachowane, od 1 do 2147483647. Wartość zero zapisuje dzienniki na czas nieokreślony (nieskończoność). |
| Kategoria |Nie |Rozdzielana przecinkami lista kategorie zdarzeń, które powinny być zbierane. Możliwe wartości to zapis, usuwanie i akcji. |

#### <a name="remove-a-log-profile"></a>Usuwanie profilu dziennika

```powershell
Remove-AzLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Konfigurowanie profilów dziennika przy użyciu wiersza polecenia platformy Azure

#### <a name="get-existing-log-profile"></a>Pobieranie istniejącego profilu dziennika

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

`name` Właściwość powinna mieć nazwę profilu dziennika.

#### <a name="add-a-log-profile"></a>Dodawanie profilu dziennika

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Aby uzyskać pełną dokumentację dotyczącą tworzenia profilu monitorowanie za pomocą interfejsu wiersza polecenia, zobacz [odniesienie do polecenia interfejsu wiersza polecenia](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Usuwanie profilu dziennika

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności (dawniej Audit Logs)](../../azure-resource-manager/resource-group-audit.md)
* [Stream dziennika aktywności platformy Azure do usługi Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)
