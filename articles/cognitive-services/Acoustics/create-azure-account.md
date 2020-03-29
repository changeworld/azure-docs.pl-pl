---
title: Konfiguracja konta wsadowego usługi Project Acoustics Azure
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano konfigurowanie konta usługi Azure Batch do użytku z integracją aparatu Project Acoustics Unity i Unreal.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855080"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Konfiguracja konta wsadowego usługi Project Acoustics Azure
W tym artykule opisano konfigurowanie konta usługi Azure Batch do użytku z integracją aparatu Project Acoustics Unity i Unreal.

## <a name="get-an-azure-subscription"></a>Uzyskiwanie subskrypcji platformy Azure
[Subskrypcja platformy Azure](https://azure.microsoft.com/free/) jest wymagana przed skonfigurowaniem kont usługi wsadowej i magazynowej. Jeśli rejestrujesz się po raz pierwszy, platforma Azure zapewnia kilka ograniczonych czasowo bezpłatnych zasobów i środki w wysokości 200 USD.

## <a name="create-azure-batch-and-storage-accounts"></a>Tworzenie kont usługi Azure Batch i storage
Następnie postępuj zgodnie z [tymi instrukcjami,](https://docs.microsoft.com/azure/batch/batch-account-create-portal) aby skonfigurować usługi Azure Batch i skojarzone konta usługi Azure Storage.

Wybierz opcje domyślne dla kont usługi Batch i Storage:
  
  ![Zrzut ekranu przedstawiający nowe opcje kont usługi Azure Batch z ustawieniami domyślnymi](media/new-batch-account-create.png)

  ![Zrzut ekranu przedstawiający nowe opcje kont usługi Azure Storage z ustawieniami domyślnymi](media/batch-storage-account-create.png)

Wdrożenie kont na platformie Azure zajmuje kilka minut. Poszukaj powiadomienia o zakończeniu w prawym górnym rogu portalu.
  
  ![Zrzut ekranu przedstawiający wdrożone powiadomienie o kontach platformy Azure](media/batch-accounts-deploy-notification.png)

Twoje konta powinny być teraz widoczne na pulpicie nawigacyjnym.
  
  ![Zrzut ekranu przedstawiający pulpit nawigacyjny portalu azure przedstawiający konto usługi Batch i Storage](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfigurowanie akustyki do pieczenia interfejsu użytkownika przy użyciu poświadczeń platformy Azure
Kliknij łącze Konto usługi Batch na pulpicie nawigacyjnym, a następnie kliknij **łącze Klucze** na stronie Konto usługi Batch, aby uzyskać dostęp do poświadczeń.
  
  ![Zrzut ekranu przedstawiający konto usługi Azure Batch z wyróżnioną łączem do strony Keys](media/batch-access-keys.png)

  ![Zrzut ekranu przedstawiający stronę kluczy kont usługi Azure Batch z kluczami dostępu](media/batch-keys-info.png)

Kliknij **łącze Konto magazynu** na stronie, aby uzyskać dostęp do poświadczeń konta usługi Azure Storage.
  
  ![Zrzut ekranu przedstawiający stronę kluczy kont usługi Azure Storage z kluczami dostępu](media/storage-keys-info.png)

Wprowadź te poświadczenia w [wtyczce Unity piec](unity-baking.md) lub [Unreal piec plugin](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Typy węzłów i obsługa regionów
Akustyka projektu wymaga fsv2- i H-series zoptymalizowane węzły maszyn wirtualnych platformy Azure, które mogą nie być obsługiwane we wszystkich regionach platformy Azure. Sprawdź [tę tabelę,](https://azure.microsoft.com/global-infrastructure/services) aby upewnić się, że wybierasz odpowiednią lokalizację dla swojego konta usługi Batch.
![Zrzut ekranu przedstawiający maszyny wirtualne platformy Azure według regionów](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Uaktualnianie przydziału
Konta usługi Azure Batch są aprowidyzowane przy tworzeniu konta z limitem 20 rdzeni obliczeniowych. Możemy chcieć zwiększyć ten limit, aby przyspieszyć czas pieczenia, ponieważ można zrównoleglić obciążenia akustyki w wielu węzłach, do liczby punktów sondy w scenie. Możesz zażądać zwiększenia przydziału, klikając **łącze Przydział** na stronie portalu usługi Azure Batch, a następnie klikając opcję Zwiększ **przydział żądania:**

![Zrzut ekranu przedstawiający stronę Przydziału platformy Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Następne kroki
* Zintegruj wtyczkę Project Acoustics ze swoim projektem [Unity](unity-integration.md) lub [Unreal](unreal-integration.md)

