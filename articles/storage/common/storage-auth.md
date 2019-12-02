---
title: Autoryzowanie dostępu do usługi Azure Storage | Microsoft Docs
description: Poznaj różne sposoby autoryzacji dostępu do usługi Azure Storage, w tym Azure Active Directory, uwierzytelnianie klucza wspólnego lub sygnatury dostępu współdzielonego.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b7669f9ec804a8fd2801474a845af7e029ee5235
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664050"
---
# <a name="authorizing-access-to-azure-storage"></a>Autoryzowanie dostępu do usługi Azure Storage

Za każdym razem, gdy uzyskujesz dostęp do danych na koncie magazynu, klient wysyła żądanie za pośrednictwem protokołu HTTP/HTTPS do usługi Azure Storage. Każde żądanie dotyczące bezpiecznego zasobu musi być autoryzowane, aby usługa zapewniała, że klient ma uprawnienia wymagane do uzyskiwania dostępu do danych.

W poniższej tabeli opisano opcje oferowane przez usługę Azure Storage do autoryzowania dostępu do zasobów:

|  |Klucz współużytkowany (klucz konta magazynu)  |Sygnatura dostępu współdzielonego (SAS)  |Azure Active Directory (Azure AD)  |Anonimowy publiczny dostęp do odczytu  |
|---------|---------|---------|---------|---------|
|Obiekty blob platformy Azure     |[Obsługiwane](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |[Obsługiwane](storage-auth-aad.md)         |[Obsługiwane](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Obsługiwane](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Brak obsługi         |[Obsługiwane tylko w przypadku usług domenowych w usłudze AAD](../files/storage-files-active-directory-overview.md)         |Brak obsługi         |
|Azure Files (REST)     |[Obsługiwane](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |Brak obsługi         |Brak obsługi         |
|Azure Queues     |[Obsługiwane](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |[Obsługiwane](storage-auth-aad.md)         |Brak obsługi         |
|Tabele platformy Azure     |[Obsługiwane](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |Brak obsługi         |Brak obsługi         |

Każdą opcję autoryzacji można krótko opisać poniżej:

- **Azure Active Directory (Azure AD) integracja** obiektów blob i kolejek. Usługa Azure AD zapewnia kontrolę dostępu opartą na rolach (RBAC) na potrzeby precyzyjnej kontroli dostępu klienta do zasobów na koncie magazynu. Aby uzyskać więcej informacji na temat integracji usługi Azure AD dla obiektów blob i kolejek, zobacz [uwierzytelnianie żądań w usłudze Azure Storage przy użyciu Azure Active Directory](storage-auth-aad.md).

- **Integracja z Azure AD Domain Services (DS) (wersja zapoznawcza)** plików. Azure Files obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem usługi Azure AD DS. Zapewnia to RBAC do szczegółowej kontroli nad dostępem klienta do zasobów na koncie magazynu. Aby uzyskać więcej informacji na temat integracji usługi Azure AD dla plików korzystających z usług domenowych, zobacz [Omówienie obsługi uwierzytelniania protokołu SMB w usłudze Azure Files Azure Active Directory (wersja zapoznawcza](../files/storage-files-active-directory-overview.md)).

- **Autoryzacja klucza współużytkowanego** dla obiektów blob, plików, kolejek i tabel. Klient korzystający z klucza współużytkowanego przekazuje nagłówek z każdym żądaniem podpisanym przy użyciu klucza dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authenticate-with-shared-key/).
- **Sygnatury dostępu współdzielonego** dla obiektów blob, plików, kolejek i tabel. Sygnatury dostępu współdzielonego (SAS) zapewniają ograniczony dostęp delegowany do zasobów na koncie magazynu. Dodanie ograniczeń w przedziale czasowym, dla którego podpis jest prawidłowy lub na uprawnienia, które ma w ten sposób zapewnia elastyczność zarządzania dostępem. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
- **Anonimowy publiczny dostęp do odczytu** dla kontenerów i obiektów BLOB. Autoryzacja nie jest wymagana. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).  

Domyślnie wszystkie zasoby w usłudze Azure Storage są zabezpieczone i są dostępne tylko dla właściciela konta. Chociaż można użyć dowolnej z tych strategii autoryzacji w celu udzielenia klientom dostępu do zasobów na koncie magazynu, firma Microsoft zaleca korzystanie z usługi Azure AD, gdy jest to możliwe, aby uzyskać maksymalne zabezpieczenia i łatwość użycia.

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja Azure Active Directory](/azure/active-directory/)
- [Ewolucja platformy tożsamości firmy Microsoft](/azure/active-directory/develop/about-microsoft-identity-platform)
