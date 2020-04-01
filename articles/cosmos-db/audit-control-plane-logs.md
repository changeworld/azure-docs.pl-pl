---
title: Jak przeprowadzić inspekcję operacji płaszczyzny sterowania usługi Azure Cosmos DB
description: Dowiedz się, jak przeprowadzić inspekcję operacji płaszczyzny sterowania, takich jak dodawanie regionu, aktualizowanie przepływności, przebłaję regionu, dodawanie sieci wirtualnej itp.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420293"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Jak przeprowadzić inspekcję operacji płaszczyzny sterowania usługi Azure Cosmos DB

Operacje płaszczyzny sterowania obejmują zmiany na koncie lub kontenerze usługi Azure Cosmos. Na przykład utwórz konto usługi Azure Cosmos, dodaj region, aktualizuj przepływność, przewijalnie w błąd regionu, dodaj sieć wirtualną itp. W tym artykule wyjaśniono, jak inspekcji operacji płaszczyzny sterowania w usłudze Azure Cosmos DB.

## <a name="disable-key-based-metadata-write-access"></a>Wyłączanie dostępu do zapisu metadanych opartych na kluczach
 
Przed inspekcją operacji płaszczyzny sterowania w usłudze Azure Cosmos DB należy wyłączyć dostęp do zapisu metadanych opartych na kluczach na swoim koncie. Gdy dostęp do zapisu metadanych opartych na kluczach jest wyłączony, klienci łączący się z kontem usługi Azure Cosmos za pośrednictwem kluczy kont nie mogą uzyskać dostępu do konta. Dostęp do zapisu można `disableKeyBasedMetadataWriteAccess` wyłączyć, ustawiając właściwość na true. Po ustawieniu tej właściwości zmiany w dowolnym zasobie mogą nastąpić od użytkownika z właściwą rolą kontroli dostępu opartą na rolach (RBAC) i tylko poświadczeniami. Aby dowiedzieć się więcej na temat ustawiania tej właściwości, zobacz [Zapobieganie zmianom z zestawu SDK.](role-based-access-control.md#preventing-changes-from-cosmos-sdk)

 Podczas wyłączania dostępu do zapisu metadanych należy wziąć pod uwagę następujące kwestie:

* Oceny i upewnij się, że aplikacje nie dokonują wywołań metadanych, które zmieniają powyższe zasoby (Na przykład tworzenie kolekcji, aktualizowanie przepływności, ...) przy użyciu sdk lub klucze konta.

* Obecnie portal Azure używa kluczy kont dla operacji metadanych i w związku z tym te operacje zostaną zablokowane. Alternatywnie należy użyć interfejsu wiersza polecenia platformy Azure, zestawów SDK lub wdrożeń szablonów Menedżera zasobów do wykonywania takich operacji.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Włączanie dzienników diagnostycznych dla operacji na płaszczyznach sterowania

Dzienniki diagnostyczne dla operacji płaszczyzny sterowania można włączyć przy użyciu witryny Azure portal. Aby włączyć rejestrowanie operacji na płaszczyźnie sterowania, należy wykonać następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do konta usługi Azure Cosmos.

1. Otwórz **okienko Ustawienia diagnostyczne,** podaj **nazwę** dzienników do utworzenia.

1. Wybierz **opcję ControlPlaneRequests** dla typu dziennika i wybierz opcję **Wyślij do analizy dzienników.**

Można również przechowywać dzienniki na koncie magazynu lub strumień do centrum zdarzeń. W tym artykule pokazano, jak wysyłać dzienniki do analizy dziennika, a następnie ich kwerendy. Po włączeniu trwa kilka minut, aby dzienniki diagnostyczne, aby wejść w życie. Wszystkie operacje płaszczyzny sterowania wykonywane po tym punkcie mogą być śledzone. Poniższy zrzut ekranu pokazuje, jak włączyć dzienniki płaszczyzny sterowania:

![Włącz rejestrowanie żądań płaszczyzny sterowania](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Wyświetlanie operacji płaszczyzny sterowania

Po włączeniu rejestrowania należy wykonać następujące czynności, aby wyśledzić operacje dla określonego konta:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. Otwórz kartę **Monitor** z nawigacji po lewej stronie, a następnie wybierz okienko **Dzienniki.** Otwiera interfejs użytkownika, w którym można łatwo uruchamiać kwerendy z tego określonego konta w zakresie. Uruchom następującą kwerendę, aby wyświetlić dzienniki płaszczyzny sterowania:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Następujące zrzuty ekranu przechwytują dzienniki po dodaniu sieci wirtualnej do konta usługi Azure Cosmos:

![Płaszczyzna sterowania rejestruje po dodaniu sieci wirtualnej](./media/audit-control-plane-logs/add-ip-filter-logs.png)

Następujące zrzuty ekranu przechwytują dzienniki, gdy przepustowość tabeli Cassandra jest aktualizowana:

![Rejestruje płaszczyznę sterowania po zaktualizowaniu przepływności](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identyfikowanie tożsamości skojarzonej z określoną operacją

Jeśli chcesz debugować dalej, można zidentyfikować określoną operację w **dzienniku działania** przy użyciu identyfikatora działania lub sygnatury czasowej operacji. Sygnatura czasowa jest używana dla niektórych klientów Menedżera zasobów, gdzie identyfikator działania nie jest jawnie przekazywany. Dziennik działania zawiera szczegółowe informacje o tożsamości, z którą operacja została zainicjowana. Poniższy zrzut ekranu pokazuje, jak używać identyfikatora działania i znaleźć operacje skojarzone z nim w dzienniku aktywności:

![Użyj identyfikatora działania i znajdź operacje](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>Następne kroki

* [Poznaj usługę Azure Monitor dla usługi Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitorowanie i debugowanie za pomocą metryk w usłudze Azure Cosmos DB](use-metrics.md)