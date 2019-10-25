---
title: AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0772446b0259b12d3f76b2020eeed4c9c2de1119
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882216"
---
# <a name="azcopy"></a>azcopy

AzCopy to narzędzie wiersza polecenia, które przenosi dane do i z usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

Ogólny format poleceń to: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Aby zgłosić problemy lub dowiedzieć się więcej na temat narzędzia, zobacz [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="options"></a>Opcje

**--Cap-MB/s UInt32**   Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**-h,--pomoc** Pomoc dla AzCopy
      
**--Typ danych wyjściowych**  Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text". (domyślny "tekst")

## <a name="see-also"></a>Zobacz także

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)
- [AzCopy](storage-ref-azcopy-bench.md)
- [kopia AzCopy](storage-ref-azcopy-copy.md)
- [AzCopy doc](storage-ref-azcopy-doc.md)
- [koperta AzCopy](storage-ref-azcopy-env.md)
- [zadania AzCopy](storage-ref-azcopy-jobs.md)
- [czyszczenie zadań AzCopy](storage-ref-azcopy-jobs-clean.md)
- [Lista zadań AzCopy](storage-ref-azcopy-jobs-list.md)
- [AzCopy zadania usuwania](storage-ref-azcopy-jobs-remove.md)
- [wznowienie zadań AzCopy](storage-ref-azcopy-jobs-resume.md)
- [Pokaż zadania AzCopy](storage-ref-azcopy-jobs-show.md)
- [Lista AzCopy](storage-ref-azcopy-list.md)
- [AzCopy logowanie](storage-ref-azcopy-login.md)
- [AzCopy Wyloguj](storage-ref-azcopy-logout.md)
- [AzCopy](storage-ref-azcopy-make.md)
- [AzCopy Usuń](storage-ref-azcopy-remove.md)
- [AzCopy synchronizacji](storage-ref-azcopy-sync.md)
