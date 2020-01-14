---
title: Zabezpieczanie klastra na platformie Azure Eksplorator danych
description: W tym artykule opisano sposób zabezpieczania klastra na platformie Azure Eksplorator danych w Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720348"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Zabezpieczanie klastra na platformie Azure Eksplorator danych — Azure Portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Zapewnia szyfrowanie woluminów systemu operacyjnego i dysków danych maszyn wirtualnych klastra. Integruje się ona również z [Azure Key Vault](/azure/key-vault/), co pozwala nam kontrolować klucze szyfrowania dysków i wpisy tajne oraz zarządzać nimi oraz upewnić się, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Włącz szyfrowanie w Azure Portal
  
Ustawienia zabezpieczeń klastra umożliwiają włączenie szyfrowania dysków w klastrze. Włączenie [szyfrowania](/azure/security/fundamentals/encryption-atrest) przechowywanego w klastrze zapewnia ochronę danych przechowywanych danych (w spoczynku). 

1. W Azure Portal przejdź do zasobu klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia** wybierz pozycję **zabezpieczenia**. 

    ![Włącz szyfrowanie w spoczynku](media/manage-cluster-security/security-encryption-at-rest.png)

1. W oknie **zabezpieczenia** wybierz pozycję **włączone** dla ustawienia zabezpieczenia **szyfrowania dysku** . 

1. Wybierz pozycję **Zapisz**.
 
> [!NOTE]
> Wybierz pozycję **wyłączone** , aby wyłączyć szyfrowanie po włączeniu.

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie kondycji klastra](/azure/data-explorer/check-cluster-health)
