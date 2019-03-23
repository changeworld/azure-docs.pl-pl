---
title: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ff543b7275ab05a83b1be1d156cbc6059a3b5430
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369901"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory

Usługa Azure Storage obsługuje uwierzytelnianie i autoryzacja przy użyciu usługi Azure Active Directory (AD) dla usług obiektów Blob i kolejek. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania dostępu do użytkowników, grup lub jednostek usług aplikacji. 

Uwierzytelnianie użytkowników lub aplikacji przy użyciu poświadczeń usługi Azure AD udostępnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z innej metody autoryzacji. Gdy możesz kontynuować klucza wspólnego autoryzacji za pomocą aplikacji, za pomocą usługi Azure AD zmierzone konieczności przechowywania klucza dostępu do konta za pomocą kodu. Nadal m ożna również udzielić szczegółową kontrolę dostępu do zasobów na koncie magazynu przy użyciu sygnatury dostępu współdzielonego (SAS), ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokeny sygnatur dostępu Współdzielonego ani martwić się o odwołaniu ze złamanymi zabezpieczeniami sygnatury dostępu Współdzielonego. Firma Microsoft zaleca korzystanie z uwierzytelniania usługi Azure AD dla aplikacji usługi Azure Storage, jeśli jest to możliwe.

Uwierzytelnianie i autoryzacja przy użyciu poświadczeń usługi Azure AD jest dostępna dla wszystkich ogólnego przeznaczenia w wersji 2, general-purpose v1 i kont usługi Blob storage we wszystkich publicznych regionach. Tylko konta magazynu z obsługą modelu wdrażania usługi Azure Resource Manager utworzony autoryzacji usługi Azure AD.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Omówienie usługi Azure AD dla kolejek i obiektów blob

Pierwszym krokiem przy użyciu integracji z usługą Azure AD z usługą Azure Storage jest przypisywanie ról RBAC dla magazynu danych do nazwy głównej usługi, (użytkownika, grupy lub aplikacji jednostki usługi) lub zarządzanych tożsamości dla zasobów platformy Azure. Role RBAC obejmują typowe zestawy uprawnień dla kontenerów i kolejek. Aby dowiedzieć się więcej na temat przypisywania ról RBAC dla usługi Azure Storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC](storage-auth-aad-rbac.md).

Aby użyć usługi Azure AD do autoryzowania dostępu do zasobów magazynu w swoich aplikacjach, musisz żądania tokenu dostępu OAuth 2.0 w kodzie. Aby dowiedzieć się, jak żądania tokenu dostępu i użyć go w celu autoryzowania żądania do usługi Azure Storage, zobacz [uwierzytelnianie w usłudze Azure AD z poziomu aplikacji usługi Azure Storage](storage-auth-aad-app.md). Jeśli używasz tożsamości zarządzanej, zobacz [uwierzytelnienia dostępu do obiektów blob i kolejki przy użyciu platformy Azure zarządzanych tożsamości dla zasobów platformy Azure](storage-auth-aad-msi.md).

Program PowerShell i interfejsu wiersza polecenia platformy Azure obsługują teraz zalogować się przy użyciu tożsamości usługi Azure AD. Po zalogowaniu się przy użyciu tożsamości usługi Azure AD, sesja działa w ramach tej tożsamości. Aby dowiedzieć się więcej, zobacz [tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do kontenerów i kolejki. 

Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń usługi Azure AD, Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, nazwy głównej usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Aby dowiedzieć się, jak przypisać rolę wbudowaną w witrynie Azure portal, zobacz [udzielać dostępu do kontenerów platformy Azure i kolejek o ROLACH w witrynie Azure portal](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>Uprawnienia przyznane przez role RBAC 

Aby uzyskać więcej informacji na temat uprawnień wymaganych do wywoływania operacji usługi Azure Storage, zobacz [uprawnień do wywoływania operacji REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Kolejne kroki

- [Udzielanie dostępu do kontenerów platformy Azure i kolejek o ROLACH w witrynie Azure portal](storage-auth-aad-rbac.md)
- [Uwierzytelnianie za pomocą usługi Azure Active Directory z poziomu aplikacji na potrzeby uzyskiwania dostępu do obiektów blob i kolejek](storage-auth-aad-app.md)
- [Uwierzytelnianie dostępu do obiektów blob i kolejki z zarządzanych tożsamości dla zasobów platformy Azure](storage-auth-aad-msi.md)
- [Tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell](storage-auth-aad-script.md)