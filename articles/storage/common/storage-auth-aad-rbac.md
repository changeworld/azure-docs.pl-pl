---
title: Użycie funkcji RBAC do zarządzania prawami dostępu do usługi Azure Storage kontenery i kolejek (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj kontroli dostępu opartej na rolach (RBA) do przypisywania ról w celu udzielenia dostępu do danych usługi Azure Storage do użytkowników, grup, nazwy główne usług aplikacji lub tożsamości usługi zarządzane. Usługa Azure Storage obsługuje role wbudowane i niestandardowe prawa dostępu do kontenerów i kolejek.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.openlocfilehash: cb77bd4418e105c877202f0f1725350380ea2308
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660877"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC (wersja zapoznawcza)

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zasobów zabezpieczonych za pomocą [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Usługa Azure Storage definiuje zestaw wbudowane role RBAC, które obejmują wspólne zestawy uprawnienia umożliwiające dostęp do kontenerów lub kolejek. Gdy RBAC rola jest przypisywana do tożsamości usługi Azure AD, czy tożsamości udzielono dostępu do tych zasobów, zgodnie z określonego zakresu. Dostęp może zakresu na poziomie subskrypcji, grupy zasobów, konta magazynu lub poszczególnych kontenera lub kolejki. Można przypisać prawa dostępu do zasobów usługi Azure Storage przy użyciu portalu Azure, narzędzi wiersza polecenia platformy Azure i interfejsów API usługi Azure Management. 

Tożsamość usługi Azure AD może być użytkownika, grupy lub nazwy głównej usługi aplikacji lub może być *tożsamość usługi zarządzanej*. Podmiot zabezpieczeń może być użytkownika, grupy lub nazwy głównej usługi aplikacji. A [tożsamość usługi zarządzanej](../../active-directory/managed-service-identity/overview.md) jest automatycznie zarządzane tożsamości używany do uwierzytelniania z aplikacji uruchomionych w maszynach wirtualnych platformy Azure, funkcja aplikacji zestawy skalowania maszyny wirtualnej i innych użytkowników. Omówienie tożsamości w usłudze Azure AD, zobacz [tożsamościach na platformie Azure z zrozumieć](https://docs.microsoft.com/en-us/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-azure-storage"></a>Role RBAC dla usługi Azure Storage

Usługa Azure Storage obsługuje zarówno wbudowane i niestandardowe role RBAC. Magazyn Azure oferuje następujące wbudowane role RBAC do użycia z usługą Azure AD:

- [Magazyn obiektów Blob danych Współautor (wersja zapoznawcza)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Czytnik danych magazynu obiektów Blob (wersja zapoznawcza)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Magazyn kolejek współautorem (wersja zapoznawcza)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Czytnik danych kolejki magazynu (wersja zapoznawcza)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Aby uzyskać więcej informacji o tym, jak wbudowane role są definiowane dla usługi Azure Storage, zobacz [poznać definicje ról](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Istnieje również możliwość definiowania ról niestandardowych do użytku z kontenerów i kolejek. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowych ról dla kontroli dostępu](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md). 

> [!IMPORTANT]
> Ta wersja zapoznawcza jest przeznaczony tylko do użytku z systemem innym niż produkcji. Produkcyjnej umowy poziomu usług (SLA) nie będą dostępne, dopóki integracji z usługą Azure AD dla usługi Azure Storage jest zadeklarowany jako ogólnie dostępna. Jeśli dla danego scenariusza integracji z usługą Azure AD nie jest jeszcze obsługiwana, nadal używać klucza wspólnego autoryzacji lub tokeny sygnatury dostępu Współdzielonego w aplikacji. Aby uzyskać dodatkowe informacje o wersji zapoznawczej, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
>
> W wersji zapoznawczej przypisań ról RBAC może potrwać maksymalnie pięć minut propagacji.

## <a name="assign-a-role-to-a-security-principal"></a>Przypisywanie roli do podmiotu zabezpieczeń

Przypisz rolę RBAC do tożsamość platformy Azure, aby udzielić uprawnień do kontenerów lub kolejek na koncie magazynu. Można określić zakres przypisania roli do konta magazynu lub do określonego kontenera lub kolejki. W poniższej tabeli przedstawiono prawa dostępu przyznane przez wbudowane role, w zależności od zakresu: 

|                                 |     Obiekt blob danych współautora                                                 |     Czytnik danych obiektów blob                                                |     Kolejka danych współautora                                  |     Czytnik danych kolejki                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Ograniczone do subskrypcji       |    Dostęp do odczytu/zapisu do wszystkich kontenerów i obiektów blob w subskrypcji       |    Dostęp do odczytu do wszystkich kontenerów i obiektów blob w subskrypcji       |    Dostęp do odczytu/zapisu do wszystkich kolejek w ramach subskrypcji       |    Dostęp do odczytu do wszystkich kolejek w ramach subskrypcji         |
|    Zakres do grupy zasobów     |    Dostęp do odczytu/zapisu do wszystkich kontenerów i obiektów blob w grupie zasobów     |    Dostęp do odczytu do wszystkich kontenerów i obiektów blob w grupie zasobów     |    Dostęp do odczytu/zapisu do wszystkich kolejek w grupie zasobów     |    Dostęp do odczytu do wszystkich kolejek w grupie zasobów     |
|    Zakres do konta magazynu    |    Dostęp do odczytu i zapisu do wszystkich kontenerów i obiektów blob na koncie magazynu    |    Dostęp do odczytu do wszystkich kontenerów i obiektów blob na koncie magazynu    |    Dostęp do odczytu/zapisu do wszystkich kolejek w ramach konta magazynu    |    Dostęp do odczytu do wszystkich kolejek w ramach konta magazynu    |
|    Zakres do kontenera/kolejki    |    Dostęp do odczytu i zapisu do określonego kontenera i jego obiektów blob              |    Dostęp do odczytu określonego kontenera i jego obiektów blob              |    Dostęp do odczytu i zapisu do określonej kolejki                  |    Dostęp do odczytu do określonej kolejki                    |

> [!NOTE]
> Jako właściciel konta magazynu Azure użytkownik nie jest automatycznie przypisywana uprawnienia dostępu do danych. Należy jawnie przypisać samodzielnie roli RBAC dla usługi Azure Storage. Należy przypisać mu na poziomie subskrypcji, grupy zasobów, konta magazynu, kontenera lub kolejki.

Aby uzyskać więcej informacji dotyczących uprawnień wymaganych do wywołania operacji usługi Azure Storage, zobacz [uprawnienia do wywoływania operacji REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

Poniższe sekcje pokazują, jak przypisać rolę ograniczone do konta magazynu lub do poszczególnych kontenera.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Przypisywanie roli ograniczone do konta magazynu w portalu Azure

Aby przypisać rolę wbudowanych udzielanie dostępu do wszystkich kontenerów lub kolejek w ramach konta magazynu w portalu Azure:

1. W [portalu Azure](https://azure.portal.com/), przejdź do swojego konta magazynu.
2. Wybierz konto magazynu, a następnie wybierz **kontroli dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla konta. Kliknij przycisk **Dodaj** przycisk, aby dodać nową rolę.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu](media/storage-auth-aad-rbac/portal-access-control.png)

3. W **dodać uprawnienia** okna, wybierz rolę do przypisania do tożsamości usługi Azure AD. Następnie wyszukaj, aby zlokalizować tożsamości, do którego chcesz przypisać tej roli. Na przykład poniższy obraz przedstawia **czytnik danych magazynu obiektów Blob (wersja zapoznawcza)** roli przypisanej do użytkownika.

    ![Zrzut ekranu przedstawiający sposób przypisywania roli RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Kliknij pozycję **Zapisz**. Tożsamość, któremu przypisano rolę wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, czy dodano użytkowników teraz ma uprawnienia do odczytu wszystkich danych obiektu blob na koncie magazynu.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Przypisywanie roli zakres do kontenera lub kolejki w portalu Azure

Kroki do przypisywania roli wbudowany zakres kontener lub kolejki są podobne. Procedury pokazane przypisuje rolę zakres do kontenera, ale można wykonać te same kroki, aby przypisać rolę zakres do kolejki: 

1. W [portalu Azure](https://azure.portal.com/), przejdź do swojego konta magazynu i wyświetlić **omówienie** dla konta.
2. W obszarze usługi obiektów Blob, wybierz **Przeglądaj obiekty BLOB**. 
3. Zlokalizuj kontener, dla którego chcesz przypisać rolę, ustawienia i wyświetlania kontenera. 
4. Wybierz **kontroli dostępu (IAM)** do wyświetlania ustawień kontroli dostępu do kontenera.
5. W **dodać uprawnienia** okna, wybierz rolę, którą chcesz przypisać do tożsamości usługi Azure AD. Następnie wyszukaj, aby zlokalizować tożsamości, do którego chcesz przypisać tej roli.
6. Kliknij pozycję **Zapisz**. Tożsamość, któremu przypisano rolę wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że użytkownik dodany teraz ma uprawnienia do odczytu danych w kontenerze o nazwie *przykładowy kontener*.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat RBAC, zobacz [Rozpoczynanie pracy z kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md).
- Aby dowiedzieć się, jak przypisać i zarządzanie nimi RBAC przypisań ról za pomocą programu Azure PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Zarządzanie kontrolą dostępu na podstawie ról (RBAC) przy użyciu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu na podstawie ról (RBAC) przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby uzyskać informacje o autoryzacji dostępu do kontenerów i kolejki ze w aplikacjach magazynu, zobacz [użycia usługi Azure AD z aplikacjami usługi Azure Storage](storage-auth-aad-app.md).
- Aby uzyskać dodatkowe informacje na temat integracji usługi Azure AD dla kontenerów Azure i kolejek, zobacz w usłudze Azure Storage team na blogu, [Announcing uwierzytelniania Podgląd Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 