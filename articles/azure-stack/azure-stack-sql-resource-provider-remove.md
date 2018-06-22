---
title: Usunięcie dostawcy zasobów SQL Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć dostawcę zasobów SQL z wdrożenia stosu Azure.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301902"
---
# <a name="remove-the-sql-resource-provider"></a>Usuń dostawcę zasobów SQL

Przed usunięciem dostawcy zasobów SQL, należy usunąć wszystkie zależności dostawcy. Należy również kopię pakiet wdrożeniowy, który został użyty do zainstalowania dostawcy zasobów.

## <a name="to-remove-the-sql-resource-provider"></a>Aby usunąć dostawcę zasobów SQL

1. Sprawdź, czy po usunięciu wszystkich istniejących zasobów dostawcy zależności SQL.

   > [!NOTE]
   > Odinstalowywanie dostawcy zasobów SQL będzie kontynuowana nawet wtedy, gdy zasoby zależne są aktualnie używa dostawcy zasobów.
  
2. Uzyskaj kopię SQL dostawcy zasobów binarnych, a następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego.

3. Otwórz okno konsoli programu PowerShell nowych z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym wyodrębniono pliki binarne dostawcy zasobów SQL.

4. Uruchom skrypt DeploySqlProvider.ps1, korzystając z następujących parametrów:

    - **Odinstaluj**. Usuwa dostawcy zasobów i wszystkie powiązane zasoby.
    - **PrivilegedEndpoint**. Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego.
    - **CloudAdminCredential**. Poświadczenia dla administratora chmury niezbędnych do uzyskania dostępu uprzywilejowanego punktu końcowego.
    - **AzCredential**. Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, które używane do wdrożenia usługi Azure stosu.

## <a name="next-steps"></a>Kolejne kroki

[Oferta usługi aplikacji jako PaaS](azure-stack-app-service-overview.md)
