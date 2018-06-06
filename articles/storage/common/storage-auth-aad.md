---
title: Uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.openlocfilehash: 9a0782b96b45d27c9b7e603959ecadf5b2632064
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737650"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)

Usługa Azure Storage obsługuje uwierzytelnianie i autoryzacja z usługi Azure Active Directory (AD) dla usług obiektów Blob i kolejek. Z usługą Azure AD służy kontroli dostępu opartej na rolach (RBAC) aby udzielić dostępu do użytkowników, grup lub nazwy główne usług aplikacji. 

Autoryzowanie aplikacje, które uzyskują dostęp do usługi Azure Storage za pomocą usługi Azure AD zapewnia wyższego poziomu bezpieczeństwa i łatwość użycia przez inne opcje autoryzacji. Można nadal używać autoryzacji klucza wspólnego z aplikacji, za pomocą usługi Azure AD prowadzenia potrzeba przechowywać klucz dostępu do konta w kodzie. Podobnie możesz użyć sygnatur dostępu współdzielonego (SAS), aby przyznać szczegółowych dostęp do zasobów na koncie magazynu, ale usługi Azure AD oferuje podobne funkcje bez konieczności zarządzania tokeny sygnatury dostępu Współdzielonego lub martwić się o odwołaniu złamany SAS.

## <a name="about-the-preview"></a>Temat w wersji zapoznawczej

Mieć na uwadze następujące kwestie dotyczące wersji zapoznawczej:

- Integracja z usługą Azure AD jest dostępna dla obiektów Blob i kolejek usługi tylko w wersji zapoznawczej.
- Integracja z usługą Azure AD jest dostępny dla konta magazynu GPv1, GPv2 i obiektów Blob we wszystkich regionach publicznego. 
- Obsługiwane są tylko konta magazynu utworzone przy użyciu modelu wdrażania usługi Resource Manager. 
- Obsługa informacji o tożsamości wywołującego rejestrowanie analityka magazynu Azure będzie dostępna wkrótce.
- Azure AD autoryzacji dostępu do zasobów na kontach magazynu w warstwie standardowa jest obecnie obsługiwane. Autoryzacja dostępu do stronicowe obiekty BLOB na kontach magazynu w warstwie premium będzie wkrótce obsługiwany.
- Usługa Azure Storage obsługuje zarówno wbudowane i niestandardowe role RBAC. Można przypisać role ograniczone do subskrypcji, grupy zasobów, konta magazynu lub poszczególnych kontenera lub kolejki.
- Biblioteki klienta magazynu Azure, obsługujących integracji z usługą Azure AD obejmują:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (Użyj 7.1.x-Preview)
    - Python
        - [Blob](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [Kolejki](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> Ta wersja zapoznawcza jest przeznaczony tylko do użytku z systemem innym niż produkcji. Produkcyjnej umowy poziomu usług (SLA) nie będą dostępne, dopóki integracji z usługą Azure AD dla usługi Azure Storage jest zadeklarowany jako ogólnie dostępna. Jeśli dla danego scenariusza integracji z usługą Azure AD nie jest jeszcze obsługiwana, nadal używać klucza wspólnego autoryzacji lub tokeny sygnatury dostępu Współdzielonego w aplikacji.
>
> W wersji zapoznawczej przypisań ról RBAC może potrwać maksymalnie pięć minut propagacji.
>
> Integracja usługi Azure AD z usługą Azure Storage wymaga używania protokołu HTTPS dla operacji usługi Azure Storage.

## <a name="get-started-with-azure-ad-for-storage"></a>Rozpoczynanie pracy z usługą Azure AD dla magazynu

Pierwszym krokiem przy użyciu integracji z usługą Azure AD z usługą Azure Storage jest przypisać role RBAC dla magazynu danych do nazwy głównej usługi (użytkownika, grupy lub nazwy głównej usługi aplikacji) lub zarządzanych tożsamości usługi (MSI). Role RBAC obejmują typowe zestawów uprawnień do kontenerów i kolejek. Aby dowiedzieć się więcej o rolach RBAC dla usługi Azure Storage, zobacz [Zarządzaj praw dostępu do magazynu danych z RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

Przy użyciu usługi Azure AD do autoryzowania dostępu do zasobów magazynu w aplikacjach, należy zażądać token dostępu protokołu OAuth 2.0 w kodzie. Aby dowiedzieć się, jak żądania tokenu dostępu i użyć go do autoryzowania żądań do usługi Azure Storage, zobacz [uwierzytelniony przez usługi Azure AD z aplikacji usługi Azure Storage (wersja zapoznawcza)](storage-auth-aad-app.md). Jeśli używasz platformy Azure zarządzanych usługi tożsamości (MSI), zobacz [uwierzytelniony przez usługi Azure AD z Azure VM zarządzane tożsamości usługi (wersja zapoznawcza)](storage-auth-aad-msi.md).

Azure CLI i programu PowerShell obsługuje teraz logującym się przy użyciu tożsamości usługi Azure AD. Po zalogowaniu się przy użyciu tożsamości usługi Azure AD, sesja działa w ramach tej tożsamości. Aby dowiedzieć się więcej, zobacz [korzystania z tożsamości usługi Azure AD dostęp do magazynu Azure z interfejsu wiersza polecenia lub środowiska PowerShell (wersja zapoznawcza)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob Azure, zobacz w usłudze Azure Storage team na blogu, [Announcing uwierzytelniania Podgląd Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
