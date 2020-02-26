---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597852"
---
Domyślnie standardowe udziały plików mogą obejmować maksymalnie 5 TiB, chociaż limit udostępniania można zwiększyć do 100 TiB. W tym celu należy włączyć funkcję *dużego udziału plików* na poziomie konta magazynu. Konta usługi Premium Storage (konta magazynu*FileStorage* ) nie mają flagi dużej funkcji udostępniania plików, ponieważ wszystkie udziały plików w warstwie Premium są już włączone do obsługi pełnej pojemności 100 TIB.

Duże udziały plików można włączyć tylko na lokalnie nadmiarowe lub nadmiarowe konta magazynu w warstwie Standardowa. Po włączeniu flagi dużej funkcji udostępniania plików nie można zmienić poziomu nadmiarowości na magazyn Geograficznie nadmiarowy lub geograficznie nadmiarowy.

Aby włączyć duże udziały plików na istniejącym koncie magazynu, przejdź do widoku **Konfiguracja** w spisie treści konta magazynu, a następnie Przełącz Rocker Przełącz duży udział plików na włączony:

![Zrzut ekranu przedstawiający Przełącznik Rocker o dużym udziale plików w Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Udziały plików 100 TiB można również włączyć za pomocą polecenia cmdlet programu PowerShell [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) i [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) .

Aby dowiedzieć się więcej na temat włączania dużych udziałów plików na nowych kontach magazynu, zobacz [Tworzenie udziału plików platformy Azure](../articles/storage/files/storage-how-to-create-file-share.md).