---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118071"
---
## <a name="about-the-user-delegation-sas"></a>Informacje o sygnaturze dostępu Współdzielonego delegowania użytkowników

Token sygnatury dostępu współdzielonego dostępu do kontenera lub obiektu blob może być zabezpieczony przy użyciu poświadczeń usługi Azure AD lub klucza konta. Sygnatury dostępu Współdzielonego zabezpieczone za pomocą poświadczeń usługi Azure AD jest nazywany sygnaturą dostępu Współdzielonego delegowania użytkowników, ponieważ token OAuth 2.0 używany do podpisywania sygnatury dostępu Współdzielonego jest wymagany w imieniu użytkownika.

Firma Microsoft zaleca używanie poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszego rozwiązania w zakresie zabezpieczeń, zamiast używania klucza konta, który można łatwiej zainfekować. Gdy projekt aplikacji wymaga podpisów dostępu współdzielonego, użyj poświadczeń usługi Azure AD, aby utworzyć sygnatury dostępu współdzielonego delegowania użytkowników dla najwyższego bezpieczeństwa. Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego delegowania użytkowników, zobacz [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Każdy klient, który posiada prawidłowy sygnatury dostępu Współdzielonego, może uzyskać dostęp do danych na koncie magazynu zgodnie z dozwolonym przez ten sygnatury dostępu Współdzielonego. Ważne jest, aby chronić sygnaturę dostępu Współdzielonego przed złośliwym lub niezamierzonym użyciem. Użyj dyskrecji w dystrybucji sygnatury dostępu Współdzielonego i mieć plan odwołania zagrożonego sygnatury dostępu Współdzielonego.

Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../articles/storage/common/storage-sas-overview.md)
