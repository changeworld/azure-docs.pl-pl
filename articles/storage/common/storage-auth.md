---
title: Autoryzowanie operacji na danych
titleSuffix: Azure Storage
description: Dowiedz się więcej o różnych sposobach autoryzacji dostępu do usługi Azure Storage, w tym Azure Active Directory, udostępnionej autoryzacji klucza lub sygnatury dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7deb773fae6ba56b6f601983ffd2b07d887e1480
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565764"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autoryzowanie dostępu do danych w usłudze Azure Storage

Za każdym razem, gdy uzyskujesz dostęp do danych na koncie magazynu, klient wysyła żądanie za pośrednictwem protokołu HTTP/HTTPS do usługi Azure Storage. Każde żądanie dotyczące bezpiecznego zasobu musi być autoryzowane, aby usługa zapewniała, że klient ma uprawnienia wymagane do uzyskiwania dostępu do danych.

W poniższej tabeli opisano opcje oferowane przez usługę Azure Storage do autoryzowania dostępu do zasobów:

|  |Klucz współużytkowany (klucz konta magazynu)  |Sygnatura dostępu współdzielonego (SAS)  |Azure Active Directory (Azure AD)  |Active Directory (wersja zapoznawcza)|Anonimowy publiczny dostęp do odczytu  |
|---------|---------|---------|---------|---------|---------|
|Obiekty blob platformy Azure     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |[Obsługiwane](storage-auth-aad.md)         |Nieobsługiwane|[Obsługiwane](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |Nieobsługiwane         |[Obsługiwane tylko w przypadku usług domenowych w usłudze AAD](../files/storage-files-active-directory-overview.md)         |[Obsługiwane, należy synchronizować poświadczenia z usługą Azure AD](../files/storage-files-active-directory-overview.md)|Nieobsługiwane         |
|Azure Files (REST)     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |Nieobsługiwane         |Nieobsługiwane |Nieobsługiwane         |
|Azure Queues     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |[Obsługiwane](storage-auth-aad.md)         |Nieobsługiwane | Nieobsługiwane         |
|Tabele platformy Azure     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |Nieobsługiwane         |Nieobsługiwane| Nieobsługiwane         |

Każdą opcję autoryzacji można krótko opisać poniżej:

- **Azure Active Directory (Azure AD) integracja** obiektów blob i kolejek. Usługa Azure AD zapewnia kontrolę dostępu opartą na rolach (RBAC) na potrzeby precyzyjnej kontroli dostępu klienta do zasobów na koncie magazynu. Aby uzyskać więcej informacji na temat integracji usługi Azure AD dla obiektów blob i kolejek, zobacz temat [autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md).

- **Azure Active Directory Domain Services (Azure AD DS) uwierzytelnianie** plików. Azure Files obsługuje autoryzację opartą na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem usługi Azure AD DS. Można użyć RBAC do szczegółowej kontroli nad dostępem klienta do Azure Files zasobów na koncie magazynu. Więcej informacji dotyczących uwierzytelniania Azure Files przy użyciu usług domenowych można znaleźć w naszym [omówieniu](../files/storage-files-active-directory-overview.md).

- **Active Directory (AD) uwierzytelnianie (wersja zapoznawcza)** plików. Azure Files obsługuje autoryzację opartą na tożsamościach przy użyciu protokołu SMB za pośrednictwem usługi AD. Usługa domeny usługi AD może być hostowana na maszynach lokalnych lub na maszynach wirtualnych platformy Azure. Dostęp do plików SMB jest obsługiwany przy użyciu poświadczeń usługi AD z komputerów przyłączonych do domeny, lokalnie lub na platformie Azure. RBAC kontroli dostępu na poziomie udziału i listy DACL systemu plików NTFS można użyć do wymuszania uprawnień na poziomie katalogu/pliku. Więcej informacji dotyczących uwierzytelniania Azure Files przy użyciu usług domenowych można znaleźć w naszym [omówieniu](../files/storage-files-active-directory-overview.md).

- **Autoryzacja klucza współużytkowanego** dla obiektów blob, plików, kolejek i tabel. Klient korzystający z klucza współużytkowanego przekazuje nagłówek z każdym żądaniem podpisanym przy użyciu klucza dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key/).
- **Sygnatury dostępu współdzielonego** dla obiektów blob, plików, kolejek i tabel. Sygnatury dostępu współdzielonego (SAS) zapewniają ograniczony dostęp delegowany do zasobów na koncie magazynu. Dodanie ograniczeń w przedziale czasowym, dla którego podpis jest prawidłowy lub na uprawnienia, które ma w ten sposób zapewnia elastyczność zarządzania dostępem. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
- **Anonimowy publiczny dostęp do odczytu** dla kontenerów i obiektów BLOB. Autoryzacja nie jest wymagana. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).  

Domyślnie wszystkie zasoby w usłudze Azure Storage są zabezpieczone i są dostępne tylko dla właściciela konta. Chociaż można użyć dowolnej z tych strategii autoryzacji w celu udzielenia klientom dostępu do zasobów na koncie magazynu, firma Microsoft zaleca korzystanie z usługi Azure AD, gdy jest to możliwe, aby uzyskać maksymalne zabezpieczenia i łatwość użycia.

## <a name="next-steps"></a>Następne kroki

- [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md)
- [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key/)
- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
