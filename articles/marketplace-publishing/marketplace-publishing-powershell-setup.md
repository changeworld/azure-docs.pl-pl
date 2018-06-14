---
title: Konfigurowanie programu PowerShell do tworzenia maszyn wirtualnych dla witryny Marketplace | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące konfigurowania programu Azure PowerShell i używać go jako opcjonalny procesu przepływać do tworzenia obrazów maszyn wirtualnych do wdrożenia i sprzedawać w portalu Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29937403"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Konfigurowanie programu Azure PowerShell, aby utworzyć ofertę do portalu Azure Marketplace
Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania programu PowerShell w programie Azure, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Proste podejście jest przy użyciu metody certyfikatów, które pobiera i importuje certyfikatu wymagane do uwierzytelniania. Aby uzyskać potrzebny certyfikat, użyj **Get-AzurePublishSettingsFile** polecenia cmdlet. Po wyświetleniu monitu, Zapisz plik. Aby zaimportować certyfikat do sesji programu PowerShell, użyj **AzurePublishSettingsFile importu** polecenia cmdlet.

Aby skonfigurować i zapisać typowe ustawienia subskrypcji Microsoft Azure dla sesji programu PowerShell, użyj **AzureSubscription zestaw** i **AzureSubscription wybierz** poleceń cmdlet:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Pierwsze polecenie kojarzy domyślne konto magazynu z subskrypcji (wymagane dla niektórych operacji inicjowania obsługi administracyjnej maszyn wirtualnych).  Drugi sprawia, że subskrypcja obecną (rozpoznawane przez inne polecenia cmdlet).

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)
* [Tworzenie obrazu maszyny wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-creation.md)

