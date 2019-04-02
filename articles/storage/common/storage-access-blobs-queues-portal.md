---
title: Uzyskiwać dostęp do obiektów blob i kolejki danych — Azure Storage, użyj portalu Azure
description: Jeśli uzyskujesz dostęp do obiektów blob lub dane w kolejce przy użyciu witryny Azure portal, portal sprawia, że żądania do usługi Azure Storage w sposób niewidoczny. Te żądania do usługi Azure Storage może zostać uwierzytelniony i autoryzowany przy użyciu swojego konta usługi Azure AD lub klucz dostępu konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3a0d10f373b045058c6df242c4d98f78d04c4490
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801139"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Azure Portal umożliwia dostęp do danych obiektów blob i kolejki

Jeśli uzyskujesz dostęp do obiektów blob i kolejki danych przy użyciu [witryny Azure portal](https://portal.azure.com), portalu zgłasza żądania do usługi Azure Storage w sposób niewidoczny. Te żądania do usługi Azure Storage może zostać uwierzytelniony i autoryzowany przy użyciu swojego konta usługi Azure AD lub klucz dostępu konta magazynu. Portal wskazuje, której metody uwierzytelniania, a umożliwia przełączanie między nimi, jeśli masz odpowiednie uprawnienia.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Uprawnienia wymagane do dostępu do danych obiektów blob i kolejki

W zależności od sposobu uwierzytelniania dostępu do danych obiektów blob lub kolejki w witrynie Azure portal określone uprawnienia będą potrzebne. W większości przypadków te uprawnienia są udostępniane za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji o ROLACH, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Klucz dostępu do konta

Dostępu do danych obiektów blob i kolejek przy użyciu klucza dostępu do konta, musi mieć rolę RBAC przypisane do Ciebie, obejmującą akcji RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Ta rola RBAC, może być wbudowaną lub niestandardową rolę. Wbudowane role, które obsługują **Microsoft.Storage/storageAccounts/listkeys/action** obejmują:

- Usługi Azure Resource Manager [właściciela](../../role-based-access-control/built-in-roles.md#owner) roli
- Usługi Azure Resource Manager [Współautor](../../role-based-access-control/built-in-roles.md#contributor) roli
- [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor) roli

Gdy użytkownik spróbuje uzyskać dostęp do danych obiektów blob lub kolejki w witrynie Azure portal, portalu najpierw sprawdza, czy przypisano Ci rolę z **Microsoft.Storage/storageAccounts/listkeys/action**. Jeśli przypisano rolę za pomocą tej akcji, następnie portal używa klucza konta do uzyskiwania dostępu do danych obiektów blob i kolejek. Jeśli nie przypisano Ci rolę za pomocą tej akcji, portalu próbuje uzyskać dostęp do danych przy użyciu swojego konta usługi Azure AD.

> [!NOTE]
> Klasyczni ról administratora, Administrator usługi i administratora współpracującego zawierają równoważne z usługi Azure Resource Manager [właściciela](../../role-based-access-control/built-in-roles.md#owner) roli. **Właściciela** rola obejmuje wszystkie czynności, w tym **Microsoft.Storage/storageAccounts/listkeys/action**, więc użytkownika przy użyciu jednego z tych ról administracyjnych mogą też uzyskiwać dostęp danych obiektów blob i kolejek przy użyciu klucz konta. Aby uzyskać więcej informacji, zobacz [ról administratora subskrypcji Classic](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Konto Azure AD

Aby uzyskać dostęp do danych obiektów blob i kolejki, w witrynie Azure portal przy użyciu swojego konta usługi Azure AD, oba z następujących instrukcji muszą być spełnione dla Ciebie:

- Przypisano Ci usługi Azure Resource Manager [czytnika](../../role-based-access-control/built-in-roles.md#reader) roli, co najmniej o określonym zakresie na poziomie konta magazynu lub nowszej. **Czytnika** rola przyznaje najbardziej ograniczone uprawnienia, ale dopuszczalne jest również innej roli usługi Azure Resource Manager, która udziela dostępu do zasobów zarządzania konta magazynu.
- Przypisano Ci albo wbudowanych lub niestandardowych rolę, która zapewnia dostęp do obiektów blob i kolejki danych.

**Czytnika** przypisania roli lub innego przypisania roli usługi Azure Resource Manager jest niezbędne, aby użytkownik można przeglądać i nawigować zasobów zarządzania konta magazynu w witrynie Azure portal. Role RBAC, określającymi udzielenie dostępu do danych obiektów blob i kolejki nie może udzielać dostępu do zasobów zarządzania konta magazynu. Aby uzyskać dostęp do danych obiektów blob lub kolejki w portalu, użytkownik musi uprawnienia do nawigacji zasobów konta magazynu. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [przypisania roli Czytelnik do dostępu do portalu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Wbudowane role, które obsługują dostęp do danych obiektów blob lub kolejki obejmują:

- [Właściciel danych obiektu Blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Dla POSIX kontroli dostępu dla usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza).
- [Współautor danych obiektu Blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Uprawnienia odczytu/zapisu/usuwania obiektów blob.
- [Czytnik danych obiektu Blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Uprawnienia tylko do odczytu dla obiektów blob.
- [Storage Queue Data Contributor](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Uprawnienia odczytu/zapisu/usuwania kolejek.
- [Storage Queue Data Reader](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Uprawnienia tylko do odczytu dla kolejek.
    
Role niestandardowe mogą obsługiwać różne kombinacje te same uprawnienia, które są dostarczane przez wbudowane role. Aby uzyskać więcej informacji na temat tworzenia niestandardowych ról RBAC, zobacz [niestandardowych ról dla zasobów platformy Azure](../../role-based-access-control/custom-roles.md) i [poznać definicje ról na potrzeby zasobów platformy Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Lista kolejek z rolą administratora klasyczni nie jest obsługiwane. Aby wyświetlić listę kolejek, użytkownik musi mieć przypisany do nich usługi Azure Resource Manager **czytnika** roli **czytnik danych kolejki magazynu** roli lub **Współautor danych kolejki magazynu** Rola.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Przejdź do obiektów blob i kolejki w portalu

Aby wyświetlić dane obiektów blob lub kolejki w portalu, przejdź do **Przegląd** swoje konto magazynu, a następnie kliknij przycisk łącza, aby **obiektów blob** lub **kolejek**. Alternatywnie możesz przejść do **usługi Blob service** i **Usługa kolejkowania** sekcje, w menu. 

![Przejdź do obiektu blob lub kolejki danych w witrynie Azure portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Określić bieżącą metodę uwierzytelniania

Po przejściu do kontenera lub kolejki witryny Azure portal wskazuje, czy obecnie używasz klucza dostępu do konta lub konta usługi Azure AD do uwierzytelniania.

W przykładach w tej sekcji przedstawiono, uzyskiwanie dostępu do kontenera i jego obiektów blob, ale portalu jest wyświetlany ten sam komunikat podczas uzyskiwania dostępu do kolejki i jego wiadomości, lub listę kolejek.

### <a name="account-access-key"></a>Klucz dostępu do konta

Jeśli używasz uwierzytelniania przy użyciu klucza dostępu do konta, zobaczysz **klucz dostępu** określony jako metodę uwierzytelniania w portalu:

![Często uzyskują dostęp do kontenera danych przy użyciu klucza konta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Aby przełączyć się do korzystania z konta usługi Azure AD, kliknij link, wyróżniony na obrazie. Jeśli masz odpowiednie uprawnienia, za pośrednictwem ról RBAC, które są przypisane do Ciebie, będzie można kontynuować. Jednakże jeśli nie masz odpowiednich uprawnień, zobaczysz komunikat o błędzie podobny do następującego:

![Błąd pokazany, jeśli konto usługi Azure AD nie obsługuje dostępu](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Należy zauważyć, że nie obiektów blob są wyświetlane na liście, jeśli Twoje konto usługi Azure AD nie ma uprawnień, aby je wyświetlić. Kliknij pozycję **przełącznik, aby klucz dostępu** łącze do uwierzytelniania w ponownie klucz dostępu.

### <a name="azure-ad-account"></a>Konto Azure AD

Jeśli używasz uwierzytelniania przy użyciu swojego konta usługi Azure AD, zobaczysz **konta użytkownika usługi Azure AD** określony jako metodę uwierzytelniania w portalu:

![Często uzyskują dostęp do kontenera danych za pomocą konta usługi Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Aby przełączyć się do korzystania z klucza dostępu do konta, kliknij link, wyróżniony na obrazie. Jeśli masz dostęp do klucza konta, będziesz mieć możliwość kontynuowania. Jednakże jeśli nie masz dostępu do klucza konta, zobaczysz komunikat o błędzie podobny do następującego:

![Błąd pokazany, jeśli nie masz dostępu do klucza konta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Należy zauważyć, że nie obiekty BLOB są wyświetlane na liście, jeśli nie masz dostępu do kluczy konta. Kliknij pozycję **Przełącz się na konto użytkownika usługi Azure AD** łącze do używania konta usługi Azure AD do uwierzytelniania ponownie.

## <a name="next-steps"></a>Kolejne kroki

- [Uwierzytelnianie dostępu do obiektów blob platformy Azure i queues, korzystając z usługi Azure Active Directory](storage-auth-aad.md)
- [Udzielanie dostępu do kontenerów platformy Azure i kolejek o ROLACH w witrynie Azure portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu RBAC przy użyciu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu kontroli RBAC przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)