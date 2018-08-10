---
title: Konfigurowanie programu PowerShell do utworzenia maszyny Wirtualnej w portalu Marketplace | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące konfigurowania programu Azure PowerShell i używać go jako opcjonalny proces przepływać do tworzenia obrazów maszyn wirtualnych do wdrożenia i sprzedawać w portalu Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ms.openlocfilehash: bbcce5093d2bbd5326523063db7d0e565fe4de6d
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713639"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Konfigurowanie programu Azure PowerShell, aby utworzyć ofertę w portalu Azure Marketplace
Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania programu PowerShell w systemie Azure, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview). Proste podejście jest użycie metody certyfikat, którego pliki do pobrania, a następnie importuje służące do uwierzytelniania certyfikatu. Aby uzyskać dla nich potrzebnego certyfikatu, użyj **Get AzurePublishSettingsFile** polecenia cmdlet. Po wyświetleniu monitu, Zapisz plik. Aby zaimportować certyfikat do sesji programu PowerShell, należy użyć **AzurePublishSettingsFile importu** polecenia cmdlet.

Aby skonfigurować i przechowywać wspólne ustawienia subskrypcji Microsoft Azure dla sesji programu PowerShell, użyj **Set-AzureSubscription** i **Select-AzureSubscription** poleceń cmdlet:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Pierwsze polecenie powoduje skojarzenie domyślne konto magazynu z subskrypcji (wymagane w przypadku niektórych operacji inicjowania obsługi administracyjnej maszyny Wirtualnej).  Drugi sprawia, że subskrypcja obecnym (rozpoznany przez inne polecenia cmdlet).

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)
* [Tworzenie obrazu maszyny wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-creation.md)

