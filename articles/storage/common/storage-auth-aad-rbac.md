---
title: Zarządzanie prawami dostępu do kontenerów i kolejek (wersja zapoznawcza) — usługi Azure Storage przy użyciu kontroli RBAC | Dokumentacja firmy Microsoft
description: Użyj kontroli dostępu opartej na rolach (RBAC) do przypisywania ról, aby uzyskać dostęp do danych obiektów blob i kolejek do użytkowników, grup, jednostek usług aplikacji lub tożsamości usługi zarządzanej. Usługa Azure Storage obsługuje role wbudowane i niestandardowe prawa dostępu do kontenerów i kolejek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: fce89cc754ac179054a60ce837949bb02b2102c6
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408701"
---
# <a name="manage-access-rights-to-azure-blob-and-queue-data-with-rbac-preview"></a>Zarządzanie prawami dostępu do obiektów Blob platformy Azure i dane w kolejce przy użyciu RBAC (wersja zapoznawcza)

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do kontenerów i kolejki. Gdy rola RBAC jest przypisany do tożsamości usługi Azure AD, tożsamość ma uprawnienia do tych zasobów, zgodnie z określonego zakresu. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Można przypisać prawa dostępu do zasobów usługi Azure Storage przy użyciu witryny Azure portal, narzędzi wiersza polecenia platformy Azure i interfejsów API zarządzania platformy Azure. 

Tożsamości usługi Azure AD może być użytkownika, grupy lub nazwy głównej usługi aplikacji, lub może być tożsamość zarządzaną dla zasobów platformy Azure. Podmiot zabezpieczeń może być użytkownika, grupy lub aplikacji jednostki usługi. A [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) jest automatycznie zarządzanych tożsamości używany do uwierzytelniania z aplikacjami uruchomionymi na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawy skalowania maszyn wirtualnych i innych. Aby uzyskać omówienie tożsamości w usłudze Azure AD, zobacz [Understand Azure tożsamością](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

Usługa Azure Storage obsługuje niestandardowe i wbudowane role RBAC. Usługa Azure Storage oferuje następujące wbudowane role kontroli RBAC do użycia z usługą Azure AD:

- [Właściciel danych obiektu Blob Storage (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview): Użyj, aby ustawić własność i zarządzania kontroli dostępu POSIX dla usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu Blob Storage (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania zasobów magazynu obiektów Blob.
- [Czytnik danych obiektu Blob Storage (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do zasobów magazynu obiektów Blob.
- [Współautor danych kolejki magazynu (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania do kolejek systemu Azure.
- [Czytnik danych kolejki magazynu (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do kolejek systemu Azure.

Aby uzyskać więcej informacji o tym, jak wbudowane role są definiowane dla usługi Azure Storage, zobacz [zrozumienie definicji ról](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Można również definiować role niestandardowe do użycia z kontenerami i kolejek. Aby uzyskać więcej informacji, zobacz [tworzenie ról niestandardowych dla kontroli dostępu](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-a-role-to-a-security-principal"></a>Przypisywanie roli do podmiotu zabezpieczeń

Przypisz rolę RBAC do tożsamości usługi platformy Azure, aby udzielić uprawnień do kontenerów lub kolejek na koncie magazynu. Można określić zakres przypisania roli do konta magazynu lub do określonego kontenera lub kolejki. W poniższej tabeli przedstawiono prawa dostępu do wbudowanych ról w zależności od zakresu:

|Zakres|Właściciel danych obiektów blob|Współautor danych obiektu blob|Czytnik danych obiektu blob|Współautor danych kolejki|Czytnik danych kolejki|
|---|---|---|---|---|---|
|Poziom subskrypcji|Dostęp do odczytu/zapisu i POSIX dostęp do zarządzania kontrolą do wszystkich kontenerów i obiektów blob w ramach subskrypcji|Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob w ramach subskrypcji| Dostęp do odczytu do wszystkich kontenerów i obiektów blob w ramach subskrypcji|Odczyt/zapis dostęp do wszystkich kolejek w ramach subskrypcji|Dostęp do odczytu do wszystkich kolejek w ramach subskrypcji|
|Poziomu grupy zasobów|Dostęp do odczytu/zapisu i POSIX dostęp do zarządzania kontrolą do wszystkich kontenerów i obiektów blob w grupie zasobów|Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob w grupie zasobów|Dostęp do odczytu do wszystkich kontenerów i obiektów blob w grupie zasobów|Odczyt/zapis dostęp do wszystkich kolejek w grupie zasobów|Dostęp do odczytu do wszystkich kolejek w grupie zasobów|
|Na poziomie konta magazynu|Dostęp do odczytu/zapisu i POSIX dostęp do zarządzania kontrolą do wszystkich kontenerów i obiektów blob na koncie magazynu|Odczyt/zapis dostęp do wszystkich kontenerów i obiektów blob na koncie magazynu|Dostęp do odczytu do wszystkich kontenerów i obiektów blob na koncie magazynu|Odczyt/zapis dostęp do wszystkich kolejek na koncie magazynu|Dostęp do odczytu do wszystkich kolejek na koncie magazynu|
|Poziom kontenera/kolejki|Dostęp do odczytu/zapisu i POSIX dostępu do zarządzania kontrolą do określonego kontenera i jego obiektów blob.|Odczyt/zapis dostęp do określonego kontenera i jego obiektów blob|Dostęp do odczytu do określonego kontenera i jego obiektów blob|Odczyt/zapis dostęp do określonej kolejki|Dostęp do odczytu do określonej kolejki|

> [!NOTE]
> Jako właściciel konta usługi Azure Storage możesz nie są automatycznie przypisywane uprawnienia dostępu do danych. Należy jawnie przypisać sobie rolę RBAC dla usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konto magazynu, kontenera lub kolejki.

Aby uzyskać więcej informacji na temat uprawnień wymaganych do wywoływania operacji usługi Azure Storage, zobacz [uprawnień do wywoływania operacji REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

Poniższe sekcje pokazują, jak przypisać rolę ograniczone do konta magazynu lub do pojedynczy kontener.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Przypisywanie roli ograniczone do konta magazynu w witrynie Azure portal

Aby przypisać rolę wbudowaną, aby umożliwić dostęp do wszystkich kontenerów lub kolejek na koncie magazynu w witrynie Azure portal:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.
1. Wybierz konto magazynu, a następnie wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla konta. Wybierz **przypisań ról** kartę, aby wyświetlić listę przypisań ról.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu](media/storage-auth-aad-rbac/portal-access-control.png)

1. Kliknij przycisk **Dodaj przypisanie roli** przycisk, aby dodać nową rolę.
1. W **Dodaj przypisanie roli** okna, wybierz rolę do przypisania do tożsamości usługi Azure AD. Następnie wyszukaj, aby zlokalizować tożsamości, do którego chcesz przypisać tę rolę. Na przykład na poniższej ilustracji przedstawiono **czytnik danych obiektu Blob Storage (wersja zapoznawcza)** roli przypisanej do użytkownika.

    ![Zrzut ekranu przedstawiający sposób Przypisz rolę RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Kliknij pozycję **Zapisz**. Tożsamość, do którego jest przypisany do roli na liście wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że użytkownik dodany teraz ma uprawnienia do odczytu do wszystkich danych obiektu blob na koncie magazynu.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Przypisywanie roli ograniczone do kontenera lub kolejki w witrynie Azure portal

> [!IMPORTANT]
> Nie można tego zrobić, jeśli używasz konta z hierarchicznej przestrzeni nazw, jeszcze włączone.

Czynności podczas przypisywania wbudowana Rola o określonym zakresie do kontenera lub kolejki są podobne. Przedstawionych tu procedurach przypisuje rolę ograniczone do kontenera, ale możesz wykonać te same kroki, aby przypisać rolę ograniczone do kolejki: 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu i wyświetlić **Przegląd** dla konta.
1. W obszarze usługi wybierz **obiektów blob**. 
1. Odszukaj kontener, dla którego chcesz przypisać rolę i wyświetlanie ustawień kontenera. 
1. Wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla kontenera. Wybierz **przypisań ról** kartę, aby wyświetlić listę przypisań ról.

    ![Ustawienia kontroli dostępu do kontenera zrzut ekranu](media/storage-auth-aad-rbac/portal-access-control-container.png)
1. Kliknij przycisk **Dodaj przypisanie roli** przycisk, aby dodać nową rolę.
1. W **Dodaj przypisanie roli** okna, wybierz rolę, którą chcesz przypisać do tożsamości usługi Azure AD. Następnie wyszukaj, aby zlokalizować tożsamości, do którego chcesz przypisać tę rolę.
1. Kliknij pozycję **Zapisz**. Tożsamość, do którego jest przypisany do roli na liście wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że użytkownik dodany teraz ma uprawnienia do odczytu do danych w kontenerze o nazwie *przykładowy kontener*.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o ROLACH, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).
- Aby dowiedzieć się, jak przypisać i zarządzać przypisaniami ról RBAC przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z aplikacjami usługi Azure Storage](storage-auth-aad-app.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kontenerów platformy Azure i kolejek, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
