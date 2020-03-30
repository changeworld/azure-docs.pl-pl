---
title: 'Referencje: Znane problemy & rozwiązywaniu problemów'
titleSuffix: Azure Data Science Virtual  Machine
description: Pobierz listę znanych problemów, obejścia i rozwiązywania problemów dla maszyny wirtualnej usługi Azure Data Science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206524"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Znane problemy i rozwiązywanie problemów z maszyną wirtualną usługi Azure Data Science

Ten artykuł ułatwia znajdowanie i poprawianie błędów lub błędów, które mogą wystąpić podczas korzystania z maszyny wirtualnej do nauki o danych platformy Azure.

## <a name="python-package-installation-issues"></a>Problemy z instalacją pakietu Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalowanie pakietów z pip przerywa zależności na Linuksie

Użyj `sudo pip install` zamiast `pip install` podczas instalowania pakietów.

## <a name="disk-encryption-issues"></a>Problemy z szyfrowaniem dysków

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Szyfrowanie dysku kończy się niepowodzeniem na Ubuntu DSVM

Szyfrowanie dysków azure (ADE) nie jest obecnie obsługiwany na Ubuntu DSVM. Aby obejść ten problem, należy rozważyć skonfigurowanie [szyfrowania po stronie serwera dysków zarządzanych platformy Azure](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Narzędzie wydaje się wyłączone

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Funkcja Hyper-V nie działa w systemie Windows DSVM

Ten funkcja Hyper-V początkowo nie działa w systemie Windows jest oczekiwanym zachowaniem. Aby uzyskać wydajność rozruchu, wyłączyliśmy niektóre usługi. Aby włączyć funkcję Hyper-V:

1. Otwieranie paska wyszukiwania w systemie Windows DSVM
1. Wpisz "Usługi",
1. Ustaw wszystkie usługi Funkcji Hyper-V na "Ręczny"
1. Ustaw "Zarządzanie maszynami wirtualnymi funkcji Hyper-V" na "Automatyczne"

Twój końcowy ekran powinien wyglądać następująco:

   ![Włączanie funkcji Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

