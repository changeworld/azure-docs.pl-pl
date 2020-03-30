---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371784"
---
Sygnatura dostępu współdzielonego (SAS) umożliwia udzielenie ograniczonego dostępu do kontenerów i obiektów blob na koncie magazynu. Podczas tworzenia sygnatury dostępu Współdzielonego można określić jego ograniczenia, w tym zasoby usługi Azure Storage, do których klient może uzyskać dostęp, jakie uprawnienia mają w tych zasobach i jak długo jest prawidłowy sygnatury dostępu współdzielonego.

Każdy sas jest podpisany za pomocą klucza. Sygnatura dostępu Współdzielonego można podpisać na jeden z dwóch sposobów:

- Z kluczem utworzonym przy użyciu poświadczeń usługi Azure Active Directory (Azure AD). Sygnatury dostępu Współdzielonego podpisanego przy użyciu poświadczeń usługi Azure AD jest sygnaturą dostępu Współdzielonego *delegowania użytkowników.*
- Z kluczem konta magazynu. Zarówno *sygnatury dostępu Współdzielonego usługi,* jak i *sygnatury dostępu Współdzielonego konta* są podpisane przy za pomocą klucza konta magazynu.

Sygnatura dostępu Współdzielonego delegowania użytkowników oferuje najwyższej jakości zabezpieczenia sygnatury dostępu Współdzielonego podpisanego przy za pomocą klucza konta magazynu. Firma Microsoft zaleca używanie sygnatury dostępu Współdzielonego delegowania użytkowników, gdy jest to możliwe. Aby uzyskać więcej informacji, zobacz [Udzielanie ograniczonego dostępu do danych za pomocą sygnatur dostępu współdzielonego (SAS)](../articles/storage/common/storage-sas-overview.md).
