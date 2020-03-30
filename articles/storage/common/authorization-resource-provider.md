---
title: Dostęp do zasobów zarządzania za pomocą dostawcy zasobów usługi Azure Storage
description: Dostawca zasobów usługi Azure Storage to usługa zapewniająca dostęp do zasobów zarządzania dla usługi Azure Storage. Dostawcy zasobów usługi Azure Storage można używać do tworzenia, aktualizowania, zarządzania i usuwania zasobów, takich jak konta magazynu, prywatne punkty końcowe i klucze dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972351"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Dostęp do zasobów zarządzania za pomocą dostawcy zasobów usługi Azure Storage

Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Dostawca zasobów usługi Azure Storage to usługa oparta na usłudze Azure Resource Manager, która zapewnia dostęp do zasobów zarządzania dla usługi Azure Storage. Dostawcy zasobów usługi Azure Storage można używać do tworzenia, aktualizowania, zarządzania i usuwania zasobów, takich jak konta magazynu, prywatne punkty końcowe i klucze dostępu do konta. Aby uzyskać więcej informacji na temat usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

Dostawcy zasobów usługi Azure Storage można używać do wykonywania akcji, takich jak tworzenie lub usuwanie konta magazynu lub uzyskiwanie listy kont magazynu w ramach subskrypcji. Aby autoryzować żądania względem dostawcy zasobów usługi Azure Storage, użyj usługi Azure Active Directory (Azure AD). W tym artykule opisano sposób przypisywania uprawnień do zasobów zarządzania i wskazuje na przykłady, które pokazują, jak wysuwać żądania względem dostawcy zasobów usługi Azure Storage.

## <a name="management-resources-versus-data-resources"></a>Zasoby do zarządzania a zasoby danych

Firma Microsoft udostępnia dwa interfejsy API REST do pracy z zasobami usługi Azure Storage. Te interfejsy API stanowią podstawę wszystkich akcji, które można wykonać w usłudze Azure Storage. Interfejs API REST usługi Azure Storage umożliwia pracę z danymi na koncie magazynu, w tym danymi obiektu blob, kolejki, pliku i tabeli. Interfejs API REST dostawcy zasobów usługi Azure Storage umożliwia pracę z kontem magazynu i powiązanymi zasobami.

Żądanie, które odczytuje lub zapisuje dane obiektu blob wymaga innych uprawnień niż żądanie, które wykonuje operację zarządzania. RBAC zapewnia szczegółową kontrolę nad uprawnieniami do obu typów zasobów. Po przypisaniu roli RBAC do podmiotu zabezpieczeń upewnij się, że rozumiesz, jakie uprawnienia zostanie przyznany podmiot zabezpieczeń. Aby uzyskać szczegółowe odwołanie opisujące akcje skojarzone z każdą wbudowaną rolą RBAC, zobacz [Wbudowane role zasobów platformy Azure.](../../role-based-access-control/built-in-roles.md)

Usługa Azure Storage obsługuje używanie usługi Azure AD do autoryzowania żądań względem obiektów Blob i magazynu kolejek. Aby uzyskać informacje o rolach RBAC dla operacji danych obiektów blob i kolejek, zobacz [Autoryzowanie dostępu do obiektów blob i kolejek przy użyciu usługi Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Przypisywanie uprawnień do zarządzania za pomocą kontroli dostępu opartej na rolach (RBAC)

Każda subskrypcja platformy Azure ma skojarzoną usługę Azure Active Directory, która zarządza użytkownikami, grupami i aplikacjami. Użytkownik, grupa lub aplikacja jest również określana jako podmiot zabezpieczeń w kontekście [platformy tożsamości firmy Microsoft](/azure/active-directory/develop/). Można udzielić dostępu do zasobów w ramach subskrypcji podmiotowi zabezpieczeń zdefiniowanemu w usłudze Active Directory przy użyciu kontroli dostępu opartej na rolach (RBAC).

Po przypisaniu roli RBAC do podmiotu zabezpieczeń, należy również wskazać zakres, w którym uprawnienia przyznane przez rolę są w mocy. W przypadku operacji zarządzania można przypisać rolę na poziomie subskrypcji, grupy zasobów lub konta magazynu. Rolę RBAC można przypisać do podmiotu zabezpieczeń przy użyciu [witryny Azure portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure,](../../cli-install-nodejs.md) [programu PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsu API REST dostawcy zasobów usługi Azure Storage.](/rest/api/storagerp)

Aby uzyskać więcej informacji na temat funkcji RBAC, zobacz Co to [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](../../role-based-access-control/rbac-and-directory-admin-roles.md)jest kontrola [dostępu oparta na rolach (RBAC) dla zasobów platformy Azure?](../../role-based-access-control/overview.md)

### <a name="built-in-roles-for-management-operations"></a>Wbudowane role dla operacji zarządzania

Platforma Azure udostępnia wbudowane role, które udzielają uprawnień do wywoływania operacji zarządzania. Usługa Azure Storage udostępnia również wbudowane role specjalnie do użytku z dostawcą zasobów usługi Azure Storage.

Wbudowane role, które udzielają uprawnień do wywoływania operacji zarządzania magazynem, obejmują role opisane w poniższej tabeli:

|    Rola RBAC    |    Opis    |    Zawiera dostęp do kluczy konta?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Właściciel** | Może zarządzać wszystkimi zasobami magazynu i dostępem do zasobów.  | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |
| **Współautor**  | Może zarządzać wszystkimi zasobami magazynu, ale nie może zarządzać przypisanie do zasobów. | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |
| **Czytelnik** | Można wyświetlić informacje o koncie magazynu, ale nie można wyświetlić klucze konta. | Nie. |
| **Współautor konta magazynu** | Może zarządzać kontem magazynu, utrzymywalać informacji o grupach zasobów i zasobach subskrypcji oraz tworzyć wdrożenia grup zasobów subskrypcji i zarządzać nimi. | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |
| **Administrator dostępu użytkowników** | Może zarządzać dostępem do konta magazynu.   | Tak, umożliwia podmiotowi zabezpieczeń przypisywanie wszelkich uprawnień do siebie i innych osób. |
| **Współautor maszyny wirtualnej** | Może zarządzać maszynami wirtualnymi, ale nie kontem magazynu, z którym są połączone.   | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |

Trzecia kolumna w tabeli wskazuje, czy wbudowana rola obsługuje **microsoft.Storage/storageAccounts/listkeys/action**. Ta akcja udziela uprawnień do odczytu i ponownego generowania kluczy konta magazynu. Uprawnienia dostępu do zasobów zarządzania usługi Azure Storage nie obejmują również uprawnień dostępu do danych. Jeśli jednak użytkownik ma dostęp do kluczy konta, może użyć kluczy konta, aby uzyskać dostęp do danych usługi Azure Storage za pośrednictwem autoryzacji klucza udostępnionego.

### <a name="custom-roles-for-management-operations"></a>Niestandardowe role dla operacji zarządzania

Platforma Azure obsługuje również definiowanie niestandardowych ról RBAC w celu uzyskania dostępu do zasobów zarządzania. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Role niestandardowe dla zasobów platformy Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Przykłady kodu

Przykłady kodu, które pokazują, jak autoryzować i wywoływać operacje zarządzania z bibliotek zarządzania usługi Azure Storage, zobacz następujące przykłady:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Usługa Azure Resource Manager a wdrożenia klasyczne

Model wdrażania przy użyciu usługi Resource Manager i model klasyczny to dwa różne sposoby wdrażania rozwiązań platformy Azure i zarządzania nimi. Firma Microsoft zaleca używanie modelu wdrażania usługi Azure Resource Manager podczas tworzenia nowego konta magazynu. Jeśli to możliwe, firma Microsoft zaleca również ponownetworzenie istniejących klasycznych kont magazynu w modelu Menedżera zasobów. Chociaż można utworzyć konto magazynu przy użyciu klasycznego modelu wdrażania, model klasyczny jest mniej elastyczny i ostatecznie zostanie przestarzały.

Aby uzyskać więcej informacji na temat modeli wdrażania platformy Azure, zobacz [Menedżer zasobów i wdrażanie klasyczne](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
- [Co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure?](../../role-based-access-control/overview.md)
- [Cele skalowalności dla dostawcy zasobów usługi Azure Storage](scalability-targets-resource-provider.md)
