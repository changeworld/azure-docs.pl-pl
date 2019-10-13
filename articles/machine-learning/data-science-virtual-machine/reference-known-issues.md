---
title: Znane problemy & Rozwiązywanie problemów
titleSuffix: Azure Data Science Virtual  Machine
description: Zapoznaj się z listą znanych problemów, obejść i rozwiązywania problemów dotyczących usługi Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301921"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Znane problemy i rozwiązywanie problemów z usługą Azure Data Science Virtual Machine

Ten artykuł pomaga znaleźć i poprawić błędy lub błędy występujące podczas korzystania z usługi Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problemy z instalacją pakietu języka Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalowanie pakietów z zależnościami przerw w systemie Linux

Użyj `sudo pip install` zamiast `pip install` podczas instalowania pakietów.

## <a name="disk-encryption-issues"></a>Problemy z szyfrowaniem dysków

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Szyfrowanie dysków kończy się niepowodzeniem na Ubuntu DSVM

Azure Disk Encryption (ADE) nie jest obecnie obsługiwany w Ubuntu DSVM. Aby obejść ten krok, należy rozważyć skonfigurowanie [szyfrowania usługi Azure Storage za pomocą kluczy zarządzanych przez klienta](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Narzędzie jest wyłączone

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Funkcja Hyper-V nie działa w systemie Windows DSVM

Jest to oczekiwane zachowanie, tak jak w przypadku wydajności rozruchu wyłączono niektóre usługi. Aby ponownie włączyć, Otwórz pasek wyszukiwania w DSVM systemu Windows, wpisz "usługi", a następnie ustaw wszystkie usługi funkcji Hyper-V na "ręczne" i ustaw "Automatyczne zarządzanie maszynami wirtualnymi funkcji Hyper-V".

Końcowy ekran powinien wyglądać następująco:

   ![Włączanie funkcji Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

