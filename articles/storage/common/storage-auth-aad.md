---
title: Autoryzowanie dostępu do obiektów blob i kolejek przy użyciu usługi Active Directory
titleSuffix: Azure Storage
description: Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255368"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autoryzowanie dostępu do obiektów blob i kolejek przy użyciu usługi Azure Active Directory

Usługa Azure Storage obsługuje używanie usługi Azure Active Directory (Azure AD) do autoryzowania żądań do obiektów Blob i magazynu kolejek. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być podmiotem usługi użytkownika, grupy lub usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniony przez usługę Azure AD w celu zwrócenia tokenu OAuth 2.0. Token może następnie służyć do autoryzowania żądania dla blob lub magazynu kolejki.

Autoryzowanie żądań względem usługi Azure Storage za pomocą usługi Azure AD zapewnia doskonałe bezpieczeństwo i łatwość użycia za pomocą autoryzacji klucza udostępnionego. Firma Microsoft zaleca używanie autoryzacji usługi Azure AD z aplikacjami obiektów blob i kolejek, jeśli to możliwe, aby zminimalizować potencjalne luki w zabezpieczeniach związane z kluczem udostępnionym.

Autoryzacja za pomocą usługi Azure AD jest dostępna dla wszystkich kont magazynu ogólnego przeznaczenia i obiektów Blob we wszystkich regionach publicznych i chmurach krajowych. Tylko konta magazynu utworzone za pomocą modelu wdrażania usługi Azure Resource Manager obsługują autoryzację usługi Azure AD.

Magazyn obiektów Blob dodatkowo obsługuje tworzenie podpisów dostępu współdzielonego (SAS), które są podpisane przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Udzielanie ograniczonego dostępu do danych za pomocą podpisów dostępu współdzielonego](storage-sas-overview.md).

Usługa Azure Files obsługuje autoryzację za pomocą usługi AD (wersja zapoznawcza) lub usługi Azure AD DS (GA) za pomocą kontrolera SMB tylko dla maszyn wirtualnych przyłączonych do domeny. Aby dowiedzieć się więcej na temat korzystania z usług AD (wersja zapoznawcza) lub usług Azure AD DS (GA) za pośrednictwem SMB dla usług Azure Files, zobacz [Omówienie obsługi uwierzytelniania opartego na tożsamości usługi Azure Files dla dostępu SMB.](../files/storage-files-active-directory-overview.md)

Autoryzacja przy usłudze Azure AD nie jest obsługiwana w magazynie tabel platformy Azure. Użyj klucza udostępnionego do autoryzowania żądań do magazynu tabel.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Omówienie usługi Azure AD dla obiektów blob i kolejek

Gdy podmiot zabezpieczeń (użytkownik, grupa lub aplikacja) próbuje uzyskać dostęp do zasobu obiektu blob lub kolejki, żądanie musi być autoryzowane, chyba że jest obiektem blob dostępnym dla dostępu anonimowego. Dzięki usłudze Azure AD dostęp do zasobu jest procesem dwuetapowym. Po pierwsze tożsamość podmiotu zabezpieczeń jest uwierzytelniana i zwracany jest token OAuth 2.0. Następnie token jest przekazywany jako część żądania do usługi blob lub queue i używany przez usługę do autoryzowania dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby aplikacja zażądała tokenu dostępu OAuth 2.0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona z wewnątrz jednostki platformy Azure, takich jak maszyna wirtualna Azure, zestaw skalowania maszyny wirtualnej lub aplikacji usługi Azure Functions, można użyć [tożsamości zarządzanej,](../../active-directory/managed-identities-azure-resources/overview.md) aby uzyskać dostęp do obiektów blob lub kolejek. Aby dowiedzieć się, jak autoryzować żądania złożone przez tożsamość zarządzaną do usługi Azure Blob lub Queue service, zobacz [Autoryzowanie dostępu do obiektów blob i kolejek za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure.](storage-auth-aad-msi.md)

Krok autoryzacji wymaga przypisania co najmniej jednej roli RBAC do podmiotu zabezpieczeń. Usługa Azure Storage udostępnia role RBAC, które obejmują typowe zestawy uprawnień dla danych obiektów blob i kolejki. Role, które są przypisane do podmiotu zabezpieczeń określić uprawnienia, które podmiot zabezpieczeń będzie mieć. Aby dowiedzieć się więcej o przypisywaniu ról RBAC dla usługi Azure Storage, zobacz [Zarządzanie prawami dostępu do danych magazynu za pomocą funkcji RBAC](storage-auth-aad-rbac.md).

Aplikacje natywne i aplikacje sieci web, które żądają usługi Azure Blob lub Queue, mogą również autoryzować dostęp za pomocą usługi Azure AD. Aby dowiedzieć się, jak zażądać tokenu dostępu i używać go do autoryzowania żądań dotyczących danych obiektów blob lub kolejek, zobacz [Autoryzowanie dostępu do usługi Azure Storage za pomocą usługi Azure AD z aplikacji usługi Azure Storage.](storage-auth-aad-app.md)

## <a name="assign-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC dla praw dostępu

Usługa Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC).](../../role-based-access-control/overview.md) Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do danych obiektów blob i kolejek. Można również zdefiniować role niestandardowe dostępu do danych obiektów blob i kolejki.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być ograniczony do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, jednostką usługi aplikacji lub [tożsamością zarządzaną dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Wbudowane role RBAC dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Aby dowiedzieć się, jak przypisać wbudowaną rolę RBAC do podmiotu zabezpieczeń, zobacz jeden z następujących artykułów:

- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach w witrynie Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach przy użyciu interfejsu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [ przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)

Aby uzyskać więcej informacji na temat definiowania ról wbudowanych dla usługi Azure Storage, zobacz [Opis definicji ról](../../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje dotyczące tworzenia niestandardowych ról RBAC, zobacz [Tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Uprawnienia dostępu do operacji danych

Aby uzyskać szczegółowe informacje na temat uprawnień wymaganych do wywoływania określonych operacji usługi obiektów Blob lub Queue, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Zakres zasobu

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Uzyskiwanie dostępu do danych za pomocą konta usługi Azure AD

Dostęp do danych obiektów blob lub kolejki za pośrednictwem witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure można autoryzować za pomocą konta usługi Azure AD użytkownika lub przy użyciu kluczy dostępu do konta (autoryzacja klucza udostępnionego).

### <a name="data-access-from-the-azure-portal"></a>Dostęp do danych z witryny Azure portal

Witryna Azure portal może używać konta usługi Azure AD lub kluczy dostępu do konta w celu uzyskania dostępu do danych obiektów blob i kolejek na koncie magazynu platformy Azure. Schemat autoryzacji używany przez witrynę Azure portal zależy od ról RBAC, które są przypisane do Ciebie.

Podczas próby uzyskania dostępu do danych obiektów blob lub kolejki portal Azure najpierw sprawdza, czy przypisano rolę RBAC za pomocą **programu Microsoft.Storage/storageAccounts/listkeys/action**. Jeśli przypisano rolę z tą akcją, witryna Azure portal używa klucza konta do uzyskiwania dostępu do danych obiektów blob i kolejek za pośrednictwem autoryzacji klucza udostępnionego. Jeśli nie przypisano roli za pomocą tej akcji, witryna Azure Portal próbuje uzyskać dostęp do danych przy użyciu konta usługi Azure AD.

Aby uzyskać dostęp do danych obiektów blob lub kolejki z witryny Azure Portal przy użyciu konta usługi Azure AD, potrzebujesz uprawnień dostępu do danych obiektów blob i kolejki, a także potrzebujesz uprawnień do poruszania się po zasobach konta magazynu w witrynie Azure portal. Wbudowane role udostępniane przez usługę Azure Storage udzielają dostępu do zasobów obiektów blob i kolejek, ale nie udzielają uprawnień do zasobów konta magazynu. Z tego powodu dostęp do portalu wymaga również przypisania roli usługi Azure Resource Manager, takiej jak rola [czytnika,](../../role-based-access-control/built-in-roles.md#reader) o zakresie do poziomu konta magazynu lub wyższej. Rola **czytelnika** udziela najbardziej ograniczonych uprawnień, ale inna rola usługi Azure Resource Manager, która udziela dostępu do zasobów zarządzania kontami magazynu jest również dopuszczalne. Aby dowiedzieć się więcej o przypisywaniu użytkownikom uprawnień do dostępu do danych w witrynie Azure portal z kontem usługi Azure AD, zobacz [Udzielanie dostępu do obiektów blob platformy Azure i danych kolejek za pomocą funkcji RBAC w witrynie Azure.](storage-auth-aad-rbac-portal.md)

Portal Azure wskazuje, który schemat autoryzacji jest używany podczas przechodzenia do kontenera lub kolejki. Aby uzyskać więcej informacji na temat dostępu do danych w portalu, zobacz Korzystanie z witryny Azure Portal w [celu uzyskania dostępu do danych obiektów blob lub kolejek.](storage-access-blobs-queues-portal.md)

### <a name="data-access-from-powershell-or-azure-cli"></a>Dostęp do danych z programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Narzędzia Platformy Azure CLI i powershell obsługują logowanie się przy użyciu poświadczeń usługi Azure AD. Po zalogowaniu sesja jest uruchamiana w obszarze tych poświadczeń. Aby dowiedzieć się więcej, zobacz [Uruchamianie poleceń interfejsu wiersza polecenia platformy Azure lub programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów blob lub kolejki.](authorize-active-directory-powershell.md)

## <a name="next-steps"></a>Następne kroki

- [Autoryzuj dostęp do obiektów blob i kolejek za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure](storage-auth-aad-msi.md)
- [Autoryzowanie za pomocą usługi Azure Active Directory z aplikacji dostępu do obiektów blob i kolejek](storage-auth-aad-app.md)
- [Ogólnie dostępna obsługa usługi Azure Storage dla kontroli dostępu opartej na usłudze Azure Active Directory](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
