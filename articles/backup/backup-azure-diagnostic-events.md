---
title: Korzystanie z ustawień diagnostycznych dla magazynów Recovery Services
description: Artykuł opisujący sposób używania starych i nowych zdarzeń diagnostycznych dla Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 7abf8873aafeb996476d818376057bfd8732d906
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583949"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Używanie ustawień diagnostyki dla magazynów usługi Recovery Services

Azure Backup wysyła zdarzenia diagnostyczne, które mogą być zbierane i używane na potrzeby analiz, alertów i raportów. 

Ustawienia diagnostyczne dla magazynu Recovery Services można skonfigurować za pośrednictwem Azure Portal, przechodząc do magazynu i klikając element menu **Ustawienia diagnostyczne** . Kliknięcie pozycji **+ Dodaj ustawienie diagnostyczne** umożliwia wysłanie co najmniej jednego zdarzenia diagnostycznego do konta magazynu, centrum zdarzeń lub obszaru roboczego log Analytics (La).

![Blok ustawień diagnostycznych](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Zdarzenia diagnostyki dostępne dla Azure Backup użytkowników

Azure Backup udostępnia następujące zdarzenia diagnostyczne, z których każdy zawiera szczegółowe dane w określonym zestawie artefaktów związanych z kopiami zapasowymi:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[Model danych dla zdarzeń diagnostyki Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Dane dla tych zdarzeń można wysłać do konta magazynu, obszaru roboczego LA lub centrum zdarzeń. Jeśli dane są wysyłane do obszaru roboczego LA, należy wybrać przełącznik **specyficzny dla zasobów** na ekranie **Ustawienia diagnostyki** (Zobacz więcej informacji w poniższych sekcjach).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Używanie ustawień diagnostycznych z Log Analytics (LA)

Dostosowanie do planu Log Analytics platformy Azure, Azure Backup teraz umożliwia wysyłanie danych diagnostycznych magazynu do dedykowanych tabel w celu utworzenia kopii zapasowej. Są one określane jako [tabele specyficzne dla zasobów](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Aby wysłać dane diagnostyczne magazynu do LA:

1.  Przejdź do magazynu, a następnie kliknij pozycję **Ustawienia diagnostyczne**. Kliknij pozycję **+ Dodaj ustawienie diagnostyczne**.
2.  Nadaj nazwę ustawieniu diagnostyki.
3.  Zaznacz pole wyboru **Wyślij do log Analytics** a następnie wybierz obszar roboczy log Analytics.
4.  Wybierz **pozycję zasób specyficzny** w przełączniku i sprawdź następujące sześć zdarzeń — **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**i **AddonAzureBackupStorage**.
5.  Kliknij przycisk **Zapisz**.

![Tryb specyficzny dla zasobów](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Po przeniesieniu danych do obszaru roboczego LA, dedykowane tabele dla każdego z tych zdarzeń są tworzone w obszarze roboczym. Możesz wykonywać zapytania dotyczące dowolnej z tych tabel bezpośrednio, a także w razie potrzeby przełączać się między tymi tabelami.

> [!IMPORTANT]
> Powyższe sześć zdarzeń, mianowicie, CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy i AddonAzureBackupStorage, są obsługiwane **tylko** w trybie specyficznym dla zasobów. **Należy pamiętać, że jeśli spróbujesz wysłać dane dla tych sześciu zdarzeń w trybie Diagnostyka Azure, żadne dane nie będą przepływać do obszaru roboczego LA.**

## <a name="legacy-event"></a>Starsze zdarzenie

Tradycyjnie wszystkie dane diagnostyczne powiązane z kopią zapasową magazynu zostały zawarte w pojedynczym zdarzeniu o nazwie "AzureBackupReport". Sześć zdarzeń opisanych powyżej to, w zasadzie, dekompozycja wszystkich danych zawartych w AzureBackupReport. 

Obecnie nadal obsługujemy zdarzenia AzureBackupReport w celu zapewnienia zgodności z poprzednimi wersjami, w przypadkach, gdy użytkownicy mają istniejące niestandardowe zapytania dotyczące tego zdarzenia, na przykład niestandardowe alerty dziennika, niestandardowe wizualizacje itp. Zalecamy jednak wybranie nowych zdarzeń dla wszystkich nowych ustawień diagnostycznych w magazynie, ponieważ sprawia to, że dane są znacznie łatwiejsze w pracy z kwerendami dziennika, co zapewnia lepszą możliwość odnajdywania schematów i ich struktury, co zwiększa wydajność w ramach obu pozyskań czasy oczekiwania i zapytania. Obsługa używania Diagnostyka Azure trybu zostanie ostatecznie przeprowadzona, a w związku z tym wybranie nowych zdarzeń może pomóc w uniknięciu złożonych migracji w późniejszym terminie.

Można utworzyć oddzielne ustawienia diagnostyki dla AzureBackupReport i sześciu nowych zdarzeń, dopóki nie przeprowadzono migracji wszystkich zapytań niestandardowych do korzystania z danych z nowych tabel. Na poniższym obrazie przedstawiono przykład magazynu mającego dwa ustawienia diagnostyczne. Pierwsze ustawienie o nazwie **Setting1** wysyła dane zdarzenia AzureBackupReport do obszaru roboczego La w trybie AzureDiagnostics. Drugie ustawienie o nazwie **Setting2** wysyła dane z sześciu nowych zdarzeń Azure Backup do obszaru roboczego La w trybie specyficznym dla zasobu.

![Dwa ustawienia](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Zdarzenie AzureBackupReport jest obsługiwane **tylko** w trybie Diagnostyka Azure. **Należy pamiętać, że jeśli spróbujesz wysłać dane dla tego zdarzenia w trybie specyficznym dla zasobów, żadne dane nie będą przepływać do obszaru roboczego LA.**

> [!NOTE]
> Przełącznik dla Diagnostyka Azure/zasobów jest wyświetlany tylko wtedy, gdy użytkownik sprawdzi **opcję Wyślij do log Analytics**. Aby wysłać dane do konta magazynu lub centrum zdarzeń, użytkownik może po prostu wybrać wymaganą lokalizację docelową i sprawdzić dowolne z żądanych zdarzeń bez dodatkowych danych wejściowych. Zaleca się, aby nie wybierać starszej wersji usługi Event AzureBackupReport, przechodząc do przodu.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Użytkownicy wysyłający zdarzenia Azure Site Recovery do Log Analytics (LA)

Zdarzenia Azure Backup i Azure Site Recovery są wysyłane z tego samego magazynu Recovery Services. Ponieważ Azure Site Recovery nie są obecnie dołączone do tabel specyficznych dla zasobów, użytkownicy chcący wysyłać zdarzenia Azure Site Recovery do LA są kierowani do używania **tylko** trybu Diagnostyka Azure (Zobacz obraz poniżej). **Wybranie trybu specyficznego dla zasobów dla zdarzeń Azure Site Recovery uniemożliwi wysyłanie wymaganych danych do obszaru roboczego La**.

![Zdarzenia Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Aby podsumować powyższe wszystkie szczegóły:

* Jeśli masz już skonfigurowaną program LA Diagnostics z Diagnostyka Azure i masz na niej zapisaną kwerendę niestandardową, Zachowaj to ustawienie **nienaruszone**, dopóki nie dokonasz migracji zapytań, aby używać danych z nowych zdarzeń.
* Jeśli chcesz również dołączyć do nowych tabel (zgodnie z zaleceniami), Utwórz **nowe** ustawienie diagnostyczne, wybierz pozycję **specyficzne dla zasobów** i wybierz sześć nowych zdarzeń zgodnie z powyższym opisem.
* Jeśli aktualnie wysyłasz zdarzenia Azure Site Recovery do warstwy LA, **nie** wybieraj trybu specyficznego dla zasobów dla tych zdarzeń, w przeciwnym razie dane dla tych zdarzeń nie będą przepływać do obszaru roboczego La. Zamiast tego Utwórz **dodatkowe ustawienie diagnostyczne**, wybierz **Diagnostyka Azure**i wybierz odpowiednie zdarzenia Azure Site Recovery.

Na poniższym obrazie przedstawiono przykład użytkownika z trzema ustawieniami diagnostycznymi dla magazynu. Pierwsze ustawienie o nazwie **Setting1** wysyła dane ze zdarzenia AzureBackupReport do obszaru roboczego La w trybie AzureDiagnostics. Drugie ustawienie o nazwie **Setting2** wysyła dane z sześciu nowych zdarzeń Azure Backup do obszaru roboczego La w trybie specyficznym dla zasobu. Trzecie ustawienie o nazwie **Setting3**wysyła dane ze zdarzeń Azure Site Recovery do obszaru roboczego La w trybie Diagnostyka Azure.

![Trzy ustawienia](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Następne kroki

[Poznaj model danych Log Analytics dla zdarzeń diagnostycznych](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
