---
title: Przypisywanie roli RBAC do dostępu do danych za pomocą portalu Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą witryny Azure Portal przypisywać uprawnienia do podmiotu zabezpieczeń usługi Azure Active Directory za pomocą kontroli dostępu opartej na rolach (RBAC). Usługa Azure Storage obsługuje wbudowane i niestandardowe role RBAC do uwierzytelniania za pośrednictwem usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d224bd9e9e7b1f8fc9eb45d85e78811d8642fc78
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519561"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Przypisywanie roli RBAC w celu uzyskania dostępu do danych obiektów blob i kolejek za pomocą witryny Azure Portal

Usługa Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC).](../../role-based-access-control/overview.md) Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do danych obiektów blob lub kolejki.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być ograniczony do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, jednostką usługi aplikacji lub [tożsamością zarządzaną dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

W tym artykule opisano sposób przypisywania ról RBAC za pomocą portalu Azure. Portal Azure zawiera prosty interfejs do przypisywania ról RBAC i zarządzania dostępem do zasobów magazynu. Można również przypisać role RBAC dla zasobów obiektów blob i kolejek przy użyciu narzędzi wiersza polecenia platformy Azure lub interfejsów API zarządzania usługą Azure Storage. Aby uzyskać więcej informacji na temat ról RBAC dla zasobów magazynu, zobacz [Uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](storage-auth-aad.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określanie zakresu zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Przypisywanie ról RBAC za pomocą witryny Azure portal

Po określenie odpowiedniego zakresu przypisania roli przejdź do tego zasobu w witrynie Azure portal. Wyświetl ustawienia **kontroli dostępu (IAM)** zasobu i postępuj zgodnie z poniższymi instrukcjami, aby zarządzać przypisaniami ról:

1. Przypisz odpowiednią rolę RBAC usługi Azure Storage, aby udzielić dostępu do podmiotu zabezpieczeń usługi Azure AD.

1. Przypisz rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) usługi Azure Resource Manager do użytkowników, którzy muszą uzyskać dostęp do kontenerów lub kolejek za pośrednictwem witryny Azure portal przy użyciu ich poświadczeń usługi Azure AD. 

W poniższych sekcjach opisano każdy z tych kroków bardziej szczegółowo.

> [!NOTE]
> Jako właściciel konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych. Należy jawnie przypisać sobie rolę RBAC dla usługi Azure Storage. Można przypisać go na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera lub kolejki.
>
> Nie można przypisać roli o zakresie do kontenera lub kolejki, jeśli na koncie magazynu jest włączony hierarchiczny obszar nazw.

### <a name="assign-a-built-in-rbac-role"></a>Przypisywanie wbudowanej roli RBAC

Przed przypisaniem roli do podmiotu zabezpieczeń należy wziąć pod uwagę zakres przyznanych uprawnień. Przejrzyj sekcję [Określanie zakresu zasobów,](#determine-resource-scope) aby zdecydować o odpowiednim zakresie.

Procedura pokazana w tym miejscu przypisuje rolę o zakresie do kontenera, ale można wykonać te same kroki, aby przypisać rolę o zakresie do kolejki:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu i wyświetl **omówienie** konta.
1. W obszarze Usługi wybierz pozycję **Blobs**.
1. Znajdź kontener, dla którego chcesz przypisać rolę, i wyświetl ustawienia kontenera.
1. Wybierz **formę kontroli dostępu (IAM),** aby wyświetlić ustawienia kontroli dostępu dla kontenera. Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do kontenera](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Kliknij przycisk **Dodaj przypisanie roli,** aby dodać nową rolę.
1. W oknie **Dodawanie przypisania roli** wybierz rolę usługi Azure Storage, którą chcesz przypisać. Następnie wyszukaj, aby zlokalizować podmiot zabezpieczeń, do którego chcesz przypisać tę rolę.

    ![Zrzut ekranu przedstawiający sposób przypisywania roli RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Kliknij przycisk **Zapisz**. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji pokazano, że użytkownik dodany ma teraz uprawnienia do odczytu do danych w kontenerze o nazwie *sample-container*.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Można wykonać podobne kroki, aby przypisać rolę o zakresie do konta magazynu, grupy zasobów lub subskrypcji.

### <a name="assign-the-reader-role-for-portal-access"></a>Przypisywanie roli czytnika dostępu do portalu

Po przypisaniu wbudowanej lub niestandardowej roli usługi Azure Storage do podmiotu zabezpieczeń udzielasz uprawnień temu podmiotowi zabezpieczeń do wykonywania operacji na danych na koncie magazynu. Wbudowane role **programu Data Reader** zapewniają uprawnienia do odczytu danych w kontenerze lub kolejce, podczas gdy wbudowane role **współautora danych** zapewniają uprawnienia do odczytu, zapisu i usuwania kontenera lub kolejki. Uprawnienia są ograniczone do określonego zasobu.  
Na przykład jeśli przypisać **roli współautora danych magazynu magazynu** do użytkownika Mary na poziomie kontenera o nazwie **sample-container**, następnie Mary jest przyznawana odczytu, zapisu i usunąć dostęp do wszystkich obiektów blob w tym kontenerze.

Jednak jeśli Mary chce wyświetlić obiekt blob w witrynie Azure portal, a następnie **roli współautora danych obiektu blob magazynu** sam nie zapewni wystarczających uprawnień do przechodzenia przez portal do obiektu blob w celu jego wyświetlenia. Dodatkowe uprawnienia usługi Azure AD są wymagane do nawigacji po portalu i wyświetlić inne zasoby, które są tam widoczne.

Jeśli użytkownicy muszą mieć możliwość dostępu do obiektów blob w witrynie Azure portal, a następnie przypisać im dodatkową rolę RBAC, roli [czytnika,](../../role-based-access-control/built-in-roles.md#reader) do tych użytkowników, na poziomie konta magazynu lub powyżej. Rola **czytnika** to rola usługi Azure Resource Manager, która umożliwia użytkownikom wyświetlanie zasobów konta magazynu, ale nie modyfikowanie ich. Nie zapewnia uprawnień do odczytu do danych w usłudze Azure Storage, ale tylko do zasobów zarządzania kontem.

Wykonaj następujące kroki, aby przypisać rolę **czytelnika,** aby użytkownik mógł uzyskać dostęp do obiektów blob z witryny Azure portal. W tym przykładzie przypisanie jest ograniczone do konta magazynu:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.
1. Wybierz **formant dostępu (IAM),** aby wyświetlić ustawienia kontroli dostępu dla konta magazynu. Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **Czytelnika.** 
1. Z pola **Przypisz dostęp do** wybierz **pozycję Użytkownik, grupa lub podmiot usługi Azure AD**.
1. Wyszukaj, aby zlokalizować podmiot zabezpieczeń, do którego chcesz przypisać rolę.
1. Zapisz przypisanie roli.

Przypisywanie roli **czytnika** jest konieczne tylko dla użytkowników, którzy muszą uzyskać dostęp do obiektów blob lub kolejek przy użyciu witryny Azure portal.

> [!IMPORTANT]
> Wersja zapoznawcza Eksploratora magazynu w witrynie Azure portal nie obsługuje przy użyciu poświadczeń usługi Azure AD do wyświetlania i modyfikowania danych obiektów blob lub kolejki. Eksplorator magazynu w witrynie Azure portal zawsze używa kluczy konta, aby uzyskać dostęp do danych. Aby korzystać z Eksploratora magazynu w witrynie Azure portal, należy przypisać rolę, która obejmuje **microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat ról RBAC dla zasobów magazynu, zobacz [Uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](storage-auth-aad.md). 
- Aby dowiedzieć się więcej o RBAC, zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).
- Aby dowiedzieć się, jak przypisywać przypisania ról RBAC i zarządzać nimi za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejek z poziomu aplikacji magazynu, zobacz [Używanie usługi Azure AD z aplikacjami usługi Azure Storage.](storage-auth-aad-app.md)
