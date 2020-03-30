---
title: Poproś o zwiększenie przydziału i uzyskaj wsparcie
description: Jak utworzyć żądanie pomocy technicznej w witrynie Azure portal dla usługi Azure Synapse Analytics. Zwiększenie przydziału żądania lub uzyskaj obsługę rozwiązywania problemów.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350894"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Żądania zwiększenia przydziału i uzyskania pomocy technicznej dla usługi Azure Synapse Analytics

W tym artykule opisano sposób przesyłania biletu pomocy technicznej w witrynie Azure portal dla usługi Azure Synapse Analytics. Ten proces umożliwia żądanie zwiększenia przydziału lub przesłanie żądania pomocy technicznej dla zespołu pomocy technicznej.

## <a name="create-a-support-ticket"></a>Tworzenie biletu pomocy technicznej

Skorzystaj z poniższych kroków, aby utworzyć nowe żądanie pomocy technicznej z witryny Azure portal dla usługi Azure Synapse Analytics.

1. W menu [portalu platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + pomoc **techniczna**.

   ![Łącze Pomoc + pomoc](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. W **pomocy + pomocy ,** wybierz nowe żądanie pomocy **.**

    ![Tworzenie nowego żądania pomocy technicznej](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Przejrzyj [swój plan pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Pomoc techniczna związana z **rozliczeniami, limitami przydziałów i zarządzaniem subskrypcjami** jest dostępna na wszystkich poziomach pomocy technicznej.
   * **Obsługa poprawek** jest świadczona za pośrednictwem pomocy technicznej [dla deweloperów,](https://azure.microsoft.com/support/plans/developer/) [standardów,](https://azure.microsoft.com/support/plans/standard/) [professional direct](https://azure.microsoft.com/support/plans/prodirect/)lub [premier.](https://azure.microsoft.com/support/plans/premier/) Naprawa w razie awarii dotyczy problemów napotykanych przez klientów podczas korzystania z platformy Azure, jeśli istnieje uzasadnione podejrzenie, że problem leży po stronie firmy Microsoft.
   * **Wsparcie dla deweloperów** i **usługi doradcze** są dostępne na poziomach pomocy technicznej [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) i [Premium](https://azure.microsoft.com/support/plans/premier/).

   Jeśli masz plan pomocy technicznej Premier, możesz również zgłosić problemy z usługą Azure Synapse Analytics w [portalu internetowym Microsoft Premier](https://premier.microsoft.com/). Zobacz [plany pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) aby dowiedzieć się więcej o różnych planach pomocy technicznej, w tym o zakresie, czasie odpowiedzi, cenach itp.  Aby uzyskać często zadawane pytania dotyczące pomocy technicznej platformy Azure, zobacz [Często zadawane pytania dotyczące pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/faq/)

1. W przypadku **typu problem**wybierz odpowiedni typ problemu. Aby uzyskać informacje na o problemach z przerwami, wybierz opcję **Techniczne**. W przypadku żądań zwiększenia przydziału wybierz pozycję **Limity usług i subskrypcji (przydziały).**

   ![Wybieranie typu problemu](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Poniżej dalsza część artykułu Koncentruje się na żądaniach zwiększenia przydziału. Ale można również wybrać **techniczne** tutaj dla żądań pomocy technicznej rozwiązywania problemów. W przypadku **wybrania**opcji Techniczne zostanie wyświetlona prośba o podanie podsumowania, a następnie zidentyfikowanie typu problemu, wybierając **pozycję Wybierz typ problemu**. Mogą pojawić się rozwiązania ułatwiające rozwiązanie problemu. Jeśli przedstawione rozwiązania nie rozwiążą problemu, wybierz **przycisk Dalej:Szczegóły** i wypełnij formularz, aby przesłać zgłoszenie pomocy technicznej.

1. W przypadku żądań zwiększenia przydziału wybierz pozycję **Azure Synapse Analytics** dla **typu przydziału**. Następnie wybierz **przycisk Dalej: Rozwiązania >>**.

   ![Wybieranie typu przydziału](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. W oknie **Szczegóły** wybierz pozycję **Podaj szczegóły,** aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typy żądań przydziału

Kliknięcie **przycisku Podaj szczegóły** powoduje wyświetlenie okna **Szczegóły przydziału,** które umożliwia dodawanie dodatkowych informacji. W poniższych sekcjach opisano różne żądania przydziału dostępne dla usługi Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Jednostki magazynu danych (DWU) na serwer

Poniższe kroki można wykonać, aby zażądać zwiększenia jednostek DWU na serwer.

1. Wybierz **jednostki hurtowni danych (DTU) dla typu przydziału serwera.**

1. Na liście **Zasób** wybierz zasób docelowy.

1. W polu **Żądanie przydziału** wprowadź nowy wymagany limit DWU.

   ![Szczegóły przydziału DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Serwery na subskrypcję

Skorzystaj z poniższych kroków, aby zażądać zwiększenia liczby serwerów na subskrypcję.

1. Wybierz typ **przydziału serwerów dla subskrypcji.**

1. Na liście **Lokalizacja** wybierz region platformy Azure do użycia. Przydział jest na subskrypcję w każdym regionie.

1. W polu **Nowy przydział** wprowadź żądanie dotyczące maksymalnej liczby serwerów w tym regionie.

   ![Szczegóły przydziału serwerów](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Włączanie dostępu do subskrypcji do regionu

Niektóre typy ofert nie są dostępne w każdym regionie. Może pojawić się błąd, taki jak następujący:

`This location is not available for subscription`

Jeśli subskrypcja wymaga dostępu w określonym regionie, użyj opcji **Inne żądanie przydziału,** aby zażądać dostępu. W swoim żądaniu określ szczegóły oferty i jednostki SKU, które chcesz włączyć dla regionu. Aby zapoznać się z opcjami oferty i jednostek SKU, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Inne szczegóły dotyczące kontyngentu](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Prześlij swoją prośbę

Ostatnim krokiem jest wypełnienie pozostałych szczegółów żądania pomocy technicznej bazy danych SQL. Następnie wybierz **przycisk Dalej: Przejrzyj + utwórz>>**, a po przejrzeniu szczegółów żądania kliknij przycisk **Utwórz,** aby przesłać żądanie.

## <a name="monitor-a-support-ticket"></a>Monitorowanie biletu pomocy technicznej

Po przesłaniu żądania pomocy technicznej zespół pomocy technicznej platformy Azure skontaktuje się z Tobą. Aby sprawdzić stan żądania i szczegółowe informacje, kliknij pozycję **Wszystkie żądania obsługi** na pulpicie nawigacyjnym.

![Sprawdzanie stanu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Inne zasoby

Można również połączyć się ze społecznością usługi Azure Synapse Analytics w [usłudze Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) lub za pośrednictwem [forum msdn usługi Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

