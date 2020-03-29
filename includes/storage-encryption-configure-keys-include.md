---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895290"
---
Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych obiektów blob i plików.

Klucze zarządzane przez klienta muszą być przechowywane w magazynie kluczy platformy Azure. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Konto magazynu i magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą być w różnych subskrypcjach. Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage i zarządzania kluczami, zobacz [Szyfrowanie usługi Azure Storage w celu uzyskania danych w spoczynku.](../articles/storage/common/storage-service-encryption.md) Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
