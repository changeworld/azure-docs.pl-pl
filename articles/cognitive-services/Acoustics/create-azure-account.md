---
title: Ustawienia konta usługi Azure Batch Akustyka projektu
titlesuffix: Azure Cognitive Services
description: Niniejszy instruktaż zawiera opis konfigurowania konta usługi Azure Batch do użytku z programem Project Akustyka Unity i Unreal integracji aparatu.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: db4f96ff7c355f3582966e4daa945f54a6e5b847
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616547"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Ustawienia konta usługi Azure Batch Akustyka projektu
Niniejszy instruktaż zawiera opis konfigurowania konta usługi Azure Batch do użytku z programem Project Akustyka Unity i Unreal integracji aparatu.

## <a name="get-an-azure-subscription"></a>Uzyskiwanie subskrypcji platformy Azure
[Subskrypcji platformy Azure](https://azure.microsoft.com/free/) jest wymagana przed rozpoczęciem konfigurowania kont usług Batch i Storage. Jeśli teraz rejestrujesz po raz pierwszy, platforma Azure udostępnia kilka ograniczonej czasowo bezpłatnymi zasobami i 200 USD środków.

## <a name="create-azure-batch-and-storage-accounts"></a>Tworzenie konta usługi Azure Batch i storage
Następnie postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/batch/batch-account-create-portal) do konfigurowania usługi Azure Batch i skojarzonych kont usługi Azure Storage.

Wybierz opcje domyślne dla kont usługi Batch i Storage:
  
  ![Zrzut ekranu usługi Azure Batch nowych kont Wyświetlanie domyślnych ustawień opcji](media/new-batch-account-create.png)

  ![Zrzut ekranu z usługi Azure Storage nowych kont Wyświetlanie domyślnych ustawień opcji](media/batch-storage-account-create.png)

Trwa kilka minut, aż platforma Azure wdrożyła kont. Zwróć uwagę na powiadomienie o ukończeniu w prawym górnym rogu portalu.
  
  ![Zrzut ekranu Azure kont wdrożone powiadomień](media/batch-accounts-deploy-notification.png)

Twoje konta powinno być teraz widoczny na pulpicie nawigacyjnym.
  
  ![Portal przedstawiający konto usługi Batch i Storage pulpitu nawigacyjnego w zrzucie ekranu systemu Azure](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfigurowanie Akustyka Tworzenie interfejsu użytkownika przy użyciu poświadczeń platformy Azure
Kliknij link konta usługi Batch na pulpicie nawigacyjnym, a następnie kliknij pozycję **klucze** łącze na stronie konta usługi Batch, aby Twoje poświadczenia dostępu.
  
  ![Zrzut ekranu z platformy Azure konto usługi Batch z linkiem do strony klucze wyróżnioną](media/batch-access-keys.png)

  ![Strona klucze konta zrzut ekranu usługi Azure Batch przy użyciu kluczy dostępu](media/batch-keys-info.png)

Kliknij pozycję **konta magazynu** łącze na stronie, aby dostęp do poświadczeń konta magazynu platformy Azure.
  
  ![Strona klucze konta zrzut ekranu usługi Azure Storage za pomocą kluczy dostępu](media/storage-keys-info.png)

Wprowadź te poświadczenia w [Unity tworzenie wtyczki](unity-baking.md) lub [Unreal tworzenie wtyczki](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Typy węzłów i pomoc techniczna w regionie
Akustyka projektu wymaga serii H i Fsv2 obliczenia zoptymalizowane węzły maszyny Wirtualnej platformy Azure, które mogą nie być obsługiwane we wszystkich regionach platformy Azure. Sprawdź, czy [tej tabeli](https://azure.microsoft.com/global-infrastructure/services) aby upewnić się, w przypadku pobrania odpowiedniej lokalizacji dla konta usługi Batch.
![Zrzut ekranu przedstawiający maszyn wirtualnych platformy Azure według regionów](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Uaktualnianie limitu przydziału
Konta usługi Azure Batch są aprowizowane konta tworzenia limit 20 rdzeni obliczeniowych. Warto zwiększyć ten limit, w celu skrócenia czasu na tworzenie, ponieważ można zrównoleglić Akustyka obciążenia na wielu węzłach, maksymalna liczba punktów sondy sceny. Możesz zażądać zwiększenia limitu przydziału, klikając **przydziału** łącze na stronie portalu usługi Azure Batch, a następnie klikając **zwiększyć limit przydziału żądań**:

![Zrzut ekranu przydział Azure strony](media/azure-quotas.png)

## <a name="next-steps"></a>Kolejne kroki
* Integrowanie wtyczki Akustyka projektu do Twojej [Unity](unity-integration.md) lub [Unreal](unreal-integration.md) projektu

