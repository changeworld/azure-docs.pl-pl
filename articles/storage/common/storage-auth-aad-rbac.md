---
title: Zarządzanie prawami dostępu do kontenerów i kolejek (wersja zapoznawcza) — usługi Azure Storage przy użyciu kontroli RBAC | Dokumentacja firmy Microsoft
description: Użyj kontroli dostępu opartej na rolach (RBAC) do przypisywania ról, aby uzyskać dostęp do danych obiektów blob i kolejek do użytkowników, grup, jednostek usług aplikacji lub tożsamości usługi zarządzanej. Usługa Azure Storage obsługuje role wbudowane i niestandardowe prawa dostępu do kontenerów i kolejek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/07/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: c26eee05a59a10036138f81086b3f6a9e0de6d5d
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45737298"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą funkcji RBAC (wersja zapoznawcza)

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do kontenerów i kolejki. Gdy rola RBAC jest przypisany do tożsamości usługi Azure AD, tożsamość ma uprawnienia do tych zasobów, zgodnie z określonego zakresu. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Można przypisać prawa dostępu do zasobów usługi Azure Storage przy użyciu witryny Azure portal, narzędzi wiersza polecenia platformy Azure i interfejsów API zarządzania platformy Azure. 

Tożsamości usługi Azure AD może być użytkownika, grupy lub nazwy głównej usługi aplikacji, lub może być tożsamość zarządzaną dla zasobów platformy Azure. Podmiot zabezpieczeń może być użytkownika, grupy lub aplikacji jednostki usługi. A [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) jest automatycznie zarządzanych tożsamości używany do uwierzytelniania z aplikacjami uruchomionymi na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawy skalowania maszyn wirtualnych i innych. Aby uzyskać omówienie tożsamości w usłudze Azure AD, zobacz [Understand Azure tożsamością](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

Usługa Azure Storage obsługuje niestandardowe i wbudowane role RBAC. Usługa Azure Storage oferuje następujące wbudowane role kontroli RBAC do użycia z usługą Azure AD:

- [Współautor danych obiektu Blob Storage (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Czytnik danych obiektu Blob Storage (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Współautor danych kolejki magazynu (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Czytnik danych kolejki magazynu (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Aby uzyskać więcej informacji o tym, jak wbudowane role są definiowane dla usługi Azure Storage, zobacz [zrozumienie definicji ról](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Można również definiować role niestandardowe do użycia z kontenerami i kolejek. Aby uzyskać więcej informacji, zobacz [tworzenie ról niestandardowych dla kontroli dostępu](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-a-role-to-a-security-principal"></a>Przypisywanie roli do podmiotu zabezpieczeń

Przypisz rolę RBAC do tożsamości usługi platformy Azure, aby udzielić uprawnień do kontenerów lub kolejek na koncie magazynu. Można określić zakres przypisania roli do konta magazynu lub do określonego kontenera lub kolejki. W poniższej tabeli przedstawiono prawa dostępu do wbudowanych ról w zależności od zakresu: 

|                                 |     Współautor danych obiektu blob                                                 |     Czytnik danych obiektu blob                                                |     Współautor danych kolejki                                  |     Czytnik danych kolejki                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Ograniczone do subskrypcji       |    Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob w ramach subskrypcji       |    Dostęp do odczytu do wszystkich kontenerów i obiektów blob w ramach subskrypcji       |    Odczyt/zapis dostęp do wszystkich kolejek w ramach subskrypcji       |    Dostęp do odczytu do wszystkich kolejek w ramach subskrypcji         |
|    Zakres do grupy zasobów     |    Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob w grupie zasobów     |    Dostęp do odczytu do wszystkich kontenerów i obiektów blob w grupie zasobów     |    Odczyt/zapis dostęp do wszystkich kolejek w grupie zasobów     |    Dostęp do odczytu do wszystkich kolejek w grupie zasobów     |
|    Ograniczone do konta magazynu    |    Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob na koncie magazynu    |    Dostęp do odczytu do wszystkich kontenerów i obiektów blob na koncie magazynu    |    Odczyt/zapis dostęp do wszystkich kolejek na koncie magazynu    |    Dostęp do odczytu do wszystkich kolejek na koncie magazynu    |
|    Ograniczone do kolejki/kontenera    |    Odczyt/zapis dostęp do określonego kontenera i jego obiektów blob              |    Dostęp do odczytu do określonego kontenera i jego obiektów blob              |    Odczyt/zapis dostęp do określonej kolejki                  |    Dostęp do odczytu do określonej kolejki                    |

> [!NOTE]
> Jako właściciel konta usługi Azure Storage możesz nie są automatycznie przypisywane uprawnienia dostępu do danych. Należy jawnie przypisać sobie rolę RBAC dla usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konto magazynu, kontenera lub kolejki.

Aby uzyskać więcej informacji na temat uprawnień wymaganych do wywoływania operacji usługi Azure Storage, zobacz [uprawnień do wywoływania operacji REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

Poniższe sekcje pokazują, jak przypisać rolę ograniczone do konta magazynu lub do pojedynczy kontener.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Przypisywanie roli ograniczone do konta magazynu w witrynie Azure portal

Aby przypisać rolę wbudowaną, aby umożliwić dostęp do wszystkich kontenerów lub kolejek na koncie magazynu w witrynie Azure portal:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.
2. Wybierz konto magazynu, a następnie wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla konta. Kliknij przycisk **Dodaj** przycisk, aby dodać nową rolę.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu](media/storage-auth-aad-rbac/portal-access-control.png)

3. W **Dodaj uprawnienia** okna, wybierz rolę do przypisania do tożsamości usługi Azure AD. Następnie wyszukaj, aby zlokalizować tożsamości, do którego chcesz przypisać tę rolę. Na przykład na poniższej ilustracji przedstawiono **czytnik danych obiektu Blob Storage (wersja zapoznawcza)** roli przypisanej do użytkownika.

    ![Zrzut ekranu przedstawiający sposób Przypisz rolę RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Kliknij pozycję **Zapisz**. Tożsamość, do którego jest przypisany do roli na liście wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że dodano użytkowników teraz mają uprawnienia do odczytu do wszystkich danych obiektu blob na koncie magazynu.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Przypisywanie roli ograniczone do kontenera lub kolejki w witrynie Azure portal

Czynności podczas przypisywania wbudowana Rola o określonym zakresie do kontenera lub kolejki są podobne. Przedstawionych tu procedurach przypisuje rolę ograniczone do kontenera, ale możesz wykonać te same kroki, aby przypisać rolę ograniczone do kolejki: 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu i wyświetlić **Przegląd** dla konta.
2. W obszarze usługi Blob Service, wybierz **Przeglądaj obiekty BLOB**. 
3. Odszukaj kontener, dla którego chcesz przypisać rolę i wyświetlanie ustawień kontenera. 
4. Wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla kontenera.
5. W **Dodaj uprawnienia** okna, wybierz rolę, którą chcesz przypisać do tożsamości usługi Azure AD. Następnie wyszukaj, aby zlokalizować tożsamości, do którego chcesz przypisać tę rolę.
6. Kliknij pozycję **Zapisz**. Tożsamość, do którego jest przypisany do roli na liście wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że użytkownik dodany teraz ma uprawnienia do odczytu do danych w kontenerze o nazwie *przykładowy kontener*.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o ROLACH, zobacz [Rozpoczynanie pracy przy użyciu kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md).
- Aby dowiedzieć się, jak przypisać i zarządzać przypisaniami ról RBAC przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z aplikacjami usługi Azure Storage](storage-auth-aad-app.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kontenerów platformy Azure i kolejek, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 
