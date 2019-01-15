---
title: Konta magazynu w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć konto magazynu usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/14/2019
ms.author: mabrigg
ms.openlocfilehash: 66a6fa88956db40ced9ee3fd13a0f74585c528fc
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303211"
---
# <a name="storage-accounts-in-azure-stack"></a>Konta magazynu w usłudze Azure Stack
Konta magazynu zawierają usługi Blob i Table oraz unikatową przestrzeń nazw dla obiektów danych magazynu. Domyślnie dane na Twoim koncie są dostępne tylko dla Ciebie, tj. właściciela konta magazynu.

1. Na komputerze usługi Azure Stack POC Zaloguj się do `https://adminportal.local.azurestack.external` jako [administrator](azure-stack-connect-azure-stack.md), a następnie kliknij przycisk **+ Utwórz zasób** > **dane + magazyn**  >  **Konta magazynu**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. W **Tworzenie konta magazynu** bloku, wpisz nazwę konta magazynu. Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę, a następnie kliknij przycisk **Utwórz** można utworzyć konta magazynu.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Aby wyświetlić nowe konto magazynu, kliknij **wszystkie zasoby**, a następnie wyszukaj konta magazynu i kliknij jego nazwę.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Kolejne kroki
[Korzystanie z szablonów usługi Azure Resource Manager](user/azure-stack-arm-templates.md)

[Dowiedz się więcej o kontach magazynu Azure](../storage/common/storage-create-storage-account.md)

[Pobierz Przewodnik Weryfikacja magazynu zgodnego z platformą Azure usługa Azure Stack](https://aka.ms/azurestacktp1doc)
