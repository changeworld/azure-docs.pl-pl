---
title: Zabezpieczanie klastra na platformie Azure Eksplorator danych
description: W tym artykule opisano sposób zabezpieczania klastra na platformie Azure Eksplorator danych w Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: fbc5b18de093b2c91b17fa310c08a5b02b113a22
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406524"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Zabezpieczanie klastra na platformie Azure Eksplorator danych

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Zapewnia szyfrowanie woluminów systemu operacyjnego i dysków danych maszyn wirtualnych klastra. Integruje się ona również z usługą [Azure Key Vault](/azure/key-vault/) , która ułatwia kontrolowanie kluczy szyfrowania dysków i wpisów tajnych oraz zarządzanie nimi i gwarantuje, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w czasie spoczynku w usłudze Azure Storage. Ustawienia zabezpieczeń klastra umożliwiają włączenie szyfrowania dysków w klastrze.
  
## <a name="enable-encryption-at-rest"></a>Włącz szyfrowanie w spoczynku
  
Włączenie [szyfrowania](/azure/security/azure-security-encryption-atrest) przechowywanego w klastrze zapewnia ochronę danych przechowywanych danych (w spoczynku). 

1. W Azure Portal przejdź do zasobu klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia** wybierz pozycję **zabezpieczenia**. 

    ![Włącz szyfrowanie w spoczynku](media/manage-cluster-security/security-encryption-at-rest.png)

1. W oknie **zabezpieczenia** wybierz pozycję **włączone** dla ustawienia zabezpieczenia **szyfrowania dysku** . 

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

[Sprawdzanie kondycji klastra](/azure/data-explorer/check-cluster-health)
