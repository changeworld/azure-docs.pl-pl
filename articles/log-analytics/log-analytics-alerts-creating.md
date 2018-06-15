---
title: Tworzenie alertów w OMS Log Analytics | Dokumentacja firmy Microsoft
description: Alerty w analizy dzienników zidentyfikować ważne informacje zawarte w repozytorium OMS i aktywne powiadamia użytkownika o problemy lub akcji, aby je poprawić.  W tym artykule opisano sposób tworzenia reguły alertu i szczegóły dla różnych działań podejmowanych przez nich.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: b692822660ab12f89b274cea75727fb808d673f8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31519583"
---
# <a name="working-with-alert-rules-in-log-analytics"></a>Praca z reguły alertów w analizy dzienników

> [!NOTE]
> W tym artykule opisano klasycznego alerty analizy dzienników, które są zarządzane w portalu OMS.  Alerty w analizy dzienników są [rozszerzaną na platformie Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  Po zakończeniu następnie będzie tworzyć i edytować reguły alertów w portalu Azure i Uwaga procedury przedstawione w tym artykule.


Alerty są tworzone przez reguły alertów, które automatycznie uruchamiają przeszukiwanie dzienników w regularnych odstępach czasu.  Jeśli wyników spełniających kryteria określonego tworzenia rekordu alertu.  Następnie reguła może automatycznie uruchomić jedną lub kilka akcji, aby aktywnie powiadomić użytkownika o alercie lub wywołać inny proces.   

W tym artykule opisano procesy można tworzyć i edytować reguły alertów za pomocą portalu OMS.  Aby uzyskać więcej informacji o różnych ustawieniach i sposobach wdrożyć logikę wymaganego, zobacz [opis alertów w analizy dzienników](log-analytics-alerts.md).


## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu

Aby utworzyć regułę alertu za pomocą portalu OMS, rozpoczyna się od utworzenia dziennika wyszukiwanie rekordów, które powinny wywoływać alert.  **Alert** przycisk będzie dostępny, można tworzyć i konfigurować reguły alertów.

>[!NOTE]
> Obecnie można tworzyć maksymalnie 250 reguły alertów w obszarze roboczym analizy dzienników. 

1. Na stronie Przegląd OMS kliknij **wyszukiwania dziennika**.
2. Utwórz nowe zapytanie wyszukiwania dziennika albo wybierz zapisany dziennik wyszukiwania. 
3. Kliknij opcję **Alert** u góry strony, aby otworzyć ekran **Dodaj regułę alertu**.
4. Skonfiguruj reguły alertu, korzystając z informacji w [szczegóły reguły alertów](#details-of-alert-rules) poniżej.
6. Kliknij przycisk **Zapisz**, aby zakończyć tworzenie reguły alertu.  Zostanie ona rozpoczęta natychmiast uruchomiona.


## <a name="edit-an-alert-rule"></a>Edytuj regułę alertu
Zostanie wyświetlona lista wszystkich reguł alertów w **alerty** menu analizy dzienników **ustawienia**.  

![Zarządzanie alertami](./media/log-analytics-alerts/configure.png)

1. W OMS konsoli wybierz **ustawienia** kafelka.
2. Wybierz **alerty**.

W tym widoku można wykonywać wiele akcji.

* Wyłączanie reguły wybierając **poza** obok niej.
* Edytuj regułę alertu, klikając ikonę ołówka obok niej.
* Usuń regułę alertu, klikając **X** ikona obok niej. 

## <a name="details-of-alert-rules"></a>Szczegóły reguły alertów
Podczas tworzenia lub edytowania reguły alertu w portalu OMS pracy z **Dodaj regułę alertu** lub **edytowanie reguły alertu** strony.  W poniższych tabelach opisano pól na ekranie.

![Reguła alertów](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Informacji o alertach
Są to ustawienia podstawowe reguły alertów i alertów, które tworzy.

| Właściwość | Opis |
|:--- |:---|
| Name (Nazwa) | Unikalną nazwę identyfikującą reguły alertów. Ta nazwa jest objęta alerty utworzony przez tę zasadę.  |
| Opis | Opcjonalny opis reguły alertu. |
| Ważność |Ważność alerty utworzone przez tę regułę. |

### <a name="search-query-and-time-window"></a>Wyszukiwanie zapytania i przedziału czasowego
Okno wyszukiwania zapytań i czas, która zwraca rekordy, które są oszacowywane, aby określić, jeśli wszystkie alerty powinny być tworzone.

| Właściwość | Opis |
|:--- |:---|
| Zapytanie wyszukiwania | To zapytanie, które będą uruchamiane.  Rekordów zwróconych przez to zapytanie będzie służyć do określenia, czy alert jest tworzony.<br><br>Wybierz **Użyj bieżącego zapytania wyszukiwania** Użyj bieżącego zapytania, lub wybierz istniejący zapisanego wyszukiwania z listy.  Składnia zapytania jest dostępne w polu tekstowym, w którym możesz ją zmodyfikować, jeśli to konieczne. |
| Przedział czasu |Określa przedział czasu dla zapytania.  Zapytanie zwraca tylko te rekordy, które zostały utworzone w ramach tego zakresu bieżącego czasu.  Może to być dowolna wartość od 5 minut do 24 godzin.  Należy większa lub równa częstotliwość alertów.  <br><br> Na przykład jeśli przedział czasu jest ustawiona na 60 minut, a uruchomieniu kwerendy, godzina 13:15 będzie, tylko rekordy między 12:15:00 a 13:15 będzie zwracaną. |

Podając przedział czasu dla reguły alertu, będzie wyświetlana liczba istniejących rekordów spełniających kryteria wyszukiwania dla przedział czasu.  To może pomóc w określeniu częstotliwość, która zapewni liczba wyników, których się oczekuje.

### <a name="schedule"></a>Harmonogram
Określa częstotliwość wykonywania kwerendy wyszukiwania.

| Właściwość | Opis |
|:--- |:---|
| Częstotliwość alertów | Określa, jak często mają być uruchamiane zapytania. Może być dowolną wartość z zakresu od 5 minut do 24 godzin. Powinna być równa lub mniejsza niż przedział czasu.  Jeśli wartość jest większa niż przedział czasu, istnieje ryzyko rekordów jest pominięte.<br><br>Rozważmy na przykład okno czasu 30 minut i częstotliwość 60 minut.  Jeśli zapytanie jest uruchomione 1:00, zwraca rekordów między 12:30 i 1:00 PM.  Przy następnym uruchom zapytanie to 2:00 po zwróci rekordów między 1:30 i 2:00.  Nigdy nie będzie można obliczyć wszystkie rekordy między 1:00 i 1:30. |


### <a name="generate-alert-based-on"></a>Generuj alert w oparciu o
Określa kryteria, które zostanie obliczone dla wyników zapytania wyszukiwania w celu ustalenia, czy można utworzyć alertu.  Te informacje będą różne w zależności od typu reguły alertu, który można wybrać.  Szczegółowe informacje można uzyskać dla typów innej reguły alertu z [opis alertów w analizy dzienników](log-analytics-alerts.md).

| Właściwość | Opis |
|:--- |:---|
| Pomijanie alertów | Po włączeniu funkcji pomijania alertów reguły akcje reguły są wyłączone dla określona długość czasu, po utworzeniu nowego alertu. Reguła jest nadal uruchomiona i utworzy rekordów alert po spełnieniu kryteriów. To zezwala na czas, aby rozwiązać ten problem, bez konieczności uruchamiania zduplikowane akcje. |

#### <a name="number-of-results-alert-rules"></a>Liczba wyników reguły alertów

| Właściwość | Opis |
|:--- |:---|
| Liczba wyników |Alert jest tworzony, jeśli jest to liczba rekordów zwróconych przez kwerendę **większe** lub **mniej niż** należy podać wartość.  |

#### <a name="metric-measurement-alert-rules"></a>Metryki pomiaru reguły alertów

| Właściwość | Opis |
|:--- |:---|
| Wartość agregacji | Wartość progową, która może przekraczać wartości łącznej w wynikach wziąć pod uwagę w przypadku naruszenia zabezpieczeń. |
| Wyzwalaj alert w oparciu o | Liczba naruszeń alertu ma zostać utworzony.  Można określić **łączna liczba naruszeń** dowolną kombinację naruszeń w wynikach można ustawić lub **kolejnych naruszeń** wymaganie, że naruszeń musi występować w kolejnych próbek. |

### <a name="actions"></a>Akcje
Reguły alertów zawsze spowoduje utworzenie [alertów rekordu](#alert-records) po ze osiągnięty jest próg.  Można również zdefiniować co najmniej jeden odpowiedzi do uruchomienia, takie jak wysyłanie wiadomości e-mail lub uruchamianie elementu runbook.



#### <a name="email-actions"></a>Akcje poczty e-mail
Akcje e-mail Wyślij wiadomość e-mail ze szczegółami alertu do co najmniej jednego adresata.

| Właściwość | Opis |
|:--- |:---|
| Powiadomienie e-mail |Określ **tak** Jeśli wiadomość e-mail do wysłania po wyzwoleniu alertu. |
| Temat |Podmiotu w wiadomości e-mail.  Nie można zmodyfikować treści wiadomości. |
| Adresaci |Adresy wszystkich adresatów wiadomości e-mail.  Jeśli określasz więcej niż jeden adres, oddziel je przy użyciu średnika (;). |

#### <a name="webhook-actions"></a>Akcje elementu Webhook
Akcje elementu Webhook umożliwiają wywołanie procesu zewnętrznego przez pojedyncze żądanie HTTP POST.

| Właściwość | Opis |
|:--- |:---|
| Webhook |Określ **tak** aby wywołać elementu webhook po wyzwoleniu alertu. |
| Adres URL elementu Webhook |Adres URL elementu webhook. |
| Uwzględnij niestandardowy ładunek JSON |Wybierz tę opcję, jeśli chcesz zastąpić domyślny ładunek niestandardowy ładunek. |
| Wprowadź niestandardowy ładunek JSON |Niestandardowy ładunek dla elementu webhook.  Zobacz poprzedniej sekcji, aby uzyskać szczegółowe informacje. |

#### <a name="runbook-actions"></a>Działania elementu Runbook
Działania elementu Runbook uruchamiania elementu runbook automatyzacji Azure. 

>[!NOTE]
> Musisz mieć zainstalowany w obszarze roboczym dla tej akcji włączyć rozwiązanie Automatyzacja. 


| Właściwość | Opis |
|:--- |:---|
| Element Runbook | Określ **tak** Jeśli chcesz uruchomić element runbook usługi Automatyzacja Azure, po wyzwoleniu alertu.  |
| Konto usługi Automation | Określa konto automatyzacji elementów runbook są wybierane w.  To konto akcji, który jest połączony z obszaru roboczego. |
| Wybierz element runbook | Wybierz element runbook, który ma być uruchamiany podczas tworzenia alertu. |
| Uruchom na | Wybierz **Azure** do uruchomienia elementu runbook w chmurze.  Wybierz **hybrydowy proces roboczy** do uruchomienia elementu runbook na agenta o [hybrydowy proces roboczy elementu Runbook](../automation/automation-hybrid-runbook-worker.md ) zainstalowane.  |




## <a name="next-steps"></a>Kolejne kroki
* Zainstaluj [rozwiązania zarządzania alertami](log-analytics-solution-alert-management.md) do analizowania alertach tworzonych w analizy dzienników oraz alertów zebranych z System Center Operations Manager (SCOM).
* Przeczytaj więcej na temat [dziennika wyszukiwania](log-analytics-log-searches.md) który generowania alertów.
* Zakończenie wskazówki dla [Konfigurowanie webook](log-analytics-alerts-webhooks.md) z reguły alertu.  
* Dowiedz się, jak napisać [elementy runbook automatyzacji Azure](https://azure.microsoft.com/documentation/services/automation) skorygować problemy zidentyfikowane przez alertów.

