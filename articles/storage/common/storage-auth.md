---
title: Autoryzowanie dostępu do usługi Azure Storage | Dokumentacja firmy Microsoft
description: Poznaj różne sposoby autoryzowania dostępu do usługi Azure Storage, w tym usługi Azure Active Directory, uwierzytelnianie klucza współużytkowanego lub sygnatury dostępu współdzielonego.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9e8c9755c444ca7b81891f5f83164bc51aa694eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147063"
---
# <a name="authorizing-access-to-azure-storage"></a>Autoryzowanie dostępu do usługi Azure Storage

Każdorazowo, uzyskujesz dostęp do danych na koncie magazynu klienta żąda za pośrednictwem protokołu HTTP/HTTPS do usługi Azure Storage. Każde żądanie do bezpiecznych zasobów muszą być autoryzowane, aby usługa gwarantuje, że klient ma uprawnienia wymagane do uzyskania dostępu do danych. Usługa Azure Storage oferuje opcje autoryzacji dostępu można zabezpieczyć zasobów:

- **Integracja usługi Azure Active Directory (Azure AD)** dla kolejek i obiektów blob. Usługa Azure AD zapewnia kontroli dostępu opartej na rolach (RBAC) dla precyzyjną kontrolę nad dostępem klientów do zasobów na koncie magazynu. Aby uzyskać więcej informacji, zobacz [uwierzytelniania żądań do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md).
- **Udostępniony klucz autoryzacji** dla obiektów blob, plików, kolejek i tabel. Klienta przy użyciu klucza wspólnego przekazuje nagłówek z każdym żądaniem, który jest podpisany przy użyciu klucza dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [autoryzacji za pomocą klucza wspólnego](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Sygnatury dostępu współdzielonego** dla obiektów blob, plików, kolejek i tabel. Sygnatury dostępu współdzielonego (SAS) zapewnia ograniczony delegowanego dostępu do zasobów na koncie magazynu. Dodawanie ograniczenia na przedział czasowy na potrzeby podpis jest nieprawidłowy lub uprawnienia, jakie daje, zapewnia elastyczność w zarządzaniu dostępem. Aby uzyskać więcej informacji, zobacz [Using shared access signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Dostęp do odczytu publiczne anonimowe** dla kontenerów i obiektów blob. Autoryzacja nie jest wymagana. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).  

Domyślnie wszystkie zasoby w usłudze Azure Storage są chronione i są dostępne tylko dla właściciela konta. Chociaż można używać dowolnego strategie autoryzacji opisanych powyżej, aby przyznać klientom dostęp do zasobów w ramach konta magazynu, firma Microsoft zaleca używanie programu Azure AD w przypadku zapewnienia maksymalnego poziomu bezpieczeństwa i łatwość użycia. 



