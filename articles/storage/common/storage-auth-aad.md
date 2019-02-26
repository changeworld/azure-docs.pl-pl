---
title: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory (wersja zapoznawcza).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57e793278af7eb03fe49fd657e45b0846db8b1c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817919"
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

## <a name="overview-of-azure-ad-for-storage"></a>Omówienie usługi Azure AD pod kątem magazynu

Pierwszym krokiem przy użyciu integracji z usługą Azure AD z usługą Azure Storage jest przypisywanie ról RBAC dla magazynu danych do nazwy głównej usługi, (użytkownika, grupy lub aplikacji jednostki usługi) lub zarządzanych tożsamości dla zasobów platformy Azure. Role RBAC obejmują typowe zestawy uprawnień dla kontenerów i kolejek. Aby dowiedzieć się więcej na temat przypisywania ról RBAC dla usługi Azure Storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

Aby użyć usługi Azure AD do autoryzowania dostępu do zasobów magazynu w swoich aplikacjach, musisz żądania tokenu dostępu OAuth 2.0 w kodzie. Aby dowiedzieć się, jak żądania tokenu dostępu i użyć go w celu autoryzowania żądania do usługi Azure Storage, zobacz [uwierzytelnianie w usłudze Azure AD z poziomu aplikacji usługi Azure Storage (wersja zapoznawcza)](storage-auth-aad-app.md). Jeśli używasz tożsamości zarządzanej, zobacz [uwierzytelnienia dostępu do obiektów blob i kolejki przy użyciu platformy Azure zarządzanych tożsamości dla zasobów platformy Azure (wersja zapoznawcza)](storage-auth-aad-msi.md).

Program PowerShell i interfejsu wiersza polecenia platformy Azure obsługują teraz zalogować się przy użyciu tożsamości usługi Azure AD. Po zalogowaniu się przy użyciu tożsamości usługi Azure AD, sesja działa w ramach tej tożsamości. Aby dowiedzieć się więcej, zobacz [tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell (wersja zapoznawcza)](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do kontenerów i kolejki. 

Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń usługi Azure AD, Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, nazwy głównej usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

### <a name="access-permissions-granted-by-rbac-roles"></a>Uprawnienia przyznane przez role RBAC 

W poniższej tabeli przedstawiono prawa dostępu do wbudowanych ról w zależności od zakresu:

|Zakres|Właściciel danych obiektów blob|Współautor danych obiektu blob|Czytnik danych obiektu blob|Queue Data Contributor|Queue Data Reader|
|---|---|---|---|---|---|
|Poziom subskrypcji|Dostęp do odczytu/zapisu i POSIX dostęp do zarządzania kontrolą do wszystkich kontenerów i obiektów blob w ramach subskrypcji|Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob w ramach subskrypcji| Dostęp do odczytu do wszystkich kontenerów i obiektów blob w ramach subskrypcji|Odczyt/zapis dostęp do wszystkich kolejek w ramach subskrypcji|Dostęp do odczytu do wszystkich kolejek w ramach subskrypcji|
|Poziomu grupy zasobów|Dostęp do odczytu/zapisu i POSIX dostęp do zarządzania kontrolą do wszystkich kontenerów i obiektów blob w grupie zasobów|Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob w grupie zasobów|Dostęp do odczytu do wszystkich kontenerów i obiektów blob w grupie zasobów|Odczyt/zapis dostęp do wszystkich kolejek w grupie zasobów|Dostęp do odczytu do wszystkich kolejek w grupie zasobów|
|Na poziomie konta magazynu|Dostęp do odczytu/zapisu i POSIX dostęp do zarządzania kontrolą do wszystkich kontenerów i obiektów blob na koncie magazynu|Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob na koncie magazynu|Dostęp do odczytu do wszystkich kontenerów i obiektów blob na koncie magazynu|Odczyt/zapis dostęp do wszystkich kolejek na koncie magazynu|Dostęp do odczytu do wszystkich kolejek na koncie magazynu|
|Poziom kontenera/kolejki|Dostęp do odczytu/zapisu i POSIX dostępu do zarządzania kontrolą do określonego kontenera i jego obiektów blob.|Odczyt/zapis dostęp do określonego kontenera i jego obiektów blob|Dostęp do odczytu do określonego kontenera i jego obiektów blob|Odczyt/zapis dostęp do określonej kolejki|Dostęp do odczytu do określonej kolejki|

Aby uzyskać więcej informacji na temat uprawnień wymaganych do wywoływania operacji usługi Azure Storage, zobacz [uprawnień do wywoływania operacji REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob platformy Azure, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
