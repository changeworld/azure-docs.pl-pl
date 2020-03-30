---
title: Zabezpiecz swój klaster w Eksploratorze danych platformy Azure
description: W tym artykule opisano sposób zabezpieczania klastra w Eksploratorze danych platformy Azure w witrynie Azure portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720348"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Zabezpiecz swój klaster w Eksploratorze danych platformy Azure — witryna Azure portal

[Usługa Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomaga chronić i chronić dane w celu spełnienia zobowiązań dotyczących zabezpieczeń i zgodności organizacji. Zapewnia szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych klastra. Integruje się również z [usługą Azure Key Vault,](/azure/key-vault/)która umożliwia nam kontrolowanie kluczy szyfrowania dysku i wpisy tajne oraz zarządzanie nimi oraz zapewnia szyfrowanie wszystkich danych na dyskach maszyn wirtualnych. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Włączanie szyfrowania w spoczynku w witrynie Azure portal
  
Ustawienia zabezpieczeń klastra umożliwiają włączenie szyfrowania dysku w klastrze. Włączenie [szyfrowania w stanie spoczynku](/azure/security/fundamentals/encryption-atrest) w klastrze zapewnia ochronę danych przechowywanych (w stanie spoczynku). 

1. W witrynie Azure portal przejdź do zasobu klastra usługi Azure Data Explorer. W obszarze **pozycji Ustawienia** wybierz pozycję **Zabezpieczenia**. 

    ![Włączanie szyfrowania w spoczynku](media/manage-cluster-security/security-encryption-at-rest.png)

1. W oknie **Zabezpieczenia** wybierz pozycję **Włączone** dla ustawienia zabezpieczeń **szyfrowania dysku.** 

1. Wybierz **pozycję Zapisz**.
 
> [!NOTE]
> Wybierz **opcję Wyłączone,** aby wyłączyć szyfrowanie po jego włączeniu.

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie kondycji klastra](/azure/data-explorer/check-cluster-health)
