---
title: Konfigurowanie kont platformy Azure dla Akustyka projektu
titlesuffix: Azure Cognitive Services
description: Postępuj zgodnie z tego przewodnika, niezbędne do pracy z Akustyka konfigurowania kont usługi Azure Batch i Storage.
services: cognitive-services
author: ashtat
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 638ea3e707352a95cb1fb407add365a83d11faa7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148646"
---
# <a name="create-an-azure-batch-account"></a>Tworzenie konta usługi Azure Batch
Postępuj zgodnie z tego przewodnika, niezbędne do pracy z Akustyka konfigurowania kont usługi Azure Batch i Storage. Aby dowiedzieć się, wtyczka Unity opracowany jako część Akustyka projektu, zobacz [co to jest Akustyka](what-is-acoustics.md). Aby dowiedzieć się, jak zastosować Akustyka w swoim projekcie aparatu Unity, zobacz [wprowadzenie](getting-started.md).  

## <a name="get-an-azure-subscription"></a>Uzyskiwanie subskrypcji platformy Azure
[Subskrypcji platformy Azure](https://azure.microsoft.com/free/) jest wymagana przed rozpoczęciem konfigurowania kont usług Batch i Storage. Jeśli teraz rejestrujesz po raz pierwszy, platforma Azure udostępnia kilka ograniczonej czasowo bezpłatnymi zasobami i 200 USD środków.

## <a name="create-azure-batch-and-storage-accounts"></a>Tworzenie konta usługi Azure Batch i storage
Następnie postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/batch/batch-account-create-portal) do konfigurowania usługi Azure Batch i skojarzonych kont usługi Azure Storage.

Wybierz opcje domyślne dla kont usługi Batch i Storage:
  
  ![Nowe konto usługi Batch](media/NewBatchAccountCreate.png)

  ![Nowe konto magazynu](media/BatchStorageAccountCreate.png)

Trwa kilka minut, aż platforma Azure wdrożyła kont. Zwróć uwagę na powiadomienie o ukończeniu w prawym górnym rogu portalu.
  
  ![Wdrożone kont](media/BatchAccountsDeployNotification.png)

Twoje konta powinno być teraz widoczny na pulpicie nawigacyjnym.
  
  ![Pulpit nawigacyjny portalu](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfigurowanie Akustyka Tworzenie interfejsu użytkownika przy użyciu poświadczeń platformy Azure
Kliknij link konta usługi Batch na pulpicie nawigacyjnym, a następnie kliknij pozycję **klucze** łącze na stronie konta usługi Batch, aby Twoje poświadczenia dostępu.
  
  ![Link do kluczy usługi Batch](media/BatchAccessKeys.png)

  ![Poświadczenia konta usługi Batch](media/BatchKeysInfo.png)

Kliknij pozycję **konta magazynu** łącze na stronie, aby dostęp do poświadczeń konta magazynu platformy Azure.
  
  ![Poświadczenia konta magazynu](media/StorageKeysInfo.png)

Wprowadź te poświadczenia w karcie Tworzenie, zgodnie z opisem w [Tworzenie interfejsu użytkownika wskazówki](bake-ui-walkthrough.md).

## <a name="node-types-and-region-support"></a>Typy węzłów i pomoc techniczna w regionie
Akustyka projektu wymaga, aby zasoby obliczeniowe serii F i H zoptymalizowane węzły maszyny Wirtualnej platformy Azure, które mogą nie być obsługiwane we wszystkich regionach platformy Azure. Sprawdź, czy [tej tabeli](https://azure.microsoft.com/global-infrastructure/services) aby upewnić się, w przypadku pobrania odpowiedniej lokalizacji dla konta usługi Batch. W tej serii H w tej chwili maszyny wirtualne są obsługiwane w regionie wschodnie stany USA, północno-środkowe stany USA, południowo-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia i Japonia Zachodnia.

## <a name="upgrading-your-quota"></a>Uaktualnianie limitu przydziału
Konta usługi Azure Batch są aprowizowane konta tworzenia limit 20 rdzeni obliczeniowych. Można zwiększyć ten limit, w celu skrócenia czasu na tworzenie, ponieważ można zrównoleglić Akustyka obciążenia na wielu węzłach, maksymalna liczba punktów sondy sceny. Możesz zażądać zwiększenia limitu przydziału, klikając **przydziału** łącze na stronie portalu usługi Azure Batch, a następnie klikając **zwiększyć limit przydziału żądań**:

![Zwiększenia limitu przydziału platformy Azure](media/azurequotas.png)

## <a name="next-steps"></a>Kolejne kroki
* Rozpoczynanie pracy [włączającego Akustyka w swoim projekcie aparatu Unity](getting-started.md)
* Zapoznaj się z [sceny próbki](sample-walkthrough.md)

