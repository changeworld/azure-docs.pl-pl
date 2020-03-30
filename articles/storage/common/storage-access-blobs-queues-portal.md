---
title: Dostęp do danych obiektów blob lub kolejek za pomocą witryny Azure Portal
titleSuffix: Azure Storage
description: Podczas uzyskiwania dostępu do danych obiektów blob lub kolejek przy użyciu witryny Azure portal wysuwa żądania do usługi Azure Storage w ramach okładek. Te żądania do usługi Azure Storage można uwierzytelnić i autoryzować przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866896"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Dostęp do danych obiektów blob lub kolejek za pomocą witryny Azure Portal

Podczas uzyskiwania dostępu do danych obiektów blob lub kolejek przy użyciu [witryny Azure portal,](https://portal.azure.com)portal żąda usługi Azure Storage w ramach okładek. Żądanie usługi Azure Storage można autoryzować przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu. Portal wskazuje, która metoda jest używany i umożliwia przełączanie się między nimi, jeśli masz odpowiednie uprawnienia.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Uprawnienia potrzebne do uzyskiwania dostępu do danych obiektów blob lub kolejki

W zależności od tego, jak chcesz autoryzować dostęp do danych obiektów blob lub kolejek w witrynie Azure portal, będziesz potrzebować określonych uprawnień. W większości przypadków te uprawnienia są dostarczane za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat RBAC, zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Klucz dostępu do konta

Aby uzyskać dostęp do danych obiektów blob i kolejki za pomocą klucza dostępu do konta, musi być przypisana rola RBAC, która obejmuje akcję RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Ta rola RBAC może być wbudowana lub niestandardowa. Wbudowane role obsługujące **firmę Microsoft.Storage/storageAccounts/listkeys/action** obejmują:

- Rola [właściciela](../../role-based-access-control/built-in-roles.md#owner) usługi Azure Resource Manager
- Rola [współautora](../../role-based-access-control/built-in-roles.md#contributor) usługi Azure Resource Manager
- Rola [współautora konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Podczas próby uzyskania dostępu do danych obiektów blob lub kolejki w witrynie Azure portal najpierw sprawdza, czy przypisano rolę za pomocą **programu Microsoft.Storage/storageAccounts/listkeys/action**. Jeśli przypisano rolę z tą akcją, portal używa klucza konta do uzyskiwania dostępu do danych obiektów blob i kolejki. Jeśli nie przypisano roli za pomocą tej akcji, portal próbuje uzyskać dostęp do danych przy użyciu konta usługi Azure AD.

> [!NOTE]
> Klasyczne role administratora subskrypcji Service Administrator i Współadministrator obejmują odpowiednik roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) usługi Azure Resource Manager. Rola **Właściciel** obejmuje wszystkie akcje, w tym **microsoft.storage/storageAccounts/listkeys/action**, dzięki czemu użytkownik z jedną z tych ról administracyjnych może również uzyskiwać dostęp do danych obiektów blob i kolejki za pomocą klucza konta. Aby uzyskać więcej informacji, zobacz [Klasyczne role administratora subskrypcji](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Konto Azure AD

Aby uzyskać dostęp do danych obiektów blob lub kolejek z witryny Azure portal przy użyciu konta usługi Azure AD, obie następujące instrukcje muszą być spełnione:

- Przypisano rolę programu Azure Resource [Manager,](../../role-based-access-control/built-in-roles.md#reader) co najmniej o zakresie do poziomu konta magazynu lub wyższego. Rola **czytelnika** udziela najbardziej ograniczonych uprawnień, ale inna rola usługi Azure Resource Manager, która udziela dostępu do zasobów zarządzania kontami magazynu jest również dopuszczalne.
- Przypisano ci rolę wbudowaną lub niestandardową, która zapewnia dostęp do danych obiektów blob lub kolejki.

Przypisanie roli **czytelnika** lub inne przypisanie roli usługi Azure Resource Manager jest konieczne, aby użytkownik mógł wyświetlać zasoby zarządzania kontami magazynu i nawigować w witrynie Azure portal. Role RBAC, które udzielają dostępu do danych obiektów blob lub kolejki, nie udzielają dostępu do zasobów zarządzania kontem magazynu. Aby uzyskać dostęp do danych obiektów blob lub kolejki w portalu, użytkownik potrzebuje uprawnień do nawigacji zasobów konta magazynu. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [Przypisywanie roli czytnika dostępu do portalu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Wbudowane role, które obsługują dostęp do danych obiektu blob lub kolejki obejmują:

- [Właściciel danych obiektów blob magazynu:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)dla kontroli dostępu POSIX dla usługi Azure Data Lake Storage Gen2.
- [Współautor danych obiektów blob magazynu:](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)uprawnienia do odczytu/zapisu/usuwania obiektów blob.
- [Czytnik danych obiektów blob magazynu:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)uprawnienia tylko do odczytu dla obiektów blob.
- [Współautor danych kolejki magazynu:](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)uprawnienia do odczytu/zapisu/usuwania dla kolejek.
- [Czytnik danych kolejki magazynu:](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)uprawnienia tylko do odczytu dla kolejek.
    
Role niestandardowe mogą obsługiwać różne kombinacje tych samych uprawnień zapewnianych przez wbudowane role. Aby uzyskać więcej informacji na temat tworzenia niestandardowych ról RBAC, zobacz [Role niestandardowe dla zasobów platformy Azure](../../role-based-access-control/custom-roles.md) i poznaj [definicje ról dla zasobów platformy Azure.](../../role-based-access-control/role-definitions.md)

Kolejki aukcji z klasyczną rolą administratora subskrypcji nie są obsługiwane. Aby wyświetlić listę kolejek, użytkownik musi mieć przypisane do nich rolę **czytnika** usługi Azure Resource Manager, rolę **czytnika danych kolejki magazynu** lub rolę **współautora danych kolejki magazynu.**

> [!IMPORTANT]
> Wersja zapoznawcza Eksploratora magazynu w witrynie Azure portal nie obsługuje przy użyciu poświadczeń usługi Azure AD do wyświetlania i modyfikowania danych obiektów blob lub kolejki. Eksplorator magazynu w witrynie Azure portal zawsze używa kluczy konta, aby uzyskać dostęp do danych. Aby korzystać z Eksploratora magazynu w witrynie Azure portal, należy przypisać rolę, która obejmuje **microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Przechodzenie do obiektów blob lub kolejek w portalu

Aby wyświetlić dane obiektów blob lub kolejki w portalu, przejdź do **przeglądu** konta magazynu i kliknij łącza do **obiektów Blobs** lub **Queues**. Alternatywnie można przejść do **usługi obiektów Blob** i **kolejki sekcji usługi** w menu. 

![Przechodzenie do danych obiektów blob lub kolejek w witrynie Azure portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Określanie bieżącej metody uwierzytelniania

Podczas przechodzenia do kontenera lub kolejki witryny Azure Portal wskazuje, czy obecnie używasz klucza dostępu do konta lub konta usługi Azure AD do uwierzytelniania.

Przykłady w tej sekcji pokazują dostęp do kontenera i jego obiektów blob, ale portal wyświetla ten sam komunikat podczas uzyskiwania dostępu do kolejki i jej wiadomości lub kolejki listy.

### <a name="account-access-key"></a>Klucz dostępu do konta

Jeśli uwierzytelniasz się przy użyciu klucza dostępu do konta, zobaczysz **klucz dostępu** określony jako metoda uwierzytelniania w portalu:

![Obecnie uzyskiwanie dostępu do danych kontenera za pomocą klucza konta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Aby przełączyć się do korzystania z konta usługi Azure AD, kliknij łącze wyróżnione na obrazie. Jeśli masz odpowiednie uprawnienia za pośrednictwem ról RBAC, które są przypisane do Ciebie, będzie można kontynuować. Jeśli jednak nie masz odpowiednich uprawnień, zostanie wyświetlony komunikat o błędzie, taki jak następujący:

![Błąd wyświetlany, jeśli konto usługi Azure AD nie obsługuje dostępu](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Należy zauważyć, że żadne obiekty blob są wyświetlane na liście, jeśli konto usługi Azure AD nie ma uprawnień do ich wyświetlania. Kliknij **łącze Przełącz, aby uzyskać dostęp** do klucza dostępu, aby ponownie użyć klucza dostępu do uwierzytelniania.

### <a name="azure-ad-account"></a>Konto Azure AD

Jeśli uwierzytelniasz się przy użyciu konta usługi Azure AD, zobaczysz **konto użytkownika usługi Azure AD** określone jako metoda uwierzytelniania w portalu:

![Obecnie uzyskiwanie dostępu do danych kontenera za pomocą konta usługi Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Aby przełączyć się na korzystanie z klucza dostępu do konta, kliknij łącze wyróżnione na obrazie. Jeśli masz dostęp do klucza konta, będziesz mógł kontynuować. Jeśli jednak nie masz dostępu do klucza konta, zostanie wyświetlony komunikat o błędzie, taki jak następujący:

![Błąd wyświetlany, jeśli nie masz dostępu do klucza konta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Należy zauważyć, że żadne obiekty blob są wyświetlane na liście, jeśli nie masz dostępu do kluczy konta. Kliknij **łącze Przełącz na konto użytkownika usługi Azure AD,** aby ponownie użyć konta usługi Azure AD do uwierzytelniania.

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnij dostęp do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](storage-auth-aad.md)
- [Udzielanie dostępu do kontenerów i kolejek platformy Azure za pomocą usługi RBAC w witrynie Azure portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach przy użyciu interfejsu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [ przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)
