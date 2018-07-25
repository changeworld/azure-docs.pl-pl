---
title: Co to jest tożsamość usługi zarządzanej dla zasobów platformy Azure
description: Omówienie tożsamości usługi zarządzanej (MSI) dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: d25d868d8f3542d74de8edf8d7d5d26037409911
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006934"
---
#  <a name="what-is-managed-service-identity-for-azure-resources"></a>Co to jest tożsamość usługi zarządzanej dla zasobów platformy Azure?

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami, które muszą znajdować się w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Zabezpieczanie tych poświadczeń to ważne zadanie. W idealnej sytuacji nie będą one nigdy wyświetlane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Tożsamość usługi zarządzanej ułatwia rozwiązywanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

Tożsamość usługi zarządzanej jest dodawana do warstwy Bezpłatna usługi Azure Active Directory. Jest to ustawienie domyślne subskrypcji platformy Azure. Za używanie tożsamości usługi zarządzanej nie ponosi się żadnych dodatkowych kosztów.

## <a name="how-does-it-work"></a>Jak to działa?

Istnieją dwa typy tożsamości usługi zarządzanej: **przypisana przez system** i **przypisana przez użytkownika**.

- **Tożsamość przypisana przez system** jest włączana bezpośrednio w wystąpieniu usługi platformy Azure. Po włączeniu platforma Azure tworzy tożsamość wystąpienia usługi w dzierżawie usługi Azure AD, która jest zaufaną dzierżawą subskrypcji wystąpienia usługi. Po utworzeniu tożsamości jej poświadczenia są aprowizowane w wystąpieniu usługi. Cykl życiowy tożsamości przypisanej przez system jest bezpośrednio powiązany z wystąpieniem usługi platformy Azure, w którym została ona włączona. Usunięcie wystąpienia usługi spowoduje, że platforma Azure automatycznie oczyści poświadczenia i tożsamość w usłudze Azure AD.
- **Tożsamość przypisana przez użytkownika** jest tworzona jako autonomiczny zasób platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure. Cykl życiowy tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życiowego wystąpień usługi platformy Azure, do których został przypisany.

W wyniku kod może używać tożsamości przypisanej przez system lub użytkownika w celu żądania tokenów dostępu do usług, które obsługują uwierzytelnianie usługi Azure AD. W trakcie tych wszystkich procesów platforma Azure zapewnia stopniową obsługę poświadczeń używanych przez wystąpienie usługi.

Oto przykład współdziałania tożsamości przypisanych przez system z usługą Azure Virtual Machines:

![Przykład działania tożsamości zarządzanej w przypadku maszyny wirtualnej](media/overview/msi-vm-vmextension-imds-example.png)

1. Usługa Azure Resource Manager otrzymuje żądanie włączenia tożsamości przypisanej przez system na maszynie wirtualnej.
2. Usługa Azure Resource Manager tworzy w usłudze Azure AD jednostkę usługi, która będzie reprezentować tożsamość maszyny wirtualnej. Jednostka usługi jest tworzona w dzierżawie usługi Azure AD, która jest zaufana w ramach tej subskrypcji.
3. Usługa Azure Resource Manager konfiguruje tożsamość na maszynie wirtualnej:
    - Aktualizuje punkt końcowy tożsamości usługi Azure Instance Metadata Service przy użyciu certyfikatu i identyfikatora klienta jednostki usługi.
    - Aprowizuje rozszerzenie maszyny wirtualnej oraz dodaje certyfikat i identyfikator klienta jednostki usługi. (Czynność do oznaczenia jako przestarzała)
4. Teraz, gdy maszyna wirtualna ma tożsamość, używamy jej informacji o jednostce usługi w celu przyznania maszynie wirtualnej dostępu do zasobów platformy Azure. Jeśli na przykład kod wymaga wywołania usługi Azure Resource Manager, należy przypisać do jednostki usługi maszyny wirtualnej odpowiednią rolę przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure AD. Jeśli kod musi wywołać usługę Key Vault, należy przyznać mu prawa dostępu do określonego wpisu tajnego lub klucza w usłudze Key Vault.
5. Kod uruchomiony na maszynie wirtualnej może żądać tokenu z dwóch punktów końcowych, które są dostępne jedynie z poziomu maszyny wirtualnej:

    - Punkt końcowy tożsamości usługi Azure Instance Metadata Service (IMDS): http://169.254.169.254/metadata/identity/oauth2/token (zalecane)
        - Parametr zasobu określa usługę, do której jest wysyłany token. Jeśli na przykład chcesz, aby kod był uwierzytelniany w usłudze Azure Resource Manager, użyj opcji resource=https://management.azure.com/.
        - Parametr wersji interfejsu API określa wersję usługi IMDS, użyj wartości api-version=2018-02-01 lub nowszej.
    - Punkt końcowy rozszerzenia maszyny wirtualnej: http://localhost:50342/oauth2/token (opcja do oznaczenia jako przestarzała)
        - Parametr zasobu określa usługę, do której jest wysyłany token. Jeśli na przykład chcesz, aby kod był uwierzytelniany w usłudze Azure Resource Manager, użyj opcji resource=https://management.azure.com/.

6. W usłudze Azure AD jest wykonywane wywołanie żądające tokenu dostępu, jak określono w kroku 5, przy użyciu certyfikatu i identyfikatora klienta skonfigurowanego w kroku 3. Usługa Azure AD zwraca token dostępu powiązany z internetowym tokenem JSON (JWT, JSON Web Token).
7. Kod wysyła token dostępu w wywołaniu do usługi, która obsługuje uwierzytelnianie w usłudze Azure AD.

Przy użyciu tego samego diagramu przedstawiamy poniżej przykład współpracy tożsamości przypisanej przez użytkownika z usługą Azure Virtual Machines.

1. Usługa Azure Resource Manager otrzymuje żądanie włączenia tożsamości przypisanej przez użytkownika.
2. Usługa Azure Resource Manager tworzy w usłudze Azure AD jednostkę usługi, która będzie reprezentować tożsamość przypisaną przez użytkownika. Jednostka usługi jest tworzona w dzierżawie usługi Azure AD, która jest zaufana w ramach tej subskrypcji.
3. Usługa Azure Resource Manager otrzymuje żądanie skonfigurowania tożsamości przypisanej przez użytkownika na maszynie wirtualnej:
    - Aktualizuje punkt końcowy tożsamości usługi Azure Instance Metadata Service przy użyciu certyfikatu i identyfikatora klienta jednostki usługi tożsamości przypisanej przez użytkownika.
    - Aprowizuje rozszerzenie maszyny wirtualnej i dodaje certyfikat i identyfikator klienta jednostki usługi tożsamości przypisanej przez użytkownika (czynność do oznaczenia jako przestarzała).
4. Teraz, gdy tożsamość przypisana przez użytkownika została utworzona, używamy jej informacji o jednostce usługi w celu przyznania jej dostępu do zasobów platformy Azure. Jeśli na przykład kod wymaga wywołania usługi Azure Resource Manager, należy przypisać do jednostki usługi tożsamości przypisanej przez użytkownika odpowiednią rolę przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure AD. Jeśli kod musi wywołać usługę Key Vault, należy przyznać mu prawa dostępu do określonego wpisu tajnego lub klucza w usłudze Key Vault. Uwaga: ten krok można również wykonać przed krokiem 3.
5. Kod uruchomiony na maszynie wirtualnej może żądać tokenu z dwóch punktów końcowych, które są dostępne jedynie z poziomu maszyny wirtualnej:

    - Punkt końcowy tożsamości usługi Azure Instance Metadata Service (IMDS): http://169.254.169.254/metadata/identity/oauth2/token (zalecane)
        - Parametr zasobu określa usługę, do której jest wysyłany token. Jeśli na przykład chcesz, aby kod był uwierzytelniany w usłudze Azure Resource Manager, użyj opcji resource=https://management.azure.com/.
        - Parametr Identyfikator klienta określa tożsamość, dla której wymagany jest token. Jest to wymagane do usunięcia niejednoznaczności, gdy na jednej maszynie wirtualnej znajduje się więcej niż jedna tożsamość przypisana przez użytkownika.
        - Parametr wersji interfejsu API określa wersję usługi IMDS, użyj wartości api-version=2018-02-01 lub nowszej.

    - Punkt końcowy rozszerzenia maszyny wirtualnej: http://localhost:50342/oauth2/token (opcja do oznaczenia jako przestarzała)
        - Parametr zasobu określa usługę, do której jest wysyłany token. Jeśli na przykład chcesz, aby kod był uwierzytelniany w usłudze Azure Resource Manager, użyj opcji resource=https://management.azure.com/.
        - Parametr Identyfikator klienta określa tożsamość, dla której wymagany jest token. Jest to wymagane do usunięcia niejednoznaczności, gdy na jednej maszynie wirtualnej znajduje się więcej niż jedna tożsamość przypisana przez użytkownika.
6. W usłudze Azure AD jest wykonywane wywołanie żądające tokenu dostępu, jak określono w kroku 5, przy użyciu certyfikatu i identyfikatora klienta skonfigurowanego w kroku 3. Usługa Azure AD zwraca token dostępu powiązany z internetowym tokenem JSON (JWT, JSON Web Token).
7. Kod wysyła token dostępu w wywołaniu do usługi, która obsługuje uwierzytelnianie w usłudze Azure AD.
     
## <a name="try-managed-service-identity"></a>Wypróbuj tożsamość usługi zarządzanej

Zapoznaj się z samouczkiem dotyczącym tożsamość usługi zarządzanej, aby dowiedzieć się więcej na temat kompleksowych scenariuszy uzyskiwania dostępu do różnych zasobów platformy Azure:
<br><br>
| Rodzaj zasobu z tożsamością zarządzaną | Omawiane tematy |
| ------- | -------- |
| Maszyna wirtualna (Windows) | [Access Azure Data Lake Store with a Windows VM Managed Service Identity](tutorial-windows-vm-access-datalake.md) (Uzyskiwanie dostępu do usługi Azure Data Lake Store przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows) |
|                    | [Access Azure Resource Manager with a Windows VM Managed Service Identity](tutorial-windows-vm-access-arm.md) (Uzyskiwanie dostępu do usługi Azure Resource Manager przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows) |
|                    | [Access Azure SQL with a Windows VM Managed Service Identity](tutorial-windows-vm-access-sql.md) (Uzyskiwanie dostępu do usługi Azure SQL przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows) |
|                    | [Access Azure Storage via access key with a Windows VM Managed Service Identity](tutorial-windows-vm-access-storage.md) (Uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem klucza dostępu przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows) |
|                    | [Access Azure Storage via SAS with a Windows VM Managed Service Identity](tutorial-windows-vm-access-storage-sas.md) (Uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem sygnatury dostępu współdzielonego przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows) |
|                    | [Access a non-Azure AD resource with a Windows VM Managed Service Identity and Azure Key Vault](tutorial-windows-vm-access-nonaad.md) (Uzyskiwanie dostępu do zasobu innego niż usługi AD Azure przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows i usługi Azure Key Vault) |
| Maszyna wirtualna platformy Azure (Linux)   | [Access Azure Data Lake Store with a Linux VM Managed Service Identity](tutorial-linux-vm-access-datalake.md) (Uzyskiwanie dostępu do usługi Azure Data Lake Store przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux) |
|                    | [Access Azure Resource Manager with a Linux VM Managed Service Identity](tutorial-linux-vm-access-arm.md) (Uzyskiwanie dostępu do usługi Azure Resource Manager przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux) |
|                    | [Access Azure Storage via access key with a Linux VM Managed Service Identity](tutorial-linux-vm-access-storage.md) (Uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem klucza dostępu przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux) |
|                    | [Access Azure Storage via SAS with a Linux VM Managed Service Identity](tutorial-linux-vm-access-storage-sas.md) (Uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem sygnatury dostępu współdzielonego przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux) |
|                    | [Access a non-Azure AD resource with a Linux VM Managed Service Identity and Azure Key Vault](tutorial-linux-vm-access-nonaad.md) (Uzyskiwanie dostępu do zasobu innego niż usługi AD Azure przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux i usługi Azure Key Vault) |
| Azure App Service  | [Use Managed Service Identity with Azure App Service or Azure Functions](/azure/app-service/app-service-managed-service-identity) (Używanie tożsamości usługi zarządzanej z usługą Azure App Service lub Azure Functions) |
| Azure Functions    | [Use Managed Service Identity with Azure App Service or Azure Functions](/azure/app-service/app-service-managed-service-identity) (Używanie tożsamości usługi zarządzanej z usługą Azure App Service lub Azure Functions) |
| Azure Service Bus  | [Use Managed Service Identity with Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md) (Używanie tożsamości usługi zarządzanej z usługą Azure Service Bus) |
| Azure Event Hubs   | [Use Managed Service Identity with Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md) (Używanie tożsamości usługi zarządzanej z usługą Azure Event Hubs) |
| Usługa Azure API Management | [Use Managed Service Identity with Azure Event Hubs (Używanie tożsamości usługi zarządzanej z usługą Azure Event Hubs)](../../api-management/api-management-howto-use-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Które usługi platformy Azure obsługują tożsamość usługi zarządzanej?

Tożsamości zarządzane mogą służyć do uwierzytelniania w usługach obsługujących uwierzytelnianie usługi Azure AD. Listę usług platformy Azure, które obsługują tożsamość usługi zarządzanej, można znaleźć w artykule:
- [Usługi, które obsługują tożsamość usługi zarządzanej](services-support-msi.md)

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z tożsamością usługi zarządzanej platformy Azure, korzystając z następujących samouczków Szybki start:

* [Use a Windows VM Managed Service Identity to access Resource Manager — Windows VM](tutorial-windows-vm-access-arm.md) (Używanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Resource Manager — maszyna wirtualna z systemem Windows)
* [Use a Linux VM Managed Service Identity to access Resource Manager — Linux VM](tutorial-linux-vm-access-arm.md) (Używanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Resource Manager — maszyna wirtualna z systemem Linux)
