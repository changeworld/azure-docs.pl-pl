---
title: Użyj witryny Azure portal do zarządzania usługi Azure AD praw dostępu do danych obiektów blob i kolejek przy użyciu RBAC — usługi Azure Storage | Dokumentacja firmy Microsoft
description: Kontrola dostępu oparta na rolach (RBAC) w witrynie Azure portal umożliwia przypisywanie dostępu do kontenerów i kolejek do podmiotów zabezpieczeń. Usługa Azure Storage obsługuje niestandardowe i wbudowane role RBAC do uwierzytelniania za pomocą usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ebb67ff295a934b4e125e298018c27a095adc590
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153863"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Udzielanie dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do danych obiektów blob i kolejki. 

Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń usługi Azure AD, Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, nazwy głównej usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano, jak przypisać role RBAC przy użyciu witryny Azure portal. Azure portal udostępnia prosty interfejs przypisywanie ról RBAC i zarządzanie dostępem do zasobów magazynu. Można również przypisywać role RBAC dla zasobów obiektów blob i kolejek przy użyciu narzędzia wiersza polecenia platformy Azure lub interfejsów API zarządzania usługi Azure Storage. Aby uzyskać więcej informacji na temat ról RBAC dla zasobów magazynu, zobacz [uwierzytelnienia dostępu do platformy Azure obiekty BLOB i kolejki, przy użyciu usługi Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określenie zakresu zasobów 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Przypisz role RBAC przy użyciu witryny Azure portal

Po określeniu odpowiedni zakres przypisania roli, przejdź do tego zasobu w witrynie Azure portal. Wyświetlanie **kontrola dostępu (IAM)** ustawienia dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

1. Przypisz odpowiednią rolę RBAC magazynu platformy Azure, aby udzielić dostępu do podmiotu zabezpieczeń usługi Azure AD.

1. Przypisywanie usługi Azure Resource Manager [czytnika](../../role-based-access-control/built-in-roles.md#reader) ról do użytkowników, którzy potrzebują dostępu do kontenerów lub kolejek w witrynie Azure portal przy użyciu swoich poświadczeń usługi Azure AD. 

W poniższych sekcjach opisano każdy z tych kroków, które bardziej szczegółowo.

### <a name="assign-a-built-in-rbac-role"></a>Przypisanie wbudowanej roli RBAC

Przed przypisaniem roli do podmiotu zabezpieczeń, należy wziąć pod uwagę zakres uprawnień, których udzielasz. Przegląd [określenie zakresu zasobów](#determine-resource-scope) sekcji, aby określić odpowiedni zakres.

Przedstawionych tu procedurach przypisuje rolę ograniczone do kontenera, ale możesz wykonać te same kroki, aby przypisać rolę ograniczone do kolejki: 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu i wyświetlić **Przegląd** dla konta.
1. W obszarze usługi wybierz **obiektów blob**. 
1. Odszukaj kontener, dla którego chcesz przypisać rolę i wyświetlanie ustawień kontenera. 
1. Wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla kontenera. Wybierz **przypisań ról** kartę, aby wyświetlić listę przypisań ról.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do kontenera](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. Kliknij przycisk **Dodaj przypisanie roli** przycisk, aby dodać nową rolę.
1. W **Dodaj przypisanie roli** okna, wybierz rolę usługi Azure Storage, którą chcesz przypisać. Następnie wyszukaj, aby zlokalizować podmiotu zabezpieczeń, do którego chcesz przypisać tę rolę.

    ![Zrzut ekranu przedstawiający sposób Przypisz rolę RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Kliknij pozycję **Zapisz**. Tożsamość, do którego jest przypisany do roli na liście wyświetlanej w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że użytkownik dodany teraz ma uprawnienia do odczytu do danych w kontenerze o nazwie *przykładowy kontener*.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

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

- Aby uzyskać więcej informacji na temat ról RBAC dla zasobów magazynu, zobacz [uwierzytelnienia dostępu do platformy Azure obiekty BLOB i kolejki, przy użyciu usługi Azure Active Directory](storage-auth-aad.md). 
- Aby dowiedzieć się więcej o ROLACH, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).
- Aby dowiedzieć się, jak przypisać i zarządzać przypisaniami ról RBAC przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartej na rolach (RBAC) przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z aplikacjami usługi Azure Storage](storage-auth-aad-app.md).
