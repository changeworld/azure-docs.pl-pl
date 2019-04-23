---
title: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 12598f3866cd1041cdf3cb89dac985b8d2caafce
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148810"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory

Usługa Azure Storage obsługuje uwierzytelnianie i autoryzacja przy użyciu usługi Azure Active Directory (AD) dla usług obiektów Blob i kolejek. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania dostępu do użytkowników, grup lub jednostek usług aplikacji. 

Uwierzytelnianie użytkowników lub aplikacji przy użyciu poświadczeń usługi Azure AD udostępnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z innej metody autoryzacji. Gdy możesz kontynuować klucza wspólnego autoryzacji za pomocą aplikacji, za pomocą usługi Azure AD zmierzone konieczności przechowywania klucza dostępu do konta za pomocą kodu. Nadal m ożna również udzielić szczegółową kontrolę dostępu do zasobów na koncie magazynu przy użyciu sygnatury dostępu współdzielonego (SAS), ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokeny sygnatur dostępu Współdzielonego ani martwić się o odwołaniu ze złamanymi zabezpieczeniami sygnatury dostępu Współdzielonego. Firma Microsoft zaleca korzystanie z uwierzytelniania usługi Azure AD dla aplikacji usługi Azure Storage, jeśli jest to możliwe.

Uwierzytelnianie i autoryzacja przy użyciu poświadczeń usługi Azure AD jest dostępna dla wszystkich ogólnego przeznaczenia i konta usługi Blob storage we wszystkich publicznych regionach. Tylko konta magazynu z obsługą modelu wdrażania usługi Azure Resource Manager utworzony autoryzacji usługi Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Omówienie usługi Azure AD dla kolejek i obiektów blob

Podmiot zabezpieczeń (użytkownika, grupy lub aplikacji) próbuje uzyskać dostęp do zasobów obiektów blob i kolejki, żądanie musi być autoryzowana chyba że jest to obiekt blob, które są dostępne dla dostępu anonimowego. Za pomocą usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. Najpierw jest uwierzytelniony podmiot zabezpieczeń tożsamości i zwracany jest token OAuth 2.0. Token jest następnie przekazywany jako część żądania do usługi blob Storage lub kolejki i używane przez usługę do autoryzacji dostępu do określonego zasobu.

W kroku uwierzytelniania wymaga, że co najmniej jedną rolę RBAC można przypisać do do podmiotu zabezpieczeń. Usługa Azure Storage udostępnia role RBAC, które obejmują typowe zestawy uprawnień dla danych obiektów blob i kolejek. Role, które są przypisane do podmiotu zabezpieczeń określają dostępu, który będzie miał tego obiektu głównego. Aby dowiedzieć się więcej na temat przypisywania ról RBAC dla usługi Azure Storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC](storage-auth-aad-rbac.md).

Krok autoryzacji wymaga, że aplikacja żąda tokenu dostępu OAuth 2.0, w czasie wykonywania. Jeśli aplikacja jest uruchomiona z w obrębie jednostki usługi Azure takie jak Maszynie wirtualnej platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacji usługi Azure Functions, można użyć [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) na dostęp do obiektów blob i kolejki. Aby dowiedzieć się, jak autoryzować żądań wysyłanych przez tożsamość zarządzaną w usłudze Azure blob Storage lub kolejki, zobacz [uwierzytelniania dostępu do obiektów blob i kolejek usługi Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](storage-auth-aad-msi.md).

Natywne aplikacje i aplikacje sieci web, które wysyłać żądania do usługi Azure blob Storage lub kolejki może także uwierzytelnić się za pomocą usługi Azure AD. Aby dowiedzieć się, jak żądania tokenu dostępu i wykorzystywania ich do autoryzowania żądania dotyczące danych obiektów blob i kolejki, zobacz [uwierzytelnianie w usłudze Azure AD z poziomu aplikacji usługi Azure Storage](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC prawa dostępu

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do danych obiektów blob i kolejek. Można również definiować role niestandardowe, aby uzyskać dostęp do danych obiektów blob i kolejek.

Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń usługi Azure AD, Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, nazwy głównej usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Wbudowane role kontroli RBAC dla kolejek i obiektów blob

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Aby dowiedzieć się, jak przypisać rolę wbudowaną RBAC do podmiotu zabezpieczeń, zobacz jeden z następujących artykułów:

- [Udzielanie dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu RBAC przy użyciu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu kontroli RBAC przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)

Aby uzyskać więcej informacji o tym, jak wbudowane role są definiowane dla usługi Azure Storage, zobacz [zrozumienie definicji ról](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Aby uzyskać informacji na temat tworzenia niestandardowych ról RBAC, zobacz [tworzenie ról niestandardowych dla kontroli dostępu](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Uprawnienia dostępu do operacji na danych

Aby uzyskać szczegółowe informacje na temat uprawnień wymaganych do wywołania określonych operacji obiektu Blob lub kolejki usługi, zobacz [uprawnień do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Zakres zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Dostęp do danych przy użyciu konta usługi Azure AD

Dostęp do danych obiektów blob lub kolejki przy użyciu witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure może być autoryzowane przy użyciu konta usługi Azure AD lub przy użyciu kluczy dostępu do konta (uwierzytelniania klucza wspólnego).

### <a name="data-access-from-the-azure-portal"></a>Dostęp do danych w witrynie Azure portal

Witryny Azure portal można użyć konta usługi Azure AD lub kluczy dostępu do konta dostępu do danych obiektów blob i kolejek na koncie usługi Azure storage. Schemat autoryzacji, które korzysta z witryny Azure portal jest zależny od ról RBAC, które są przypisane do Ciebie.

Gdy użytkownik spróbuje uzyskać dostęp do danych obiektów blob i kolejki, witryny Azure portal najpierw sprawdza, czy przypisano Ci rolę RBAC przy użyciu **Microsoft.Storage/storageAccounts/listkeys/action**. Jeśli masz przypisanej roli za pomocą tej akcji, a następnie korzysta z witryny Azure portal klucz konta do uzyskiwania dostępu do danych obiektów blob i kolejek, za pomocą klucza wspólnego autoryzacji. Jeśli nie przypisano Ci rolę za pomocą tej akcji, witryny Azure portal próbuje uzyskać dostęp do danych przy użyciu swojego konta usługi Azure AD.

Aby uzyskać dostęp do danych obiektów blob i kolejki, w witrynie Azure portal przy użyciu swojego konta usługi Azure AD, oba z następujących instrukcji muszą być spełnione dla Ciebie:

- Przypisano Ci usługi Azure Resource Manager [czytnika](../../role-based-access-control/built-in-roles.md#reader) roli, co najmniej o określonym zakresie na poziomie konta magazynu lub nowszej. **Czytnika** rola przyznaje najbardziej ograniczone uprawnienia, ale dopuszczalne jest również innej roli usługi Azure Resource Manager, która udziela dostępu do zasobów zarządzania konta magazynu.
- Przypisano Ci albo wbudowanych lub niestandardowych ról RBAC, który zapewnia dostęp do danych obiektów blob i kolejki.

Witryna Azure portal wskazuje schematu, który jest używany po przejściu do kontenera lub kolejki. Aby uzyskać więcej informacji na temat dostępu do danych w portalu, zobacz [Azure Portal umożliwia dostęp do danych obiektów blob i kolejki](storage-access-blobs-queues-portal.md).

Aby dowiedzieć się więcej na temat sposobu przypisywania uprawnień użytkownikom dostępu do danych w witrynie Azure portal przy użyciu konta usługi Azure AD, zobacz [udzielić dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Dostęp do danych z programu PowerShell lub wiersza polecenia platformy Azure

Program PowerShell i interfejsu wiersza polecenia platformy Azure obsługuje logowanie się przy użyciu poświadczeń usługi Azure AD. Po zalogowaniu w sesji działa w ramach tych poświadczeń. Aby dowiedzieć się więcej, zobacz [polecenia uruchomienia wiersza polecenia platformy Azure lub programu PowerShell przy użyciu poświadczeń usługi Azure AD, aby uzyskiwać dostęp do danych obiektów blob i kolejki](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB dla usługi Azure Files

Usługa pliki systemu Azure obsługuje uwierzytelnianie przy użyciu usługi Azure AD za pośrednictwem protokołu SMB dla przyłączonych do domeny tylko maszyn wirtualnych (wersja zapoznawcza). Aby dowiedzieć się więcej o korzystaniu z usługi Azure AD przy użyciu protokołu SMB dla usługi Azure Files, zobacz [uwierzytelniania Omówienie programu Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Kolejne kroki

- [Uwierzytelnianie dostępu do obiektów blob i kolejek usługi Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](storage-auth-aad-msi.md)
- [Uwierzytelnianie za pomocą usługi Azure Active Directory z poziomu aplikacji na potrzeby uzyskiwania dostępu do obiektów blob i kolejek](storage-auth-aad-app.md)
- [Usługa Azure Storage — pomoc techniczna dla usługi Azure Active Directory na podstawie kontroli dostępu jest ogólnie dostępna](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
