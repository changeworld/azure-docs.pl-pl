---
title: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory (wersja zapoznawcza).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 1a9283ad688c63642df880a74cca9189c7c59042
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456701"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory (wersja zapoznawcza)

Usługa Azure Storage obsługuje uwierzytelnianie i autoryzacja przy użyciu usługi Azure Active Directory (AD) dla usług obiektów Blob i kolejek. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania dostępu do użytkowników, grup lub jednostek usług aplikacji. 

Uwierzytelnianie użytkowników lub aplikacji przy użyciu poświadczeń usługi Azure AD udostępnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z innej metody autoryzacji. Gdy możesz kontynuować klucza wspólnego autoryzacji za pomocą aplikacji, za pomocą usługi Azure AD zmierzone konieczności przechowywania klucza dostępu do konta za pomocą kodu. Nadal m ożna również udzielić szczegółową kontrolę dostępu do zasobów na koncie magazynu przy użyciu sygnatury dostępu współdzielonego (SAS), ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokeny sygnatur dostępu Współdzielonego ani martwić się o odwołaniu ze złamanymi zabezpieczeniami sygnatury dostępu Współdzielonego. Firma Microsoft zaleca korzystanie z uwierzytelniania usługi Azure AD dla aplikacji usługi Azure Storage, jeśli jest to możliwe.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>O wersji zapoznawczej

Należy pamiętać o następujących kwestiach dotyczących korzystania z wersji zapoznawczej:

- Integracja z usługą Azure AD jest dostępna dla obiektów Blob i kolejek usługi tylko w wersji zapoznawczej.
- Integracja z usługą Azure AD jest dostępna dla kont magazynu w przypadku kont GPv1, GPv2 i Blob we wszystkich publicznych regionach. 
- Obsługiwane są tylko konta magazynu przy użyciu modelu wdrażania usługi Resource Manager. 
- Obsługa obiektu wywołującego informacji o tożsamości podczas rejestrowania usługi Azure Storage Analytics będzie dostępna wkrótce.
- Autoryzacja dostępu do zasobów w ramach kont magazynu w warstwie standardowa w usłudze Azure AD jest obecnie obsługiwane. Autoryzacja dostępu do stronicowych obiektów blob na kontach magazynu premium będzie obsługiwane wkrótce.
- Usługa Azure Storage obsługuje niestandardowe i wbudowane role RBAC. Można przypisać role ograniczone do subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki.
- Bibliotek klienta usługi Azure Storage, które obecnie obsługują integracji z usługą Azure AD obejmują:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [Obiekt blob, kolejki i pliki](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="get-started-with-azure-ad-for-storage"></a>Rozpoczynanie pracy z usługą Azure AD pod kątem magazynu

Pierwszym krokiem przy użyciu integracji z usługą Azure AD z usługą Azure Storage jest przypisywanie ról RBAC dla magazynu danych do nazwy głównej usługi, (użytkownika, grupy lub aplikacji jednostki usługi) lub zarządzanych tożsamości dla zasobów platformy Azure. Role RBAC obejmują typowe zestawy uprawnień dla kontenerów i kolejek. Aby dowiedzieć się więcej na temat ról RBAC dla usługi Azure Storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

Aby użyć usługi Azure AD do autoryzowania dostępu do zasobów magazynu w swoich aplikacjach, musisz żądania tokenu dostępu OAuth 2.0 w kodzie. Aby dowiedzieć się, jak żądania tokenu dostępu i użyć go w celu autoryzowania żądania do usługi Azure Storage, zobacz [uwierzytelnianie w usłudze Azure AD z poziomu aplikacji usługi Azure Storage (wersja zapoznawcza)](storage-auth-aad-app.md). Jeśli używasz tożsamości zarządzanej, zobacz [uwierzytelnienia dostępu do obiektów blob i kolejki przy użyciu platformy Azure zarządzanych tożsamości dla zasobów platformy Azure (wersja zapoznawcza)](storage-auth-aad-msi.md).

Program PowerShell i interfejsu wiersza polecenia platformy Azure obsługują teraz zalogować się przy użyciu tożsamości usługi Azure AD. Po zalogowaniu się przy użyciu tożsamości usługi Azure AD, sesja działa w ramach tej tożsamości. Aby dowiedzieć się więcej, zobacz [tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell (wersja zapoznawcza)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob platformy Azure, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
