---
title: Usunięcie dostawcy zasobów SQL Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć z wdrożenia stosu Azure dostawcy zasobów SQL.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294784"
---
# <a name="removing-the-mysql-resource-provider"></a>Usunięcie dostawcy zasobów MySQL  
Przed usunięciem dostawcy zasobów SQL, konieczne jest najpierw usunąć wszelkie zależności.

## <a name="remove-the-mysql-resource-provider"></a>Usuń dostawcę zasobów MySQL 

1. Sprawdź usunięto zależności istniejącego dostawcy zasobów SQL.

  > [!NOTE]
  > Odinstalowywanie dostawcy zasobów SQL będzie kontynuowana nawet wtedy, gdy zasoby zależne są aktualnie używa dostawcy zasobów. 
  
2. Upewnij się, że masz oryginalny pakiet wdrożeniowy, który został pobrany dla tej wersji karty dostawcy zasobów SQL.
3. Uruchom ponownie skrypt wdrażania, korzystając z następujących parametrów:
    - Użyj odinstalować parametru
    - Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego.
    - Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego.
    - Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, używane do wdrażania usługi Azure stosu.

## <a name="next-steps"></a>Kolejne kroki
[Oferta usługi aplikacji jako PaaS](azure-stack-app-service-overview.md)
