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
ms.openlocfilehash: f3b9f6c27fb8d423350eac5d286c9859ad6fbd37
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104281"
---
## <a name="about-the-user-delegation-sas-preview"></a>Informacje o obszarze SAS delegowania użytkownika (wersja zapoznawcza)

Token sygnatury dostępu współdzielonego, który umożliwia dostęp do kontenera lub obiektu BLOB, może być zabezpieczony przy użyciu poświadczeń usługi Azure AD lub klucza konta. Sygnatura dostępu współdzielonego zabezpieczony przy użyciu poświadczeń usługi Azure AD nazywa się sygnaturą dostępu współdzielonego użytkownika, ponieważ token OAuth 2,0 używany do podpisywania sygnatury dostępu współdzielonego jest wymagany w imieniu użytkownika.

Firma Microsoft zaleca używanie poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszych rozwiązań w zakresie zabezpieczeń zamiast używania klucza konta, co może być bardziej łatwe. Gdy projekt aplikacji wymaga sygnatur dostępu współdzielonego, Użyj poświadczeń usługi Azure AD, aby utworzyć SYGNATURę czasową delegowania użytkowników w celu zapewnienia bezpieczeństwa wyższego poziomu. Aby uzyskać więcej informacji na temat delegowania SKOJARZEŃ użytkowników, zobacz [Tworzenie skojarzeń zabezpieczeń delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas).

> [!NOTE]
> Wersja zapoznawcza sygnatury dostępu współdzielonego użytkownika jest przeznaczona wyłącznie do użytku w trybie nieprodukcyjnym.

> [!CAUTION]
> Każdy klient, który dysponuje prawidłowym sygnaturą dostępu współdzielonego, może uzyskać dostęp do danych na koncie magazynu, zgodnie z dozwolonym przez ten SAS. Ważne jest, aby chronić sygnaturę dostępu współdzielonego przed złośliwym lub niezamierzonym użyciem. Używaj uznania w dystrybucji sygnatury dostępu współdzielonego i masz plan do odwoływania złamanej sygnatury dostępu współdzielonego.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../articles/storage/common/storage-sas-overview.md).
