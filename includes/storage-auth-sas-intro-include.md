---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011937"
---
Sygnatura dostępu współdzielonego umożliwia przyznanie ograniczonego dostępu do kontenerów i obiektów BLOB na koncie magazynu. Podczas tworzenia sygnatury dostępu współdzielonego należy określić jego ograniczenia, w tym zasoby usługi Azure Storage, do których klient może uzyskać dostęp, jakie mają uprawnienia do tych zasobów oraz czas ważności sygnatury dostępu współdzielonego.

Każde sygnatura dostępu współdzielonego jest podpisana przy użyciu klucza. Sygnaturę dostępu współdzielonego można podpisać na jeden z dwóch sposobów:

- Z kluczem utworzonym przy użyciu poświadczeń Azure Active Directory (Azure AD). Sygnatura dostępu współdzielonego z poświadczeniami usługi Azure AD jest delegowana przez *użytkownika* .
- Za pomocą klucza konta magazynu. Sygnatura dostępu współdzielonego *usługi* i sygnatura dostępu współdzielonego *konta* są podpisane przy użyciu klucza konta magazynu.
