---
title: Autoryzowanie dostępu do usługi Azure Storage | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobów autoryzacji dostępu do magazynu Azure, w tym usługi Azure Active Directory, uwierzytelniania klucza współużytkowanego lub sygnatury dostępu współdzielonego.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 46bb332e28d7503e543ca3c99fa1099ef17f34c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660865"
---
# <a name="authorizing-access-to-azure-storage"></a>Autoryzowanie dostępu do magazynu Azure

Zawsze dostęp do danych na koncie magazynu, klient wysyła żądanie za pośrednictwem protokołu HTTP/HTTPS do magazynu Azure. Każde żądanie do bezpiecznego zasobu musi być autoryzowany, dzięki czemu usługa zapewnia, że klient ma uprawnienia wymagane do uzyskania dostępu do danych. Magazyn Azure oferuje tych opcji w celu autoryzowania dostępu do zabezpieczania zasobów:

- **Integracja usługi Active Directory (Azure AD) Azure (wersja zapoznawcza)** obiekty BLOB i kolejek. Kontrola dostępu oparta na rolach (RBAC) usługi Azure AD zapewnia precyzyjną kontrolę nad dostępem klientów do zasobów na koncie magazynu. Aby uzyskać więcej informacji, zobacz [uwierzytelniania żądań do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
- **Udostępniony klucz autoryzacji** obiektów blob, plików, kolejek i tabel. Klienta przy użyciu klucza wspólnego przekazuje nagłówek z każdym żądaniem, który jest podpisany przy użyciu klucza dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [autoryzacji z klucza wspólnego](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/).
- **Sygnatury dostępu współdzielonego** obiektów blob, plików, kolejek i tabel. Sygnatury dostępu współdzielonego (SAS) udzielany ograniczony dostęp delegowany do zasobów na koncie magazynu. Dodanie ograniczeń na interwał podpis jest nieprawidłowy lub uprawnienia, które przyznaje, zapewnia elastyczność w zarządzaniu dostępu. Aby uzyskać więcej informacji, zobacz [używanie udostępnionych sygnatur dostępu (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Publicznego anonimowy dostęp do odczytu** dla kontenerów i obiektów blob. Autoryzacji nie jest wymagane. Aby uzyskać więcej informacji, zobacz [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).  

Domyślnie wszystkie zasoby w usłudze Azure Storage są zabezpieczone i są dostępne tylko dla właściciela konta. Mimo że można użyć dowolnej z strategie autoryzacji opisanych powyżej, aby przyznać klientom dostęp do zasobów na koncie magazynu, firma Microsoft zaleca używanie programu Azure AD podczas maksymalne bezpieczeństwo i łatwość użycia. 



