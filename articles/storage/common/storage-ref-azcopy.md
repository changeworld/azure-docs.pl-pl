---
title: AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195895"
---
# <a name="azcopy"></a>AzCopy

AzCopy to narzędzie wiersza polecenia, które przenosi dane do i z usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

Ogólny format poleceń to: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Aby zgłosić problemy lub dowiedzieć się więcej na temat narzędzia, [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)Zobacz.

## <a name="options"></a>Opcje

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|-h,--pomoc|Wyświetla zawartość pomocy dla AzCopy.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)
- [kopia AzCopy](storage-ref-azcopy-copy.md)
- [AzCopy doc](storage-ref-azcopy-doc.md)
- [koperta AzCopy](storage-ref-azcopy-env.md)
- [zadania AzCopy](storage-ref-azcopy-jobs.md)
- [Lista AzCopy](storage-ref-azcopy-list.md)
- [AzCopy logowanie](storage-ref-azcopy-login.md)
- [AzCopy Wyloguj](storage-ref-azcopy-logout.md)
- [AzCopy](storage-ref-azcopy-make.md)
- [AzCopy Usuń](storage-ref-azcopy-remove.md)
- [AzCopy synchronizacji](storage-ref-azcopy-sync.md)
