---
title: Ustawienia akustyczne dla projektu Azure Batch konta
titlesuffix: Azure Cognitive Services
description: W tym temacie opisano sposób konfigurowania konta Azure Batch do użycia z mechanizmami akustycznymi projektu i integracją aparatu Unreal.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855080"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Ustawienia akustyczne dla projektu Azure Batch konta
W tym temacie opisano sposób konfigurowania konta Azure Batch do użycia z mechanizmami akustycznymi projektu i integracją aparatu Unreal.

## <a name="get-an-azure-subscription"></a>Uzyskaj subskrypcję Azure
[Subskrypcja platformy Azure](https://azure.microsoft.com/free/) jest wymagana przed skonfigurowaniem kont usługi Batch i Storage. Jeśli logujesz się po raz pierwszy, platforma Azure udostępnia kilka bezpłatnych i $200 środków na korzystanie z usług.

## <a name="create-azure-batch-and-storage-accounts"></a>Tworzenie kont Azure Batch i magazynu
Następnie postępuj zgodnie z poniższymi [instrukcjami](https://docs.microsoft.com/azure/batch/batch-account-create-portal) , aby skonfigurować Azure Batch i powiązane konta usługi Azure Storage.

Wybierz opcje domyślne dla konta usługi Batch i magazynu:
  
  ![Zrzut ekranu przedstawiający opcje Azure Batch nowe konta z ustawieniami domyślnymi](media/new-batch-account-create.png)

  ![Zrzut ekranu przedstawiający opcje nowych kont usługi Azure Storage z ustawieniami domyślnymi](media/batch-storage-account-create.png)

Wdrożenie kont przez platformę Azure może potrwać kilka minut. Wyszukaj powiadomienie o ukończeniu w prawym górnym rogu portalu.
  
  ![Zrzut ekranu przedstawiający powiadomienie dotyczące wdrożonych kont platformy Azure](media/batch-accounts-deploy-notification.png)

Twoje konta powinny być teraz widoczne na pulpicie nawigacyjnym.
  
  ![Zrzut ekranu przedstawiający pulpit nawigacyjny Azure Portal, w którym jest wyświetlana partia i konto magazynu](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfiguracja interfejsu użytkownika tworzenie akustycznego przy użyciu poświadczeń platformy Azure
Kliknij link konto wsadowe na pulpicie nawigacyjnym, a następnie kliknij link **klucze** na stronie konto w usłudze Batch, aby uzyskać dostęp do poświadczeń.
  
  ![Zrzut ekranu konta Azure Batch z wyróżnioną stroną link do kluczy](media/batch-access-keys.png)

  ![Zrzut ekranu przedstawiający stronę kluczy konta Azure Batch z kluczami dostępu](media/batch-keys-info.png)

Kliknij link **konto magazynu** na stronie, aby uzyskać dostęp do poświadczeń konta usługi Azure Storage.
  
  ![Zrzut ekranu strony kluczy konta usługi Azure Storage z kluczami dostępu](media/storage-keys-info.png)

Wprowadź te poświadczenia w dodatku [Unity tworzenie](unity-baking.md) lub [wtyczki Tworzenie Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Obsługa typów węzłów i regionów
W przypadku maszyn wirtualnych z systemem Azure, które mogą nie być obsługiwane we wszystkich regionach świadczenia usługi Azure, muszą być zgodne z seriami Fsv2. Sprawdź [tę tabelę](https://azure.microsoft.com/global-infrastructure/services) , aby upewnić się, że wybierasz odpowiednią lokalizację dla konta usługi Batch.
![Zrzut ekranu przedstawiający Virtual Machines platformy Azure według regionu](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Uaktualnianie limitu przydziału
Konta Azure Batch są inicjowane przy tworzeniu konta z limitem 20 rdzeni obliczeniowych. Możemy chcieć zwiększyć ten limit w krótszym czasie tworzenie, ponieważ można zrównoleglanie obciążenie akustyczne w wielu węzłach, aż do liczby punktów sondy w scenie. Aby poprosić o zwiększenie limitu przydziału, kliknij link **Limit** przydziału na stronie portalu Azure Batch, a następnie kliknij pozycję **Zwiększ przydział limitu przydziału**:

![Zrzut ekranu strony przydziału platformy Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Następne kroki
* Integruj wtyczkę akustyczną projektu z projektem [Unity](unity-integration.md) lub [Unreal](unreal-integration.md)

