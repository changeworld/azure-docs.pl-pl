---
title: Zarządzanie prawami dostępu do usługi Azure AD do kontenerów i kolejek o RBAC — usługi Azure Storage za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Kontrola dostępu oparta na rolach (RBAC) w witrynie Azure portal umożliwia przypisywanie dostępu do kontenerów i kolejek do podmiotów zabezpieczeń. Usługa Azure Storage obsługuje niestandardowe i wbudowane role RBAC do uwierzytelniania za pomocą usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 66e6cff40d71842ef19f99d7c96219af83fc9b4e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368242"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal"></a>Udzielanie dostępu do kontenerów platformy Azure i kolejek o ROLACH w witrynie Azure portal

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do kontenerów i kolejki. 

Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń usługi Azure AD, Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, nazwy głównej usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano, jak przypisać role RBAC przy użyciu witryny Azure portal. Azure portal udostępnia prosty interfejs przypisywanie ról RBAC i zarządzanie dostępem do zasobów magazynu. Można również przypisywać role RBAC dla zasobów obiektów blob i kolejek przy użyciu narzędzia wiersza polecenia platformy Azure lub interfejsów API zarządzania usługi Azure Storage. Aby uzyskać więcej informacji na temat ról RBAC dla zasobów magazynu, zobacz [uwierzytelnienia dostępu do platformy Azure obiekty BLOB i kolejki, przy użyciu usługi Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określenie zakresu zasobów 

Przed przypisaniem roli RBAC do podmiotu zabezpieczeń, należy określić zakres dostępu, którą powinien posiadać podmiotu zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest udzielić tylko to możliwe najwęższego zakresu.

Na poniższej liście opisano poziomów, w których możesz ograniczyć dostęp do zasobów platformy Azure obiektów blob i kolejek, począwszy od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich obiektów blob w kontenerze, a także właściwości kontenera i metadanych.
- **Pojedynczej kolejki.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wiadomości w kolejce, a także właściwości kolejki i metadanych.
- **Konto magazynu.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów i ich obiektów blob lub wszystkich kolejek i ich wiadomości.
- **Grupa zasobów.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów lub kolejek we wszystkich kont magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów lub kolejek we wszystkich kont magazynu we wszystkich grupach zasobów w subskrypcji.

Po określeniu żądany zakres przypisania roli, przejdź do odpowiedniego zasobu w witrynie Azure portal. Wyświetlanie **kontrola dostępu (IAM)** ustawienia dla zasobu i postępuj zgodnie z instrukcjami w kolejnych sekcjach, aby zarządzać przypisaniami ról.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Przypisz role RBAC przy użyciu witryny Azure portal

Udzielanie dostępu do zasobów obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD w witrynie Azure portal obejmuje następujące czynności: 

1. Przypisz odpowiednią rolę RBAC magazynu platformy Azure do udzielania dostępu do kontenerów i kolejki. Aby uzyskać dostęp do odczytu, Przypisz **czytnik danych obiektu Blob** lub **czytnik danych kolejki** roli. Do odczytu, zapisu i usuwania dostęp, Przypisz **Współautor danych obiektu Blob** lub **Współautor danych kolejki** roli. Można także przypisać rolę niestandardową.

1. Przypisywanie usługi Azure Resource Manager [czytnika](../../role-based-access-control/built-in-roles.md#reader) ról do użytkowników, którzy potrzebują dostępu do kontenerów lub kolejek w witrynie Azure portal przy użyciu swoich poświadczeń usługi Azure AD. 

W poniższych sekcjach opisano każdy z tych kroków, które bardziej szczegółowo.

### <a name="assign-a-built-in-rbac-role"></a>Przypisanie wbudowanej roli RBAC

Przed przypisaniem roli do podmiotu zabezpieczeń, należy wziąć pod uwagę zakres uprawnień, których udzielasz. Przegląd [określenie zakresu zasobów](#determine-resource-scope) sekcji, aby określić odpowiedni zakres.

Przedstawionych tu procedurach przypisuje rolę ograniczone do kontenera, ale możesz wykonać te same kroki, aby przypisać rolę ograniczone do kolejki: 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu i wyświetlić **Przegląd** dla konta.
1. W obszarze usługi wybierz **obiektów blob**. 
1. Odszukaj kontener, dla którego chcesz przypisać rolę i wyświetlanie ustawień kontenera. 
1. Wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla kontenera. Wybierz **przypisań ról** kartę, aby wyświetlić listę przypisań ról.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do kontenera](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Kliknij przycisk **Dodaj przypisanie roli** przycisk, aby dodać nową rolę.
1. W **Dodaj przypisanie roli** okna, wybierz rolę usługi Azure Storage, którą chcesz przypisać. Następnie wyszukaj, aby zlokalizować podmiotu zabezpieczeń, do którego chcesz przypisać tę rolę.

    ![Zrzut ekranu przedstawiający sposób Przypisz rolę RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Kliknij pozycję **Zapisz**. Tożsamość, do którego jest przypisany do roli na liście wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że użytkownik dodany teraz ma uprawnienia do odczytu do danych w kontenerze o nazwie *przykładowy kontener*.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac/container-scoped-role.png)

Można wykonać podobne kroki, aby przypisać rolę ograniczone do konta magazynu, grupy zasobów lub subskrypcji.

> [!NOTE]
> Jako właściciel konta usługi Azure Storage możesz nie są automatycznie przypisywane uprawnienia dostępu do danych. Należy jawnie przypisać sobie rolę RBAC dla usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konto magazynu, kontenera lub kolejki.
> 
> Nie można przypisać roli do kontenera lub kolejki zakresu, jeśli konto magazynu ma hierarchicznej przestrzeni nazw, włączone.

### <a name="assign-the-reader-role-for-portal-access"></a>Przypisywanie roli Czytelnik do dostępu do portalu

Po przypisaniu roli wbudowanej lub niestandardowej dla usługi Azure Storage do podmiotu zabezpieczeń oznacza udzielenie uprawnień do tego podmiotu zabezpieczeń, aby wykonywać operacje na danych na koncie magazynu. Wbudowane **czytnik danych** role zawierają uprawnień do odczytu do danych w ramach kontenera lub kolejki podczas wbudowane **Współautor Data** role zawierają odczytu, zapisu i usuwania uprawnień do kontenera lub kolejka. Uprawnienia są ograniczone do określonego zasobu.  

Na przykład, jeśli przypisujesz **Współautor danych obiektu Blob magazynu** roli do użytkownika Mary na poziomie kontenera o nazwie **przykładowy kontener**, następnie Mary przyznanych jest do odczytu, zapisu i usuwania dostęp do wszystkich obiektów blob w Ten kontener.

Jednak, jeśli Mary chce, aby wyświetlić obiekt blob w witrynie Azure portal, a następnie **Współautor danych obiektu Blob magazynu** roli samodzielnie, nie będą umożliwiać wystarczających uprawnień, aby przejść za pośrednictwem portalu do obiektu blob, aby go wyświetlić. Dodatkowe uprawnienia usługi Azure AD są wymagane do nawigacji w portalu i wyświetlić inne zasoby, które są widoczne.

Jeśli użytkownicy muszą mieć możliwość dostępu do obiektów blob w witrynie Azure portal, a następnie przypisać je dodatkową rolę RBAC, [czytnika](../../role-based-access-control/built-in-roles.md#reader) roli do tych użytkowników, na poziomie konta magazynu lub nowszej. **Czytnika** roli jest rola usługi Azure Resource Manager, która pozwala użytkownikom na wyświetlanie zasobów konta magazynu, ale nie można ich modyfikować. Nie obejmuje uprawnień do odczytu do danych w usłudze Azure Storage, ale tylko dla konta zarządzania zasobami.

Wykonaj następujące kroki, aby przypisać **czytnika** roli, aby użytkownik mógł korzystać z obiektów blob, w witrynie Azure portal. W tym przykładzie przypisanie obejmuje konta magazynu:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.
1. Wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla konta magazynu. Wybierz **przypisań ról** kartę, aby wyświetlić listę przypisań ról.
1. W **Dodaj przypisanie roli** wybierz **czytnika** roli. 
1. Z **Przypisz dostęp do** pól, zaznacz **użytkownika, grupy lub jednostki usługi Azure AD**.
1. Wyszukaj, aby zlokalizować podmiotu zabezpieczeń, do którego chcesz przypisać rolę.
1. Zapisz przypisania roli.

> [!NOTE]
> Przypisywanie roli Czytelnik jest niezbędne tylko w przypadku użytkowników, którzy potrzebują dostępu do obiektów blob lub kolejkach przy użyciu witryny Azure portal. 

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o ROLACH, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).
- Aby dowiedzieć się, jak przypisać i zarządzać przypisaniami ról RBAC przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z aplikacjami usługi Azure Storage](storage-auth-aad-app.md).
