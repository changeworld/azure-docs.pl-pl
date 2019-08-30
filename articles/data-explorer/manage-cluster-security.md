---
title: Zabezpieczanie klastra na platformie Azure Eksplorator danych
description: W tym artykule opisano sposób zabezpieczania klastra na platformie Azure Eksplorator danych w Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: c6f7e921886a6acdaa31d0f69f57119c339c0b8b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172592"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Zabezpieczanie klastra na platformie Azure Eksplorator danych

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Zapewnia szyfrowanie woluminów systemu operacyjnego i dysków danych maszyn wirtualnych klastra. Integruje się ona również z usługą [Azure Key Vault](/azure/key-vault/) , która pozwala nam kontrolować klucze szyfrowania dysków i wpisy tajne oraz zarządzać nimi, a także upewnić się, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w stanie spoczynku w usłudze Azure Storage. 

Ustawienia zabezpieczeń klastra umożliwiają włączenie szyfrowania dysków w klastrze.
  
## <a name="enable-encryption-at-rest"></a>Włącz szyfrowanie w spoczynku
  
Włączenie [szyfrowania](/azure/security/fundamentals/encryption-atrest) przechowywanego w klastrze zapewnia ochronę danych przechowywanych danych (w spoczynku). 

1. W Azure Portal przejdź do zasobu klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia** wybierz pozycję **zabezpieczenia**. 

    ![Włącz szyfrowanie w spoczynku](media/manage-cluster-security/security-encryption-at-rest.png)

1. W oknie **zabezpieczenia** wybierz pozycję **włączone** dla ustawienia zabezpieczenia **szyfrowania dysku** . 

1. Wybierz pozycję **Zapisz**.
 
> [!NOTE]
> Wybierz pozycję **wyłączone** , aby wyłączyć szyfrowanie po włączeniu.

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie kondycji klastra](/azure/data-explorer/check-cluster-health)
