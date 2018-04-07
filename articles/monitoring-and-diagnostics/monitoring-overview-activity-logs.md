---
title: Omówienie dziennika aktywności platformy Azure | Dokumentacja firmy Microsoft
description: Poznaj dziennika aktywności platformy Azure i jak go używać do zrozumienia zdarzeń występujących w ramach Twojej subskrypcji platformy Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: 9768fd96b8023ac97d8c5711e0c02f2c147e28f6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorowanie aktywności subskrypcji z dziennika aktywności platformy Azure

**Dziennika aktywności platformy Azure** jest Dziennik subskrypcji, która zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. W tym zakresie danych z usługi Azure Resource Manager danych operacyjnych do aktualizacji na zdarzenia kondycji usługi. Dziennik aktywności była wcześniej znana jako "Dzienników inspekcji" lub "Operacyjne dzienniki", ponieważ zdarzenia płaszczyzny kontroli Raporty Kategoria administracyjna dla subskrypcji. Korzystając z dziennika aktywności, można określić ", co, która i kiedy" dla żadnego zapisu (PUT, POST, DELETE) podejmowaną w odniesieniu do zasobów w ramach subskrypcji. Można także zrozumienie stanu operacji i inne odpowiednie właściwości. Dziennik nie zawiera operacje odczytu (GET) lub operacji dla zasobów korzystających z klasycznego / modelu "RDFE".

![Vs Dzienniki aktywności innych typów dzienników ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Rysunek 1: Dzienniki aktywności vs innych typów dzienników

Dziennik aktywności różni się od [dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md). Dzienniki aktywności zawierają dane dotyczące operacji na zasobie z zewnątrz ("płaszczyzny sterowania"). Dzienniki diagnostyczne są emitowane przez zasób i podaj informacje na temat operacji zasobu ("płaszczyzna danych").

> [!WARNING]
> Dziennik aktywności platformy Azure jest przeznaczone głównie dla działania wykonywane w usłudze Azure Resource Manager. Nie śledzi zasobów przy użyciu modelu klasycznego/frontonu REDDOG. Niektóre typy zasobów Classic ma dostawcy zasobów serwera proxy w usłudze Azure Resource Manager (na przykład obszar Microsoft.ClassicCompute). Jeśli w interakcję z typem zasobu Classic za pośrednictwem usługi Azure Resource Manager przy użyciu tych dostawców zasobów serwera proxy, operacje pojawiają się w dzienniku aktywności. Jeśli w interakcję z typem zasobu Classic poza serwerów proxy usługi Azure Resource Manager, czynności użytkownika tylko są rejestrowane w dzienniku operacji. Dziennik operacji można przeglądać w oddzielnym części portalu.
>
>

Można pobrać zdarzenia z dziennika aktywności przy użyciu portalu Azure, interfejsu wiersza polecenia, poleceń cmdlet programu PowerShell i interfejsu API REST Monitor Azure.

> [!NOTE]
>  [Alerty nowszej](monitoring-overview-unified-alerts.md) oferuje udoskonalone środowisko, podczas tworzenia i zarządzania nimi aktywności logowania reguł alertów.  [Dowiedz się więcej](monitoring-activity-log-alerts-new-experience.md).

Umożliwia wyświetlenie poniższego klipu wideo wprowadzające dziennik aktywności.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]


## <a name="categories-in-the-activity-log"></a>Kategorie w dzienniku aktywności
Dziennik zawiera kilka kategorii danych. Aby uzyskać szczegółowe informacje na schematów z tych kategorii [znajduje się w artykule](monitoring-activity-log-schema.md). Należą do nich:
* **Administracyjne** — ta kategoria zawiera rekord wszystkich utworzyć, update, delete i akcji operacje wykonywane za pomocą Menedżera zasobów. Typy zdarzeń, które można zobaczyć w tej kategorii należą "Utwórz maszynę wirtualną" i "Usuń sieciową grupę zabezpieczeń" co akcję wykonywaną przez użytkownika lub aplikacji przy użyciu usługi Resource Manager ma formę operację określonego typu zasobu. W przypadku typu operacji zapisu, usuń lub działania, rekordy start i powodzenie lub niepowodzenie tej operacji są rejestrowane w kategorii administracyjnej. Kategoria administracyjna także wszystkie zmiany do kontroli dostępu opartej na rolach w ramach subskrypcji.
* **Kondycja usługi** — ta kategoria zawiera rekord określone zdarzenia kondycji usługi, które wystąpiły na platformie Azure. Przykładem typu zdarzenia, które widać w tej kategorii jest "Azure SQL w wschodnie stany USA występuje Przestój." Zdarzenia kondycji usługi są dostępne w pięciu odmian: wymagana akcja, wspierana odzyskiwania, zdarzenie, konserwacji, informacje lub zabezpieczeń i są wyświetlane tylko, jeśli zasób w subskrypcji, która może wpływać na zdarzenia.
* **Alert** — ta kategoria zawiera rekord wszystkich aktywacji Azure alertów. Przykładem typu zdarzenia, które widać w tej kategorii jest "procent użycia procesora CPU na myVM została ponad 80 dla ostatnich 5 minut." Z różnymi systemami Azure ma alertów koncepcji — można zdefiniować regułę jakiegoś i otrzymasz powiadomienie, gdy warunki reguły są zgodne. Zawsze Azure obsługiwanego typu alertu "aktywuje," lub warunki są spełnione, aby wygenerować powiadomienie, rekord aktywacji jest również przypisany do tej kategorii dziennik aktywności.
* **Funkcja automatycznego skalowania** — ta kategoria zawiera rekord wszystkie zdarzenia związane z operacji skalowania automatycznego aparatu oparte na wszystkie ustawienia skalowania automatycznego zdefiniowane w ramach subskrypcji. Przykładem typu zdarzenia, które widać w tej kategorii jest "Skalowania automatycznego skalowania w górę akcja nie powiodła się". Przy użyciu automatycznego skalowania, można automatycznie skalować w poziomie lub skalowanie liczby wystąpień w typie zasobów obsługiwanych na podstawie czasu dnia i/lub obciążenia () dane przy użyciu ustawienia skalowania automatycznego. Po spełnieniu warunków do skalowania w górę lub w dół, start i Zakończono powodzeniem lub niepowodzeniem zdarzenia są rejestrowane w tej kategorii.
* **Zalecenie** — ta kategoria zawiera zalecenia zdarzenia z określonych typów zasobów, takich jak witryny sieci web i serwerami programu SQL Server. Zdarzenia te oferują zalecenia dotyczące lepsze wykorzystanie zasobów. Jeśli masz zasoby, które Emituj zalecenia tylko odbierać zdarzenia tego typu.
* **Zabezpieczenia** — ta kategoria zawiera rekord wszystkie alerty wygenerowane przez Centrum zabezpieczeń Azure. Przykładem typu zdarzenia, które widać w tej kategorii jest "podejrzane podwójne rozszerzenie pliku wykonywane".
* **Zasady i kondycja zasobów** -tych kategorii nie zawierają żadnych zdarzeń; są one zarezerwowane do użytku w przyszłości.

## <a name="event-schema-per-category"></a>Schemat zdarzenia według kategorii
[Zobacz ten artykuł, aby zrozumieć schematu zdarzeń dziennika aktywności według kategorii.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Co należy zrobić z dziennika aktywności
Poniżej podano niektóre czynności, które można wykonywać z dziennika aktywności:

![Dziennik aktywności platformy Azure](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Zapytanie i wyświetlić je w **portalu Azure**.
* [Utwórz alert na zdarzenie dziennika aktywności.](monitoring-activity-log-alerts.md)
* [Do strumienia **Centrum zdarzeń** ](monitoring-stream-activity-logs-event-hubs.md) dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
* Przeanalizuj go za pomocą usługi Power BI [ **pakiet zawartości usługi Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Zapisać go do **konta magazynu** inspekcji archiwizacji lub ręcznie](monitoring-archive-activity-log.md). Można określić za pomocą czasu (w dniach) przechowywania **profilu dziennika**.
* Zapytanie go za pomocą polecenia Cmdlet programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Zapytanie dziennik aktywności w portalu Azure
W portalu Azure można wyświetlić dziennik aktywności w kilku miejscach:
* **Dziennik aktywności** dostępnej przez wyszukiwanie dziennik aktywności w obszarze **wszystkie usługi** w okienku nawigacji po lewej stronie.
* **Monitor** pojawia się domyślnie w okienku nawigacji po lewej stronie. Dziennik aktywności jest jedną sekcję Azure Monitor.
* Dowolnego zasobu **zasobów**, na przykład blok konfiguracji maszyny wirtualnej. Dziennik aktywności jest się w sekcji większość tych blokach zasobów i kliknięcie jej automatycznie filtruje zdarzenia do tych związanych z tym określonego zasobu.

W portalu Azure można filtrować dziennik aktywności przez te pola:
* TimeSpan — godzina rozpoczęcia i zakończenia zdarzenia.
* Kategoria — Kategoria zdarzenia, zgodnie z powyższym opisem.
* Subskrypcja — co najmniej jedną nazwę subskrypcji platformy Azure.
* Grupa zasobów — co najmniej jeden zasób grupuje się w tych subskrypcjach.
* Zasób (nazwa) — Nazwa określonego zasobu.
* Typ zasobu — Typ zasobu, na przykład Microsoft.Compute/virtualmachines.
* Nazwa operacji - nazwy operacji usługi Azure Resource Manager, na przykład Microsoft.SQL/servers/Write.
* Ważność — poziom ważności zdarzenia (krytyczny komunikat informacyjny, ostrzeżenie, błąd,).
* Zdarzenie inicjowane przez — "wywołującego" lub użytkownik, który wykonał działanie.
* Otwórz wyszukiwanie — jest to pole wyszukiwania tekstu Otwórz szuka tego ciągu we wszystkich pól w wszystkie zdarzenia.

Po zdefiniowaniu zestaw filtrów można zapisać go jako kwerendę, która jest zachowywane między sesjami, jeśli kiedykolwiek zajdzie potrzeba wykonania tego samego zapytania z tych filtrów ponownie w przyszłości. Zapytania można także przypiąć do pulpitu nawigacyjnego platformy Azure, aby zawsze śledzić na określone zdarzenia.

Klikając przycisk "Zastosuj" uruchamia kwerendy i Pokaż wszystkie zdarzenia dopasowania. Kliknięcie dowolnego zdarzenia na liście zawiera podsumowanie tego zdarzenia, a także pełne nieprzetworzone dane JSON tego zdarzenia.

Jeszcze więcej zasilania, możesz kliknąć **wyszukiwania dziennika** ikonę, która zawiera dane dziennika aktywności [rozwiązania analizy dziennika aktywności analizy dziennika](../log-analytics/log-analytics-activity.md). Blok dziennika aktywności umożliwia podstawowe filtru/przeglądania dzienników, ale analizy dzienników umożliwia przestawnego, zapytań i wizualizować dane w bardziej wydajny sposób.

## <a name="export-the-activity-log-with-a-log-profile"></a>Eksportuj Dziennik aktywności z profilem dziennika
A **profilu dziennika** kontroluje sposób eksportowania jest dziennik aktywności. Przy użyciu profilu dziennika, można skonfigurować:

* Gdzie mają być wysyłane dziennika aktywności (konta magazynu lub Event Hubs)
* Kategorie zdarzeń (Akcja zapisu, usuwanie i) powinny być przesyłane. *Różni się znaczenie "kategorii" w profilach dziennika i dziennik zdarzeń. W profilu dziennika "Kategorii" reprezentuje typ operacji (Akcja zapisu, usuwanie i). W przypadku dziennika aktywności właściwość "kategorii" reprezentuje źródło lub typ zdarzenia (na przykład administracji, ServiceHealth Alert i więcej).*
* Regiony (lokalizacja) powinny być wyeksportowane. Upewnij się, że obejmują "globalne", ponieważ wiele zdarzeń w dzienniku aktywności są zdarzenia globalne.
* Jak długo dziennika aktywności powinny być przechowywane na koncie magazynu.
    - Przechowywanie 0 oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni od 1 do 2147483647.
    - Jeśli zasady przechowywania są skonfigurowane, ale przechowywanie dzienniki na koncie magazynu jest wyłączone (na przykład, jeśli tylko są zaznaczone opcje usługi Event Hubs lub analizy dzienników), zasad przechowywania nie obowiązują.
    - Zasady przechowywania są zastosowane na dni, więc pod koniec dnia (UTC), dzienniki od dnia, która jest teraz poza przechowywania zasad są usuwane. Na przykład jeśli masz zasady przechowywania jeden dzień na początku dnia dzisiaj dzienniki na wczoraj zanim dzień zostaną usunięte.

Możesz użyć magazynu konta lub zdarzenia koncentratora przestrzeni nazw, która nie znajduje się w tej samej subskrypcji co emitowanie dzienników. Użytkownik, który konfiguruje ustawienie musi mieć odpowiedni dostęp RBAC do obu subskrypcji.

Te ustawienia można skonfigurować przy użyciu opcji "Export" w bloku dziennika aktywności w portalu. Ich można również skonfigurować programowo [przy użyciu interfejsu API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. Subskrypcja może mieć tylko jeden profil dziennika.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Konfigurowanie profilów dziennika przy użyciu portalu Azure
Możesz strumienia dziennika aktywności do Centrum zdarzeń lub przechowywać je w ramach konta magazynu przy użyciu opcji "Export" w portalu Azure.

1. Przejdź do **dziennik aktywności** za pomocą menu po lewej stronie portalu.

    ![Przejdź do dziennika aktywności w portalu](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Kliknij przycisk **wyeksportować** u góry bloku.

    ![Przycisk Eksportuj w portalu](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. W wyświetlonym bloku możesz wybrać:  
  * regiony, dla których chcesz wyeksportować zdarzeń
  * Konto magazynu, do której chcesz zapisywać zdarzenia
  * Liczba dni, aby zachować te zdarzenia w magazynie. Wartość 0 dni zachowuje dzienniki nieskończona.
  * Namespace magistrali usług, w którym chcesz Centrum zdarzeń należy utworzyć do przesyłania strumieniowego te zdarzenia.

     ![Eksport dziennika aktywności bloku](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Kliknij przycisk **zapisać** Aby zapisać te ustawienia. Ustawienia są natychmiastowo stosowane do subskrypcji.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Konfigurowanie profilów dziennika przy użyciu poleceń cmdlet programu Azure PowerShell

#### <a name="get-existing-log-profile"></a>Pobierz profil dziennika

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Dodawanie profilu dziennika

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| Name (Nazwa) |Yes |Nazwa profilu dziennika. |
| StorageAccountId |Nie |Identyfikator zasobu konta magazynu, do której ma zostać zapisany dziennik aktywności. |
| serviceBusRuleId |Nie |Identyfikator reguły magistrali usługi chcesz mieć centra zdarzeń utworzonych w przestrzeni nazw usługi Service Bus. Ciąg w formacie: `{service bus resource ID}/authorizationrules/{key name}`. |
| Lokalizacje |Yes |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. |
| retentionInDays |Yes |Liczba dni dla zdarzenia, które mają być przechowywane, od 1 do 2147483647. Wartość zero przechowuje dzienniki w nieskończoność (zawsze). |
| Kategorie |Nie |Rozdzielana przecinkami lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to zapisu, usuwania i akcji. |

#### <a name="remove-a-log-profile"></a>Usuń profil dziennika
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli-20"></a>Konfigurowanie profilów dziennika przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure

#### <a name="get-existing-log-profile"></a>Pobierz profil dziennika

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

`name` Właściwość powinna być nazwą profilu dziennika.

#### <a name="add-a-log-profile"></a>Dodawanie profilu dziennika

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Aby wyświetlić pełną dokumentację do tworzenia profilu monitora z poziomu interfejsu wiersza polecenia, zobacz [Dokumentacja poleceń interfejsu wiersza polecenia](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Usuń profil dziennika

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o dziennik aktywności (dawniej dzienników inspekcji)](../azure-resource-manager/resource-group-audit.md)
* [Strumień dziennika aktywności platformy Azure do usługi Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
